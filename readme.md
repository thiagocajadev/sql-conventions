# Convenções SQL

Fala Dev! Aqui estou mais um dia, guardando minhas preferências sobre SQL.

Fui "convencido" a escrever e ler melhor instruções na linguagem de consulta, e nada melhor do que
registrar isso, não é mesmo?

```sql
-- ✅ Deixando um "spoiler" aqui sobre o SPFC.
SELECT
  CampoId,
  CampoNome,
  CampoTimeDoCoracao
FROM
  Tabela
WHERE
  CampoId = 1 AND
  CampoNome = 'THIAGO' AND
  CampoTimeDoCoracao = 'SÃO PAULO'
```

Se precisar de um guia rápido, venha por [aqui](docs/estilizando-sql-guia-rapido.md).

## Guias de estilo

É inevitável a busca por padronização após passar um certo tempo com olhos e mãos no código. Não
encontrei uma preferência universal, só debates sobre o tema. Bons debates no campo das ideias,
seguidos de respeito, geram resultados interessantes.

Começando com uma **Query** simples:

```txt
-- Essa instrução está fácil de compreender pois é enxuta
select * from tabela
```

Melhorando a visibilidade, destacando as palavras reservadas do SQL em MAIÚSCULO:

```sql
-- ✅ Instruções com baixa complexidade e curtas são fáceis de visualizar em linha.
-- ✅ O script foi destacado como SQL, reforçando o estilo visual com diferenciação de cor.
SELECT * FROM Tabela
```

Agora uma consulta um pouco mais elaborada, seguindo o mesmo estilo:

```sql
-- ❌ Aqui a leitura começa a ficar cansativa, precisando arrastar pro lado, em algumas telas, pra ver o conteúdo.
SELECT CampoId, CampoNome, CampoTimeDoCoracao FROM Tabela WHERE CampoId = 1 AND CampoNome = 'THIAGO' AND CampoTimeDoCoracao = 'SÃO PAULO'
```

Pra resolver essa questão, aplicamos a **indentação**.

> **Indentação** de código é o espaço usado no começo da linha para mostrar que um comando está
> dentro de outro. Melhor exemplo possível aqui — um bloco SQL.

Indentando o script:

```sql
-- Só de quebrar a linha, há uma melhora na separação dos campos (colunas) da tabela.
SELECT CampoId, CampoNome, CampoTimeDoCoracao
FROM Tabela WHERE CampoId = 1 AND CampoNome = 'THIAGO' AND CampoTimeDoCoracao = 'SÃO PAULO'
```

Vamos subir de nível? Vamos! Vou continuar a documentação passo a passo, evitando que o conteúdo
fique massante.

## Subindo de nível

[Estilizando instruções SQL - parte 1](docs/estilizando-sql-parte-1.md)  
[Estilizando instruções SQL - parte 2](docs/estilizando-sql-parte-2.md)  
[Estilizando instruções SQL - parte 3](docs/estilizando-sql-parte-3.md)  
[Estilizando instruções SQL - parte 4](docs/estilizando-sql-parte-4.md)

### Referências

[Guia de estilos SQL](https://www.sqlstyle.guide/)  
[Guia de referência SQL](https://brainstation.io/learn/sql/reference)
