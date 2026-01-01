# Examples

## Example 1: Daily Optimization Job

Run this in Databricks job to optimize tables nightly:

```python
from delta_utils import DeltaOptimizer

spark = spark  # Available in Databricks
optimizer = DeltaOptimizer(spark)

tables_to_optimize = [
    '/mnt/delta/bronze/transactions',
    '/mnt/delta/bronze/customers',
    '/mnt/delta/silver/orders'
]

for table in tables_to_optimize:
    print(f"\nOptimizing {table}...")
    result = optimizer.auto_optimize(table, auto_zorder=True)
    print(f"Removed {result.files_removed} files")
```

## Example 2: Health Check Report

Generate health report for all tables:

```python
from delta_utils import DeltaHealthChecker

checker = DeltaHealthChecker(spark)

tables = [
    '/mnt/delta/sales',
    '/mnt/delta/customers',
    '/mnt/delta/products'
]

for table in tables:
    print(f"\n{'='*60}")
    print(f"Checking: {table}")
    print('='*60)
    
    report = checker.check_table(table)
    
    if report['score'] < 70:
        print(f"WARNING: Health score {report['score']}/100")
        checker.print_report(report)
```

## Example 3: Performance Benchmarking

Compare query performance before and after optimization:

```python
from delta_utils import DeltaProfiler, DeltaOptimizer

profiler = DeltaProfiler(spark)
optimizer = DeltaOptimizer(spark)

table = '/mnt/delta/large_table'

# Profile before optimization
print("Before optimization:")
before = profiler.profile_read(table, filter_expr='date >= "2024-01-01"')

# Optimize
optimizer.auto_optimize(table)

# Profile after optimization
print("\nAfter optimization:")
after = profiler.profile_read(table, filter_expr='date >= "2024-01-01"')

# Compare
improvement = (before.duration_seconds - after.duration_seconds) / before.duration_seconds * 100
print(f"\nPerformance improvement: {improvement:.1f}%")
```
