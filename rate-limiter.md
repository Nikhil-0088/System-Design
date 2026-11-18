**Question**
Design a distributed rate limiter that protects APIs at 1M+ RPS with per-user / per-IP / per-API quotas.and quotas are defined in the service config.

**Requirments**
1. for a scle of 1M RPS we cannot have one node for a Rate limiter, we need horizontal scaling and then these horizintal nodes must agree on the same value while rate limiting
2. We need to enforce the limiting at per API KEY, per IP and per endpoint
3. if the limit reached we will throllte with HTTP 429 status code and headers like X-Ratelimit-Retry-After 
4. for rate limiting we will use a token bucket algorithm
5. rate limiter will be like a middel-ware

**HLD**
1, 



*Design Choices**
1. Rate Limiting Algorithm:
    a. One idea is we classify request into buckets , each bucket indicating one second, nand maintain teh count of requests in that second, but lets say at the end of one second we get burst of 1M reuest and at the start of next second we get another 1M requests so our rate limiter wil pass 1M request in less thn 1 sec, that is not intended --> BAD idea ---> this is called fixed window

    b. to fix the above burst at th edge of fixed windows we can use sliding windows, that is We esitmate the count of this request byt adding current count in this window + count of requests on previous window* overlap Percentage --> near accurate ---> no actual proof

    c. we add number of tokens to a bucket, each request will take one token and new token will be filled back only after a certain time interval --<> token bucket algorithm.



**Extra**
1. we can propose to hold a few rate limited request in a queue and take them once the load on server is less ,instead of outrigth rejecting the request, this can be for systems where latency in response is ok.