## CRUD_CATEGORIAS_PORTFOLIO

Esse arquivo realiza o armazenamento de encomendas realizadas no site e o status de disponibilidade no Banco de Dados MySQL.

## Estrutura do Banco de Dados


### -- Tabela categorias

Mostra o cliente que pediu, o que ele pediu e o prazo para o criador finalizar e entregar o produto final para o cliente:

    
    
    CREATE TABLE IF NOT EXISTS categorias (

    id INT AUTO_INCREMENT PRIMARY KEY,

    nome VARCHAR(100) NOT NULL,

    descricao TEXT NULL

    ) ENGINE=InnoDB;



### -- Tabela portfólio

Status da encomenda que só é permitido nessas condições (aberto, em andamento, concluído e cancelado), valor em dinheiro e estilo que o cliente deseja:

    
    CREATE TABLE IF NOT EXISTS portfolio_itens (

    id INT AUTO_INCREMENT PRIMARY KEY,

    artista INT NOT NULL,

    categoria INT NOT NULL,

    titulo VARCHAR(150) NOT NULL,

    arquivo VARCHAR(255) NOT NULL,


    
    
### -- Tipo de mídia

    tipo_midia ENUM('imagem', 'video', 'audio') NOT NULL,

    criado_em DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_portfolio_artista FOREIGN KEY (artista) REFERENCES usuarios(id),

    CONSTRAINT fk_portfolio_categoria FOREIGN KEY (categoria) REFERENCES categorias(id)

    ) ENGINE=InnoDB;



##  Categoria e Portfólio


CRUD_CATEGORIAS_PORTFOLIO/pages/categorias/index.php

Cadastrar nova categoria e nova obra no portfólio do usuário.

### SOMENTE ADMIN CONSEGUE ACRESCENTAR UMA CATEGORIA

    <div class="cabecalho-pagina">

    		<h1>Categorias</h1>

    		<?php if (($_SESSION['usuario']['tipo'] ?? '') === 'admin'): ?>

       	 		<button class="botao botao--primario" id="btn-nova-categoria">+Nova categoria</button>

    		<?php endif; ?>
    </div>


### ARTISTA ACRESCENTA UMA OBRA NO SEU PERFIL

    <div class="cabecalho-pagina">

    		<h1>Portfólio</h1>

    		<?php if (($_SESSION['usuario']['tipo'] ?? '') === 'artista'): ?>

        			<button class="botao botao--primario" id="btn-nova-obra">+ Adicionar obra</button>

    		<?php endif; ?>
        
    </div>

