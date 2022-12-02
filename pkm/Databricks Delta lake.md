Descrevendo metadata de uma tabela do delta lake:
**DESCRIBE EXTENDED students**

Descrevendo detalhes de infraestrutura de uma tabela:
**DESCRIBE DETAIL students**

O delta lake não é uma tabela dentro de um banco de dados, ele é formado por diverso arquivos em um object storage.

Visualizando deltalake files:

```
%python
display(dbutils.fs.ls(f"{DA.paths.user_db}/students"))
```

![[Pasted image 20221119173717.png]]

Os registros no delta lake, são armazenados em arquivos parquet.

As transações são registradas no diretório `_delta_log`, cada transação gera um arquivo json

![[Pasted image 20221119174114.png]]


O delta lake não exclui imediatamente arquivos que tiveram os dados alterados, é utilizado o transaction log para indicar quais arquivos são validos na versão atual.

Visualizando uma transaction:
```
%python
display(spark.sql(f"SELECT * FROM json.`{DA.paths.user_db}/students/_delta_log/00000000000000000007.json`"))
```

O comando [OPTIMIZE](https://docs.databricks.com/sql/language-manual/delta-optimize.html) vai substituir os arquivos existentes, combinando e reescrevendo o resultado. 

```
OPTIMIZE students
ZORDER BY id
```

![[Pasted image 20221119175422.png]]

Visualizando o histórico de transações:
`DESCRIBE HISTORY beans`

![[Pasted image 20221119175654.png]]


É possível fazer queries em versões anteriores da tabela, especificando campos como timestamp ou versão.

![[Pasted image 20221119180036.png]]

![[Pasted image 20221119180708.png]]


Restaurando uma versão da tabela:
`RESTORE TABLE students TO VERSION AS OF 8`

![[Pasted image 20221119180952.png]]

O databricks vai limpar automaticamente arquivos antigos das tabelas do delta lake

Utilizando o comando VACUUM para limpar os arquivos manualmente:
 - Não é possível limpar aquivos dos últimos 7 dias por padrão
![[Pasted image 20221119181427.png]]


Usando co comando DRY RUN para simular os arquivos que vão ser deletados (o check de deleção dos últimos 7 dias foi desabilitado para testar o exemplo).

```
SET spark.databricks.delta.retentionDurationCheck.enabled = false;
SET spark.databricks.delta.vacuum.logging.enabled = true;

VACUUM students RETAIN 0 HOURS DRY RUN
```

![[Pasted image 20221119181547.png]]

Limpando os arquivos de fato:
`VACUUM students RETAIN 0 HOURS`

![[Pasted image 20221119181704.png]]


## Databases And Tables

Criação de banco de dados:
Sem a localização especificada:
`CREATE DATABASE IF NOT EXISTS ${da.db_name}_default_location;`

Com localização 
`CREATE DATABASE IF NOT EXISTS ${da.db_name}_custom_location LOCATION '${da.paths.working_dir}/_custom_location.db';`



Tags:
#databricks #big-data #data-engineering