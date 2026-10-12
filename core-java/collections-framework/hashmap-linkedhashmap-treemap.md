# HashMap vs LinkedHashMap vs TreeMap

## Interview-ready answer

HashMap gives fast average lookup with no ordering guarantee. LinkedHashMap preserves insertion order by default, or access order when configured. TreeMap keeps keys sorted according to their natural order or a Comparator, with logarithmic operations.

## Simple explanation

Choose based on the order the caller needs, not on habit. If output order is irrelevant, HashMap is usually simplest. Use LinkedHashMap for stable iteration or an LRU-style structure. Use TreeMap for range queries or sorted traversal.

## Easy way to remember

HashMap is the fast unordered shelf; LinkedHashMap remembers a line; TreeMap sorts the labels.

## Example

A report that must print customers alphabetically can use TreeMap; a recently-used cache can use access-ordered LinkedHashMap.

## Common mistakes

HashMap and LinkedHashMap do not sort keys. TreeMap needs mutually comparable keys or a comparator, and changing a key in a way that affects comparison can break lookup.

## Interview follow-ups

What is the complexity of TreeMap operations? How can LinkedHashMap support a simple LRU policy?

## Quick revision

No order: HashMap. Insertion/access order: LinkedHashMap. Sorted keys: TreeMap.
