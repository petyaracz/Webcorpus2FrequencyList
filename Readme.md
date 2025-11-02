## Word frequency list from the Hungarian Webcorpus 2

Original [here](https://hlt.bme.hu/en/resources/webcorpus2).

### R + duckdb + dplyr

```r 
library(duckdb)
library(dplyr)

con = dbConnect(duckdb())
  
# Set memory limit explicitly
dbExecute(con, "SET memory_limit='2GB'")

tbl(con, sql("SELECT * FROM read_parquet('frequencies.parquet')")) |>
  head() |> # run your dplyr queries here
  collect() # collect results


```

### SQL

```sh
# Using DuckDB CLI
duckdb -c "SELECT * FROM read_parquet('frequencies.parquet') LIMIT 10;"

# Or interactively
duckdb
D SELECT * FROM read_parquet('frequencies.parquet') WHERE frequency > 1000;
```

### python + pandas

```python
import pandas as pd

# Direct read (loads entire file into memory)
df = pd.read_parquet('frequencies.parquet')

# Or streaming with filters using pyarrow
import pyarrow.parquet as pq
table = pq.read_table('frequencies.parquet', 
                      filters=[('frequency', '>', 100)])
df = table.to_pandas()
```

### python + polars

```python
import polars as pl

# Lazy reading (more memory efficient)
df = pl.scan_parquet('frequencies.parquet').head(10).collect()

# Or eager
df = pl.read_parquet('frequencies.parquet')
```

### julia

```julia
using Parquet2, DataFrames

df = DataFrame(read_parquet("frequencies.parquet"))
first(df, 10)
```