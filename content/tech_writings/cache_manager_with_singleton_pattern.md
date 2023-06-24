---
title: "Cache Manager with Singleton Pattern using Python"
date: 2023-02-27T17:29:58+02:00
author: Moustafa Elghrib
location: Minya, Egypt
---

### Introduction
In this article we will build a cache manager that stores frequently used data in memory using the Singleton pattern where the Singleton instance can ensure that the data is accessed efficiently and that memory usage is optimized.

{{< figure src="/posts/cache_manager.png" width="100%" >}}

### UML Diagram of the Cache Manager
![UML Diagram of the Cache Manager](/images/cache_manager_uml.png)

### Code Implementation
The cache manager is very simple approach, we need to save data with predefined key and then use this key to retrieve this data and this approach could be achieved through the set and get methods as shown below, we will also use the singleton pattern that require a static method and private constructor, so we could get only one instance from the class every time we call it


```python
class CacheManager(object):

    _instance = None
    _cache_store = []

    def __init__(self):
        raise RuntimeError("Call get_instance() instead")

    @classmethod
    def get_instance(cls):
        if cls._instance is None:
            cls._instance = cls.__new__(cls)
        return cls._instance

    def set(self, key, value):
        self._cache_store.append((key, value))

    def get(self, key):
        value = None
        for item in self._cache_store:
            if key in item:
                value = item[1]
        return value
```

This is just a simple example of cache manager, where it has two methods for setting and getting cache values.

### Testing the Cache Manager
We could test the cache manager as follows:
```python
cache1 = CacheManager.get_instance()
cache1.set("name", "Mustafa")

cache2 = CacheManager.get_instance()
cache2.set("role", "backend engineer")

print(cache2.get("name")) # note we use cache2 to get the name
print(cache2.get("role"))
```

And here is the result:
```shell
Mustafa
backend engineer
```
As you can see, even creating multiple instances from the cache manager, but we still access only one instance.

### Conclusion
This cache manager could be useful in any Python application, where it caches data in memory and optimize the application performance.

### References
- [Head First Design Patterns: A Brain-Friendly Guide](https://www.amazon.com/Head-First-Design-Patterns-Brain-Friendly/dp/0596007124)
- [Singleton Pattern – Design Patterns (ep 6) by Christopher Okhravi](https://www.youtube.com/watch?v=hUE_j6q0LTQ)
- I used [draw.io](https://www.draw.io/) for drawing the UML diagram
