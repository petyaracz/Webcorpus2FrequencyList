# Word frequency list from the Hungarian Webcorpus 2

[![DOI](https://zenodo.org/badge/1088274776.svg)](https://doi.org/10.5281/zenodo.17508385)

Plz cite:

```
Péter, Rácz. (2025). Word frequency list from the Hungarian Webcorpus (Version 1.0) [Software/Data]. Zenodo. [https://doi.org/10.5281/zenodo.17508385](https://doi.org/10.5281/zenodo.17508385)
```

```bibtex
@software{racz2025hungarian,
  author       = {Rácz, Péter},
  title        = {Word frequency list from the Hungarian Webcorpus},
  year         = {2025},
  publisher    = {Zenodo},
  version      = {1.0},
  doi          = {10.5281/zenodo.17508385},
  url          = {https://doi.org/10.5281/zenodo.17508385}
}
```

Original [here](https://hlt.bme.hu/en/resources/webcorpus2).

## Dictionary

| Column | Description |
|--------|-------------|
| form | word form, orthographic |
| lemma | word lemma, orthographic |
| xpostag | form pos tag |
| freq | form raw frequency |
| corpus_size | corpus size (total frequency) |
| lemma_freq | lemma raw frequency |
| lfpm10 | log10 form frequency per million |
| llfpm10 | log10 lemma frequency per million |
| form_length | nchar form |
| form_syl_count | n vowels form |
| lemma_length | nchar lemma |
| lemma_syl_count | n vowels lemma |
| hunspell | does the hunspell spelling dictionary contain the lemma? |

## How to use

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
