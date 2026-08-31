# Quadro de encomendas v3108 ‘database.sql’

Esse arquivo realiza o armazenamento de encomendas realizadas no site e o status de disponibilidade no Banco de Dados MySQL.

# Estrutura do Banco de Dados


-- Tabela clientes 

Armazena as informações dos clientes que encomendaram o produto ou serviço do trabalhador, como nome e e-mail:

/* CREATE TABLE clientes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
); */


-- Tabela categorias

Organiza os tipos de artes oferecidos para os clientes:

/* CREATE TABLE categorias (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(50) NOT NULL UNIQUE
); */


—- Tabela encomendas

Mostra o cliente que pediu, o que ele pediu e o prazo para o criador finalizar e entregar o produto final para o cliente:

/* CREATE TABLE encomendas (
    id INT AUTO_INCREMENT PRIMARY KEY,
    cliente_id INT,
    categoria_id INT,
    titulo VARCHAR(100) NOT NULL,
    descricao TEXT,
    prazo DATE NOT NULL,
*/

-- Status e Integridade

Status da encomenda que só é permitido nessas condições (aberto, em andamento, concluído e cancelado), valor em dinheiro e estilo que o cliente deseja:

/*
status ENUM('aberto', 'em_andamento', 'concluido', 'cancelado') DEFAULT 'aberto', 
    preco DECIMAL(10,2) NOT NULL,                  
    estilo VARCHAR(50),
*/

Integridade (bloqueia dados inexistentes utilizando FOREIGN KEY) e Performance (otimiza a velocidade de carregamento do site):

/* FOREIGN KEY (cliente_id) REFERENCES clientes(id),
    FOREIGN KEY (categoria_id) REFERENCES categorias(id),
    */
    INDEX idx_prazo (prazo),
    INDEX idx_status (status) */
