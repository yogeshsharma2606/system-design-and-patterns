# Distributed Rate Limiter

Use Redis atomic counters. Key: `rate_limit:{userId}:{endpoint}:{window}`. Per-IP limits are unfair for shared NAT.
