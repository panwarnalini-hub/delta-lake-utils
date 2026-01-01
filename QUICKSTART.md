# Quick Start Guide

## Installation

```bash
pip install delta-lake-utils
```

## 5-Minute Tutorial

### 1. Optimize a Table

```python
from pyspark.sql import SparkSession
from delta_utils import DeltaOptimizer

spark = SparkSession.builder.getOrCreate()
optimizer = DeltaOptimizer(spark)

# Optimize your table
result = optimizer.auto_optimize('/mnt/delta/my_table')

print(f"Done! Reduced from {result.files_before} to {result.files_after} files")
```

### 2. Check Table Health

```python
from delta_utils import DeltaHealthChecker

checker = DeltaHealthChecker(spark)
report = checker.check_table('/mnt/delta/my_table')

# See health score and issues
checker.print_report(report)
```

### 3. Generate Pipeline

```python
from delta_utils import MedallionGenerator

generator = MedallionGenerator(base_path='/mnt/delta')

# Generate Bronze/Silver/Gold notebooks
notebooks = generator.generate_full_pipeline(
    table_name='sales_data',
    source_path='/mnt/raw/sales.json'
)

# Notebooks created in ./notebooks/ folder
```

## Common Commands

```bash
# Optimize from command line
delta-utils optimize /mnt/delta/my_table

# Health check
delta-utils health /mnt/delta/my_table
```

## What Next?

- Read EXPLANATION.md for detailed use cases
- See examples/ folder for Jupyter notebooks
- Check README.md for full API documentation
