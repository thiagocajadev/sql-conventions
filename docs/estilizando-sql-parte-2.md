# Estilizando instruções SQL - Parte 2

Vamos falar sobre o uso de `ponto e vírgula` ao término das instruções, além de exemplos `CRUD`.

## Uso de ponto e vírgula

Em muitos bancos de dados como **PostgreSQL**, ele é obrigatório para separar múltiplas instruções. Já em outros
como **SQL Server** ou **MySQL**, é opcional em instruções únicas, mas recomendado.

Então, como no último exemplo, vou utilizando `;` ao término das instruções. Isso é muito útil para o caso de executar
um script com comandos SQL em sequência.

> Há uma variação também no uso do ponto e vírgula em uma linha após o final da query.

## Exemplos CRUD

Criar, Ler, Atualizar e Deletar. É o que quase todo sistema faz.

```sql
-- ✅ "Spoiler" com instruções enxutas Inline.
INSERT INTO Tabela(ColunaId, ColunaNome) VALUES (1, 'THIAGO');

SELECT ColunaNome FROM Tabela WHERE ColunaId = 1;

UPDATE Tabela SET ColunaNome = 'TIAGO' WHERE ColunaId = 1;

DELETE FROM Tabela WHERE ColunaId = 1;
```

### Fazendo `INSERT` (Create)

Se você veio para parte 2, é porque já está "convertido" a estilização vertical 😃.

```sql
-- ✅ Fica fácil adicionar mais colunas e valores, criando uma nova linha, sem mexer no layout do comando.
-- Aqui mantemos o nome da tabela a frente do comando, evitando espaçamento extra na indentação.
INSERT INTO Tabela 
(
  ColunaId, 
  ColunaNome, 
  ColunaEmail
) 
VALUES
(
  1, 
  'THIAGO', 
  'thiago@email.com'
);

-- Adicionando mais colunas e valores.
INSERT INTO Tabela
(
  ColunaId,
  ColunaNome,
  ColunaEmail,
  ColunaTelefone,
  ColunaDataNascimento,
  ColunaEndereco,
  ColunaCidade,
  ColunaEstado,
  ColunaPais,
  ColunaTimeDoCoracao,
  ColunaAtivo,
  ColunaStatusId
)
VALUES
(
  1,
  'THIAGO',
  'thiago@email.com',
  '(11) 91234-5678',
  '1990-05-15',
  'Rua Exemplo, 123',
  'São Paulo',
  'SP',
  'Brasil',
  'SÃO PAULO FUTEBOL CLUBE',
  1,
  2
);

-- Populando uma tabela com dados de outra tabela INSERT... SELECT.
INSERT INTO Tabela
(
  ColunaId,
  ColunaNome,
  ColunaEmail,
  ColunaTelefone,
  ColunaDataNascimento,
  ColunaEndereco,
  ColunaCidade,
  ColunaEstado,
  ColunaPais,
  ColunaTimeDoCoracao,
  ColunaAtivo,
  ColunaStatusId
)
SELECT
  Id,
  Nome,
  Email,
  Telefone,
  DataNascimento,
  Endereco,
  Cidade,
  Estado,
  Pais,
  TimeDoCoracao,
  Ativo,
  StatusId
FROM
  OutraTabela
WHERE
  Ativo = 1;
```

### Fazendo `SELECT` (Read)

```sql
-- ✅ Fica o detalhe do ponto e virgula na última linha do comando, útil para indentação de scripts declarados
-- explicitamente em aplicações. Ex: Javascript, C#, Kotlin.
SELECT
  ColunaId,
  ColunaNome,
  OutraTabela.ColunaDescricaoStatus
FROM
  Tabela
JOIN
  OutraTabela
  ON Tabela.ColunaStatusId = OutraTabela.ColunaStatusId AND
     Tabela.ColunaAtivo = OutraTabela.ColunaAtivo AND
     OutraTabela.ColunaTipo = 'PADRAO'
WHERE
  ColunaId = 1 AND
  ColunaNome = 'THIAGO' AND
  ColunaAtivo = 1
;
```

### Fazendo `UPDATE`

Seguindo o estilo vertical:

```sql
-- ✅ Fica fácil adicionar ou remover condições, sem mexer no layout do comando.
UPDATE 
  Tabela
SET 
  ColunaNome = 'THIAGO',
  ColunaEmail = 'thiago@email.com'
WHERE
  ColunaId = 1;

-- Adicionando mais condições
UPDATE
  Tabela
SET
  ColunaNome = 'THIAGO',
  ColunaEmail = 'thiago@email.com',
  ColunaTelefone = '(11) 91234-5678',
  ColunaDataNascimento = '1990-05-15',
  ColunaEndereco = 'Rua Exemplo, 123',
  ColunaCidade = 'São Paulo',
  ColunaEstado = 'SP',
  ColunaPais = 'Brasil',
  ColunaTimeDoCoracao = 'SÃO PAULO',
  ColunaAtivo = 1,
  ColunaStatusId = 2
WHERE
  ColunaId = 1;
```

Outros exemplos de atualização, fazendo um **UPDATE** com **FROM**, referênciando dados de uma tabela externa:

```sql
-- ✅ FROM no UPDATE serve pra atualizar uma tabela, usando dados que estão em outra.
UPDATE
  TabelaClientes
SET
  Email = NovosEmailsDeOutraTabela.NovoEmail
FROM
  NovosEmailsDeOutraTabela
WHERE
  TabelaClientes.Id = NovosEmailsDeOutraTabela.ClienteId;

-- Complicando um pouco com uma condicional. Lembre-se, fuja quando possível.
UPDATE
  Tabela
SET
  ColunaStatusId = (
    CASE
        WHEN OutraTabela.ColunaAtivo = 1 THEN 2
        ELSE 3
    END
  ),
  ColunaEmail = OutraTabela.NovoEmail,
  ColunaTelefone = OutraTabela.NovoTelefone
FROM 
  OutraTabela
WHERE
  Tabela.ColunaId = OutraTabela.ColunaId AND
  Tabela.ColunaAtivo = 1 AND
  OutraTabela.DataAtualizacao >= '2025-01-01';
```

Nesse último exemplo, fica aquela curiosidade, o que será que esses **números mágicos** representam?
**Coluna Ativo** pode ser 1, 2 ou 3. Seria bom deixar um comentário no script, mas **comentários também são perigosos**,
pois o código pode mudar e alguém pode não se atentar a atualizar o comentário. Em outro momento vamos buscar soluções
pra isso.

### Fazendo `DELETE`

Será que realmente é necessário apagar dados? Sempre faça essa pergunta antes que seja tarde. Às vezes um caminho mais
seguro é apenas **inativar** ou **anonimizar os dados**. E aqui temos então 2 opções — **DELETE** e **SOFT DELETE**.

```sql
-- ✅ Normalmente apagar dados são instruções bem enxutas para uma única tabela envolvida.
DELETE FROM
 Tabela 
WHERE 
  ColunaId = 1;

-- Exemplo apagando dados de uma tabela, com o critério de comparação sendo uma outra tabela.
DELETE FROM
  Tabela
JOIN
  OutraTabela ON Tabela.ColunaId = OutraTabela.ColunaId
WHERE
  Tabela.ColunaAtivo = 0 AND
  OutraTabela.DataAtualizacao < '2024-01-01' AND
  OutraTabela.ColunaStatusId IN (3, 4, 5);
```

Agora um **SOFT DELETE**, que só inativa os dados.

```sql
-- ✅ O SOFT DELETE na prática é um UPDATE. Ao invés de remover os dados,
-- alteramos os registros de status (ColunaAtivo) para indicar que o mesmo está inativo.
UPDATE
  Tabela
SET
  ColunaAtivo = 0
WHERE
  ColunaId = 1;

-- Exemplo mais elaborado usando JOIN para inativar registros com base em informações de outra tabela.
UPDATE
  Tabela
SET
  Tabela.ColunaAtivo = 0,
  Tabela.DataInativacao = GETDATE()
FROM
  Tabela
JOIN
  OutraTabela ON Tabela.ColunaId = OutraTabela.ColunaId
WHERE
  OutraTabela.ColunaStatusId IN (3, 4, 5) AND
  OutraTabela.DataAtualizacao < '2024-01-01';
```

Evolução contínua. Aqui fica um atalho para [parte 3](estilizando-sql-parte-3.md).
