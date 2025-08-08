# Estilizando instruções SQL - Parte 3

Vamos falar um pouco sobre a diferença na escrita entre `SQL SERVER` e `POSTGRES`, mostrando **PROCEDURES**,
**FUNÇÕES**, **CTEs** e **Tabelas Temporárias**.

## SQL Server e Postgres

O **MS SQL Server** é um dos sistemas de gerenciamento de banco de dados (SGDB) mais populares do mundo. A escrita de
instruções é muito prática. Sua maior "desvantagem" é a questão de custos de licenciamento. No mais, é excelente.

E aí falando de custos, quem tem "vantagem" nesse quesito é o **Postgres** - robusto e gratuito para uso comercial.

### Escrevendo `PROCEDURES` e `FUNCTIONS`

**PROCEDURES** são procedimentos armazenados no banco de dados. Normalmente scripts em sequência, que recebem parâmetros
para executar ações no banco de dados. É uma boa prática trabalhar com procedimentos, reduzindo queries
**explicitas no código**, gerando clareza e organização nas aplicações.

Aqui vai um exemplo para SQL SERVER de **PROCEDURE** (também chamada de **PROC**), com estilização vertical:

```sql
-- ✅  O uso de ";" é opcional ao final de cada comando.
CREATE PROCEDURE ProcedimentoArmazenado_RetornaTimeDoCoracaoPorId
(
  @ParametroTimeDoCoracaoId INT
)
AS
BEGIN
  SELECT
    TimesDeFutebol.Nome,
    TimesDeFutebol.NumeroDeTitulos
  FROM
    TimesDeFutebol
  WHERE
    TimesDeFutebol.Id = @ParametroTimeDoCoracaoId;
END;

-- ✅ Exemplo declarando uma variável interna, exibindo na query.
-- Aqui também é acrescentado OR ALTER, atualizando a PROC caso ela já exista
CREATE OR ALTER PROCEDURE ProcedimentoArmazenado_RetornaTimeDoCoracaoPorId
(
  @ParametroTimeDoCoracaoId INT
)
AS
BEGIN
  DECLARE @UsuarioLogado NVARCHAR(100) = SYSTEM_USER;
  SELECT
    TimesDeFutebol.Nome,
    TimesDeFutebol.NumeroDeTitulos,
    @UsuarioLogado AS UsuarioLogado
  FROM
    TimesDeFutebol
  WHERE
    TimesDeFutebol.Id = @ParametroTimeDoCoracaoId;
END;

-- Comando para executar a PROC.
EXEC ProcedimentoArmazenado_RetornaTimeDoCoracaoPorId @ParametroTimeDoCoracaoId = 1;

-- Versão enxuta. Caso haja mais de um parametro, a passagem deve seguir a mesma ordem de declaração
EXEC ProcedimentoArmazenado_RetornaTimeDoCoracaoPorId 1;
```

Exemplo do retorno na execução (Imagine seu time do coração retornando):

| Nome      | NumeroDeTitulos |
|-----------|-----------------|
| São Paulo | 42              |

No **Postgres** (nome popular, o nome de verdade é **PostgreSQL**), é mais comum escrever uma **FUNCTION** para retornar
resultados.

```sql
-- ⚠️ O uso de ";" é OBRIGATÓRIO para terminar instruções SQL dentro do cliente ou scripts.
CREATE OR REPLACE FUNCTION RetornaTimeDoCoracaoPorId
(
  ParametroTimeDoCoracaoId INT
)
RETURNS TABLE
(
  Nome TEXT,
  NumeroDeTitulos INT,
  Observacoes TEXT
) AS $$
BEGIN
  RETURN QUERY
  SELECT
    Nome,
    NumeroDeTitulos,
    Observacoes
  FROM
    TimesDeFutebol
  WHERE
    Id = ParametroTimeDoCoracaoId;
END;
$$ LANGUAGE plpgsql;

-- Executando a Function
SELECT * FROM RetornaTimeDoCoracaoPorId(2);
```

Exemplo do retorno na execução (Imagine seu time do coração retornando):

| Nome      | NumeroDeTitulos | Observacoes     |
|-----------|-----------------|-----------------|
| Palmeiras | 55              | Sem mundial  🤪 |

### CTE's

Visando sempre escapar, fugir e nunca se render a complexidade. Uma boa prática para sair de consultas gigantes e volume
de processamento, é quebrar em partes menores. Delegue isso para locais temporários.

Definição: **CTE** (Common Table Expression) é uma expressão temporária nomeada que você pode usar dentro de uma query
para organizar e simplificar consultas complexas. Ela funciona como uma "tabela temporária" disponível só durante a
execução daquela query ou procedimento.

Vamos primeiro com exemplos no SQL SERVER:

```sql
-- ⚠️ o ";" é obrigatório quando a CTE não é a primeira instrução do batch ou está dentro de uma procedure,
-- para evitar erro de sintaxe.
CREATE OR ALTER PROCEDURE ProcedimentoComCTE
(
  @ParametroTimeDoCoracaoId INT
)
AS
BEGIN
  ;WITH TimeFiltradoCTE AS
  (
    SELECT
      Nome,
      NumeroDeTitulos,
      Id
    FROM
      TimesDeFutebol
    WHERE
      Id = @ParametroTimeDoCoracaoId
  )
   
  SELECT
    Nome,
    NumeroDeTitulos
  FROM
    TimeFiltradoCTE;
END;

-- Exemplo com duas CTE's. Primeiro recupera o time, depois os jogagadores. Combina os dados e retorna em uma consulta.
CREATE OR ALTER PROCEDURE ProcedimentoComDuasCTEs
(
  @ParametroTimeDoCoracaoId INT
)
AS
BEGIN
  ;WITH TimeFiltradoCTE AS
  (
    SELECT
      Id,
      Nome,
      NumeroDeTitulos
    FROM
      TimesDeFutebol
    WHERE
      Id = @ParametroTimeDoCoracaoId
  ),
  JogadoresAtivosCTE AS
  (
    SELECT
      JogadorId,
      NomeJogador,
      TimeId
    FROM
      Jogadores
    WHERE
      Ativo = 1
  )

  SELECT
    TimeFiltradoCTE.Nome,
    TimeFiltradoCTE.NumeroDeTitulos,
    JogadoresAtivosCTE.NomeJogador
  FROM
    TimeFiltradoCTE
 JOIN
    JogadoresAtivosCTE ON TimeFiltradoCTE.Id = JogadoresAtivosCTE.TimeId;
END;
```

Saída fictícia da PROC:

| Nome      | NumeroDeTitulos | NomeJogador     |
|-----------|-----------------|-----------------|
| São Paulo | 6               | João Silva      |
| São Paulo | 6               | Carlos Pereira  |
| São Paulo | 6               | Felipe Oliveira |

Mesmo exemplo, só que no Postgres:

```sql
-- No PostgreSQL, a sintaxe é diferente. Não existe CREATE OR ALTER PROCEDURE com @ para parâmetros
-- — parâmetros são declarados diretamente, e CTEs usam WITH sem ";" obrigatório antes.
CREATE OR REPLACE FUNCTION ProcedimentoComCTE
(
  ParametroTimeDoCoracaoId INT
)
RETURNS TABLE
(
  Nome TEXT,
  NumeroDeTitulos INT
) AS $$
BEGIN
  RETURN QUERY
  WITH TimeFiltradoCTE AS 
  (
    SELECT
      Nome,
      NumeroDeTitulos,
      Id
    FROM
      TimesDeFutebol
    WHERE
      Id = ParametroTimeDoCoracaoId
  )
  
  SELECT
    Nome,
    NumeroDeTitulos
  FROM
    TimeFiltradoCTE;
END;
$$ LANGUAGE plpgsql;

-- Exemplo com 2 CTEs no Postgres
CREATE OR REPLACE FUNCTION ProcedimentoComDuasCTEs
(
  ParametroTimeDoCoracaoId INT
)
RETURNS TABLE
(
  Nome TEXT,
  NumeroDeTitulos INT,
  NomeJogador TEXT
) AS $$
BEGIN
  RETURN QUERY
    WITH TimeFiltradoCTE AS
    (
      SELECT
        Id,
        Nome,
        NumeroDeTitulos
      FROM
        TimesDeFutebol
      WHERE
        Id = ParametroTimeDoCoracaoId
    ),
    JogadoresAtivosCTE AS
    (
     SELECT
       JogadorId,
       NomeJogador,
       TimeId
     FROM
       Jogadores
     WHERE
       Ativo = 1
    )
    
    SELECT
      TimeFiltradoCTE.Nome,
      TimeFiltradoCTE.NumeroDeTitulos,
      JogadoresAtivosCTE.NomeJogador
    FROM
      TimeFiltradoCTE
    JOIN
      JogadoresAtivosCTE ON TimeFiltradoCTE.Id = JogadoresAtivosCTE.TimeId;
END;
$$ LANGUAGE plpgsql;
```

### Tabelas Temporárias

Agora, ao invés de CTEs, vou usar **tabelas temporárias**. Primeiro no SQL SERVER:

```sql
-- Exemplo com a criação explicita das tabelas temporárias, exibindo toda a estrutura
CREATE OR ALTER PROCEDURE ProcedimentoComTempTables
(
  @ParametroTimeDoCoracaoId INT
)
AS
BEGIN
  -- Remove a tabela temporária #TimeFiltrado se existir
  IF OBJECT_ID('tempdb..#TimeFiltrado') IS NOT NULL
    DROP TABLE #TimeFiltrado;

  -- Criação explícita da tabela temporária #TimeFiltrado com definição dos tipos
  CREATE TABLE #TimeFiltrado
  (
    Id INT,
    Nome NVARCHAR(100),
    NumeroDeTitulos INT
  );

  -- Inserção dos dados filtrados na tabela temporária #TimeFiltrado
  INSERT INTO #TimeFiltrado
  (
    Id,
    Nome,
    NumeroDeTitulos
  )
  SELECT
    Id,
    Nome,
    NumeroDeTitulos
  FROM
    TimesDeFutebol
  WHERE
    Id = @ParametroTimeDoCoracaoId;

  -- Remove a tabela temporária #JogadoresAtivos se existir
  IF OBJECT_ID('tempdb..#JogadoresAtivos') IS NOT NULL
    DROP TABLE #JogadoresAtivos;

  -- Criação explícita da tabela temporária #JogadoresAtivos com definição dos tipos
  CREATE TABLE #JogadoresAtivos
  (
    JogadorId INT,
    NomeJogador NVARCHAR(100),
    TimeId INT
  );

  -- Inserção dos dados filtrados na tabela temporária #JogadoresAtivos
  INSERT INTO #JogadoresAtivos 
  (
    JogadorId,
    NomeJogador,
    TimeId
  )
  SELECT
    JogadorId,
    NomeJogador,
    TimeId
  FROM
    Jogadores
  WHERE
    Ativo = 1;

  -- Consulta final usando as tabelas temporárias com apelidos claros
  SELECT
    TempTimeFiltrado.Nome,
    TempTimeFiltrado.NumeroDeTitulos,
    TempJogadoresAtivos.NomeJogador
  FROM
    #TimeFiltrado AS TempTimeFiltrado
  JOIN
    #JogadoresAtivos AS TempJogadoresAtivos ON TempTimeFiltrado.Id = TempJogadoresAtivos.TimeId;
  END;
```

Exemplo com a criação implícita das tabelas:

```sql
-- Mais enxuto, poŕém sem os tipos de dados das colunas exibidos.
CREATE OR ALTER PROCEDURE ProcedimentoComTempTables
(
  @ParametroTimeDoCoracaoId INT
)
AS
BEGIN
  -- Cria tabela temporária para TimeFiltrado
  IF OBJECT_ID('tempdb..#TimeFiltrado') IS NOT NULL
    DROP TABLE #TimeFiltrado;

  SELECT
    Id,
    Nome,
    NumeroDeTitulos
  INTO
    #TimeFiltrado
  FROM
    TimesDeFutebol
  WHERE
    Id = @ParametroTimeDoCoracaoId;

  -- Cria tabela temporária para JogadoresAtivos
  IF OBJECT_ID('tempdb..#JogadoresAtivos') IS NOT NULL
    DROP TABLE #JogadoresAtivos;

  SELECT
    JogadorId,
    NomeJogador,
    TimeId
  INTO
    #JogadoresAtivos
  FROM
    Jogadores
  WHERE
    Ativo = 1;

  -- Consulta final usando tabelas temporárias, com apelidos claros
  SELECT
    TempTimeFiltrado.Nome,
    TempTimeFiltrado.NumeroDeTitulos,
    TempJogadoresAtivos.NomeJogador
  FROM
    #TimeFiltrado AS TempTimeFiltrado
  JOIN
    #JogadoresAtivos AS TempJogadoresAtivos ON TempTimeFiltrado.Id = TempJogadoresAtivos.TimeId;
END;
```

Agora, repetindo os exemplos acima, no banco Postgres

```sql

-- Exemplo com a criação explícita das tabelas temporárias, exibindo toda a estrutura
CREATE OR REPLACE FUNCTION ProcedimentoComTempTables
(
  ParametroTimeDoCoracaoId INT
)
RETURNS TABLE
(
  Nome TEXT,
  NumeroDeTitulos INT,
  NomeJogador TEXT
) AS $$
BEGIN
  -- Remove a tabela temporária TimeFiltrado se existir
  DROP TABLE IF EXISTS TempTimeFiltrado;

  -- Criação explícita da tabela temporária TempTimeFiltrado com definição dos tipos
  CREATE TEMP TABLE TempTimeFiltrado
  (
    Id INT,
    Nome TEXT,
    NumeroDeTitulos INT
  ) ON COMMIT DROP;

  -- Inserção dos dados filtrados na tabela temporária TempTimeFiltrado
  INSERT INTO TempTimeFiltrado
  (
    Id,
    Nome,
    NumeroDeTitulos
  )
  SELECT
    Id,
    Nome,
    NumeroDeTitulos
  FROM
    TimesDeFutebol
  WHERE
    Id = ParametroTimeDoCoracaoId;

  -- Remove a tabela temporária JogadoresAtivos se existir
  DROP TABLE IF EXISTS TempJogadoresAtivos;

  -- Criação explícita da tabela temporária TempJogadoresAtivos com definição dos tipos
  CREATE TEMP TABLE TempJogadoresAtivos
  (
    JogadorId INT,
    NomeJogador TEXT,
    TimeId INT
  ) ON COMMIT DROP;

  -- Inserção dos dados filtrados na tabela temporária TempJogadoresAtivos
  INSERT INTO TempJogadoresAtivos
  (
    JogadorId,
    NomeJogador,
    TimeId
  )
  SELECT
    JogadorId,
    NomeJogador,
    TimeId
  FROM
    Jogadores
  WHERE
    Ativo = 1;

  -- Consulta final usando as tabelas temporárias com apelidos claros
  RETURN QUERY
  SELECT
    TempTimeFiltrado.Nome,
    TempTimeFiltrado.NumeroDeTitulos,
    TempJogadoresAtivos.NomeJogador
  FROM
    TempTimeFiltrado AS TempTimeFiltrado
  JOIN
    TempJogadoresAtivos AS TempJogadoresAtivos ON TempTimeFiltrado.Id = TempJogadoresAtivos.TimeId;
END;
$$ LANGUAGE plpgsql;
```

Versão mais enxuta:

```sql
-- Mais enxuto, porém sem os tipos de dados das colunas exibidos.
CREATE OR REPLACE FUNCTION ProcedimentoComTempTables
(
  ParametroTimeDoCoracaoId INT
)
RETURNS TABLE
(
  Nome TEXT,
  NumeroDeTitulos INT,
  NomeJogador TEXT
) AS $$
BEGIN
  -- Cria tabela temporária para TimeFiltrado
  DROP TABLE IF EXISTS TempTimeFiltrado;

  CREATE TEMP TABLE TempTimeFiltrado AS
  SELECT
    Id,
    Nome,
    NumeroDeTitulos
  FROM
    TimesDeFutebol
  WHERE
    Id = ParametroTimeDoCoracaoId;

  -- Cria tabela temporária para JogadoresAtivos
  DROP TABLE IF EXISTS TempJogadoresAtivos;

  CREATE TEMP TABLE TempJogadoresAtivos AS
  SELECT
    JogadorId,
    NomeJogador,
    TimeId
  FROM
    Jogadores
  WHERE
    Ativo = 1;

  -- Consulta final usando tabelas temporárias, com apelidos claros
  RETURN QUERY
  SELECT
    TempTimeFiltrado.Nome,
    TempTimeFiltrado.NumeroDeTitulos,
    TempJogadoresAtivos.NomeJogador
  FROM
    TempTimeFiltrado AS TempTimeFiltrado
  JOIN
    TempJogadoresAtivos AS TempJogadoresAtivos ON TempTimeFiltrado.Id = TempJogadoresAtivos.TimeId;
END;
$$ LANGUAGE plpgsql;
```

Bom, é isso aí! Lembrando que o escopo é olhar para os códigos visando a estilização. Esses são apenas exemplos
fictícios.

Futuramente vem uma parte 4, com sugestões de nomenclatura, mais detalhes sobre procedures e functions, e uma pasta com
scripts de exemplo.
