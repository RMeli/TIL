# C++

## Least recently used cache (LRU cache)

Python provides a decorator to wrap a function with a memoizing callable that
saves up to the `maxsize` most recent calls:

```python
from functools import lru_cache

@lru_cache(maxsize=100)
def foo():
    pass
```

The *Functional Programming in C++*[^1] book provides an example of general unbounded function cache
(similar to Python's `@functools.cache`) implemented using `std::map` ($\mathcal{O}(\log n)$) 
insertion and retrieval with `operator[]`).

Since insertion and retrieval of elements (using `operator[]`) is $\mathcal{O}(\log n)$ for `std::map`,
the implementation in is not the most efficient. 
Additionally, `std::map` does not retain the order of insertion which is necessary to implement LRU cache.

An LRU cache can be implemented efficiently using two data structure: 

1. a `std::deque` (ordered, with $\mathcal{O}(1)$ insertion or removal of elements at the end or beginning), and
2. a `std::unordered_map` (average $\mathcal{O}(1)$ insertion and removal).

The `std::deque` keeps track of the elements added to cache,
and allows to easily and efficiently evict old elements when the cache is full and a new element needs to be stored.
`std::unordered_map` provides an efficient mapping between the function arguments (stored as keys)
and the position of the corresponding cached value (stored as values).

```cpp
#include <deque>
#include <tuple>
#include <utility>
#include <unordered_map>
#include <optional>

template <typename Result, typename... Args>
auto lru_cache(Result (*f)(Args...), std::optional<size_t> cache_size = std::nullopt) {
    using cache_t = std::deque<std::pair<Args..., Result>>; 
    cache_t cache; // (1)!

    std::unordered_map<Args..., typename cache_t::iterator> cache_map; // (2)!

    return [f, cache, cache_map, cache_size](Args... args) mutable -> Result { // (3)!
        auto cached_map_it = cache_map.find(args...);

        if(cached_map_it == cache_map.end()){ // (4)!
            if(cache_size && cache.size() == *cache_size){ // (5)! 
                // Remove oldest element, at the front of the queue
                // Remove first from unordered_map, using the argument as key
                // Then remove from the front of the queue
                cache_map.erase(cache.front().first);
                cache.pop_front();
            }
            // Compute element and add it to the cache
            const auto result = f(args...);
            cache.emplace_back(std::make_pair(args..., result));
            cache_map.emplace(args..., cache.end() - 1); // (6)!
            return result;
        }
        else{ // (7)!
            // Move cached element to the end of the queue
            // It is now the most recently used element
            auto cached_it = cached_map_it->second;
            cache.push_back(*cached_it);
            cache.erase(cached_it);
            cached_map_it->second = cache.end() - 1;

            return cache.back().second;
        }
    };
}
```

1. `std::deque` maps arguments to results
2. `std::unordered_map` maps arguments to elements in the cache and provides $\mathcal{O}(1)$ access to the cache
3. The lambda is `mutable` in order to modify the cache and the cache map.
4. The arguments are not found in the cache
5. The cache is full
6. End iterator points after the last element
7. The arguments are found in the cache

[^1]: Functional Programming in C++, Ivan Čukić, Manning Publications Co., ISBN 9781617293818.
