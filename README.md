This is an implementation of a c++ hash set / map using open addressing, linear
probing, and robin hood bucket stealing.  It is a zero-dependency copy of the
implementation I wrote for Starbound.

It has three main design goals, roughly in inverse order of importance:

- Be faster than std::unordered_map and std::unordered_set, focusing on forward
  iteration and find, including find with missing keys.  Everything else just
  needs to be not worse than std::unordered_map.
- Be as compatible with std::unordered_map and std::unordered_set as possible
- Don't be clever

It contains a MOSTLY compatible re-implementation of std::unordered_map and
std::unordered_set, with the main exceptions being pointer stability, and the
bucket API.  With the standard implementations, one may assume that the pointers
to keys and values in std::unordered_map and std::unordered_set will stay the
same as long as the value exists in the map / set.  That is very much NOT true
with these, due to the nature of array backed open address hash tables, they
will be changed on a rehash.

There has been very little micro-optimization done, these are mostly written to
just be simple.  Still, they are, at least for Starbound, much faster than
std::unordered_map and std::unordered_set (because it's not hard!).

## BAD PARTS ##

- **Probably don't use this for anything important or depend on this repo!** It
  may however be useful for educational purposes for how to implement hash sets
  / maps in C++, or as a starting point for your own.

- The included "tests" are are basically non-existent, and there are NO
  performance tests whatsoever.  The performance testing I DID do was mostly
  inside Starbound, where there was a measurable improvement.

- I spent very little effort thinking about extreme corner case behavior like
  throwing move constructors etc, so there are definitely limitations there.

- The bucket vector size is always a power of two, there is nothing clever
  going on like using prime number bucket counts or anything like that to get
  around a bad std::hash implementation.  It is possible that, especially with
  the default hash functions on integers in c++ that this may cause problems
  for certiain key sets.

- Even though the point was compatibility, there are some methods that would be
  very easy to implement that are missing simply because Starbound didn't use
  them.

## WHY DID YOU EVEN DO THIS ##

I'm sure there are better hash tables, in fact I tried a lot of them first.  I
had a specific hard requirement that the type be compatible with
std::unordered_map, including accepting the same template parameters, all the
same typedefs (const keys!), etc.

Starbound has a specific style decision to ignore the possibility that move
constructors or move assignment operators can possibly throw.  On inspection of
some alternatives that I tried seemed to be doing a lot of
std::is_nothrow_move_constructible dancing, and Starbound hit all the slow
paths.  Making types in Starbound be std::is_nothrow_move_constructible turned
out to be more painful than writing this hashtable.

I also noticed some very bad pathalogical behavior on several of the hashmaps I
tried, and I spent so long reading the code and working out why the pathalogical
behavior was happening that the easiest way to understand it became to make my
own hashmap.

I have very bad NIH syndrome sometimes, and an occasional inability to
understand something until I do it myself.

## License ##

This code is licensed under either of:

* MIT license [LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT
* Creative Commons CC0 1.0 Universal Public Domain Dedication
  [LICENSE-CC0](LICENSE-CC0) or
  https://creativecommons.org/publicdomain/zero/1.0/

at your option.
