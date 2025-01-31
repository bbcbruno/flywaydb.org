---
layout: documentation
menu: postgresql
subtitle: PostgreSQL
---
# PostgreSQL

## Supported Versions

- `11`
- `10`
- `9.6`
- `9.5`
- `9.4` 
- `9.3` {% include enterprise.html %}
- `9.2` {% include enterprise.html %}
- `9.1` {% include enterprise.html %}
- `9.0` {% include enterprise.html %}

## Driver

<table class="table">
<tr>
<th>URL format</th>
<td><code>jdbc:postgresql://<i>host</i>:<i>port</i>/<i>database</i></code></td>
</tr>
<tr>
<th>Ships with Flyway Command-line</th>
<td>Yes</td>
</tr>
<tr>
<th>Maven Central coordinates</th>
<td><code>org.postgresql:postgresql:42.2.5</code></td>
</tr>
<tr>
<th>Supported versions</th>
<td><code>9.3-1104-jdbc4</code> and later</td>
</tr>
<tr>
<th>Default Java class</th>
<td><code>org.postgresql.Driver</code></td>
</tr>
</table>

## SQL Script Syntax

- [Standard SQL syntax](/documentation/migrations#sql-based-migrations#syntax) with statement delimiter **;**
- Stored procedures (`CREATE FUNCTION` with `$$` escapes, as generated by pg_dump)
- `COPY ... FROM STDIN` (as generated by pg_dump)

### Compatibility

- DDL exported by pg_dump can be used unchanged in a Flyway migration.
- Any PostgreSQL sql script executed by Flyway, can be executed by the PostgreSQL command-line tool and other
        PostgreSQL-compatible tools (after the placeholders have been replaced).

### Example

<pre class="prettyprint">/* Single line comment */
CREATE TABLE test_data (
 value VARCHAR(25) NOT NULL PRIMARY KEY
);


/*
Multi-line
comment
*/

-- Multi-statement PostgreSQL function
CREATE FUNCTION AddData() RETURNS INTEGER
AS $$
   BEGIN
    INSERT INTO test_data (value) VALUES ('Hello');
    RETURN 1;
  END;
$$ LANGUAGE plpgsql;

SELECT *  INTO TEMP adddata_temp_table FROM AddData() ;

-- Single-statement PostgreSQL function
CREATE FUNCTION add(integer, integer) RETURNS integer
   LANGUAGE sql IMMUTABLE STRICT
   AS $_$select $1 + $2;$_$;

-- Placeholder
INSERT INTO ${tableName} (name) VALUES ('Mr. T');

-- COPY ... FROM STDIN
COPY copy_test (c1, c2, c3) FROM stdin;
1	utf8: ümlaute: äüß	NaN
2	\N	123
3	text	123.234444444444449
\.</pre>

## Limitations

- No support for psql meta-commands with no JDBC equivalent like `\set`
- Clean does not remove objects created by extensions. It is therefore highly recommended to create your extensions
 using `CREATE EXTENSION IF NOT EXISTS` in order to be able to clean and (re-)migrate your schema(s) at will.
- No support for cleaning referenced large objects in `pg_largeobject` ([Issue 1934](https://github.com/flyway/flyway/issues/1934))

<p class="next-steps">
    <a class="btn btn-primary" href="/documentation/database/aurora-postgresql">Aurora PostgreSQL <i class="fa fa-arrow-right"></i></a>
</p>