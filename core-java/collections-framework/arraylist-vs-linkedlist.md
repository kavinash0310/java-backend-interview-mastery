# ArrayList vs LinkedList

## Interview-ready answer

ArrayList stores elements in a resizable array, so indexed reads are fast and appending is usually amortized constant time. LinkedList stores nodes, so inserting at a known node is cheap, but finding an index takes linear time. For most ordinary lists, ArrayList is the better default.

## Simple explanation

A LinkedList does not make arbitrary insertion fast: the program first has to walk to the position. It also uses more memory per element and has less cache-friendly traversal. LinkedList is useful when its deque operations fit the workload; ArrayDeque is often a better queue choice.

## Easy way to remember

ArrayList jumps to an index; LinkedList walks from link to link.

## Example

Use ArrayList for a list of API results that is read by index. Use a deque abstraction for queue behavior rather than choosing LinkedList just because insertion is mentioned.

## Common mistakes

Do not compare only Big-O labels; memory layout and access patterns matter. Do not assume LinkedList insertion at an arbitrary index is O(1).

## Interview follow-ups

What is amortized O(1)? Which collection would you choose for a FIFO queue?

## Quick revision

ArrayList for general list access; linked nodes only when their specific operations are useful.
