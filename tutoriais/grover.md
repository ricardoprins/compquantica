# Algoritmo de Grover
por [Ricardo Prins](https://github.com/ricardoprins)

A well-known paradigm in searching algorithms states that in order to find data that has been stored with a given identification in a set, for example, to find a specific value within a set of N records, the optimal speed can't be faster than O(n). This, obviously considering the worst-case scenario, and not a lucky _Maverick_ draw (if you can't get that reference, it means you aren't as old as I am).

There are many optimal ways to deal with the time issues involved in searching, such as using doubly linked lists or hashing instead of "brute force" searching approaches. However, with the advent of quantum computing, there are significant improvements that can be done to the searching process. In this brief article, it is not my intention to delve into the mystics of Quantum Mechanics, nor to provide a detailed explanation of anything related to the basics of Quantum Computing (I will even make the bizarre assumption that all my readers are fully versed in the metaphysical world of Quanta!). The sole goal here is to provide a simple explanation about how efficient can Quantum searching algorithms be - starting with the presentation of Grover's Algorithm.

Grover's Algorithm was presented in 1996 [(article link)](https://arxiv.org/abs/quant-ph/9605043), with a summary that, by itself, explains the impressiveness of its results:

> Imagine a phone directory containing N names arranged in completely random order. In order to find someone's phone number with a probability of 1/2, any classical algorithm (whether deterministic or probabilistic) will need to look at a minimum of names. Quantum mechanical systems can be in a superposition of states and simultaneously examine multiple names. By properly adjusting the phases of various operations, successful computations reinforce each other while others interfere randomly. As a result, the desired phone number can be obtained in only _O(sqrt(n))_ steps. The algorithm is within a small constant factor of the fastest possible quantum mechanical algorithm.

However, although impressive at first sight, it is still not possible to apply such efficient results to a _real_ database: Grover’s quantum algorithm searches for a subset of items in an unstructured set of N items.1 The algorithm incorporates the search criteria in the form of a blackbox predicate that can be evaluated on any items in the set. The complexity of this evaluation (query) varies depending on the search criteria. With conventional algorithms, searching an unstructured set of N items requires _omega(N)_ queries in the worst case. In the quantum domain,
however, Grover’s algorithm can perform unstructured search by making only _O(sqrt(n))_ queries, a quadratic speedup over the classical case. This improvement is contingent on the assumption that the search predicate can be evaluated on a superposition of all database items. Additionally, converting classical search criteria to quantum circuits often entails a moderate overhead, and the quantum predicate’s complexity can offset the reduction in the number of queries. [(more info)](https://web.eecs.umich.edu/~imarkov/pubs/jour/cise05-grov.pdf)

But let's skip the written nonsense and attempt a visual explanation of this weird contraption:
<p align="center">
<img src="https://github.com/TesseractCoding/NeoAlgo/blob/master/img/grover.png">
</p>

Well, if this is still unclear to you (and I'm not saying it is impossible that you can't understand it just from what has been said so far), the logical next step would be to attempt to explain it using a pseudocode structure.

## Pseudo-Code for Grover
Since GitHub doesn't allow me to use LaTeX here, I'll try to write this section to the best of my ability.

Let's say, for example, that we're trying to find the number 1 within an unstructured set S={1,2,3,...,n}, and that for that, we resort of a function that returns _true_ if the value is 1 or _false_ if otherwise:<br>

```
Oracle function:
f(x) is 1 if x = 1
or
f(x) is 0 if x = {0,2,3,...,n}
```

For a _regular_ computer, we'd need to _run_ f(n) _n_ times (as discussed above), and check the result of the _n_ executions. With Grover's and a quantum computer, we'd only need to execute five operations over the entire set of _n_ numbers.

Here's how it would work:

```
 Initialize state |0>
 for i = 1, I do 
    Call Oracle function
    Apply Hadamard
    Negate |x>, for all x != 0^n
    Apply Hadamard
```
