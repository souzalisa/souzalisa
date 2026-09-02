## CRUD_CATEGORIAS_PORTFOLIO

Essa pasta contém a permissão de somente o administrador adicionar uma categoria e a permissão para o artista publicar sua obra no seu portfólio.

## Estrutura do Banco de Dados


### -- Tabela categorias


Armazena o nome e a descrição de uma categoria publicada por um admin.
    

    
    CREATE TABLE IF NOT EXISTS categorias (

    id INT AUTO_INCREMENT PRIMARY KEY,

    nome VARCHAR(100) NOT NULL,

    descricao TEXT NULL

    ) ENGINE=InnoDB;



### -- Tabela portfólio

Armazena os dados do artista e sua obra publicada em seu perfil


    
    CREATE TABLE IF NOT EXISTS portfolio_itens (

    id INT AUTO_INCREMENT PRIMARY KEY,

    artista INT NOT NULL,

    categoria INT NOT NULL,

    titulo VARCHAR(150) NOT NULL,

    arquivo VARCHAR(255) NOT NULL,


    
    
### -- Tipo de mídia

Guarda o tipo de conteúdo publicado no portfólio do artista



    tipo_midia ENUM('imagem', 'video', 'audio') NOT NULL,

    criado_em DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_portfolio_artista FOREIGN KEY (artista) REFERENCES usuarios(id),

    CONSTRAINT fk_portfolio_categoria FOREIGN KEY (categoria) REFERENCES categorias(id)

    ) ENGINE=InnoDB;




##  Categoria e Portfólio



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

