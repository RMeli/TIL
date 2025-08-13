# C++

## Libraries

List of random interesting C/C++ libraries:

* [cpptrace]: simple, portable, and self-contained stacktrace library for C++11 and newer
* [GSL]: guidelines support library
* [zdys]: fast and lightweight x86/x86-64 disassembler and code generation library

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
                // Remove oldest element from the cache
                cache_map.erase(cache.front().first); // (6)!
                cache.pop_front(); // (7)!
            }
            // Compute new element, add it to the cache, and return it
            const auto result = f(args...);
            cache.emplace_back(std::make_pair(args..., result)); // (8)!
            cache_map.emplace(args..., cache.end() - 1); // (9)!
            return result;
        }
        else{ // (10)!
            // Update cached element position in the cache and return it
            auto cached_it = cached_map_it->second; // (11)!
            cache.push_back(*cached_it); // (12)!
            cache.erase(cached_it); // (13)!
            cached_map_it->second = cache.end() - 1; // (14)!

            return cache.back().second;
        }
    };
}
```

1. `std::deque` stores a pair of arguments and the result of the function, and represents the cache.
2. `std::unordered_map` maps arguments to elements in the cache (`std::deque`) and provides $\mathcal{O}(1)$ access to the cache.
3. The lambda is `mutable` in order to modify the cache and the cache map.
4. The arguments are not found in the cache.
5. The cache is full.
6. Remove element from `cache_map` corresponding to the arguments of the oldest element in the cache (at the front of the queue).
7. Remove the oldest element from the cache.
8. Add pair of results and arguments to the cache.
9. Add the arguments and the iterator to the last element in the cache to `cache_map`.
   The `std::deque::end` iterator points after the last element in the `std::deque`.
10. The arguments are found in the cache.
11. `std::unordered_map::find` returns an iterator to the element in the map.
    An element of `std::unordered_map` is a `std::pair` with the key and the value.
    The value is an iterator to the element in the `std::deque`.
    `cached_map_it->second` is the iterator to the element in the `std::deque` (the cache).
12. Add the requested cahed element at the back of the queue, it is now the most recently used element.
13. Remove the cached element from the current position in the queue, since it is now in the back.
14. Update the iterator in the `std::unordered_map` to point to the new position of the cached element, which is at the back of the queue.
    `std::deque::end` points after the last element in the `std::deque`.

The `lru_cache` function returns a lambda that wraps the function `f` and implements the LRU cache.
It can be used as follows:

```cpp
auto cached_f = lru_cache(f, 100);
```

[^1]: Functional Programming in C++, Ivan Čukić, Manning Publications Co., ISBN 9781617293818.

[cpptrace]: https://github.com/jeremy-rifkin/cpptrace
[zdys]: https://zydis.re
[GSL]: https://github.com/microsoft/GSL?tab=readme-ov-file
