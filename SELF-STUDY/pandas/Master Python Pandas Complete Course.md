# Master Python Pandas: Complete Course

Nov 21, 2026 · @Thisara Priyamal

This 12-week course takes you from your first `pd.read_csv()` to production-grade data pipelines, covering every major area of the pandas API with the real-world use case for each.

## Course overview

**Who it is for:** analysts, data scientists, engineers and students who know basic Python (variables, lists, dicts, functions, loops).

**What you will be able to do:** load data from any common source, clean and reshape it, aggregate and join it, analyse time series, handle large datasets efficiently, and ship tested, reusable pandas code.

**Prerequisites:** Python 3.10+, comfort with a terminal, and a willingness to type every example yourself.

**How to use it:** each module has (1) topics with descriptions, (2) core API to learn, (3) typical use cases, (4) a short code sample, and (5) exercises. Work in Jupyter or VS Code notebooks and do the exercises before moving on.

| Week | Modules | Milestone |
| --- | --- | --- |
| 1 | 1 to 3 | Create and manipulate Series and DataFrames |
| 2 | 4 to 5 | Load and profile any dataset |
| 3 | 6 to 7 | Select precisely; clean messy data |
| 4 | 8 to 9 | Transform columns; work with text and categories |
| 5 | 10 | Analyse time series |
| 6 | 11 | Aggregate with groupby, transform and agg |
| 7 | 12 to 13 | Join tables; pivot and melt |
| 8 | 14 to 15 | MultiIndex and rolling windows |
| 9 | 16 | Visualise and style results |
| 10 | 17 | Optimise speed and memory |
| 11 | 18 | Advanced patterns, testing, interop |
| 12 | 19 | Finish two capstone projects |

**Standard imports used throughout:**

```python
import pandas as pd
import numpy as np

print(pd.__version__)
```

## Module 1: Setup and foundations

Get a working environment and understand what pandas is built on, so every later behaviour makes sense.

| Topic | Description | Use case |
| --- | --- | --- |
| What pandas is | Labelled, tabular data structures built on NumPy and Apache Arrow; the standard Python tool for in-memory data analysis | Spreadsheet-style analysis in code |
| Installation | `pip install pandas`, conda, virtual environments, optional dependencies (`openpyxl`, `pyarrow`, `sqlalchemy`, `matplotlib`) | Reproducible project setup |
| Notebooks and IDEs | Jupyter, VS Code, Colab; display options and magic commands | Interactive exploration |
| NumPy refresher | `ndarray`, dtypes, vectorisation, broadcasting, boolean masks | Fast math without loops |
| Pandas versions | Pandas 2.x added Arrow-backed dtypes and Copy-on-Write; pandas 3.x makes Copy-on-Write and a dedicated string dtype the default. Check `pd.__version__` and the release notes for your version | Avoiding version surprises |
| Copy-on-Write | Each derived object behaves as an independent copy; chained assignment like `df[df.a > 1]['b'] = 0` no longer modifies `df`. Use `df.loc[mask, 'b'] = 0` | Writing safe, predictable code |
| Dtypes overview | `int64`, `float64`, `bool`, `object`/`str`, `category`, `datetime64`, `timedelta64`, nullable `Int64`, `boolean`, `string`, and `pyarrow` types | Choosing correct types early |
| Missing value markers | `NaN`, `NaT`, `pd.NA` and how each behaves | Handling incomplete data |

```python
import numpy as np

arr = np.array([1, 2, 3, 4])
print(arr * 2)            # vectorised: [2 4 6 8]
print(arr[arr > 2])       # boolean mask: [3 4]

pd.set_option('display.max_columns', 50)
pd.set_option('display.float_format', '{:,.2f}'.format)
```

**Exercises**

1. Create a virtual environment, install pandas, and print its version.
2. Build a NumPy array of 1 million random numbers and compute mean, std and a boolean-masked subset.
3. Explain in your own words why `df[df.a > 1]['b'] = 0` is unreliable and write the correct form.

## Module 2: Series in depth

A Series is a one-dimensional labelled array. Mastering it makes every DataFrame column operation intuitive.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Creating Series | From lists, dicts, scalars, NumPy arrays, ranges | `pd.Series(data, index=, dtype=, name=)` | Single metric, such as prices by ticker |
| Index and values | Labels vs positions; index alignment is automatic in arithmetic | `.index`, `.values`, `.to_numpy()`, `.name` | Aligning two datasets by label |
| Vectorised arithmetic | Element-wise math, comparison, boolean logic | `+ - * / ** & \| ~` | Unit conversion, flags |
| Accessing elements | Label, position, slice, boolean mask, fancy list | `s['a']`, `s.iloc[0]`, `s.loc['a':'c']` | Lookup and subsetting |
| Descriptive statistics | Summary measures | `sum mean median std var min max quantile mode skew kurt describe` | Quick distributions |
| Counting and uniqueness | Frequencies and distinct values | `value_counts`, `unique`, `nunique`, `is_unique`, `duplicated` | Category frequency tables |
| Ranking and ordering | Sort and rank | `sort_values`, `sort_index`, `rank`, `nlargest`, `nsmallest`, `idxmax`, `idxmin` | Top-N products |
| Applying functions | Per-element transforms | `map`, `apply`, `transform`, `agg` | Cleaning codes, recoding |
| Cumulative operations | Running totals and extremes | `cumsum cumprod cummax cummin`, `diff`, `pct_change`, `shift` | Growth rates, running balances |
| Membership and range tests | Check values against sets and intervals | `isin`, `between`, `where`, `mask`, `clip` | Filtering, capping outliers |
| Missing data in Series | Detect and fill | `isna`, `notna`, `fillna`, `dropna`, `interpolate` | Sensor gaps |
| Type conversion | Cast between types | `astype`, `pd.to_numeric`, `convert_dtypes` | Fixing numbers stored as text |
| Series accessors | Namespaced methods | `.str`, `.dt`, `.cat`, `.sparse` | Text and date handling |

```python
prices = pd.Series([102.5, 99.1, 105.3, 101.0],
                   index=['AAPL', 'MSFT', 'GOOG', 'AMZN'], name='price')

prices['AAPL']                      # 102.5
prices[prices > 100]                # boolean filter
prices.pct_change()                 # NaN for first element
prices.nlargest(2)                  # top two
(prices / prices.sum()).round(3)    # portfolio weights
```

**Exercises**

1. Build a Series of 10 monthly sales figures indexed by month name, then find the best month and month-over-month change.
2. Add two Series with partially overlapping indexes and explain the NaN result.
3. Convert a Series of strings like `'1,200'` to integers.

## Module 3: DataFrame in depth

The DataFrame is a two-dimensional table of labelled columns, each a Series. This module covers its anatomy and everyday manipulation.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Creating DataFrames | From dicts of lists, list of dicts, NumPy arrays, Series, records | `pd.DataFrame(...)`, `from_dict`, `from_records` | Test data, API responses |
| Anatomy | Rows, columns, index, dtypes, shape | `.shape .columns .index .dtypes .size .ndim .T .values` | Understanding structure |
| Column operations | Add, rename, reorder, drop, insert | `df['c']=`, `assign`, `insert`, `rename`, `drop`, `pop`, `reindex` | Feature engineering |
| Index operations | Set, reset, sort, rename index | `set_index`, `reset_index`, `sort_index`, `rename_axis`, `reindex`, `reindex_like` | Keyed lookups |
| Row operations | Append and remove rows | `pd.concat`, `drop`, `loc[new] =` | Growing a dataset |
| Iteration | Looping options and why to avoid them | `iterrows`, `itertuples`, `items` | Rare row-by-row logic |
| Arithmetic and alignment | Column-wise and row-wise math, align by labels | `add sub mul div`, `axis=`, `fill_value=` | Combining metrics |
| Aggregation basics | Reductions by axis | `sum mean median count min max std`, `agg` | Totals per column or row |
| Sorting | Sort by one or many columns | `sort_values(by=, ascending=, na_position=)` | Rank orders |
| Copying | Shallow and deep copies, and Copy-on-Write behaviour | `copy()` | Preventing side effects |
| Conversion | To other structures | `to_dict`, `to_numpy`, `to_records`, `to_list` | Passing data to other libraries |
| Transposing and swapping | Flip rows and columns | `.T`, `swapaxes` | Wide-to-long views |

```python
df = pd.DataFrame({
    'name':   ['Asha', 'Ben', 'Chen', 'Dina'],
    'dept':   ['Sales', 'IT', 'IT', 'HR'],
    'salary': [52000, 71000, 68000, 49000],
})

df = df.assign(bonus=df['salary'] * 0.10,
               total=lambda d: d['salary'] + d['salary'] * 0.10)
df = df.rename(columns={'name': 'employee'}).set_index('employee')
df.sort_values('total', ascending=False)
```

**Exercises**

1. Build a 6-row product DataFrame, add a `revenue` column, then rename and reorder the columns.
2. Set a meaningful index, then reset it and explain when each is preferable.
3. Rewrite an `iterrows()` loop as a vectorised expression.

## Module 4: Reading and writing data

Every analysis starts with I/O. Learn the right reader, and the options that save hours of cleaning afterwards.

| Format | Read / write | Important options | Use case |
| --- | --- | --- | --- |
| CSV / TSV / text | `read_csv`, `to_csv`, `read_table`, `read_fwf` | `sep`, `header`, `names`, `usecols`, `dtype`, `parse_dates`, `na_values`, `skiprows`, `nrows`, `chunksize`, `encoding`, `thousands`, `decimal`, `index_col`, `compression` | Exports, logs, open datasets |
| Excel | `read_excel`, `to_excel`, `ExcelWriter` | `sheet_name`, `header`, `usecols`, `engine='openpyxl'`, multiple sheets | Business reports |
| JSON | `read_json`, `to_json`, `json_normalize` | `orient`, `lines=True`, `record_path`, `meta` | Web APIs, nested data |
| SQL | `read_sql`, `read_sql_query`, `read_sql_table`, `to_sql` | SQLAlchemy engine, `params`, `chunksize`, `if_exists`, `method` | Databases and warehouses |
| Parquet / Feather / ORC | `read_parquet`, `to_parquet`, `read_feather`, `read_orc` | `engine='pyarrow'`, `columns`, `filters`, compression | Fast columnar storage |
| HTML | `read_html` | `match`, `attrs` | Scraping tables from web pages |
| XML | `read_xml`, `to_xml` | `xpath`, `parser` | Feeds, government data |
| Pickle | `read_pickle`, `to_pickle` | `compression` | Caching Python objects (only with trusted files) |
| HDF5 | `read_hdf`, `to_hdf`, `HDFStore` | `key`, `format='table'` | Large local stores |
| Clipboard | `read_clipboard`, `to_clipboard` |  | Pasting from spreadsheets |
| Markdown / LaTeX / HTML / string | `to_markdown`, `to_latex`, `to_html`, `to_string` |  | Reports and documentation |
| Stata / SAS / SPSS | `read_stata`, `read_sas`, `read_spss` |  | Survey and academic data |
| Cloud and remote | URLs, `s3://`, `gs://` via `fsspec` | `storage_options` | Reading directly from storage |

**Key ideas:** specify `dtype` and `parse_dates` up front, read only the `usecols` you need, use `chunksize` for files bigger than memory, and prefer Parquet over CSV for repeated work.

```python
df = pd.read_csv(
    'sales.csv',
    usecols=['date', 'region', 'units', 'price'],
    dtype={'region': 'category'},
    parse_dates=['date'],
    na_values=['N/A', '-'],
)

df.to_parquet('sales.parquet', index=False)

# Process a huge file in pieces
totals = sum(chunk['units'].sum()
             for chunk in pd.read_csv('big.csv', chunksize=500_000))

# Several sheets in one workbook
with pd.ExcelWriter('report.xlsx') as xw:
    df.to_excel(xw, sheet_name='Data', index=False)
    df.describe().to_excel(xw, sheet_name='Summary')
```

**Exercises**

1. Read a CSV with a European number format (`1.234,56`) correctly.
2. Flatten a nested JSON API response with `json_normalize`.
3. Load a table into SQLite with `to_sql`, then query it back with `read_sql`.
4. Compare file size and load time of the same data as CSV vs Parquet.

## Module 5: Inspecting and exploring data

Before changing anything, profile the data: size, types, gaps, distributions and relationships.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| First look | Peek at rows | `head`, `tail`, `sample(n, random_state=)` | Sanity check after loading |
| Structure | Rows, columns, types, memory | `shape`, `info(memory_usage='deep')`, `dtypes`, `columns` | Spotting wrong dtypes |
| Summary statistics | Numeric and categorical summaries | `describe(include='all')`, `percentiles=` | Distribution overview |
| Missingness profile | Count and share of nulls | `isna().sum()`, `isna().mean()`, `notna` | Data quality audit |
| Uniqueness | Distinct values and duplicates | `nunique`, `unique`, `value_counts(normalize=True)`, `duplicated` | Key and category checks |
| Correlation | Pairwise relationships | `corr(method=)`, `cov`, `corrwith` | Feature screening |
| Ranges and extremes | Min, max, quantiles, outliers | `quantile`, `nlargest`, `nsmallest`, `idxmax` | Finding anomalies |
| Selecting by dtype | Pick column groups | `select_dtypes(include=, exclude=)` | Separate numeric and text columns |
| Cross-tabulation | Counts across two categories | `pd.crosstab`, `groupby().size()` | Segment comparison |
| Memory usage | Per-column bytes | `memory_usage(deep=True)` | Planning optimisation |
| Comparing frames | Find differences | `equals`, `compare`, `isin` | Validating changes |
| Quick plots | Histogram, box, scatter | `df.hist()`, `df.plot()` | Visual profiling |

```python
df = pd.read_csv('customers.csv')

df.info(memory_usage='deep')
df.describe(include='all').T
(df.isna().mean() * 100).round(1).sort_values(ascending=False)
df['segment'].value_counts(normalize=True)
df.select_dtypes('number').corr().round(2)
```

**Exercises**

1. Write a function `profile(df)` that returns per-column dtype, null %, unique count and sample value.
2. Find the five columns most correlated with a target column.
3. Identify columns that could be converted to `category` to save memory.

## Module 6: Selection, indexing and filtering

The most common source of pandas bugs. Learn each accessor and when to use it.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Column selection | One column returns a Series; a list returns a DataFrame | `df['a']`, `df[['a','b']]`, `df.a` | Subsetting fields |
| Label-based `.loc` | Rows and columns by label, inclusive slices | `df.loc[rows, cols]` | Named lookups, safe assignment |
| Position-based `.iloc` | Rows and columns by integer position, exclusive slices | `df.iloc[0:5, [0, 2]]` | Positional slicing |
| Fast scalars | Single cell access | `.at`, `.iat` | Tight loops |
| Boolean filtering | Masks combined with `&`, `\|`, `~` and parentheses | `df[(df.a > 5) & (df.b == 'x')]` | Row filters |
| `isin` / `between` | Membership and range | `isin`, `between(inclusive=)` | Filter by list or interval |
| `query()` | String expressions with `@variable` | `df.query('a > @t and b in ["x","y"]')` | Readable filters |
| Text filters | Pattern matching | `df[df.name.str.contains('^A', na=False)]` | Name searches |
| Conditional assignment | Set values by condition | `df.loc[mask, 'c'] = v`, `np.where`, `np.select`, `where`, `mask` | Flagging, bucketing |
| Label slicing on sorted index | Range selection | `df.loc['2026-01':'2026-03']` | Time or alphabetical ranges |
| `filter()` | Select labels by pattern | `df.filter(like=, regex=, items=)` | Pick columns by name pattern |
| `take`, `xs`, `get` | Specialised access | `xs('key', level=)`, `df.get('col', default)` | MultiIndex, safe lookup |
| Head and tail by group | Positional picks | `nlargest`, `nsmallest`, `head`, `tail`, `drop_duplicates` | Top rows |
| Setting and dropping | Modify safely | `drop(columns=, index=)`, `df.loc[...] =` | Removing and updating |
| Copy-on-Write rules | Never assign through chained indexing | Use one `.loc` call | Avoiding silent no-ops |

```python
mask = (df['country'] == 'LK') & (df['revenue'] > 1000)
high_lk = df.loc[mask, ['customer', 'revenue']]

df.loc[df['revenue'] > 5000, 'tier'] = 'gold'

df['size'] = np.select(
    [df['units'] < 10, df['units'] < 100],
    ['small', 'medium'],
    default='large',
)

df.query('country in ["LK", "IN"] and revenue > @threshold')
```

**Exercises**

1. Select the 10th to 20th rows and every second column using `iloc`.
2. Filter orders placed in 2026 by customers in three given countries with revenue above the median.
3. Create a `tier` column with `np.select` using four ranges.

## Module 7: Data cleaning

Analysts spend most of their time here. This module covers every standard cleaning task.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Detecting missing data | Nulls per column or row | `isna`, `notna`, `isna().sum()`, `info` | Quality audit |
| Dropping missing data | Remove rows or columns | `dropna(axis=, how=, thresh=, subset=)` | Discard unusable records |
| Filling missing data | Constants, statistics, neighbours | `fillna(value, method via ffill/bfill)`, `ffill`, `bfill` | Imputation |
| Interpolation | Estimate gaps from neighbours | `interpolate(method='linear'/'time'/'spline')` | Sensor and price series |
| Group-wise imputation | Fill by group statistics | `groupby().transform('median')` | Better imputations |
| Duplicates | Find and remove | `duplicated(subset=, keep=)`, `drop_duplicates` | Deduplicating customers |
| Data type fixing | Convert safely | `astype`, `to_numeric(errors='coerce')`, `to_datetime`, `to_timedelta`, `convert_dtypes` | Text-stored numbers |
| Nullable types | Integer or boolean columns with missing values | `Int64`, `boolean`, `string`, `pd.NA` | Preserve integers with gaps |
| Replacing values | Map bad values | `replace`, `map`, regex replace | Standardising codes |
| Renaming and labels | Tidy column names | `rename`, `df.columns.str.strip().str.lower().str.replace(' ', '_')` | Consistent schema |
| Whitespace and case | Normalise text | `str.strip`, `str.lower`, `str.title` | Matching keys |
| Outliers | Detect and treat | IQR rule, z-score, `clip`, `quantile`, `winsorise` by clipping | Robust statistics |
| Validation | Assert expectations | `assert`, `df.eval`, `pd.testing`, `between`, `isin` | Pipeline safety |
| Inconsistent categories | Fuzzy or mapped fixes | `map` with dict, `str.replace`, `Categorical` | `'USA'`, `'U.S.'`, `'United States'` |
| Reshaping bad structure | Split and combine columns | `str.split(expand=True)`, `explode`, `melt` | Multi-value cells |
| Cleaning pipeline | Chain steps with `pipe` | `df.pipe(func)` | Repeatable cleaning |

```python
def clean(df):
    df = df.copy()
    df.columns = (df.columns.str.strip().str.lower()
                  .str.replace(r'\W+', '_', regex=True))
    df['amount'] = pd.to_numeric(df['amount'].astype('string')
                                 .str.replace(',', ''), errors='coerce')
    df['date'] = pd.to_datetime(df['date'], errors='coerce')
    df['country'] = df['country'].str.strip().str.upper().replace({'USA': 'US'})
    df['amount'] = df['amount'].fillna(
        df.groupby('country')['amount'].transform('median'))
    q1, q3 = df['amount'].quantile([.25, .75])
    iqr = q3 - q1
    df['amount'] = df['amount'].clip(q1 - 1.5 * iqr, q3 + 1.5 * iqr)
    return df.drop_duplicates(subset=['order_id'])

clean_df = raw.pipe(clean)
```

**Exercises**

1. Take a messy CSV with mixed date formats, currency symbols and duplicate IDs and produce a clean table.
2. Impute missing ages with the median age per job title.
3. Write three assertions that must hold on your cleaned data (unique IDs, no negative amounts, valid country codes).

## Module 8: Data transformation

Create new information from existing columns, using vectorised methods first and `apply` only as a last resort.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| `assign` and method chaining | Add columns without mutating | `df.assign(x=lambda d: ...)`, `pipe` | Readable pipelines |
| `map` | Element-wise lookup with dict, Series or function | `Series.map` | Recoding categories |
| `apply` | Function along an axis or per element | `apply(axis=0/1)`, `result_type=` | Custom logic when vectorising is impossible |
| `applymap` / `DataFrame.map` | Element-wise over whole frame | `df.map(func)` | Formatting every cell |
| `agg` and `transform` | Multiple reductions; same-shape results | `agg(['mean','std'])`, `transform` | Standardising columns |
| Conditional logic | Vectorised if/else | `np.where`, `np.select`, `where`, `mask`, `case_when` where available | Flags and tiers |
| Binning | Turn numbers into intervals | `pd.cut`, `pd.qcut`, `bins=`, `labels=` | Age bands, quantiles |
| Sorting and ranking | Order data | `sort_values`, `sort_index`, `rank(method=, pct=)`, `nlargest` | Leaderboards |
| Arithmetic helpers | Rounding, clipping, absolute | `round`, `clip`, `abs`, `cumsum`, `diff`, `pct_change`, `shift` | Growth, returns |
| Scaling and normalising | Min-max, z-score | `(x - x.min())/(x.max()-x.min())`, `(x-x.mean())/x.std()` | Machine learning features |
| Dummy variables | One-hot encoding | `pd.get_dummies(drop_first=)`, `pd.from_dummies` | Model inputs |
| Factorising | Integer codes for categories | `pd.factorize`, `cat.codes` | Label encoding |
| String splitting and combining | Expand or merge columns | `str.split(expand=True)`, `str.cat`, `+` | Parsing fields |
| `eval` and `query` | Fast expression evaluation | `df.eval('c = a + b')` | Large frames |
| Replace and rename | Value and label substitution | `replace`, `rename`, `rename_axis` | Standardisation |
| Duplicating and sampling | Random and weighted subsets | `sample(frac=, weights=)`, `repeat`, `take` | Train/test splits |
| Row-wise operations | Per-row calculations | `df[cols].sum(axis=1)`, `max(axis=1)`, `idxmax(axis=1)` | Totals across columns |

```python
out = (
    df
    .assign(
        revenue=lambda d: d['units'] * d['price'],
        age_band=lambda d: pd.cut(d['age'], [0, 18, 35, 60, 120],
                                  labels=['teen', 'young', 'adult', 'senior']),
        spend_rank=lambda d: d['revenue'].rank(pct=True),
    )
    .pipe(pd.get_dummies, columns=['age_band'], drop_first=True)
    .sort_values('revenue', ascending=False)
)

# z-score each numeric column
num = df.select_dtypes('number')
z = (num - num.mean()) / num.std()
```

**Exercises**

1. Replace an `apply(axis=1)` row function with `np.select` and measure the speed-up.
2. Bin customers into spending quartiles and count them per quartile.
3. One-hot encode `region`, then rebuild the original column with `from_dummies`.

## Module 9: Text data and categoricals

Two specialised data types that need their own toolkits: strings through the `.str` accessor and fixed-vocabulary data through `category`.

**Part A: String methods (`.str`)**

| Topic | Key API | Use case |
| --- | --- | --- |
| Case and whitespace | `lower upper title capitalize strip lstrip rstrip` | Normalising names |
| Searching | `contains startswith endswith match fullmatch find count` | Filtering text |
| Extracting | `extract(r'(\d+)')`, `extractall`, `findall`, `get`, slicing `str[:3]` | Pull IDs, codes, domains |
| Replacing | `replace(regex=True)`, `removeprefix`, `removesuffix`, `translate` | Clean symbols |
| Splitting and joining | `split(expand=True)`, `rsplit`, `partition`, `cat`, `join` | Split full names, build keys |
| Padding and formatting | `pad`, `zfill`, `center`, `wrap` | Fixed-width codes |
| Testing | `isnumeric isalpha isdigit isspace isupper len` | Validation |
| Dummies | `get_dummies(sep='\|')` | Multi-label fields such as tags |
| Encoding | `encode`, `decode`, `normalize` | Unicode clean-up |

**Part B: Categorical data**

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Creating categoricals | Convert or declare with order | `astype('category')`, `pd.Categorical`, `CategoricalDtype(ordered=True)` | Sizes: S < M < L |
| Categories | Inspect and edit the vocabulary | `.cat.categories`, `add_categories`, `remove_categories`, `rename_categories`, `reorder_categories`, `set_categories`, `remove_unused_categories` | Controlled vocabularies |
| Codes | Integer representation | `.cat.codes` | Encoding |
| Ordered comparisons | Compare and sort by custom order | `ordered=True`, `min`, `max`, `sort_values` | Ratings, severity levels |
| Memory and speed | Big savings for repeated strings | `memory_usage(deep=True)` | Large categorical columns |
| GroupBy behaviour | `observed=True` avoids empty groups | `groupby(col, observed=True)` | Faster aggregation |

```python
emails = pd.Series(['Ann.Lee@Corp.com ', 'bob@mail.org', None], dtype='string')

clean = emails.str.strip().str.lower()
domain = clean.str.extract(r'@([\w.]+)$')[0]
user = clean.str.split('@').str[0]

size = pd.Series(['S', 'L', 'M', 'S'],
                 dtype=pd.CategoricalDtype(['S', 'M', 'L'], ordered=True))
size.sort_values()              # S, S, M, L
(size >= 'M').sum()             # 2
```

**Exercises**

1. Extract the 4-digit year and order number from strings like `ORD-2026-00871`.
2. Split `'Colombo, Sri Lanka'` into two columns, trimming spaces.
3. Convert a 5-million-row string column with 20 unique values to `category` and report memory saved.

## Module 10: Dates, times and time series

Pandas was born in finance, and its time-series tools remain among its strongest.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Parsing dates | Strings to timestamps | `to_datetime(format=, errors='coerce', dayfirst=, utc=)`, `parse_dates` | Mixed date formats |
| Core types | Point in time, span, difference | `Timestamp`, `Period`, `Timedelta`, `DateOffset` | Durations, ages |
| Generating ranges | Regular date sequences | `date_range(start, end, periods, freq)`, `period_range`, `timedelta_range`, `bdate_range` | Calendars, scaffolds |
| `.dt` accessor | Extract components | `year month day dayofweek day_name hour quarter is_month_end dt.date normalize` | Seasonality features |
| Date arithmetic | Add or subtract time | `+ pd.Timedelta`, `pd.DateOffset(months=1)`, `MonthEnd`, `BDay` | Due dates, tenure |
| DatetimeIndex | Time-based index and slicing | `set_index`, `df.loc['2026-03']`, `between_time`, `at_time` | Fast range selection |
| Resampling | Change frequency | `resample('D'/'W'/'ME'/'QE').sum()/mean()/ohlc()`, `asfreq` | Daily to monthly reports |
| Shifting and lagging | Move data in time | `shift`, `diff`, `pct_change`, `tshift` alternatives | Period-over-period change |
| Rolling statistics | Moving average on time windows | `rolling('7D').mean()` | Smoothing |
| Time zones | Localise and convert | `tz_localize`, `tz_convert` | Global event data |
| Missing time points | Reindex to a full calendar | `reindex(date_range)`, `interpolate(method='time')` | Gaps in series |
| Merge by nearest time | Join on closest timestamp | `merge_asof(direction=, tolerance=)` | Trades with quotes |
| Periods and fiscal calendars | Business periods | `to_period('M')`, `PeriodIndex`, `asfreq`, custom `freq='QS-APR'` | Fiscal reporting |
| Holidays and business days | Custom calendars | `CustomBusinessDay`, `pandas.tseries.holiday` | Working-day calculations |
| Date-based groupby | Group by period | `groupby(pd.Grouper(key='date', freq='ME'))` | Monthly aggregates |
| Time-series features | Lags, rolling, calendar flags | Combine all above | Forecasting inputs |

Note: recent pandas versions renamed some frequency aliases (for example `'ME'` for month-end and `'QE'` for quarter-end replace `'M'` and `'Q'`), so check the docs for your version.

```python
sales = (pd.read_csv('orders.csv', parse_dates=['order_date'])
           .set_index('order_date')
           .sort_index())

monthly = sales['amount'].resample('ME').sum()
monthly_growth = monthly.pct_change()
trend = sales['amount'].resample('D').sum().rolling('30D').mean()

sales['weekday'] = sales.index.day_name()
sales['tenure_days'] = (pd.Timestamp('2026-11-21') - sales['signup']).dt.days

# Attach the latest known price to each trade
pd.merge_asof(trades.sort_values('time'), quotes.sort_values('time'),
              on='time', by='ticker', direction='backward')
```

**Exercises**

1. Resample daily sales to weekly totals and plot week-over-week growth.
2. Fill missing days in a series so every date from January to December appears.
3. Convert timestamps from UTC to Asia/Colombo and count orders per local hour.
4. Calculate the number of business days between two columns of dates.

## Module 11: GroupBy and aggregation

GroupBy follows split, apply, combine: divide rows into groups, compute something per group, and assemble the result.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Basic groupby | Group by one or more keys | `df.groupby('a')`, `groupby(['a','b'])`, `.groups`, `get_group` | Sales by region and product |
| Built-in aggregations | Reductions per group | `sum mean median count size nunique min max std var first last prod sem` | KPIs |
| `agg` with named output | Different functions per column with clean names | `agg(total=('amt','sum'), avg=('amt','mean'))` | Summary tables |
| Multiple and custom functions | Lists, dicts, lambdas | `agg(['min','max'])`, `agg(lambda s: ...)` | Custom metrics |
| `transform` | Group result broadcast back to every row | `groupby().transform('mean')`, `transform(lambda ...)` | Share of group total, z-score in group |
| `filter` | Keep or drop whole groups | `groupby().filter(lambda g: len(g) > 5)` | Remove small groups |
| `apply` | Arbitrary function per group | `groupby().apply(func)` | Complex per-group logic |
| Ranking inside groups | Order within each group | `groupby().rank()`, `cumcount`, `nth`, `head`, `tail` | Top-N per category |
| Cumulative by group | Running calculations | `groupby().cumsum()`, `cummax`, `shift`, `diff`, `pct_change` | Customer running totals |
| Time grouping | Group by calendar period | `pd.Grouper(freq=)`, `groupby().resample()` | Monthly per segment |
| Binning then grouping | Group by interval | `groupby(pd.cut(...))` | Age bands |
| Group options | Control output | `as_index=False`, `sort=False`, `dropna=False`, `observed=True`, `group_keys=` | Tidy output |
| Pivot-style aggregation | Cross-tab of aggregates | `pivot_table`, `unstack()` | Report layouts |
| Window functions in groups | Rolling or expanding per group | `groupby().rolling(3).mean()`, `expanding` | Per-entity trends |
| Weighted averages | Aggregate with weights | `np.average(x, weights=w)` inside `apply` | Price by volume |
| Iterating groups | Loop over groups | `for key, g in df.groupby(...)` | Export a file per group |

```python
summary = (
    df.groupby(['region', 'product'], as_index=False)
      .agg(orders=('order_id', 'nunique'),
           revenue=('amount', 'sum'),
           avg_order=('amount', 'mean'))
      .sort_values('revenue', ascending=False)
)

# share of region total on every row
df['region_share'] = df['amount'] / df.groupby('region')['amount'].transform('sum')

# top 3 products per region
top3 = (df.groupby('region', group_keys=False)
          .apply(lambda g: g.nlargest(3, 'amount')))

# customers with more than 5 orders
loyal = df.groupby('customer').filter(lambda g: len(g) > 5)
```

**Exercises**

1. For each customer, compute order count, first order date, last order date and total spend with one `agg` call.
2. Add a column showing each order's percentage of its customer's total.
3. Find the top 2 products in each category by revenue.
4. Compute a weighted average price per product where weights are quantities.

## Module 12: Combining data

Real analysis rarely lives in one table. Learn to stack, join and align datasets without duplicating or losing rows.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Concatenation | Stack frames vertically or side by side | `pd.concat([a, b], axis=, ignore_index=True, keys=, join='inner'/'outer')` | Monthly files into one table |
| Merge (SQL-style joins) | Combine on key columns | `pd.merge(left, right, on=, how='inner'/'left'/'right'/'outer'/'cross')` | Orders with customers |
| Different key names | Keys differ between tables | `left_on=`, `right_on=`, `left_index=`, `right_index=` | Legacy schemas |
| Overlapping columns | Control suffixes | `suffixes=('_x','_y')` | Same-named columns |
| Join validation | Catch accidental duplication | `validate='one_to_one'/'one_to_many'/'many_to_one'`, `indicator=True` | Data integrity |
| `DataFrame.join` | Index-based join shortcut | `df.join(other, how=, on=, lsuffix=)` | Index-aligned data |
| Anti and semi joins | Rows with or without a match | `merge(indicator=True)` then filter `_merge`, or `isin` | Customers with no orders |
| `merge_asof` | Nearest-key join for ordered data | `merge_asof(on=, by=, tolerance=, direction=)` | Time-aligned events |
| `merge_ordered` | Ordered merge with fill | `merge_ordered(fill_method=)` | Time series with gaps |
| `combine_first` and `update` | Fill gaps from a second frame | `combine_first`, `update` | Patching missing values |
| Alignment | Auto-align on labels | `align`, `reindex_like` | Different indexes |
| Appending rows | Add one or more rows | `pd.concat` (replaces removed `append`) | Incremental loads |
| Comparing frames | Row and column differences | `compare`, `equals`, `merge(indicator=True)` | Change detection |
| Many-to-many joins | Understand row explosion | Check key uniqueness first | Avoiding multiplied rows |
| Lookup enrichment | Add attributes by key | `map` with Series or dict | Fast single-column lookup |

```python
from pathlib import Path

# combine monthly files
all_orders = pd.concat(
    (pd.read_csv(f).assign(source=f.name) for f in Path('data').glob('orders_*.csv')),
    ignore_index=True,
)

enriched = all_orders.merge(
    customers[['customer_id', 'segment', 'country']],
    on='customer_id', how='left', validate='many_to_one', indicator=True,
)

# customers who never ordered
no_orders = customers.merge(all_orders[['customer_id']].drop_duplicates(),
                            on='customer_id', how='left', indicator=True)
no_orders = no_orders.query('_merge == "left_only"')

# fast lookup
all_orders['product_name'] = all_orders['sku'].map(products.set_index('sku')['name'])
```

**Exercises**

1. Build one frame from twelve monthly CSV files and verify row counts add up.
2. Left-join orders to customers and report how many orders have no matching customer.
3. Demonstrate a many-to-many merge row explosion, then fix it with `validate` and de-duplication.
4. Join price quotes to trades using `merge_asof` with a 1-second tolerance.

## Module 13: Reshaping

Move data between wide format (one column per period or category) and long format (one row per observation), plus cross-tabulation and list expansion.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Tidy data principles | One variable per column, one observation per row, one table per observational unit | Concept | Plot- and model-ready data |
| `pivot` | Long to wide, unique index and column pairs only | `df.pivot(index=, columns=, values=)` | Matrix of values |
| `pivot_table` | Pivot with aggregation for duplicates | `pivot_table(values, index, columns, aggfunc, fill_value, margins=True, observed=)` | Excel-style summaries |
| `melt` | Wide to long | `pd.melt(id_vars, value_vars, var_name, value_name)` | Year columns into rows |
| `wide_to_long` | Wide with stub names to long | `pd.wide_to_long(stubnames, i, j)` | `sales_2024`, `sales_2025` columns |
| `stack` / `unstack` | Move column labels to rows and back (index levels) | `stack()`, `unstack(level=, fill_value=)` | MultiIndex reshaping |
| `crosstab` | Frequency table for two or more factors | `pd.crosstab(a, b, normalize=, margins=, values=, aggfunc=)` | Survey response counts |
| `explode` | One row per list element | `df.explode('tags')` | Multi-value cells |
| Split into columns | Delimited text to columns | `str.split(expand=True)` | Parse composite fields |
| `transpose` | Swap axes | `.T` | Small summary tables |
| `get_dummies` | Categorical to indicator columns | `pd.get_dummies` | Model features |
| `json_normalize` | Nested JSON to flat table | `pd.json_normalize` | API payloads |
| `squeeze`, `droplevel` | Tidy shapes | `squeeze`, `droplevel`, `reset_index` | Flatten column MultiIndex |
| Flatten columns | Join MultiIndex labels | `df.columns = ['_'.join(c) for c in df.columns]` | After pivot\_table |

```python
wide = pd.DataFrame({
    'country': ['LK', 'IN'], '2024': [10, 40], '2025': [12, 46],
})
long = wide.melt(id_vars='country', var_name='year', value_name='gdp')

pt = df.pivot_table(index='region', columns='quarter', values='amount',
                    aggfunc='sum', fill_value=0, margins=True)

ct = pd.crosstab(df['gender'], df['plan'], normalize='index')

posts = pd.DataFrame({'id': [1, 2], 'tags': [['a', 'b'], ['c']]}).explode('tags')
```

**Exercises**

1. Convert a table with 12 month columns into long format and compute monthly averages by year.
2. Create a region by quarter revenue matrix with totals.
3. Build a crosstab of customer segment and churn, normalised by row.
4. Explode a comma-separated tags column and count the most common tags.

## Module 14: MultiIndex and hierarchical data

A MultiIndex gives rows or columns several levels, such as country then city. It is powerful for grouped output and panel data, but easy to misuse, so learn when to keep it and when to flatten it.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Creating a MultiIndex | From columns, tuples, arrays, products | `set_index([a, b])`, `MultiIndex.from_tuples`, `from_arrays`, `from_product` | Panel data (entity and time) |
| Inspecting levels | Names, levels, codes | `index.names`, `index.levels`, `index.get_level_values('a')`, `nlevels` | Understanding structure |
| Selecting | Partial and full-key lookup | `df.loc['LK']`, `df.loc[('LK','Colombo')]`, `xs('LK', level='country')`, `pd.IndexSlice` | Slicing a hierarchy |
| Sorting | Required for slicing | `sort_index(level=)`, `is_monotonic_increasing` | Avoid `PerformanceWarning` |
| Reordering and renaming | Change level order and labels | `swaplevel`, `reorder_levels`, `rename_axis`, `set_names` | Presentation |
| Aggregating by level | Reduce over a level | `groupby(level=)`, `sum(level)` alternatives via `groupby` | Totals per outer level |
| Moving levels | Between index and columns | `reset_index(level=)`, `stack`, `unstack(level=)` | Reshape |
| Dropping and flattening | Remove levels | `droplevel`, `reset_index`, join column labels | Simpler frames |
| Column MultiIndex | Hierarchical headers | `pd.concat(keys=)`, `agg` with several functions | Multi-metric summaries |
| Boolean filters on levels | Filter by level value | `df[df.index.get_level_values('a') == 'LK']` | Conditional selection |
| Updating values | Assign with tuples | `df.loc[('LK', 'Colombo'), 'pop'] = ...` | Targeted edits |
| Practical advice | Prefer flat columns with `groupby` unless hierarchy truly helps | Concept | Maintainable code |

```python
idx = pd.MultiIndex.from_product(
    [['LK', 'IN'], [2024, 2025]], names=['country', 'year'])
panel = pd.DataFrame({'gdp': [10, 12, 40, 46]}, index=idx)

panel.loc['LK']                          # all LK years
panel.xs(2025, level='year')             # every country in 2025
panel.groupby(level='country').pct_change()
panel.unstack('year')                    # years become columns
```

**Exercises**

1. Build a country and year panel and compute year-over-year growth within each country.
2. Use `pd.IndexSlice` to select several countries and a range of years in one call.
3. Flatten the column MultiIndex produced by `groupby().agg(['sum','mean'])`.

## Module 15: Window functions

Window functions compute statistics over a sliding, growing or exponentially weighted set of rows, which is essential for trends and smoothing.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Rolling windows | Fixed-size sliding window | `rolling(window, min_periods=, center=, closed=)` | 7-day moving average |
| Rolling statistics | Built-in functions | `mean sum std var min max median quantile count corr cov skew kurt apply` | Volatility, smoothing |
| Time-based windows | Window defined by duration on a DatetimeIndex | `rolling('30D')`, `rolling('1h', on='ts')` | Irregular timestamps |
| Custom windows | Weighted or non-standard windows | `win_type='gaussian'`, `rolling(...).apply(func, raw=True)`, `BaseIndexer` | Forward-looking windows |
| Expanding windows | Growing window from the start | `expanding(min_periods=).mean()/max()/sum()` | Running average, all-time high |
| Exponentially weighted | Recent values weigh more | `ewm(span=, halflife=, alpha=, adjust=).mean()/std()` | EMA, adaptive smoothing |
| Pairwise windows | Rolling correlation between series | `rolling(60).corr(other)`, `ewm().corr()` | Rolling correlation of assets |
| Grouped windows | Window within each group | `groupby('k').rolling(3).mean()` | Per-customer trends |
| Shift-based features | Lags and leads | `shift(n)`, `diff`, `pct_change(periods=)` | Forecasting features |
| Rank and cumulative | Position-based helpers | `rank`, `cumsum`, `cummax`, `cumcount` | Running totals |
| Numba engine | Faster window calculations | `engine='numba'` | Large data |

```python
price = df.set_index('date')['close']

features = pd.DataFrame({
    'close': price,
    'ma20': price.rolling(20).mean(),
    'vol20': price.pct_change().rolling(20).std(),
    'ema12': price.ewm(span=12, adjust=False).mean(),
    'high_to_date': price.expanding().max(),
    'ret_1d': price.pct_change(),
    'lag_5': price.shift(5),
})

features['drawdown'] = features['close'] / features['high_to_date'] - 1
```

**Exercises**

1. Compute 7-day and 30-day moving averages of daily sales and flag the days the short average crosses above the long one.
2. Compute a rolling 60-day correlation between two stocks.
3. Compute each customer's running total of spend and the order number within the customer.

## Module 16: Visualization and Styler

Pandas wraps Matplotlib for quick charts and includes a Styler for formatted, conditionally highlighted tables.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Plot basics | Plot from a Series or DataFrame | `df.plot(kind=, x=, y=, title=, figsize=, ax=)` | Fast exploration |
| Line and area | Trends over time | `kind='line'`, `'area'`, `subplots=True`, `secondary_y=` | Time series |
| Bar charts | Compare categories | `kind='bar'`, `'barh'`, `stacked=True` | Revenue by region |
| Distributions | Spread and shape | `kind='hist'`, `'kde'`, `'box'`, `df.hist()`, `bins=` | Outliers, skew |
| Relationships | Variable pairs | `kind='scatter'`, `'hexbin'`, `pd.plotting.scatter_matrix` | Correlation checks |
| Part of whole | Shares | `kind='pie'` (use sparingly) | Simple shares |
| Specialised plots | Extra pandas tools | `pd.plotting.parallel_coordinates`, `andrews_curves`, `radviz`, `lag_plot`, `autocorrelation_plot` | Time series diagnostics |
| Grouped plots | Plot per group | `df.groupby('k')['v'].plot(legend=True)`, `df.boxplot(by=)` | Compare segments |
| Customising | Matplotlib control | `ax.set_title`, `plt.tight_layout`, `plt.savefig` | Publication-ready charts |
| Plot backends | Other libraries | `pd.options.plotting.backend = 'plotly'` | Interactive charts |
| Styler basics | Format and colour tables | `df.style.format()`, `highlight_max`, `highlight_null`, `background_gradient`, `bar` | Reports in notebooks |
| Styler advanced | Custom CSS and export | `style.apply`, `map`, `set_properties`, `set_table_styles`, `to_html`, `to_latex`, `to_excel` | Branded reports |
| Display options | Control notebook output | `pd.set_option('display.max_rows', ...)`, `option_context` | Readable output |
| Companion libraries | Beyond pandas | Matplotlib, Seaborn, Plotly, Altair | Publication and interactive charts |

```python
import matplotlib.pyplot as plt

ax = (df.groupby('region')['revenue'].sum()
        .sort_values().plot.barh(title='Revenue by region'))
ax.set_xlabel('Revenue')
plt.tight_layout()
plt.savefig('revenue.png', dpi=150)

summary.style.format({'revenue': '{:,.0f}', 'margin': '{:.1%}'}) \
       .background_gradient(subset=['revenue'], cmap='Blues') \
       .highlight_max(subset=['margin'], color='lightgreen')
```

**Exercises**

1. Plot monthly revenue as a line with a 3-month moving average overlay.
2. Draw boxplots of order value by customer segment.
3. Style a summary table with thousands separators, a colour gradient and the maximum highlighted, then export it to HTML.

## Module 17: Performance, memory and scaling

Most slow pandas code has one of three causes: Python loops, wrong dtypes, or loading more data than needed.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Vectorisation | Replace loops and `apply` with column operations | Arithmetic, `np.where`, `str`/`dt` accessors | 10x to 100x speed-ups |
| Profiling | Measure before optimising | `%timeit`, `%%timeit`, `cProfile`, `line_profiler` | Finding bottlenecks |
| Memory reduction | Smaller dtypes | `astype('int32')`, `float32`, `category`, `pd.to_numeric(downcast=)`, `memory_usage(deep=True)` | Fit large data in RAM |
| Load less | Read only what you need | `usecols`, `nrows`, `dtype`, Parquet `columns=`, `filters=` | Faster loads |
| Chunking | Stream large files | `read_csv(chunksize=)`, `iterator=True` | Files larger than memory |
| Arrow-backed dtypes | Faster, compact strings and nullable types | `dtype_backend='pyarrow'`, `ArrowDtype`, `string[pyarrow]` | Text-heavy data |
| Efficient storage | Binary columnar formats | `to_parquet`, `to_feather` | Repeated analysis |
| `eval` / `query` | Optimised expression evaluation with `numexpr` | `df.eval`, `df.query` | Big arithmetic expressions |
| Fast groupby | Use built-in aggregations, `observed=True`, `sort=False` | `groupby().agg('sum')` | Large aggregations |
| Avoiding copies | Understand Copy-on-Write | `copy()`, in-place pitfalls | Memory and correctness |
| Index usage | Index for lookups and joins | `set_index`, sorted indexes | Fast `loc` |
| Numba / Cython | Compile custom functions | `apply(engine='numba')`, `rolling.apply(engine='numba')` | Heavy custom logic |
| Parallel and out-of-core | Libraries that scale pandas code | Dask, Polars, Modin, PySpark pandas API, DuckDB | Beyond a single machine's memory |
| Anti-patterns | What to avoid | `iterrows`, growing a frame in a loop, repeated `concat` | Common slowness |

```python
def shrink(df):
    for c in df.select_dtypes('integer'):
        df[c] = pd.to_numeric(df[c], downcast='integer')
    for c in df.select_dtypes('float'):
        df[c] = pd.to_numeric(df[c], downcast='float')
    for c in df.select_dtypes(['object', 'string']):
        if df[c].nunique() / len(df) < 0.5:
            df[c] = df[c].astype('category')
    return df

before = df.memory_usage(deep=True).sum() / 1e6
df = shrink(df)
after = df.memory_usage(deep=True).sum() / 1e6
print(f'{before:.0f} MB -> {after:.0f} MB')

# slow
df['c'] = df.apply(lambda r: r['a'] * r['b'], axis=1)
# fast
df['c'] = df['a'] * df['b']
```

**Exercises**

1. Reduce a 1 GB dataset's memory by at least 50% and report which change helped most.
2. Time `iterrows`, `apply(axis=1)` and a vectorised version on 1 million rows.
3. Process a file larger than memory with `chunksize` and produce per-category totals.

## Module 18: Advanced topics

These skills separate a competent user from a master: writing maintainable pipelines, testing them, and connecting pandas to the wider ecosystem.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Method chaining and `pipe` | Build pipelines of small, named functions | `df.pipe(f, arg)`, `assign`, `query` | Readable ETL |
| Options system | Global and scoped settings | `pd.set_option`, `get_option`, `reset_option`, `option_context`, `describe_option` | Display and behaviour control |
| Index types | Specialised indexes | `RangeIndex`, `DatetimeIndex`, `PeriodIndex`, `TimedeltaIndex`, `IntervalIndex`, `CategoricalIndex`, `Index.union/intersection/difference` | Efficient lookups |
| Intervals | Ranges as values | `pd.Interval`, `interval_range`, `IntervalIndex.from_breaks` | Bin lookups, overlapping ranges |
| Extension dtypes | Nullable and Arrow types | `Int64`, `Float64`, `boolean`, `string`, `ArrowDtype` | Missing-safe integers |
| Sparse data | Memory-efficient mostly-empty data | `pd.arrays.SparseArray`, `astype(pd.SparseDtype)`, `.sparse` accessor | One-hot matrices |
| Custom accessors | Add your own namespace | `@pd.api.extensions.register_dataframe_accessor` | Team utility libraries |
| Testing | Compare frames and Series in tests | `pd.testing.assert_frame_equal`, `assert_series_equal`, `assert_index_equal` | Unit tests with pytest |
| Data validation | Schema checks | `assert`, `pandera`, `pydantic` with `pandas` | Pipeline safety |
| Warnings and errors | Understand common ones | `SettingWithCopyWarning` (older versions), `FutureWarning`, `PerformanceWarning` | Debugging |
| Interoperability | Move data between tools | `to_numpy`, `to_dict`, `pyarrow.Table.from_pandas`, `polars.from_pandas`, `scikit-learn`, `statsmodels` | ML and statistics |
| Dataframe interchange | Standard protocol | `df.__dataframe__()`, `pd.api.interchange.from_dataframe` | Cross-library exchange |
| SQL-like operations | Express SQL ideas | `merge`, `groupby`, `query`, `pandasql` / DuckDB over frames | SQL users migrating |
| Random data and sampling | Reproducible experiments | `sample(random_state=)`, `np.random.default_rng` | Simulations, test splits |
| Functional helpers | Utilities | `pd.api.types.is_numeric_dtype`, `infer_objects`, `pd.unique`, `pd.cut`, `pd.isna` | Generic code |
| Logging and reproducibility | Make results repeatable | Seeds, fixed dtypes, versions in `requirements.txt` | Auditable analysis |
| Packaging pipelines | Move code from notebook to script | Functions, modules, CLI with `argparse` / `typer`, scheduled jobs | Production ETL |

```python
import pandas.testing as pdt

def load(path):
    return pd.read_csv(path, parse_dates=['date'])

def add_features(df):
    return df.assign(month=df['date'].dt.to_period('M'),
                     revenue=df['units'] * df['price'])

def monthly(df):
    return df.groupby('month', as_index=False)['revenue'].sum()

report = load('sales.csv').pipe(add_features).pipe(monthly)

# pytest-style test
def test_monthly():
    df = pd.DataFrame({'date': pd.to_datetime(['2026-01-01', '2026-01-15']),
                       'units': [1, 2], 'price': [10, 10]})
    out = df.pipe(add_features).pipe(monthly)
    expected = pd.DataFrame({'month': [pd.Period('2026-01')], 'revenue': [30]})
    pdt.assert_frame_equal(out, expected, check_dtype=False)

@pd.api.extensions.register_dataframe_accessor('ana')
class AnaAccessor:
    def __init__(self, df):
        self._df = df
    def null_report(self):
        return self._df.isna().mean().sort_values(ascending=False)
```

**Exercises**

1. Refactor a 60-line notebook into three tested functions chained with `pipe`.
2. Write a pytest suite with at least five tests for a cleaning function, including edge cases such as empty frames.
3. Register a custom accessor with two methods used in your team's projects.

## Module 19: Real-world use cases and capstone projects

Apply everything to realistic problems. Do at least two capstones end to end, from raw file to a written conclusion.

**Use cases by domain**

| Domain | Typical pandas work | Modules used |
| --- | --- | --- |
| Business and sales | Revenue reports, cohort and retention tables, RFM customer segmentation, basket analysis | 7, 11, 12, 13 |
| Finance | Returns, rolling volatility, drawdown, portfolio weights, OHLC resampling, trade and quote alignment | 10, 11, 15 |
| Marketing | Funnel conversion, A/B test summaries, campaign attribution, UTM parsing | 9, 11, 13 |
| Operations and supply chain | Inventory turnover, delivery delays, demand forecasting features | 10, 11, 15 |
| Healthcare and science | Patient cohorts, lab result cleaning, survey crosstabs, missing-data handling | 7, 11, 13 |
| HR and people analytics | Attrition rates, tenure bands, pay equity by group | 8, 11 |
| IoT and sensors | Gap filling, resampling, anomaly flags with rolling z-scores | 7, 10, 15 |
| Web and product analytics | Sessionisation, log parsing, user retention curves | 9, 10, 11 |
| Machine learning prep | Feature engineering, encoding, scaling, train/test splits | 8, 11, 15 |
| Data engineering | ETL scripts, validation, Parquet data lakes, SQL loads | 4, 17, 18 |

**Capstone projects**

| Project | Dataset idea | Skills tested | Deliverable |
| --- | --- | --- | --- |
| 1. Sales performance dashboard | Retail orders (several CSVs) | I/O, cleaning, merge, groupby, resample, plots | Notebook with monthly KPIs, top products, regional trends |
| 2. Customer segmentation (RFM) | Transaction history | Date maths, groupby, `qcut`, transform | Customer table with recency, frequency, monetary scores and segments |
| 3. Stock analysis | Daily prices for 5 tickers | Time series, rolling, correlation, drawdown | Risk and return report with charts |
| 4. Data cleaning challenge | Deliberately messy survey or HR export | Text cleaning, dtypes, missing data, validation | Clean dataset, cleaning function, tests |
| 5. Big-file pipeline | Multi-GB log or trip data | Chunking, dtypes, Parquet, aggregation | Script producing summary tables under a memory budget |
| 6. Cohort retention analysis | Sign-up and activity events | Pivot, `cumcount`, period arithmetic | Retention heatmap table |

```python
# RFM segmentation sketch
snapshot = pd.Timestamp('2026-11-21')
rfm = (orders.groupby('customer_id')
       .agg(last=('order_date', 'max'),
            frequency=('order_id', 'nunique'),
            monetary=('amount', 'sum'))
       .assign(recency=lambda d: (snapshot - d['last']).dt.days))

rfm['R'] = pd.qcut(rfm['recency'], 4, labels=[4, 3, 2, 1]).astype(int)
rfm['F'] = pd.qcut(rfm['frequency'].rank(method='first'), 4, labels=[1, 2, 3, 4]).astype(int)
rfm['M'] = pd.qcut(rfm['monetary'], 4, labels=[1, 2, 3, 4]).astype(int)
rfm['score'] = rfm[['R', 'F', 'M']].sum(axis=1)
```

**Open datasets to practise on:** NYC taxi trips, Kaggle retail and Titanic datasets, World Bank indicators, UCI Machine Learning Repository, Our World in Data, and your own bank or fitness exports.

## Appendix: cheat sheet, pitfalls and practice plan

**Quick-reference by task**

| I want to... | Use |
| --- | --- |
| Load a file | `read_csv`, `read_excel`, `read_parquet`, `read_json`, `read_sql` |
| See the data | `head`, `info`, `describe`, `value_counts`, `isna().sum()` |
| Pick rows or columns | `loc`, `iloc`, boolean mask, `query`, `filter` |
| Fix types and gaps | `astype`, `to_numeric`, `to_datetime`, `fillna`, `dropna`, `interpolate` |
| Create columns | `assign`, `np.where`, `np.select`, `map`, `cut`, `qcut` |
| Summarise by group | `groupby().agg()`, `transform`, `pivot_table`, `crosstab` |
| Combine tables | `concat`, `merge`, `join`, `merge_asof` |
| Reshape | `melt`, `pivot`, `stack`, `unstack`, `explode` |
| Work with time | `to_datetime`, `.dt`, `resample`, `rolling`, `shift`, `pct_change` |
| Work with text | `.str.contains`, `.extract`, `.split`, `.replace` |
| Speed up | vectorise, `category`, Parquet, `usecols`, `chunksize` |
| Save results | `to_csv`, `to_parquet`, `to_excel`, `to_sql` |

**Common pitfalls**

1. Chained assignment such as `df[mask]['c'] = 1`. Use `df.loc[mask, 'c'] = 1`.
2. Using `and` / `or` instead of `&` / `|`, or forgetting parentheses around each condition.
3. Merging on non-unique keys and silently multiplying rows. Use `validate=` and compare row counts.
4. Treating `NaN` like a normal value: `NaN == NaN` is False, so use `isna()`.
5. Looping with `iterrows` or `apply(axis=1)` when a vectorised expression exists.
6. Forgetting that `loc` slices include the end label while `iloc` slices exclude it.
7. Reading numbers or dates as text because `dtype` and `parse_dates` were not set.
8. Relying on defaults that change between versions. Pin the pandas version and read the release notes before upgrading.
9. Growing a DataFrame inside a loop. Collect pieces in a list and call `pd.concat` once.
10. Using `groupby().apply` where a built-in aggregation or `transform` is faster and clearer.

**12-week practice plan**

| Weeks | Daily habit (45 to 60 minutes) |
| --- | --- |
| 1 to 4 | Read one module, type every example, finish its exercises |
| 5 to 8 | Redo exercises on a new dataset of your choice; explain each solution aloud |
| 9 to 10 | Optimise an earlier notebook for speed and memory; write tests |
| 11 to 12 | Complete two capstone projects and publish them (GitHub or a blog post) |

**Mastery checklist**

- [ ] I can load any common file format and control dtypes, dates and missing values at read time.
- [ ] I can select any subset of data with `loc`, `iloc`, masks and `query` without warnings.
- [ ] I can clean a messy dataset and prove it with assertions.
- [ ] I can answer a business question with `groupby`, `agg`, `transform` and `pivot_table`.
- [ ] I can join several tables and verify that no rows were lost or duplicated.
- [ ] I can resample, shift and roll time series and handle time zones.
- [ ] I can reshape between wide and long formats in either direction.
- [ ] I can profile and speed up slow pandas code and cut its memory use.
- [ ] I can write a tested, reusable pipeline with `pipe` and ship it as a script.

**Resources**

- Official pandas documentation: user guide, API reference and cookbook (pandas.pydata.org)
- *Python for Data Analysis* by Wes McKinney, the creator of pandas
- *Effective Pandas* by Matt Harrison
- Kaggle Learn: Pandas micro-course and public notebooks
- Practice: Kaggle datasets, StrataScratch, DataLemur and LeetCode's pandas problems
