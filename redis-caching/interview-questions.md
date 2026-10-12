# Redis and Caching Interview Questions

## How would you implement Redis caching?

For a common cache-aside flow, the application checks Redis first. On a miss, it reads the database, stores the result with a suitable TTL, and returns it. On writes, update the source of truth and then invalidate or refresh the cache according to the consistency needs.

~~~text
Read: Redis hit -> return
      Redis miss -> database -> cache with TTL -> return
Write: database commit -> invalidate or refresh cache
~~~

Use clear key names, set expiry deliberately, and avoid caching data whose freshness requirements you cannot explain. Watch hit rate, latency, evictions, and database load. A cache stampede can happen when many requests all miss for the same hot key; consider request coalescing or controlled refresh.

See [Redis cache-aside guidance](https://redis.io/docs/latest/develop/use-cases/cache-aside/).

## Redis crashes. How should the application behave?

Decide whether Redis is a cache or a required data store. If it is a cache, the service can fall back to the database for a limited period, but should use timeouts, concurrency limits, and possibly a circuit breaker so a Redis outage does not flood the database. Degraded responses may be better than unbounded fallback traffic.

If Redis stores authoritative state, cache-aside fallback is not safe; recovery, replication, persistence, and data-loss expectations need to be designed explicitly. Alert on the failure and test the fallback path before an incident.
