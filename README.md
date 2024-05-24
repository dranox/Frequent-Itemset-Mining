# Frequent-Itemset-Mining
# 1. Preliminaries
## This is how it all started ...
- Rakesh Agrawal, Tomasz Imielinski, Arun N. Swami: Mining Association Rules between Sets of Items in Large Databases. SIGMOD Conference 1993: 207-216
- Rakesh Agrawal, Ramakrishnan Srikant: Fast Algorithms for Mining Association Rules in Large Databases. VLDB 1994: 487-499

**These two papers are credited with the birth of Data Mining**
## Frequent itemset mining (FIM)

Find combinations of items (itemsets) that occur frequently.
## Applications
- Items = products, transactions = sets of products someone bought in one trip to the store.
$\Rightarrow$ items people frequently buy together.
    + Example: if people usually buy bread and coffee together, we run a sale of bread to attract people attention and raise price of coffee.
- Items = webpages, transactions = words. Unusual words appearing together in a large number of documents, e.g., “Brad” and “Angelina,” may indicate an interesting relationship.
- Transactions = Sentences, Items = Documents containing those sentences. Items that appear together too often could represent plagiarism.
## Transactional Database
A transactional database $D$ consists of $N$ transactions: $D = \{ T_1, T_2, \ldots, T_N \}$. A transaction $T_n \in D (1 \le n \le N)$ contains one or more items and that $I= \{ i_1,i_2,…,i_M \}$ is the set of distinct items in $D$, $T_n \subset I$. Commonly, a transactional database is represented by a flat file instead of a database system: items are non-negative integers, each row represents a transaction, items in a transaction separated by space.

Example: 

1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 

30 31 32 

33 34 35 

36 37 38 39 40 41 42 43 44 45 46 

38 39 47 48 

38 39 48 49 50 51 52 53 54 55 56 57 58 

32 41 59 60 61 62 

3 39 48 

63 64 65 66 67 68 



# Definition

- Itemset: A collection of one or more items.
    + Example: {1 4 5}
- **k-itemset**: An itemset that contains k items.
- Support: Frequency of occurrence of an itemset.
    + Example: From the example above, item 3 appear in 2 transactions so its support is 2.
- Frequent itemset: An itemset whose support is greater than or equal to a `minsup` threshold
# 2. Implementation
## The Apriori algorithm
Suppose:

$C_k$ candidate itemsets of size k.

$L_k$ frequent itemsets of size k.

The level-wise approach of Apriori algorithm can be descibed as follow:
1. k=1, $C_k$ = all items.
2. While $C_k$ not empty:
    3. Scan the database to find which itemsets in $C_k$ are frequent and put them into $L_k$.
    4. Use $L_k$ to generate a collection of candidate itemsets $C_{k+1}$ of size k+1.
    5. k=k+1.
## FP Growth
### Steps of the FP-Growth Algorithm

1. **Build the FP-Tree**:
    - Scan the database to determine the frequency of each item.
    - Discard infrequent items (those below the support threshold).
    - Sort the frequent items in descending order of their frequency.
    - Create the root of the FP-tree, labeled with “null”.
    - For each transaction in the database, add it to the FP-tree. Use the order of frequent items to ensure common prefixes are shared.

2. **Mine the FP-Tree**:
    - Starting from each frequent item, construct its conditional pattern base, a sub-database containing the set of prefix paths in the FP-tree co-occurring with the item.
    - Construct the conditional FP-tree from the conditional pattern base.
    - Recursively mine the conditional FP-tree to find frequent patterns. 

### Example of the FP-Growth Algorithm

1. **Building the FP-Tree**:
    - Given a database of transactions, e.g., 

        ```
        T1: {A, B, D}
        T2: {B, C, E}
        T3: {A, B, C, E}
        T4: {A, D, E}
        T5: {A, B, C, E}
        ```

    - Scan the database to find the frequency of each item:

        ```
        A: 4, B: 3, C: 3, D: 2, E: 3
        ```

    - Assume the support threshold is 2.
    - Sort items by frequency and construct the FP-tree. The tree will look like this (showing shared prefixes):

        ```
        null
        ├── A: 4
        │   ├── B: 2
        │   │   ├── D: 1
        │   │   └── C: 1
        │   │       └── E: 1
        │   ├── D: 1
        │   └── C: 1
        │       └── E: 1
        └── B: 1
            └── C: 1
                └── E: 1
        ```

2. **Mining the FP-Tree**:
    - Start from the least frequent item and construct its conditional pattern base.
    - For example, starting with `E`:

        - The conditional pattern base for `E`:
            ```
            {A, C}: 2
            {B, C}: 1
            {A}: 1
            ```

        - Construct the conditional FP-tree for `E`:

            ```
            null
            ├── A: 3
            └── C: 3
                └── A: 2
            ```

    - Recursively mine this tree to find frequent patterns involving `E`.
