# Estilizando instruções SQL - Parte 4

Aquela busca continua por padrões e excelência parece não ter fim. 😅

Criação de tabelas e modelagem são bem-vindas em nossa linguagem nativa para facilitar a compreensão. Porém, nomear
esses objetos em inglês "casam" muito melhor e são padrão de mercado. Imagine trabalhar com pessoas de outras
nacionalidades, há um bom ponto aqui.

## Padronizando a codificação em inglês

Casos comuns de se deparar:

- Palavras reservadas em algumas linguagens:
  - Data — em pt-br: dados, em SQL é **reserved word** para tipo, funções, ou keyword.
  - Time — em inglês: tempo, em pt-br: equipe.
  - Status — palavra comum, mas usada em alguns comandos ou funções internas.

Para evitar problemas, use delimitadores com **colchetes**:

```sql
-- ❌ Exemplo com tabelas em Portugûes. Nem sempre fica legal colocar no plural.
SELECT * FROM TimesDeFutebol
SELECT * FROM PedidosPendentes
SELECT * FROM ProdutosEstocados

-- ✅ Exemplo com tabelas em Inglês. Fica mais natural a leitura e escrita.
SELECT * FROM FootballTeams
SELECT * FROM PendingOrders
SELECT * FROM StockedProducts

-- ❌ Exemplo com delimitador em PT-BR
SELECT [Time] FROM TimesDeFutebol

-- ✅ Exemplo direto, sem delimitador em EN
SELECT Team FROM FootballTeams
```

Uma convenção para te convencer: Tabelas no **plural** e colunas no **singular**.

```sql
-- Exemplo de criação de tabela
CREATE TABLE FootballTeams
(
  Id UNIQUEIDENTIFIER PRIMARY KEY DEFAULT NEWID(),
  Name NVARCHAR(100) NOT NULL,
  FoundedYear INT,
  Stadium NVARCHAR(150),
  City NVARCHAR(100),
  Country NVARCHAR(100),
  Coach NVARCHAR(100),
  ChampionshipsWon INT DEFAULT 0
)

-- Exemplo com query
SELECT
  Id,
  Name,
  FoundedYear,
  Stadium,
  City,
  Country,
  Coach,
  ChampionshipsWon
FROM
  FootballTeams
```

É isso ai! Bons estudos e bons SQL's. 🛢️
