

# Introduction #

C++ B-tree containers have the same interface as the standard C++ containers, so it is relatively simple to replace `map` with `btree_map`, `set` with `btree_set`, `multimap` with `btree_multimap`, and `multiset` with `btree_multiset`. All the standard features (e.g., forward and reverse iterators) and operations (e.g., find, insert, add, lower\_bound, upper\_bound) are supported.

These containers take the same template parameters as the STL containers: the Key type, the Data type (for maps), a Compare function (optional), and an Allocator (optional). They take one additional template parameter, suggesting its node size. Based on experimental results for a variety of data types, the node size is set by default to 256 bytes, around the size of one or two L2 cache lines on most modern processors.  The node size is the 4th template parameter for sets and the 5th template parameter for maps.  All types are declared in the "btree" namespace.

```
namespace btree {
template <typename Key,
          typename Compare = std::less<Key>,
          typename Alloc = std::allocator<Key>,
          int NodeSize = 256>
class btree_set;

template <typename Key, 
          typename Value,
          typename Compare = std::less<Key>,
          typename Alloc = std::allocator<std::pair<const Key, Value> >,
          int NodeSize = 256>
class btree_map;
}
```

## Memory usage comparison ##

For trees containing more than just a few elements, and especially for those with small value types, the C++ B-tree containers promise to reduce memory usage significantly.   The following table shows the average number of bytes per value in the Red-Black set/map compared with the B-tree set/map for several types.  Numbers are given for both sorted and random insertion, because the B-tree treats sorted insertion as a special case, yielding a full tree.  Keep in mind that in the worst case, B-tree nodes will be 66% full, meaning average bytes per value can be up to 50% greater than the sorted insertion results shown here.

| **Type**                | **Insertion** | **B-Tree (32-bit)** | **Red-Black (32-bit)** | **B-Tree (64-bit)** | **Red-Black (64-bit)** |
|:------------------------|:--------------|:--------------------|:-----------------------|:--------------------|:-----------------------|
| `set<int32_t>`          | sorted      |   4.19  |  20.00  |   4.40  |  40.00  |
|                       | random      |   4.90  |  20.00  |   5.15  |  40.00  |
| `set<int64_t>`          | sorted      |   8.39  |  24.00  |   8.80  |  40.00  |
|                       | random      |   9.96  |  24.00  |  10.47  |  40.00  |
| `set<string>`         | sorted      |  24.57  |  40.00  |  33.60  |  64.00  |
|                       | random      |  29.49  |  40.00  |  40.74  |  64.00  |
| `map<int32_t, void*>`   | sorted      |   8.39  |  24.00  |   8.80  |  48.00  |
|                       | random      |   9.96  |  24.00  |  10.47  |  48.00  |
| `map<int64_t, void*>`   | sorted      |  12.60  |  28.00  |  13.20  |  48.00  |
|                       | random      |  15.16  |  28.00  |  15.92  |  48.00  |
| `map<string, void*>`  | sorted      |  28.67  |  44.00  |  38.16  |  72.00  |
|                       | random      |  34.49  |  44.00  |  46.53  |  72.00  |

## Performance comparison ##

The chart below shows the average time to insert randomly-ordered keys using `set<int32_t>`, `btree_set<int32_t>`, `map<int32_t, void*>`, and `btree_map<int32_t, void*>`, on a 64-bit platform, as a function of container size.


![http://chart.apis.google.com/chart?chxl=0%3A%7C10%7C100%7C1K%7C10K%7C100K%7C1M%7C10M&chd=s%3AaEDDFHP%2CdFDEEHN%2CbFDHLe6%2CfEEHLe2&chco=4488dd%2C8844dd%2Cdd4488%2Cdd8844&chxt=x&chls=1%2C1%2C0%7C1%2C1%2C0%7C1%2C1%2C0%7C1%2C1%2C0&chdl=B-tree%20map%7CB-tree%20set%7CSTL%20map%7CSTL%20set&cht=lc&chs=700x400&nonsense=image.png](http://chart.apis.google.com/chart?chxl=0%3A%7C10%7C100%7C1K%7C10K%7C100K%7C1M%7C10M&chd=s%3AaEDDFHP%2CdFDEEHN%2CbFDHLe6%2CfEEHLe2&chco=4488dd%2C8844dd%2Cdd4488%2Cdd8844&chxt=x&chls=1%2C1%2C0%7C1%2C1%2C0%7C1%2C1%2C0%7C1%2C1%2C0&chdl=B-tree%20map%7CB-tree%20set%7CSTL%20map%7CSTL%20set&cht=lc&chs=700x400&nonsense=image.png)


The next chart shows the average time to lookup randomly-ordered keys using the same containers, also as a function of container size.


![http://chart.apis.google.com/chart?chxl=0%3A%7C10%7C100%7C1K%7C10K%7C100K%7C1M%7C10M&chd=s%3ADDFGILU%2CDEFHIKR%2CDDFJOh6%2CDDFJOg5&chco=4488dd%2C8844dd%2Cdd4488%2Cdd8844&chxt=x&chls=1%2C1%2C0%7C1%2C1%2C0%7C1%2C1%2C0%7C1%2C1%2C0&chdl=B-tree%20map%7CB-tree%20set%7CSTL%20map%7CSTL%20set&cht=lc&chs=700x400&nonsense=image.png](http://chart.apis.google.com/chart?chxl=0%3A%7C10%7C100%7C1K%7C10K%7C100K%7C1M%7C10M&chd=s%3ADDFGILU%2CDEFHIKR%2CDDFJOh6%2CDDFJOg5&chco=4488dd%2C8844dd%2Cdd4488%2Cdd8844&chxt=x&chls=1%2C1%2C0%7C1%2C1%2C0%7C1%2C1%2C0%7C1%2C1%2C0&chdl=B-tree%20map%7CB-tree%20set%7CSTL%20map%7CSTL%20set&cht=lc&chs=700x400&nonsense=image.png)

## Limitations ##

As described below, there are a few limitations of the C++ B-tree.
  * Insertions into and deletions from B-tree containers **invalidate** existing iterators. See the discussion of "safe" maps and sets, below.
  * Iterator increment and decrement operations are slightly slower than their standard counterparts.
  * Due to a certain optimization (see the discussion of 3-way compare functions, below), this library depends on `<type_traits>` from the C++11 standard.
  * The C++11 `emplace` operations are not yet implemented (a TODO).

# Details #

## Example Use: btree\_map ##

The following example returns a new map of integer keys and pointers to `MyObject`. In the inner loop, it checks whether the key already exists, and if not inserts a new pair.

```
#include "btree_map.h"

typedef btree::btree_map<int, MyObject*> MyMap;

MyMap* BuildMap() {
   MyMap *obj_map = new MyMap;

  for (...) {
    int id = ...;
    MyMap::const_iterator lookup = obj_map->find(id);
    if (lookup != obj_map->end()) {
      // "id" already exists.
      continue;
    }
    MyObject *obj = ...;
    obj_map->insert(std::make_pair(id, obj));
  }
  return obj_map;
}
```

## Example Use: btree\_set ##

This example counts the number of duplicated integers in a series.

```
#include "btree_set.h"

typedef btree::btree_set<int> IntSet;

int CountDuplicates() {
  IntSet iset;
  int dups = 0;
  for (...) {
    int id = ...;
    IntSet::const_iterator lookup = iset.find(id);
    if (lookup != iset.end()) {
      dups++;
      continue;
    }
    iset.add(id);
  }
  return dups;
}
```

## Safe B-Tree maps and sets ##

If iterator invalidation would otherwise prevent you from using the B-tree, it may make sense to consider `safe_btree_map` or `safe_btree_set`. These containers keep a generation count, which is incremented every time there is an insertion or deletion. Iterators for these containers keep a copy of their current position's key and the last-valid generation count. When accessed, the iterator will reposition itself using its last-current key.

Safe versions of `btree_multiset` and `btree_multimap` are not provided.

In one common scenario, the only reason to keep an iterator across mutations to the container is to erase elements while performing a sequential scan.  For this limited use-case, the `erase()` method returns an iterator at the next position in the tree,

## Intra-node search ##

When searching a B-tree container, within each node in the tree there are several approaches taken based on the kind of key and the type of compare function provided.

For integer and floating point keys, we choose _linear search_ through the array of keys. Typically, this is faster than binary search due to CPU branch-prediction. For other types of key, we use binary search within each node, as you might expect.

Ordinarily, tree searches are performed using _less than_, a boolean function. Some data types (e.g., `std::string`) provide a natural 3-way compare function, which returns more information and can therefore reduce the number of comparisons needed. C++ B-tree containers support the use of 3-way compare functions by providing a Compare class that subclasses `btree_key_compare_to_tag`, for example:

```
struct MyStringComparer
    : public btree_key_compare_to_tag {
  int operator()(const string &a, const string &b) const {
    return a.compare(b);
  }
};
```

The user-provided compare function should return `< 0` if `a` is less than `b`, `0` if `a` is equal to `b`, and `> 0` if `a` is greater than `b`. C++ B-tree automatically configures the use of 3-way compare for std::string keys.

## Biased insertion and deletion ##

The insertion and deletion routines have special support to avoid unnecessary rebalancing of perfectly full trees. _Biased insertion_: special-case code ensures that when keys are inserted in ascending or descending order, the resulting tree will be completely full. _Biased deletion_: special-case code avoids rebalancing when removing the first or last element.