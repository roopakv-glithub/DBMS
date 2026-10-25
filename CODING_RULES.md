# DBMS Experiment Coding Rules

Use these conventions for Oracle Database 11g scripts run in SQL*Plus. Match the examples and schema used in the college experiments.

## SQL style and SQL*Plus

- Write SQL keywords in lowercase, as in the notes. Use lowercase table names, column names, and constraint names; separate words with underscores where needed (`course_student_faculty`, `fk_student_school`).
- End each SQL statement with a semicolon. In examples, show the SQL*Plus prompt (`SQL>`) and useful results such as `Table created.`, `DESC`, or `SELECT` output when documenting an experiment.
- Use `desc table_name;` to show the structure after creating or altering a table.
- Use SQL*Plus substitution variables for interactive experiment input. Quote character values, but do not quote numeric values:

  ```sql
  insert into player values('&name', '&country', &matches, &runs, &jersy);
  ```

- Commit inserted or updated experiment data explicitly with `commit;` where the exercise asks for it.

## Data types and table definitions

- Use `number` for all numeric columns, including whole-number values. Do not declare columns as `integer`. Add precision and scale when appropriate: `number(3)`, `number(10,2)`.
- Use `char(n)` for fixed-width character values when the examples do so; use `varchar2(n)` for variable-width text. Use `date` for dates.
- Put column definitions inside `create table table_name (...)`. Declare simple column constraints inline and name table-level or foreign-key constraints with the `constraint` clause.
- Use primary keys for row identity and foreign keys to reference the parent table and column. Use `not null`, `unique`, `default`, and `check` only when required by the exercise or data rules.
- Match referenced and referencing column types and sizes. Create parent tables before child tables.

  ```sql
  create table books (
      isbn varchar2(13) primary key,
      title varchar2(100),
      price number(8,2),
      qty number(3) not null
  );

  create table book_authors (
      isbn varchar2(13),
      author_id number(3),
      constraint fk_book foreign key (isbn) references books(isbn),
      constraint fk_author foreign key (author_id) references authors(author_id)
  );
  ```

- Give constraints short descriptive names with the prefixes seen in the experiments: `pk_`, `fk_`, `uk_`, and `chk_` (or `ck_` when following a supplied example).
- Change a column's nullability or default with `alter table ... modify`; add or drop named constraints with `alter table ... add/drop constraint`. Drop referencing foreign keys before dropping a referenced primary key.

  ```sql
  alter table books modify price number(8,2) not null;
  alter table books add constraint uk_books_title unique (title);
  ```

## Inserts and queries

- Prefer `insert into table_name values (...)` when supplying every column in table order. Name the column list when inserting only some columns.
- Put character literals in single quotes. Use `null` without quotes. Use `is null` / `is not null` for null tests, not `= null`.
- Use `select * from table_name;` to display a full relation, and list only needed columns for a focused result. Use `distinct` to remove duplicates and `as` or a following identifier to label result columns when useful.
- Use `where` with comparison operators and `and`, `or`, and `not`; use `between ... and ...` for ranges, `in` / `not in` for membership, and `like` with `%` and `_` for patterns.
- Sort with `order by column asc` or `order by column desc`; combine sort keys with commas when an exercise requests mixed ordering.
- For summaries, use `count`, `sum`, `avg`, `min`, and `max`. Group non-aggregate selected columns with `group by`; use `having` to filter groups. Use `round(value, 2)` when a two-decimal result is requested.
- Use Oracle 11g syntax: `substr` for string extraction, `sysdate` for the current date, `to_date` / `to_char` for date conversion and formatting, and `months_between`, `add_months`, `next_day`, or `last_day` for date exercises. Do not use MySQL/PostgreSQL-only functions such as `curdate()`.
- Write joins in the form used by the relevant exercise: `natural join` / outer joins, `join ... on (...)`, `join ... using (...)`, or comma-separated tables with matching conditions in `where`. Qualify columns with table names or aliases when names are ambiguous.
- Use subqueries with `in`, `not in`, `exists`, `not exists`, `some`, or `all` as needed. Use Oracle's set operators `union`, `intersect`, and `minus` (not `except`). Parenthesize component queries when following the notes' set-operation examples.
- Use `dual` when selecting an expression without querying a table.

## PL/SQL

- Enable displayed output in SQL*Plus with `set serveroutput on;` before blocks that call `dbms_output.put_line`.
- Keep the block structure `declare` (when variables are needed), `begin`, optional `exception`, and `end;`. Terminate the block with `/` on the next line so SQL*Plus executes it.
- Use `number` for numeric variables too. Declare variables with a clear name and initialize or assign them before use.
- Use the constructs taught in the experiments: `if ... then ... else ... end if;`, `case ... when ... then ... end case;`, and basic `loop`, `while`, or `for` loops. Close each construct with its matching `end` clause.
- Use `dbms_output.put_line(...)` for experiment output and `||` to concatenate text and values.
- For procedures, functions, triggers, and cursors, keep the required object header, declare local variables/cursors in the declaration section, and put executable statements between `begin` and `end`. End stored PL/SQL definitions with `/` in SQL*Plus.
- Use `:old` and `:new` in row-level triggers when comparing values before and after an update. Use explicit cursor steps (`open`, `fetch`, test `%found`/`%notfound`, `close`) when the exercise asks for an explicit cursor.

  ```sql
  set serveroutput on;
  declare
      n number := 153;
  begin
      dbms_output.put_line('Input Number = ' || n);
  end;
  /
  ```

## Experiment presentation

- State the question in plain English, then show the SQL/PLSQL query text and the relevant output. Keep one query for each requested operation, and use values that make the requested query return a meaningful result when the exercise requires result rows.
- Include `desc` output after table or constraint changes when the experiment asks to show the structure. Include `commit;` after data changes when required.
