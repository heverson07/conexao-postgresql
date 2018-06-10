<?php

class banco {

    private $conn;
    private $db;
    private $query;
    private $data;
    private $depurar = false;

    function conectar($dbname = "postgres", $host = "127.0.0.1", $user = "postgres", $password = "1234", $port = "5432") {

        $stat = pg_connection_status($this->getConn());
        if ($stat === PGSQL_CONNECTION_OK) {
            return $this->getConn();
        } else {
            $this->setConn(pg_connect("host=$host port=$port dbname=$dbname user=$user password=$password"));
        }

        return $this->getConn();

       
    }

    function seleciona($tabela, $campos, $where = NULL, $order_by = NULL, $group_by = NULL, $limit = NULL, $offset = NULL) {

        $conexao = $this->conectar();

        $this->query = 'SELECT ' . $campos . ' FROM ' . $tabela;

        if ($where != NULL)
            $this->query .= ' WHERE ' . $where;

        if ($group_by != NULL)
            $this->query .= ' GROUP BY ' . $group_by;

        if ($order_by != NULL)
            $this->query .= ' ORDER BY ' . $order_by;

        if ($limit != NULL)
            $this->query .= ' LIMIT ' . $limit;

        if ($offset != NULL)
            $this->query .= ' OFFSET ' . $offset;

        if ($this->isDepurar()) {
            echo $this->query . "<br>";
        }

        $result = pg_query($conexao, $this->query);

        return $result;

        $this->LiberaMemoria($result);
    }

    function insere_select($tabela, $campos, $valores) {

        $conexao = $this->conectar();

        $this->query = "INSERT INTO " . $tabela . " (" . $campos . ") " . $valores;


        if ($this->isDepurar()) {
            echo $this->query . "<br>";
        }

        $result = pg_query($conexao, $this->query);


        return $result;

        $this->LiberaMemoria($result);
    }

    function insere($tabela, $campos, $valores) {

        $conexao = $this->conectar();

        $this->query = "INSERT INTO " . $tabela . " (" . $campos . ") VALUES (" . $valores . ")";

        if ($this->isDepurar()) {
            echo $this->query . "<br>";
        }

        $result = pg_query($conexao, $this->query);


        return $result;

        $this->LiberaMemoria($result);
    }

    function apaga($tabela, $where = NULL) {

        $conexao = $this->conectar();

        $this->query = "DELETE FROM " . $tabela;

        if ($where != NULL)
            $this->query .= " WHERE " . $where;

        if ($this->isDepurar()) {
            echo $this->query . "<br>";
        }

        $result = pg_query($conexao, $this->query);

        return $result;

        $this->LiberaMemoria($result);
    }

    
    function altera($tabela, $campos, $where = NULL) {

        $conexao = $this->conectar();

        $this->query = "UPDATE " . $tabela . " SET " . $campos;

        if ($where != NULL) {
            $this->query .= " WHERE " . $where;
        }

        if ($this->isDepurar()) {
            echo $this->query . "<br>";
        }


        $result = pg_query($conexao, $this->query);

        return $result;

        $this->LiberaMemoria($result);
    }

    function ultimo_registro($tabela, $campos) {

        $conexao = $this->conectar();

        $this->query = "SELECT MAX(" . $campos . ") as ultimo FROM " . $tabela;

        if ($this->isDepurar()) {
            echo $this->query . "<br>";
        }

        $result = pg_query($conexao, $this->query);

        return $result;

        $this->LiberaMemoria($result);
    }

    function LiberaMemoria($result) {

        pg_free_result($result);
    }

    function executar_funcao($nome_esquema, $nome_funcao, $parametros) {
        $this->query = "SELECT * FROM " . $nome_esquema . "." . $nome_funcao . "(" . $parametros . ")";
        $conexao = $this->conectar();

        if ($this->isDepurar()) {
            echo $this->query . "<br>";
        }

        $result = pg_query($conexao, $this->query);

        return $result;
    }

    function rel($nome_esquema, $nome_funcao, $parametros, $depurar) {
        
    }

    function iniciarTransacao() {
        $conexao = $this->conectar();
        pg_query($conexao, "BEGIN TRANSACTION");

        if ($this->isDepurar()) {
            echo "BEGIN" . "<br>";
        }
    }

    function confirmarTransacao() {
        $conexao = $this->conectar();
        pg_query($conexao, "COMMIT");

        if ($this->isDepurar()) {
            echo "COMMIT" . "<br>";
        }
    }
    
    function confirmarImpressao() {
        $conexao = $this->conectar();
        pg_query($conexao, "ROLLBACK");

        if ($this->isDepurar()) {
            echo "ROLLBACK" . "<br>";
        }
    }

    function desfazerTransacao() {
        $conexao = $this->conectar();
        pg_query($conexao, "ROLLBACK");

        if ($this->isDepurar()) {
            echo "ROLLBACK" . "<br>";
        }
    }

    public function obterProximoValorDaSequencia($sequencia) {
        $conexao = $this->conectar();
        $this->query = "SELECT nextval('" . $sequencia . "') valor";

        if ($this->isDepurar()) {
            echo $this->query . "<br>";
        }

        $result = pg_query($conexao, $this->query);

        return pg_fetch_result($result, 0, 'valor');
    }

   

    private function getConn() {
        return $this->conn;
    }

    private function setConn($conn) {
        $this->conn = $conn;
    }

    private function isDepurar() {
        return $this->depurar;
    }

    public function setDepurar($depurar) {
        $this->depurar = $depurar;
    }

}

?>
