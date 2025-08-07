# Estilizando instruções SQL

Continuando a melhoria na estilização visual, vamos separar os comandos:

> Nota: Os **campos** são as **colunas** da **tabela**. **Coluna** é o termo mais correto e técnico.  
> Resumindo: **Colunas** definem o tipo de dado, **campos** armazenam os valores.

```sql
-- ✅ Agora cada comando está em uma linha separada, facilitando a visualização.
SELECT ColunaId, ColunaNome, ColunaTimeDoCoracao
FROM Tabela 
WHERE ColunaId = 1
AND ColunaNome = 'THIAGO' AND ColunaTimeDoCoracao = 'SÃO PAULO'
```

## Estilização Vertical

Esses estilos têm um nome né? E a resposta é sim, mesmo que informalmente. Para essa convenção aplicada chamamos de
**Vertical SQL Formatting** ou **Estilização Vertical**.

> Nota: Acho que não vou abreviar **Vertical SQL Formatting**  para **V.S.F.** — Olha o meme ai! 😅

Legal, evoluímos a escrita, porém quanto maior o script, maiores as chances de complicar a leitura.

```sql
-- ❌ Não curti. Dependendo de onde está essa instrução, fica mais difícil interpretar.
SELECT 
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
ColunaAtivo
FROM Tabela
WHERE ColunaId = 1
AND ColunaNome = 'THIAGO'
AND ColunaEmail = 'thiago@email.com'
AND ColunaTelefone = '(11) 91234-5678'
AND ColunaTimeDoCoracao = 'SÃO PAULO'
AND ColunaAtivo = 1
```

Mesmo exemplo, aninhando cada parte do código SQL, colocando abaixo de cada comando um recuo no espaçamento:

```sql
-- ✅ Agradável. Imagine ter de adicionar uma coluna extra, bem simples, só criar mais uma linha.
SELECT 
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
  ColunaAtivo
FROM 
  Tabela
WHERE
  ColunaId = 1
  AND ColunaNome = 'THIAGO'
  AND ColunaEmail = 'thiago@email.com'
  AND ColunaTelefone = '(11) 91234-5678'
  AND ColunaTimeDoCoracao = 'SÃO PAULO'
  AND ColunaAtivo = 1
```

## Combinando variações no estilo

Uma pequena variação, com **Trailing AND Style** aplicado dentro do **Vertical/Indented SQL Style**. Traduzindo seria:
SQL formatado com **AND** ao final da linha.

```sql
-- ✅ Vantagem: as colunas ficam alinhadas. Desvantagem: perda no foco da clausula AND, que fica pra trás.
SELECT 
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
  ColunaAtivo
FROM 
  Tabela
WHERE
  ColunaId = 1 AND
  ColunaNome = 'THIAGO' AND
  ColunaEmail = 'thiago@email.com' AND
  ColunaTelefone = '(11) 91234-5678' AND
  ColunaTimeDoCoracao = 'SÃO PAULO' AND
  ColunaAtivo = 1
```

E uma hora vão chegar mais e mais scripts SQL complexos. O que posso fazer? A dica é fugir do complexo e optar sempre
que possível por uma **versão simples e sofisticada**. Exemplo com junção de tabelas:

```sql
-- ✅ Vertical SQL com Inline JOIN. Só é necessário quebrar a linha na clausula ON se for mais complexo.
SELECT 
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
  OutraTabela.ColunaDescricaoStatus
FROM 
  Tabela
JOIN 
  OutraTabela ON Tabela.ColunaStatusId = OutraTabela.ColunaStatusId
WHERE
  ColunaId = 1 AND
  ColunaNome = 'THIAGO' AND
  ColunaEmail = 'thiago@email.com' AND
  ColunaTelefone = '(11) 91234-5678' AND
  ColunaTimeDoCoracao = 'SÃO PAULO' AND
  ColunaAtivo = 1

-- ✅ Olha o ON mais complexo ai. Vertical + Indented SQL com ON multilinha e ANDs finais, simplificando...
SELECT
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
  ColunaEmail = 'thiago@email.com' AND
  ColunaTelefone = '(11) 91234-5678' AND
  ColunaTimeDoCoracao = 'SÃO PAULO' AND
  ColunaAtivo = 1;
```

Show! Do primeiro script até aqui, já há uma grande evolução. Uma continuação na parte 2 vem ai!
