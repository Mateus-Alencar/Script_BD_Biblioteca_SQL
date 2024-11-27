# Criação de Banco de Dados com PostgreSQL: Aprendizados e Exemplos

Este repositório contém o código que desenvolvi durante um curso de criação de bancos de dados usando PostgreSQL. Aqui, compartilho os principais conceitos e técnicas que aprendi, desde os fundamentos de banco de dados até tópicos avançados, como normalização e álgebra relacional.

## Conteúdo do Repositório
- **Conceitos Básicos**
- **Parte 1:** Banco de Dados de Pedidos
- **Parte 2:** Tópicos Especiais
- **Parte 3:** Álgebra Relacional
- **Parte 4:** Projeto de Banco de Dados

## Conceitos Básicos

No início do curso, aprendi os conceitos fundamentais de bancos de dados:

- **Entidades e Atributos:** Representação de tabelas e colunas no banco de dados.
- **Chaves Primárias:** Identificação única de registros em uma tabela.
- **Chaves Estrangeiras:** Relacionamento entre tabelas.

### Exemplo básico:

```sql
CREATE TABLE clientes (
    cliente_id SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL
);
```

## Parte 1: Banco de Dados de Pedidos

Nesta etapa, construí um banco de dados para gerenciar pedidos, aprendendo:

- **Junções (Joins):** Como conectar dados de várias tabelas.
- **Agrupamento:** Uso de `GROUP BY` para sumarizar informações.
- **Views:** Criação de visualizações personalizadas.

### Exemplo de Join e Agrupamento:

```sql
SELECT 
    clientes.nome,
    COUNT(pedidos.pedido_id) AS total_pedidos
FROM clientes
LEFT JOIN pedidos ON clientes.cliente_id = pedidos.cliente_id
GROUP BY clientes.nome;
```
### Exemplo de View:
```sql
CREATE VIEW pedidos_resumo AS
SELECT 
    cliente_id,
    SUM(valor_total) AS total_gasto
FROM pedidos
GROUP BY cliente_id;
```

# Joins em SQL

**Joins** (ou junções) em SQL são usados para combinar dados de duas ou mais tabelas com base em uma condição de relacionamento entre elas. O tipo de join que você utiliza depende do que você quer incluir no resultado final. Existem vários tipos de joins, sendo os mais comuns:

- **INNER JOIN**: Retorna apenas as linhas que têm correspondência em ambas as tabelas.
- **LEFT JOIN (ou LEFT OUTER JOIN)**: Retorna todos os registros da tabela à esquerda, com as correspondências da tabela à direita. Se não houver correspondência, os valores da tabela à direita serão nulos.
- **RIGHT JOIN (ou RIGHT OUTER JOIN)**: Retorna todos os registros da tabela à direita, com as correspondências da tabela à esquerda. Se não houver correspondência, os valores da tabela à esquerda serão nulos.
- **FULL JOIN (ou FULL OUTER JOIN)**: Retorna todos os registros de ambas as tabelas, com valores nulos onde não houver correspondência.
- **CROSS JOIN**: Retorna o produto cartesiano de ambas as tabelas, ou seja, combina todas as linhas de uma tabela com todas as linhas da outra.

## Tabela Resumo dos Joins

| Tipo de Join   | Retorna                                                           |
|----------------|-------------------------------------------------------------------|
| **INNER JOIN** | Apenas registros que têm correspondência em ambas as tabelas.     |
| **LEFT JOIN**  | Todos os registros da tabela à esquerda, com correspondências da tabela à direita. |
| **RIGHT JOIN** | Todos os registros da tabela à direita, com correspondências da tabela à esquerda. |
| **FULL JOIN**  | Todos os registros de ambas as tabelas, com valores nulos onde não houver correspondência. |
| **CROSS JOIN** | Produto cartesiano de ambas as tabelas.                           |

## Exemplos de Joins

###  **INNER JOIN**
O **INNER JOIN** retorna apenas as linhas em que há uma correspondência em ambas as tabelas.

**Exemplo:**

```sql
SELECT clientes.nome, pedidos.pedido_id
FROM clientes
INNER JOIN pedidos ON clientes.cliente_id = pedidos.cliente_id;
```


## Parte 2: Tópicos Especiais

Técnicas avançadas para melhorar a funcionalidade do banco de dados:

- **Funções:** Automação de cálculos e lógica.
- **Procedimentos Armazenados:** Scripts executados diretamente no banco.
- **Triggers:** Ações automáticas em resposta a eventos.
- **Transações:** Garantia de consistência dos dados.
- **Domínios:** Padronização de tipos de dados.
- **Controle de Usuário:** Configuração de permissões.
  
### Exemplo de Trigger:

```sql
CREATE OR REPLACE FUNCTION atualiza_estoque()
RETURNS TRIGGER AS $$
BEGIN
    UPDATE produtos
    SET estoque = estoque - NEW.quantidade
    WHERE produto_id = NEW.produto_id;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_estoque
AFTER INSERT ON pedidos
FOR EACH ROW
EXECUTE FUNCTION atualiza_estoque();
```

### Controle de Usuário:

```sql
create role gerente;
create role estagiario;

grant select, insert, delete on bairro, cliente, complemento, fornecedor, municipio;

grant all on all sequences in schema public to gerente;
grant select on cliente_dados, dados_pedido to estagiario;

create role maria login password '123' in role gerente;
create role pedro login password '321' in role estagiario;
```
Esse código cria dois roles (gerente e estagiario), concede permissões específicas a essas funções e cria dois usuários (maria e pedro) com diferentes permissões de acesso, de acordo com suas funções.

## Parte 3: Álgebra Relacional

Introdução aos fundamentos matemáticos de bancos de dados relacionais:

- **Seleção:** Filtragem de linhas.
- **Projeção:** Seleção de colunas.
- **Junções e Combinações:** Relações entre tabelas.
- **União e Interseção:** Operações para combinar conjuntos de dados.

### Exemplo prático:

```sql
-- Seleção e projeção
SELECT nome, email 
FROM clientes
WHERE cliente_id > 10;
```

## Parte 4: Projeto de Banco de Dados

A etapa final do curso envolveu o projeto e implementação de um banco de dados completo, aplicando:

- **Álgebra Relacional:** Para construir consultas e normalizar dados.
- **Formas Normais:** Normalização para evitar redundância e inconsistências.

[Modelo Conceitual - BD Biblioteca]([URL](https://app.brmodeloweb.com/#!/publicview/67448de373e29bfdd79fb099))

![Captura de tela 2024-11-25 091434](https://github.com/user-attachments/assets/8f94718f-b659-4a54-8e74-8dbbf4153e3e)

### Exemplo de Normalização:

- **Forma 1:** Dados não atômicos.
- **Forma 2:** Eliminação de dependências parciais.
- **Forma 3:** Eliminação de dependências transitivas.

#### Tabela não normalizada:

**Clientes**
| ID  | Nome   | Pedido1 | Pedido2 |

| --- | ------ | ------- | ------- |
| 1   | João   | TV      | Geladeira |

#### Tabela normalizada:
```sql
CREATE TABLE clientes (
    cliente_id SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL
);

CREATE TABLE pedidos (
    pedido_id SERIAL PRIMARY KEY,
    cliente_id INT REFERENCES clientes(cliente_id),
    produto VARCHAR(100)
);
```

# Script Banco de Dados para Biblioteca

Este repositório contém o script SQL completo utilizado para criar o banco de dados de uma biblioteca. O banco de dados foi projetado para gerenciar livros, autores, categorias, usuários e empréstimos, proporcionando uma estrutura organizada para o armazenamento e recuperação de informações essenciais para a operação de uma biblioteca.

## Funcionalidades

- **Estrutura de Tabelas:** Inclui tabelas para livros, autores, categorias, usuários e empréstimos.
- **Relacionamentos:** Implementa os relacionamentos necessários entre as tabelas (por exemplo, livros e autores, usuários e empréstimos).
- **Consultas Básicas:** O script inclui comandos SQL para realizar operações comuns como inserção, atualização, exclusão e busca de registros.
