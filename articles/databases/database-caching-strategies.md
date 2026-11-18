---
id: kt:database-caching-strategies
type: Pattern
title: Database caching strategies
aliases: [Caching strategies, Cache-aside, Lazy loading cache, Write-through cache, Database caching patterns]
domain: databases
summary: >-
  The set of patterns (lazy loading/cache-aside, write-through, TTL expiry and invalidation) used to
  put an in-memory cache such as Amazon ElastiCache or DynamoDB Accelerator in front of a database so
  that frequently read data is served faster while bounding staleness and protecting the database.
verified: 2026-11-18
status: concept
broader: [kt:purpose-built-databases, kt:performance-efficiency-pillar]
related: [kt:amazon-rds, kt:amazon-aurora, kt:amazon-api-gateway]
relations:
  - {predicate: implements, object: kt:performance-efficiency-pillar}
  - {predicate: dependsOn, object: kt:amazon-elasticache}
  - {predicate: integratesWith, object: kt:amazon-dynamodb-accelerator}
  - {predicate: integratesWith, object: kt:amazon-dynamodb}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
sources:
  - id: s1
    title: "Caching strategies for Memcached - Amazon ElastiCache"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html
    type: official-docs
    accessed: 2026-11-18
  - id: s2
    title: "PERF03-BP05 Implement data access patterns that utilize caching - AWS Well-Architected Framework"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/wellarchitected/latest/framework/perf_data_access_patterns_caching.html
    type: official-docs
    accessed: 2026-11-18
  - id: s3
    title: "Caching Best Practices"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/caching/best-practices/
    type: official-docs
    accessed: 2026-11-18
  - id: s4
    title: "Caching database query results - Amazon ElastiCache"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/caching-database-query-results.html
    type: official-docs
    accessed: 2026-11-18
  - id: s5
    title: "DAX: How it works - Amazon DynamoDB"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide//DAX.concepts.html
    type: official-docs
    accessed: 2026-11-18
created: 2026-11-18
---

# Database caching strategies

Database caching strategies are the patterns that decide **when data enters a cache, how it is kept current, and when it leaves**. On AWS they are usually applied with Amazon ElastiCache (Valkey, Redis OSS or Memcached) in front of a relational or key-value store, or with DynamoDB Accelerator (DAX) in front of DynamoDB. AWS's ElastiCache guide stresses that the right strategy depends on the data and how it is accessed; a gaming top-10 leaderboard and a trending-news list call for different approaches.[^s1] The Well-Architected Framework makes caching a Performance Efficiency best practice (PERF03-BP05), rates the risk of skipping it as medium, and requires that anything cached can be rebuilt from its source of truth.[^s2]

## Lazy loading (cache-aside)

The application reads the cache first. On a hit it returns the value; on a miss (absent or expired key) it queries the database, writes the result into the cache and returns it.[^s1] Only data that is actually requested gets cached, and losing a cache node is not fatal: a replacement starts empty and refills as requests arrive, at the cost of extra latency in the meantime.[^s1] The drawbacks are a **miss penalty** of three round trips (cache read, database query, cache write) and **stale data**, because nothing updates the cache when the database changes.[^s1] AWS's caching best-practices page treats laziness as the foundation of a good strategy and notes that invalidation is as simple as deleting the key.[^s3]

## Write-through

The application updates the cache every time it writes to the database, so cached values are never stale.[^s1] The costs are a **write penalty** (two trips per write), **missing data** on a new or replaced node until each key is written again, and **cache churn**, since much of what is written is never read.[^s1] It suits data known to be hot, such as leaderboards and top stories, and AWS recommends pairing it with lazy loading so that misses still fill the cache.[^s3] A common variant is invalidate-on-write: write the database first, then delete the cached key and let the next read reload it, which the Well-Architected example shows alongside cache-aside reads.[^s2]

## TTLs, invalidation and eviction

A time to live (TTL) bounds how stale a key can get and trims unused entries; Memcached takes TTLs in seconds, while Valkey and Redis OSS accept seconds or milliseconds.[^s1] A TTL does not guarantee freshness—it only forces a periodic refetch—and AWS's own examples use 300 seconds.[^s1] Well-Architected advises a TTL on all cached data, chosen by how fast the data changes and how much staleness is tolerable.[^s2] To avoid many keys expiring together, add random jitter, for example up to 120 seconds on a 3,600-second TTL.[^s3] When memory fills, the engine evicts keys by its configured policy; per AWS's caching page (as of 2026-11), ElastiCache's default is volatile-lru, and frequent evictions usually mean the cache needs to scale up or out.[^s3]

## Gotchas

- **Thundering herd.** When a popular key expires, or an empty node joins, many clients miss at once and run the same expensive query. AWS suggests prewarming new nodes by replaying typical requests before adding them.[^s3]
- **Treating the cache as durable.** Well-Architected lists this as an anti-pattern, along with caching frequently changing data, ignoring consistency and not monitoring efficiency.[^s2]
- **Hit rate.** Aim for 80% or higher, computed from the `CacheHits` and `CacheMisses` CloudWatch metrics; a lower figure can mean an undersized cache or an access pattern that doesn't benefit.[^s2]
- **Consistency-sensitive reads.** Query-result caching is not recommended for queries needing strong consistency or read-after-write inside multi-statement transactions.[^s4]

## Choosing an implementation

- **Client-side (local) cache** gives the lowest latency but duplicates storage and lets clients disagree; a **remote cache** like ElastiCache is consistent across clients and scales on its own, at the cost of a network hop; multi-level caching combines both.[^s2]
- **SQL query-result caching** for PostgreSQL, MySQL and MariaDB can be added with the Remote Query Cache plugin of the AWS Advanced JDBC Wrapper (3.3.0 or later), which caches hinted queries in ElastiCache with a per-query TTL and minimal code changes.[^s4]
- **DynamoDB** workloads can use DAX, which is read-through for eventually consistent reads and write-through for writes (a write succeeds only if both DynamoDB and DAX are updated). Its item cache has a default TTL of 5 minutes, both caches evict by an always-on LRU, and strongly consistent reads bypass the cache.[^s5]
- **API responses** can also be cached at Amazon API Gateway, which Well-Architected lists beside ElastiCache.[^s2]

## Connections

This pattern sits under `kt:purpose-built-databases` and `kt:performance-efficiency-pillar`, and `implements` the latter's PERF03-BP05 practice.[^s2] It `dependsOn` `kt:amazon-elasticache`, the remote cache AWS's strategy pages are written for, and is `monitoredBy` `kt:amazon-cloudwatch` through ElastiCache hit and miss metrics.[^s1][^s2] It `integratesWith` `kt:amazon-dynamodb` and `kt:amazon-dynamodb-accelerator`, whose built-in read-through/write-through cache applies these strategies without application code.[^s5] It relates to `kt:amazon-rds` and `kt:amazon-aurora`, the usual relational back ends for query-result caching, and to `kt:amazon-api-gateway`, whose response cache is another layer in a multi-level design.[^s2][^s4]

## Sources
[^s1]: Amazon Web Services, "Caching strategies for Memcached - Amazon ElastiCache". https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html (accessed 2026-11-18)
[^s2]: Amazon Web Services, "PERF03-BP05 Implement data access patterns that utilize caching - AWS Well-Architected Framework". https://docs.aws.amazon.com/wellarchitected/latest/framework/perf_data_access_patterns_caching.html (accessed 2026-11-18)
[^s3]: Amazon Web Services, "Caching Best Practices". https://aws.amazon.com/caching/best-practices/ (accessed 2026-11-18)
[^s4]: Amazon Web Services, "Caching database query results - Amazon ElastiCache". https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/caching-database-query-results.html (accessed 2026-11-18)
[^s5]: Amazon Web Services, "DAX: How it works - Amazon DynamoDB". https://docs.aws.amazon.com/amazondynamodb/latest/developerguide//DAX.concepts.html (accessed 2026-11-18)
