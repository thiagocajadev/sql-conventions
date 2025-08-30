# Estilizando instruções SQL - Guia rápido

Anotações e guias rápidos sobre convenção de nomes são ótimos aliados.

> [!TIP]
>
> Regra de ouro das **gigantes de tecnologia:**  
> Se o nome não for **autoexplicativo em inglês** e **consistente com o resto do schema**, ele está
> errado.

## ✅ SQL Naming Conventions (Big Tech Style)

| Categoria                | Convenção / Prefixo                                           | Exemplos                                           |
| ------------------------ | ------------------------------------------------------------- | -------------------------------------------------- |
| **Tabelas**              | `snake_case`, **sempre plural**                               | `users`, `orders`, `products`, `order_items`       |
| **Colunas**              | `snake_case`, minúsculas, descritivas                         | `id`, `user_id`, `created_at`, `last_login`        |
| **Chave Primária**       | `id` (único dentro da tabela)                                 | `users.id`, `orders.id`                            |
| **Chaves Estrangeiras**  | `<tabela_singular>_id`                                        | `user_id`, `product_id`, `order_id`                |
| **Índices**              | `ix_<tabela>_<coluna>`                                        | `ix_users_email`, `ix_orders_created_at`           |
| **Unique Keys**          | `uq_<tabela>_<coluna>`                                        | `uq_users_email`                                   |
| **Foreign Keys**         | `fk_<tabela>_<coluna>`                                        | `fk_orders_user_id`                                |
| **Check Constraints**    | `ck_<tabela>_<regra>`                                         | `ck_users_age_positive`                            |
| **Views**                | Prefixo `vw_` + `snake_case`, plural                          | `vw_active_users`, `vw_monthly_sales`              |
| **Stored Procedures**    | Prefixo `sp_` + `snake_case`                                  | `sp_create_order`, `sp_close_account`              |
| **Functions**            | Prefixo `fn_` + `snake_case`                                  | `fn_get_age`, `fn_format_currency`                 |
| **Triggers**             | Prefixo `tr_` + `snake_case`                                  | `tr_users_before_insert`, `tr_orders_after_update` |
| **Schemas**              | Agrupamento lógico, minúsculo                                 | `auth.users`, `sales.orders`, `logs.events`        |
| **Defaults / Sequences** | `df_<tabela>_<coluna>`, `seq_<tabela>`                        | `df_users_created_at`, `seq_orders`                |
| **Naming geral**         | Evitar abreviações obscuras, usar nomes claros e consistentes | ✅ `customer_address` 🚫 `cust_addr`               |

Resumo das Big Techs:

- **Tabelas sempre no plural** → menos ambiguidade (users, orders).
- **Colunas descritivas, snake_case e minúsculas** → legibilidade e padrão universal.
- **Chave primária sempre id** → simples e consistente.
- **Foreign keys com <tabela_singular>\_id** → deixa claro o relacionamento.
- **Nada de camelCase ou PascalCase** → SQL padrão é snake_case.
- **Nomes curtos mas claros** → nunca x_nm ou val1.

> [!NOTE]
>
> **snake_case minúsculo** é o padrão universal. Muitas big techs recomendam snake_case mesmo em
> **SQL Server** — justamente para manter consistência cross-stack e evitar problemas em queries
> portadas.
>
> Times que trabalham só dentro do ecossistema **Microsoft** não sofrem tanto com portabilidade,
> então PascalCase acaba virando "default". SQL Server (corporate / .NET shops) → **PascalCase** é
> comum e aceito.

## 🚫 SQL Naming Anti-Patterns (Evitar Sempre)

| Anti-pattern                                   | Por que evitar?                            | Exemplo ruim                     | Exemplo correto                                    |
| ---------------------------------------------- | ------------------------------------------ | -------------------------------- | -------------------------------------------------- |
| **Prefixar com `tbl`, `t_`**                   | Redundante, já se sabe que é tabela        | `tblUsers`, `t_orders`           | `users`, `orders`                                  |
| **CamelCase / PascalCase**                     | SQL é case-insensitive, quebra padrão      | `UserID`, `OrderItems`           | `user_id`, `order_items`                           |
| **Singular em tabelas** (quando inconsistente) | Pode gerar ambiguidade na leitura          | `user`, `orderItem`              | `users`, `order_items`                             |
| **Abreviações obscuras**                       | Dificulta manutenção e leitura             | `usr_addr`, `cust_tel`           | `user_address`, `customer_phone`                   |
| **Colunas com nomes genéricos**                | Não deixam claro o propósito               | `value`, `status`, `data`        | `total_amount`, `order_status`, `created_at`       |
| **Misturar idiomas**                           | Confunde devs globais                      | `usuarios`, `order_items`        | `users`, `order_items`                             |
| **Nomes com espaços ou caracteres especiais**  | Geram problemas em queries e portabilidade | `"Order Items"`, `user-name`     | `order_items`, `user_name`                         |
| **Coluna duplicando nome da tabela**           | Redundante, aumenta verbosidade            | `user_user_id`, `order_order_id` | `id`, `user_id`, `order_id`                        |
| **Sequências/Defaults sem padrão**             | Dificulta debugging                        | `seq1`, `defaultX`               | `seq_orders`, `df_users_created_at`                |
| **Triggers sem verbo claro**                   | Fica impossível saber quando disparam      | `trigger1`, `userTrigger`        | `tr_users_before_insert`, `tr_orders_after_update` |

> [!IMPORTANT]
>
> Qual o melhor padrão ou convenção?  
> A melhor escolha sempre é o **alinhamento por contexto do projeto**. Defina isso com o time e
> todos ganham!

## Referências

### snake_case

- [Postgres naming conventions](https://www.geeksforgeeks.org/postgresql/postgresql-naming-conventions/)
- [Pigsty – PostgreSQL Naming Convention](https://pigsty.io/blog/pg/pg-convention/?utm_source=chatgpt.com)
- [API-Fiddle Blog – Naming Conventions in PostgreSQL](https://blog.api-fiddle.com/posts/naming-conventions-in-postgresql?utm_source=chatgpt.com)
- [Npgsql Docs – EF Core PostgreSQL Modeling](https://www.npgsql.org/efcore/modeling/tables.html?utm_source=chatgpt.com)

### PascalCase

- [Ometa Docs – SQL Conventions](https://docs.ometa.net/5.6.x/public/articles/best-practices/sql/sql-conventions.html?utm_source=chatgpt.com)
- [ScholarHat – SQL Server Naming Conventions and Standards](https://www.scholarhat.com/tutorial/sqlserver/sql-server-naming-conventions-and-standards?utm_source=chatgpt.com)
- [StackOverflow – Database naming conventions by Microsoft](https://stackoverflow.com/questions/3593582/database-naming-conventions-by-microsoft?utm_source=chatgpt.com)
- [Ed-Fi – SQL Coding Standards](https://docs.ed-fi.org/community/sdlc/code-contribution-guidelines/coding-standards/sql-coding-standards?utm_source=chatgpt.com)
- [BBKane Blog – SQL Naming Conventions](https://www.bbkane.com/blog/sql-naming-conventions/?utm_source=chatgpt.com)
- [Emergent Software – SQL Server Development Assessment](https://emergentsoftware.github.io/SQL-Server-Development-Assessment/best-practices-and-potential-findings/naming-conventions?utm_source=chatgpt.com)
