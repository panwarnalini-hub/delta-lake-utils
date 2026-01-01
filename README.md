# delta-lake-utils

Production-grade utilities for Delta Lake table management on Databricks.

## What Does This Package Do?

This package provides automated tools to manage and optimize Delta Lake tables. Think of it as a Swiss Army knife for Databricks data engineers.

### Main Features:

1. **Smart OPTIMIZE** - Automatically consolidates small files and improves query performance
   - Detects when your Delta table has too many small files
   - Intelligently chooses which columns to Z-ORDER by
   - Reduces query time by up to 10x

2. **Health Checker** - Diagnoses table problems before they impact production
   - Identifies small file problems
   - Detects data skew across partitions
   - Finds configuration issues

3. **Performance Profiler** - Measures how fast your Delta operations run
   - Track read/write speeds
   - Identify bottlenecks
   - Compare before/after optimization

4. **Medallion Generator** - Auto-creates Bronze/Silver/Gold pipeline code
   - Generates production-ready notebooks
   - Follows best practices
   - Saves hours of boilerplate coding

5. **Unity Catalog Auditor** - Manages permissions and access control
   - Audits table permissions
   - Generates permission scripts
   - Ensures security compliance

## Quick Start

```python
from pyspark.sql import SparkSession
from delta_utils import DeltaOptimizer

spark = SparkSession.builder.getOrCreate()
optimizer = DeltaOptimizer(spark)

# Optimize a table - reduces files, improves performance
result = optimizer.auto_optimize('/mnt/delta/my_table')
print(f"Optimized! Removed {result.files_removed} files")
```

## Installation

```bash
pip install delta-lake-utils
```

## Use Cases

### Use Case 1: Your queries are slow
Problem: Delta table has 5000 small files, queries take 10 minutes
Solution: Run optimizer, consolidates to 50 files, queries now take 1 minute

### Use Case 2: Starting a new data pipeline
Problem: Need to build Bronze/Silver/Gold architecture from scratch
Solution: Use medallion generator, get complete pipeline in 30 seconds

### Use Case 3: Data quality issues
Problem: Not sure if table is healthy, production keeps failing
Solution: Run health checker, get specific recommendations to fix issues

### Use Case 4: Permission audit required
Problem: Need to verify all tables have correct access controls
Solution: Use catalog auditor to check and fix permissions

## Full Documentation

See examples/ folder for Jupyter notebooks with detailed usage.

## Requirements

- Python 3.8+
- PySpark 3.2+
- Delta Lake 2.0+
- Databricks Runtime 11.0+ recommended

## Author

Nalini Panwar - Lead Data Engineer
