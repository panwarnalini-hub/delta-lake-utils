# Delta Lake Utils - What It Does and How to Use It

## Overview

This package solves common Delta Lake problems that data engineers face daily:
- Too many small files slowing down queries
- No visibility into table health
- Manual optimization taking hours
- Repetitive pipeline code

## Core Components

### 1. DeltaOptimizer

**What it does:**
Automatically consolidates small files in Delta tables and optimizes data layout.

**When to use:**
- After many small writes to a table
- When queries are slower than expected
- After DELETE or UPDATE operations
- Weekly/nightly maintenance jobs

**Example:**
```python
from delta_utils import DeltaOptimizer

optimizer = DeltaOptimizer(spark)
result = optimizer.auto_optimize('/mnt/delta/sales_data')

# Before: 2000 small files, queries take 5 minutes
# After: 100 optimized files, queries take 30 seconds
```

**How it works:**
1. Analyzes current file structure
2. Detects if files are too small or imbalanced
3. Consolidates files to optimal size (128MB default)
4. Applies Z-ORDER on frequently queried columns
5. Returns metrics on improvement

### 2. DeltaHealthChecker

**What it does:**
Scans Delta tables for common problems and provides fix recommendations.

**When to use:**
- Before releasing to production
- After major data loads
- Investigating performance issues
- Regular health audits

**Example:**
```python
from delta_utils import DeltaHealthChecker

checker = DeltaHealthChecker(spark)
report = checker.check_table('/mnt/delta/customer_data')

# Output shows:
# - 500 small files detected (CRITICAL)
# - Data skew ratio 8.5x (WARNING)
# - Recommendations: Run OPTIMIZE, review partitioning
```

**Checks performed:**
- Small files (< 10MB)
- Data skew across partitions
- Excessive DELETE operations
- Too many partitions
- Missing configuration settings

### 3. DeltaProfiler

**What it does:**
Measures performance of Delta operations to identify bottlenecks.

**When to use:**
- Performance tuning
- Before/after optimization comparison
- Capacity planning
- SLA monitoring

**Example:**
```python
from delta_utils import DeltaProfiler

profiler = DeltaProfiler(spark)
result = profiler.profile_read(
    '/mnt/delta/orders',
    filter_expr='order_date >= "2024-01-01"'
)

# Shows: 1.2M rows/sec throughput, 45 sec duration
```

### 4. MedallionGenerator

**What it does:**
Auto-generates Bronze/Silver/Gold pipeline notebooks following best practices.

**When to use:**
- Starting new data pipeline
- Standardizing team's code structure
- Teaching best practices
- Rapid prototyping

**Example:**
```python
from delta_utils import MedallionGenerator

generator = MedallionGenerator(base_path='/mnt/delta')
notebooks = generator.generate_full_pipeline(
    table_name='customer_transactions',
    source_path='/mnt/raw/transactions.json'
)

# Creates 3 notebooks:
# - Bronze: Raw data ingestion
# - Silver: Data quality and deduplication
# - Gold: Business aggregations
```

**Generated code includes:**
- Streaming or batch processing
- Data quality checks
- Error handling
- Logging and monitoring
- Unity Catalog integration

### 5. CatalogAuditor

**What it does:**
Audits Unity Catalog permissions and generates fix scripts.

**When to use:**
- Security audits
- Compliance requirements
- Permission cleanup
- Access control validation

**Example:**
```python
from delta_utils import CatalogAuditor

auditor = CatalogAuditor(spark)

expected_perms = {
    'data_engineers': ['SELECT', 'MODIFY'],
    'analysts': ['SELECT']
}

issues = auditor.audit_table_permissions(
    catalog='production',
    schema='sales',
    expected_permissions=expected_perms
)

# Shows missing or excessive permissions
# Generates SQL scripts to fix
```

## Real-World Scenarios

### Scenario 1: Daily Sales Data Pipeline

**Problem:** Sales data arrives hourly in small JSON files, table has 10,000+ files

**Solution:**
```python
# Schedule nightly
optimizer = DeltaOptimizer(spark)
optimizer.auto_optimize('/mnt/delta/sales', auto_zorder=True)
# Reduces to ~200 files, queries 20x faster
```

### Scenario 2: New Customer Analytics Pipeline

**Problem:** Need to build Bronze/Silver/Gold for customer data

**Solution:**
```python
generator = MedallionGenerator(base_path='/mnt/delta')
notebooks = generator.generate_full_pipeline(
    table_name='customers',
    source_path='/mnt/raw/customers'
)
# Complete pipeline in 30 seconds vs 4 hours manual coding
```

### Scenario 3: Production Performance Issue

**Problem:** Queries suddenly slow, management asking why

**Solution:**
```python
checker = DeltaHealthChecker(spark)
report = checker.check_table('/mnt/delta/orders')
# Identifies: 3500 small files from weekend batch job
# Recommendation: Run OPTIMIZE

optimizer.auto_optimize('/mnt/delta/orders')
# Queries back to normal speed
```

### Scenario 4: Quarterly Security Audit

**Problem:** Need to verify all production tables have correct permissions

**Solution:**
```python
auditor = CatalogAuditor(spark)

for table in production_tables:
    issues = auditor.audit_table_permissions(
        catalog='prod',
        schema='finance',
        expected_permissions=security_policy
    )
    # Auto-generates fix scripts for each issue
```

## Best Practices

### When to Optimize
- After loading large batches
- Weekly for high-write tables
- After 100+ files accumulate
- When queries slow down

### When to Check Health
- Before production releases
- After major schema changes
- Monthly audits
- When troubleshooting performance

### When to Profile
- Benchmarking new queries
- Before/after optimization
- Capacity planning
- SLA validation

## Command Line Usage

```bash
# Quick health check
delta-utils health /mnt/delta/my_table

# Optimize with auto Z-ORDER
delta-utils optimize /mnt/delta/my_table --auto-zorder

# Generate medallion pipeline
delta-utils generate customer_data --base-path /mnt/delta

# Profile performance
delta-utils profile /mnt/delta/my_table --filter "date >= '2024-01-01'"
```

## Performance Impact

Typical results from production use:

- File count: 5000 → 100 (95% reduction)
- Query time: 10 min → 45 sec (93% faster)  
- Storage: Same (just reorganized)
- Write speed: 10-20% improvement with auto-optimize enabled

## Next Steps

1. Install: `pip install delta-lake-utils`
2. Try optimizer on a test table
3. Run health checker on production tables
4. Generate your first medallion pipeline
5. Set up nightly optimization jobs

## Support

For issues or questions:
- GitHub: github.com/panwarnalini-hub/delta-lake-utils
- Email: panwarnalini@gmail.com
