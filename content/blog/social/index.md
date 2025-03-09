---
title: "A Trick to Never Confuse Cache Penetration and Cache Breakdown Again"
subtitle: "In Redis, there are two concepts: Cache Breakdown and Cache Penetration. Because their names are similar, it’s easy to mix them up. However, this article provides a simple memory trick to help you clearly distinguish between the two."
excerpt: ""
date: 2024-06-28
author: "Travis Liang"
draft: false
layout: single

---

<!-- 
There are five places where you can choose to show social icons. Here is the tl;dr:

+ site header (set in `config.toml`), 
+ site footer (set in `config.toml`), 
+ [homepage](/) (set in `content/_index.md`),
+ [about page](/about) in the sidebar (set in `content/about/sidebar/index.md`), and
+ [contact page](/contact) (set in `content/form/contact.md`). 

Read on to learn how to set up your social icons, and how to show/hide them. 
-->

## 1. Definitions and Differences

### Cache Penetration
Cache Penetration occurs when the requested data is **neither in the cache nor in the database**, yet users keep requesting it. This leads to queries directly hitting the database and increasing its load.

+ **Core issue**: The requested data **does not exist**, but a high request volume leads to invalid queries hitting the database.
+ **Common scenarios**:
  - Requests for non-existent IDs (e.g., negative numbers, invalid characters).
  - Malicious attacks deliberately querying non-existent data.
  - *Example*: Requesting a user ID of `-1` or querying a non-existent product.

### Cache Breakdown
Cache Breakdown happens when **hotspot data in the cache expires**, and a **large number of concurrent requests** simultaneously query the same data, causing a surge in database load.

+ **Core issue**: Cached data becomes **invalid**, while the database still contains the correct data, leading to a sudden spike in database pressure.
+ **Common scenarios**:
  - Expiration of hotspot data (e.g., popular product details, trending news).
  - High concurrency scenarios where many users access the same data at the same time.
  - *Example*: A flash sale product suddenly losing its cached details.

## 2. Solutions

### 2.1 Solutions for Cache Penetration
1. **Bloom Filter**
   - Add a Bloom Filter before the cache layer to store hash values of all valid keys.
   - If a request comes in and the key is **not in the Bloom Filter**, block the request immediately.
   - **Pros**: Minimal memory usage; effectively blocks non-existent queries.
   - **Cons**: May cause false positives if not configured properly.

2. **Cache Null Values**
   - Store a null value in the cache for queries that return no data, preventing repeated database hits.
   - **Pros**: Simple implementation; reduces database load.
   - **Cons**: Uses extra cache space.

```java
//sample code
public object GetProductListNew() {
    int cacheTime = 30;
    String cacheKey = "product_list";

    String cacheValue = CacheHelper.Get(cacheKey);
    if (cacheValue != null) {
        return cacheValue;
    }

    cacheValue = CacheHelper.Get(cacheKey);
    if (cacheValue != null) {
        return cacheValue;
    } else {
        // Database query returns no data, set to empty
        cacheValue = GetProductListFromDB();
        if (cacheValue == null) {
            // If found to be empty, set a default value and cache it
            cacheValue = string.Empty;
        }
        CacheHelper.Add(cacheKey, cacheValue, cacheTime);
        return cacheValue;
    }
}
```

3. **Parameter Validation**
   - Validate request parameters before querying the cache to filter out illegal inputs.
   - *Example*: Reject requests with obviously invalid IDs or formats.

### 2.2 Solutions for Cache Breakdown
1. **Mutex Lock**
   - When the cache expires, use a distributed lock to ensure only one thread rebuilds the cache while others wait.
   - **Pros**: Prevents multiple threads from querying the database simultaneously.
   - **Cons**: Complex implementation; might affect performance.

```java
//sample code
public String get(key) {
   String value = redis.get(key);
   if (value == null) { // Cache value has expired
      // Set a 3-minute timeout to prevent failure of the del operation, ensuring the cache can be reloaded from the database next time
      if (redis.setnx(key_mutex, 1, 3 * 60) == 1) {  // Successfully set the mutex
         value = db.get(key);
         redis.set(key, value, expire_secs);
         redis.del(key_mutex);
      } else {  // Another thread has already loaded the data from the database and set it in the cache, retry to get the cache value
         sleep(50);
         get(key);  // Retry
      }
   } else {
      return value;
   }
}
```
2. **Logical Expiration (Never Expire Strategy)**
   - Set hotspot keys to **never expire physically**, with background threads periodically refreshing the cache.
   - **Pros**: Prevents sudden cache expiration from overloading the database.
   - **Cons**: Requires additional logic to manage cache updates.

3. **Circuit Breaker & Downgrade Strategy**
   - In case of cache expiration, return default values or static pages to reduce database load.
   - **Pros**: Protects the database during cache failures.
   - **Cons**: May negatively impact user experience.

## 3. Comparison

| Issue                | Definition                                                                                 | Common Scenarios                                     | Solutions                                                  |
|----------------------|---------------------------------------------------------------------------------------------|------------------------------------------------------|------------------------------------------------------------|
| **Cache Penetration** | Data **does not exist** in both cache and database, causing direct hits to the database      | Non-existent IDs, malicious attacks                  | Bloom Filter, Cache Null Values, Parameter Validation      |
| **Cache Breakdown**   | **Hotspot data expires** and simultaneous requests lead to a surge in database load         | Hotspot data expiration, high concurrency scenarios  | Mutex Lock, Logical Expiration, Circuit Breaker & Downgrade  |

## 4. Memory Trick

- **Cache Breakdown** → Core issue: **Hotspot data expires** → Solution: **Rebuild the hotspot data.**
- **Cache Penetration** → Core issue: **Data does not exist in Redis or MySQL** → Solution: **Intercept invalid requests.**

Think of it this way:
- **Cache Breakdown** results from the expiration of **valid data.**
- **Cache Penetration** is due to accessing **invalid data.**

It’s similar to police interrogation: you can **"break through"** a suspect’s psychological defenses, but you cannot **"penetrate"** them. "Breakthrough" sounds **righteous**, while "penetration" often carries a **negative connotation** (e.g., "I can see through your little tricks").

By using this simple memory trick, you’ll never confuse **Cache Breakdown** with **Cache Penetration** again!

## Summary

**Cache Breakdown** and **Cache Penetration** are common topics in Redis system design interviews that are often confused. With the definitions, scenarios, solutions, and memory trick explained above, you should be able to distinguish them clearly and apply the appropriate strategy in practice.

---


