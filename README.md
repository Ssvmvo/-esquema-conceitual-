# -esquema-conceitual-
Para este cenário você irá utilizar seu esquema conceitual, criado no desafio do módulo de modelagem de BD com modelo ER, para criar o esquema lógico para o contexto de uma oficina. Neste desafio, você definirá todas as etapas. Desde o esquema até a implementação do banco de dados. Sendo assim, neste projeto você será o protagonista. 

1. Modelagem do Banco de Dados
Entidades Principais:

Cliente: Informações sobre os clientes que trazem veículos para manutenção.
Veículo: Dados dos veículos dos clientes, incluindo marca, modelo e ano.
Serviço: Serviços oferecidos pela oficina, como troca de óleo, alinhamento, etc.
Ordem de Serviço (OS): Registro dos serviços solicitados por um cliente para um veículo.
Funcionário: Informações sobre os funcionários da oficina que realizam os serviços.
Pagamento: Registro das transações realizadas para os serviços prestados.
Relacionamentos:

Um cliente pode ter vários veículos.
Um veículo pode ter várias ordens de serviço.
Uma ordem de serviço pode incluir vários serviços.
Um funcionário pode ser responsável por várias ordens de serviço.
Cada ordem de serviço tem um pagamento associado.
2. Esquema Lógico do Banco de Dados
Modelo Relacional:

Cliente (cliente_id, nome, telefone, endereco)
Veículo (veiculo_id, cliente_id, marca, modelo, ano, placa)
Serviço (servico_id, descricao, preco)
Ordem_Servico (os_id, veiculo_id, funcionario_id, data, status)
Servicos_Ordem (os_id, servico_id)
Funcionário (funcionario_id, nome, cargo)
Pagamento (pagamento_id, os_id, valor, data_pagamento, forma_pagamento)
3. Script SQL para Criação do Banco de Dados
Aqui está o script SQL que implementa o esquema lógico:

sql
Copiar código
CREATE TABLE Cliente (
    cliente_id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    telefone VARCHAR(20),
    endereco VARCHAR(255)
);

CREATE TABLE Veiculo (
    veiculo_id INT PRIMARY KEY AUTO_INCREMENT,
    cliente_id INT,
    marca VARCHAR(100) NOT NULL,
    modelo VARCHAR(100) NOT NULL,
    ano INT NOT NULL,
    placa VARCHAR(7) UNIQUE NOT NULL,
    FOREIGN KEY (cliente_id) REFERENCES Cliente(cliente_id)
);

CREATE TABLE Servico (
    servico_id INT PRIMARY KEY AUTO_INCREMENT,
    descricao VARCHAR(255) NOT NULL,
    preco DECIMAL(10, 2) NOT NULL
);

CREATE TABLE Funcionario (
    funcionario_id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    cargo VARCHAR(100)
);

CREATE TABLE Ordem_Servico (
    os_id INT PRIMARY KEY AUTO_INCREMENT,
    veiculo_id INT,
    funcionario_id INT,
    data DATETIME DEFAULT CURRENT_TIMESTAMP,
    status ENUM('Pendente', 'Em Andamento', 'Concluída') NOT NULL,
    FOREIGN KEY (veiculo_id) REFERENCES Veiculo(veiculo_id),
    FOREIGN KEY (funcionario_id) REFERENCES Funcionario(funcionario_id)
);

CREATE TABLE Servicos_Ordem (
    os_id INT,
    servico_id INT,
    PRIMARY KEY (os_id, servico_id),
    FOREIGN KEY (os_id) REFERENCES Ordem_Servico(os_id),
    FOREIGN KEY (servico_id) REFERENCES Servico(servico_id)
);

CREATE TABLE Pagamento (
    pagamento_id INT PRIMARY KEY AUTO_INCREMENT,
    os_id INT,
    valor DECIMAL(10, 2) NOT NULL,
    data_pagamento DATETIME DEFAULT CURRENT_TIMESTAMP,
    forma_pagamento VARCHAR(50),
    FOREIGN KEY (os_id) REFERENCES Ordem_Servico(os_id)
);
4. Persistência de Dados
Aqui estão alguns exemplos de inserções de dados para testes:

sql
Copiar código
INSERT INTO Cliente (nome, telefone, endereco) VALUES
('João da Silva', '1234-5678', 'Rua A, 123'),
('Maria Oliveira', '9876-5432', 'Av. B, 456');

INSERT INTO Veiculo (cliente_id, marca, modelo, ano, placa) VALUES
(1, 'Fiat', 'Palio', 2015, 'ABC1D23'),
(2, 'Volkswagen', 'Gol', 2018, 'XYZ4W56');

INSERT INTO Servico (descricao, preco) VALUES
('Troca de óleo', 150.00),
('Alinhamento', 80.00),
('Balanceamento', 50.00);

INSERT INTO Funcionario (nome, cargo) VALUES
('Carlos', 'Mecânico'),
('Ana', 'Assistente');

INSERT INTO Ordem_Servico (veiculo_id, funcionario_id, status) VALUES
(1, 1, 'Pendente'),
(2, 2, 'Em Andamento');

INSERT INTO Servicos_Ordem (os_id, servico_id) VALUES
(1, 1),
(1, 2),
(2, 3);

INSERT INTO Pagamento (os_id, valor, forma_pagamento) VALUES
(1, 230.00, 'Cartão'),
(2, 50.00, 'Dinheiro');
5. Exemplos de Queries SQL
Aqui estão algumas consultas SQL que você pode realizar:

1. Recuperações simples com SELECT Statement:

sql
Copiar código
SELECT nome FROM Cliente;
2. Filtros com WHERE Statement:

sql
Copiar código
SELECT * FROM Veiculo WHERE ano > 2017;
3. Expressões para gerar atributos derivados:

sql
Copiar código
SELECT os_id, (SELECT SUM(preco) FROM Servicos_Ordem so JOIN Servico s ON so.servico_id = s.servico_id WHERE so.os_id = o.os_id) AS total_servicos
FROM Ordem_Servico o;
4. Definição de ordenações dos dados com ORDER BY:

sql
Copiar código
SELECT * FROM Pagamento ORDER BY data_pagamento DESC;
5. Condições de filtros aos grupos – HAVING Statement:

sql
Copiar código
SELECT f.nome, COUNT(os_id) AS total_os
FROM Funcionario f
JOIN Ordem_Servico o ON f.funcionario_id = o.funcionario_id
GROUP BY f.nome
HAVING total_os > 1;
6. Criação de junções entre tabelas:

sql
Copiar código
SELECT c.nome AS cliente, v.marca, v.modelo, os.data, os.status
FROM Cliente c
JOIN Veiculo v ON c.cliente_id = v.cliente_id
JOIN Ordem_Servico os ON v.veiculo_id = os.veiculo_id;
6. Subindo o Projeto para o GitHub
Para finalizar, você deve:

Criar um repositório no GitHub.
Adicionar o script SQL e um arquivo README.md com a descrição do projeto.
Incluir as queries e suas explicações no README.
Exemplo de Descrição do Projeto para o README.md:
markdown
Copiar código
# Projeto de Modelagem de Banco de Dados para Oficina Mecânica

## Descrição do Projeto

Este projeto tem como objetivo criar um banco de dados para gerenciar as operações de uma oficina mecânica, utilizando um modelo relacional. O banco de dados foi projetado para armazenar informações sobre clientes, veículos, serviços prestados, ordens de serviço, funcionários e pagamentos, permitindo uma gestão eficiente das atividades da oficina.

### Estrutura do Banco de Dados

O esquema lógico do banco de dados é composto pelas seguintes entidades:

1. **Cliente**: Armazena informações sobre os clientes da oficina.
2. **Veículo**: Contém dados sobre os veículos dos clientes.
3. **Serviço**: Registra os serviços oferecidos pela oficina.
4. **Ordem de Serviço (OS)**: Registra as solicitações de serviços para os veículos.
5. **Funcionário**: Informações sobre os funcionários da oficina.
6. **Pagamento**: Registra as transações financeiras relacionadas aos serviços.

### Funcionalidades Implementadas

- Criação das tabelas com definição de chaves primárias e estrangeiras.
- Inserção de dados de exemplo para testes.
- Consultas SQL que permitem obter informações sobre clientes, serviços, ordens de serviço, e pagamentos.

### Tecnologias Utilizadas

- **Banco de Dados**: MySQL
- **Linguagem de Consulta**: SQL

### Como Executar o Projeto

1. Clone este repositório para sua máquina local.
2. Execute o script SQL fornecido para criar o esquema do banco de dados.
3. Insira os dados de exemplo conforme o script de persistência de dados.
4. Utilize as consultas SQL fornecidas para testar e explorar o banco de dados.

### Contribuições

Sinta-se à vontade para contribuir com melhorias, sugestões ou relatórios de bugs.
