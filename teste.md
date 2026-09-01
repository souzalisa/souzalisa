## Catálogo e encomendas

### Front-End

quadro encomendas sprint 2 v3108/quadro-encomendas/public/catalogo.html


Estrutura da página do catálogo de artistas contendo quadro de encomendas (com link para o feed) e filtros avançados (ordenar por mais recentes, menor preço e maior preço).


### Back-End

quadro encomendas sprint 2 v3108/quadro-encomendas/api/catalogo.php


Consulta ao Banco de Dados, buscando registro na tabela encomendas utilizando filtros via método GET.


    if (!empty($_GET['categoria'])) {
    $categoria = $_GET['categoria'];
    $sql = $sql . " AND categoria = '$categoria'";
    }
 
