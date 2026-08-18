---
title: 'The Difference Between UNION and UNION ALL in Postgres'
description: 'Brief explantion about the difference between UNION and UNION ALL in Postgres'
pubDate: '2026-08-12'
heroImage: '/blog-placeholder-2.jpg'
categories: ['postgres', 'database']
language: en
---

# The Difference Between UNION and UNION ALL in Postgres

Use **UNION** if you want to return unique values. For Example:

```sql
SELECT 1 AS n 
UNION
SELECT 1 AS n 

```

The Result is:

```
1
```

Use **UNION ALL** if you want to let non unique values. For Example:

```sql
SELECT 1 AS n 
UNION ALL
SELECT 1 AS n 

```

The Result is:

```
1
1
```