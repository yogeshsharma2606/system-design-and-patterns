# Cache Stampede

Many requests miss cache simultaneously and hit DB. Solutions: TTL jitter, request coalescing, stale-while-revalidate, pre-warming.
