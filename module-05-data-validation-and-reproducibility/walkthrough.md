# Module 5 — Walkthrough: Data Validation and Reproducibility

## 1. Write inline asserts

You're about to load a transactions CSV. The first thing you should write — *before* any analysis — is what you *expect* about it:

```python
import pandas as pd

df = pd.read_csv("data/raw/transactions.csv")

assert len(df) > 0, "transactions file is empty"
assert {"date", "amount", "vendor"}.issubset(df.columns), \
    f"missing required columns; got {df.columns.tolist()}"
assert df["amount"].notna().all(), "amount column has nulls"
assert (df["amount"] >= 0).all(), "negative amounts not allowed"
```

If any assertion fails, the script stops *immediately* with a clear message — far better than silently propagating bad data into a final number.

## 2. Step up to a `pandera` schema

`assert` is fine for one-off checks. For real data, use a real schema.

```bash
uv add pandera
```

```python
import pandas as pd
import pandera.pandas as pa
from pandera.typing import Series

class TransactionSchema(pa.DataFrameModel):
    date: Series[pa.DateTime]
    amount: Series[float] = pa.Field(ge=0)
    vendor: Series[str] = pa.Field(str_length={"min_value": 1})

df = pd.read_csv("data/raw/transactions.csv", parse_dates=["date"])
TransactionSchema.validate(df)
```

If the data violates the schema, `pandera` raises a detailed report — which row, which column, which constraint.

## 3. Set random seeds (everywhere)

If your analysis uses any randomness — sampling, train/test split, simulation — set the seed at the top of the script:

```python
import random
import numpy as np

random.seed(42)
np.random.seed(42)
```

Run your analysis twice. The output should be identical bit-for-bit. If it isn't, you have unseeded randomness somewhere — find it.

## 4. Sort before you aggregate

```python
df = df.sort_values(["date", "vendor"]).reset_index(drop=True)
```

Pandas' groupby and merge can produce different row orders on different runs. Sorting at known checkpoints makes your output deterministic.

## 5. Write your first `pytest` test

```bash
uv add --dev pytest
mkdir -p tests
touch tests/__init__.py tests/test_analysis.py
```

In `tests/test_analysis.py`:

```python
import pandas as pd
from src.analysis import summary_stats   # the function you wrote in Module 3

def test_summary_stats_returns_a_row_per_column():
    df = pd.DataFrame({"a": [1, 2, 3], "b": [4, 5, 6]})
    result = summary_stats(df)
    assert len(result) == 2
```

Run:

```bash
uv run pytest
```

Green dots = passing. If not, fix the function or the test.

## 6. Snapshot test for analysis results

For larger analyses, snapshot the answer so a regression breaks the test loudly:

```python
# tests/test_monthly_totals_snapshot.py
import pandas as pd
from src.analysis import compute_monthly_totals

def test_monthly_totals_match_snapshot():
    df = pd.read_csv("tests/fixtures/sample_transactions.csv", parse_dates=["date"])
    result = compute_monthly_totals(df)
    expected = pd.read_csv("tests/fixtures/expected_monthly_totals.csv")
    pd.testing.assert_frame_equal(
        result.reset_index(drop=True),
        expected.reset_index(drop=True),
    )
```

Generate the expected file once by hand (after you've eyeballed the result and confirmed it's right), commit it, and any future change that breaks the answer will fail this test.

## You're done if…

- [ ] Every input dataset in your project is validated by `pandera` or `assert`.
- [ ] All randomness is seeded.
- [ ] You have a `tests/` directory with at least 3 passing tests.
- [ ] `uv run pytest` is green.
