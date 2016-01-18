# The Anatomy of a Large-Scale Hypertextual Web Search Engine

# 1. Introdution
Human maintained list:
- Hard and expensive to update

Current Search Engine
- Quality of search result
- Easy to be mislead by advertiser
- Scalability: Google is designed to scale well to extremely large data sets

[Search Engine Watch](http://searchenginewatch.com/#)

## 1.1 Chanlledge
- crawling technology is needed to gather the web documents and keep them up to date
- Storage space must be used efficiently to store indices and, optionally, the documents themselves
- The indexing system must process hundreds of gigabytes of data efficiently
- Queries must be handled quickly, at a rate of hundreds to thousands per second.


## 1.2 Design Goals

One of the main causes of this problem is that the number of documents in the indices has been increasing by many orders of magnitude.
There is quite a bit of recent optimism that the use of more hypertextual information can help improve search and other applications.  In particular, link structure and link text provide a lot of information for making relevance judgments and quality filtering. Google makes use of both link structure and anchor text


# 2 Anatomy / Architecture
<img src="SearchEngineArchitecture.gif" width="400" height="400/>


##   __

## 2.1 Crawler
- There is a **URLserver** that sends lists of URLs to be fetched to the **crawlers**. 
- The web pages that are fetched are then sent to the **storeserver**. 
- The storeserver then compresses and stores the web pages into a **repository**. Every web page has an associated ID number called a docID which is assigned whenever a new URL is parsed out of a web page. 

## 2.2 Indexer
- The indexing function is performed by the **indexer** and the **sorter**. 
- The indexer performs a number of functions. 
    - It reads the repository, uncompresses the documents, and parses them. 
    - Each document is converted into a set of word occurrences called **hits**. The hits record the **word**, **position** in document, an approximation of **font size**, and **capitalization**. 
    - The indexer distributes these hits into a set of "barrels", creating a partially sorted **forward inde**x. 
    - The indexer performs another important function. It parses out all the links in every web page and stores important information about them in an **anchors** file. This file contains enough information to determine where each link points from and to, and the text of the link.

### 2.3 Parser
- The **URLresolver** reads the anchors file and converts relative URLs into absolute URLs and in turn into docIDs. 
- It puts the anchor text into the **forward index**. 
- It also generates a database of **links** which are pairs of docIDs. The links database is used to compute **PageRanks** for all the documents.

### 2.4 Sorter
- The sorter takes the barrels, which are sorted by docID. 
- resorts them by wordID to generate the inverted index. 
- This is done in place so that little temporary space is needed for this operation. 
- The sorter also produces a list of wordIDs and offsets into the inverted index. 

### 2.5 Search
- The searcher is run by a web server and uses the **lexicon** together with the **inverted index** and the **PageRanks** to answer queries.


# 3. Major Data Structures
Google's data structures are optimized so that a large document collection can be crawled, indexed, and searched with little IO cost. 
Although, CPUs and bulk input output rates have improved dramatically over the years, a disk seek still requires about 10 ms to complete. 
Google is designed to ** avoid disk seeks whenever possible**, and this has had a considerable influence on the design of the data structures.

## 3.1 Repository of Pages
The repository contains the compressed HTML of every web page. 
**The choice of compression technique is a tradeoff between speed and compression ratio.** We chose zlib's speed over a significant improvement in compression offered by bzip. The compression rate of bzip was approximately 4 to 1 as compared to zlib's 3 to 1 compression.
**The repository is self-completed, and requires no other data structures to be used in order to access it. This helps with data consistency and makes development much easier.**

| Data Structure|  | | | |
| :-- | :-- | :-- | :-- | :-- |
| DocID | URL_length | URL | page_length | compressed page |

## 3.2 Document Index

- DocId to Meta-Info
The document index keeps meta information about each document. 
It is a **fixed width ISAM (Index sequential access mode) index**, ordered by docID. Each entry includes the current document status, a pointer into the repository, a document checksum, and various statistics.
The variable part of meta information is also contains a pointer into a variable width file called docinfo, which contains its URL, title and other things.
**This design decision was driven by the desire to have a reasonably compact data structure, and the ability to fetch a record in one disk seek during a search.**

- URL to DocId
Additionally, there is a file which is used to convert URLs into docIDs. It is a list of **URL checksums** with their corresponding docIDs and is sorted by checksum. 
URLs may be converted into docIDs in batch mode by doing a merge with this file. This is the technique the URLresolver uses to turn URLs into docIDs. 
**This batch mode of update is crucial because otherwise we must perform one seek for every link which assuming one disk would take more than a month for our 322 million link dataset.
**

## 3.3 Lexicon 
The lexicon has several different forms. **One important optimization is that the lexicon can fit in memory for a reasonable price.** 
In the current implementation we can keep the lexicon in memory on a machine with 256 MB of main memory. The current lexicon contains 14 million words. 
It is implemented in two parts -- a list of the words (concatenated together but separated by nulls) and a hash table of pointers. For various functions, the list of words has some auxiliary information which is beyond the scope of this paper to explain fully.

## 3.4 Hit Lists

A hit list corresponds to a list of occurrences of a particular word in a particular document including **position**, **font**, and **capitalization** information.
Hit lists account for most of the space used in both the forward and the inverted indices. Because of this, it is important to represent them as efficiently as possible.
We considered several alternatives for encoding:
- **simple encoding** (a triple of integers), 
- **a compact encoding** (a hand optimized allocation of bits)
- **Huffman coding**. 
**In the end we chose a hand optimized compact encoding since it required far less space than the simple encoding and far less bit manipulation than Huffman coding**

Our compact encoding uses two bytes for every hit. There are two types of hits: fancy hits and plain hits. 
- Fancy hits include hits occurring in a URL, title, anchor text, or meta tag. 
- Plain hits include everything else. 

A plain hit consists of a **capitalization** (1 bit), **font size** (3 bits), and **12 bits of word position** in a document (all positions higher than 4095 are labeled 4096). Font size is represented relative to the rest of the document using three bits (only 7 values are actually used because 111 is the flag that signals a fancy hit). 
A fancy hit consists of a **capitalization** (1 bit(), the **font size** (3 bits) set to 7 to indicate it is a fancy hit, the type of **fancy hit** (4 bits), and **position** (8 bits). 
For anchor hits, the 8 bits of position are split into 4 bits for position in anchor and 4 bits for a hash of the docID the anchor occurs in. This gives us some limited phrase searching as long as there are not that many anchors for a particular word. We expect to update the way that anchor hits are stored to allow for greater resolution in the position and docID hash fields. 

We use font size relative to the rest of the document because when searching, you do not want to rank otherwise identical documents differently just because one of the documents is in a larger font.

## 3.5 Forward Index

The forward index is actually already partially sorted (**bucket sorted by word, not by docID**). It is stored in a number of barrels (we used 64). Each barrel holds a range of wordID's. 
If a document contains words that fall into a particular barrel, the docID is recorded into the barrel, followed by a list of wordID's with hitlists which correspond to those words. 

**This scheme requires slightly more storage because of duplicated docIDs but the difference is very small for a reasonable number of buckets and saves considerable time and coding complexity in the final indexing phase done by the sorter. **

**Furthermore, instead of storing actual wordID's, we store each wordID as a relative difference from the minimum wordID that falls into the barrel the wordID is in. This way, we can use just 24 bits for the wordID's in the unsorted barrels, leaving 8 bits for the hit list length.**

## 3.6 Inverted Index
The inverted index consists of the same barrels as the forward index, except that they have been processed by the sorter. 
For every valid wordID, the lexicon contains a pointer into the barrel that wordID falls into. It points to a doclist of docID's together with their corresponding hit lists. This doclist represents all the occurrences of that word in all documents.

An important issue is in what order the docID's should appear in the doclist? 
- One simple solution is to store them sorted by docID. This allows for **quick merging** of different doclists for **multiple word queries**. 
- Another option is to store them sorted by a ranking of the occurrence of the word in each document. This makes **answering one word queries** trivial and makes it likely that the answers to multiple word queries are near the start. However, merging is much more difficult. Also, this makes development much more difficult in that a change to the ranking function requires a rebuild of the index. 

We chose a compromise between these options, keeping two sets of inverted barrels -- one set for hit lists which include title or anchor hits and another set for all hit lists. This way, we check the first set of barrels first and if there are not enough matches within those barrels we check the larger ones.


# 4. Crawling
Running a web crawler is a challenging task. There are tricky **performance** and **reliability** issues and **social** issues. Crawling is the most fragile application since it involves interacting with hundreds of thousands of web servers and various name servers which are all **beyond the control** of the system.

Google has a fast distributed crawling system. A single **URLserver** serves lists of URLs to a number of **crawlers** (we typically ran about 3). Each crawler keeps **roughly 300 connections open at once**. This is necessary to retrieve web pages at a fast enough pace. At peak speeds, the system can crawl over **100 web pages per second** using four crawlers. This amounts to roughly **600K per second** of data. 
A major performance stress is DNS lookup. Each crawler maintains a its own **DNS cache** so it does not need to do a DNS lookup before crawling each document. 
Each of the hundreds of connections can be in a number of different states: 
- looking up DNS, 
- connecting to host, 
- sending request, 
- receiving response. 
These factors make the crawler a complex component of the system. It uses asynchronous IO to manage events, and a number of queues to move page fetches from state to state.



# 5. Indexing

## 5.1 Parsing
Any parser must handle a huge array of possible errors. These range from:
- typos in HTML tags to kilobytes of zeros in the middle of a tag
- non-ASCII characters
- HTML tags nested hundreds deep, 
- a great variety of other errors that challenge anyone's imagination to come up with equally creative ones.

For maximum speed, instead of using YACC to generate a CFG parser, we use **flex** to generate a lexical analyzer which we outfit with its own stack. 
Developing this parser which runs at a reasonable speed and is very robust involved a fair amount of work.

## 5.2 Indexing Documents into Barrels
Every word is converted into a **wordID** by using an in-memory hash table -- the lexicon. New additions to the lexicon hash table are logged to a file. 
Once the words are converted into wordID's, their occurrences in the current document are translated into **hit lists** and are written into the **forward barrels**. 

The main difficulty with **parallelization** of the indexing phase is that the lexicon needs to be shared. Instead of sharing the lexicon, we took the approach of writing a log of all the extra words that were not in a base lexicon, which we fixed at 14 million words. That way multiple indexers can run in parallel and then the small log file of extra words can be processed by one final indexer.

## 5.3 Sorting
In order to generate the inverted index, the sorter takes each of the forward barrels and sorts it by wordID to produce an inverted barrel for **title and anchor hits** and a **full text** inverted barrel. 

This process happens one barrel at a time, thus requiring little temporary storage. 
Also, we parallelize the sorting phase to use as many machines as we have simply by running multiple sorters, which can process different buckets at the same time. 

Since the barrels don't fit into main memory, the sorter further subdivides them into baskets which do fit into memory based on wordID and docID. Then the sorter, loads each basket into memory, sorts it and writes its contents into the short inverted barrel and the full inverted barrel.



# 6. Searching
Our solutions are scalable to commercial volumes with a bit more effort

1. **Parse** the query.
2. **Convert** words into wordIDs.
3. **Seek** to the start of the doclist in the **short barrel** for every word.
4. Scan through the doclists until there is a document that **matches all the search terms**.
5. Compute the **rank** of that document for the query.
6. If we are in the short barrels and at the end of any doclist, seek to the start of the doclist in the full barrel for every word and go to step 4.
7. If we are not at the end of any doclist go to step 4.
8. **Sort** the documents that have matched by rank and return the **top k**.

To put a limit on response time, once a certain number (currently 40,000) of matching documents are found, the searcher automatically stop. This means that it is possible that sub-optimal results would be returned. We are currently investigating other ways to solve this problem. In the past, we sorted the hits according to **PageRank**, which seemed to improve the situation.

# 6.1 Combining Ranking System
Every hitlist includes position, font, and capitalization information. Additionally, we factor in hits from anchor text and the PageRank of the document. 
Combining all of this information into a rank is difficult. We designed our ranking function so that no particular factor can have too much influence. 

# 6.1.1 The simplest case -- a single word query. 
Google considers each hit to be one of several different types (**title**, **anchor**, **URL**, **plain text large font**, **plain text small font**, ...), each of which has its own **type-weight**. 
The type-weights make up a vector indexed by type. Google counts the number of hits of each type in the hit list. Then every count is converted into a **count-weight**. Count-weights increase linearly with counts at first but quickly taper off so that more than a certain count will not help. 
We take the **dot product** of the vector of **count-weights** with the vector of **type-weights** to compute an **IR score** for the document. 
Finally, the IR score is combined with **PageRank** to give a final rank to the document.

# 6.1.2 The complexed case -- multi-word query. 
Now multiple hit lists must be scanned through at once so that hits occurring close together in a document are weighted higher than hits occurring far apart. 
The hits from the multiple hit lists are matched up so that nearby hits are matched together. For every matched set of hits, a **proximity** is computed. The proximity is based on how far apart the hits are in the document (or anchor) but is classified into 10 different value "bins" ranging from a **phrase match** to "**not even close**". 

**Counts** are computed not only for every type of hit but for every type and proximity. Every type and proximity pair has a **type-prox-weight**. The counts are converted into **count-weights**.
We take the dot product of the **count-weights** and the **type-prox-weights** to compute an **IR score**. 
All of these numbers and matrices can all be displayed with the search results using a special debug mode. These displays have been very helpful in developing the ranking system.


# 7 Results and Performance

## 7.1 Storage Requirements
Google is designed to scale cost effectively to the size of the Web as it grows. One aspect of this is to use storage efficiently. 
Table 1 has a breakdown of some statistics and storage requirements of Google. Due to compression the total size of the repository is about 53 GB, just over one third of the total data it stores. 
At current disk prices this makes the repository a relatively cheap source of useful data. 

More importantly, the total of all the data used by the search engine requires a comparable amount of storage, about 55 GB. 
Furthermore, most queries can be answered using just the short inverted index. With better encoding and compression of the Document Index, a high quality web search engine may fit onto a 7GB drive of a new PC.

| Web Page Statistics | - |
| :-- | :-- |
| Number of Web Pages Fetched | 24 million
| Number of Urls Seen | 76.5 million

| Storage Statistics | - |
| :-- | :-- |
| Total Size of Fetched Pages | 147.8 GB
| Compressed Repository | 53.5 GB
| Lexicon | 293 MB
| Temporary Anchor Data | 6.6 GB
| Document Index | 9.7 GB
| Links Database | 3.9 GB
| Short Inverted Index | 4.1 GB
| Full Inverted Index | 37.2 GB
| **Total Without Repository** | 55.2 GB
| **Total With Repository** | 108.7 GB

# 7.2 System Performance: Crawling, Indexing and Sorting

For Google, the major operations are Crawling, Indexing, and Sorting. 
- Crawller
It is difficult to measure how long crawling took overall because disks filled up, name servers crashed, or any number of other problems which stopped the system. 
In total it took roughly 9 days to download the 26 million pages. However, once the system was running smoothly, it ran much faster, downloading the last 11 million pages in just 63 hours, averaging just over **4 million pages per day** or **48.5 pages per second**. 
- Indexer
We ran the indexer and the crawler simultaneously. The indexer ran just faster than the crawlers. These optimizations included **bulk updates** to the document index and placement of critical data structures on the local disk. The indexer runs at roughly **54 pages per second**. 
- Sorter
The sorters can be run completely in parallel; using four machines, the whole process of sorting takes about 24 hours.

# 7.3 System Performance: Searching
The current version of Google answers most queries in between 1 and 10 seconds. 
This time is mostly dominated by disk IO over NFS (since disks are spread over a number of machines). 
Furthermore, Google does not have any optimizations such as **query caching**, subindices on common terms, and other common optimizations. 
We intend to speed up Google considerably through distribution and hardware, software, and algorithmic improvements. 
Our target is to be able to handle several hundred queries per second.


# 8 Conclusions
In implementing Google, we have seen bottlenecks in **CPU**, **memory access**, **memory capacity**,** disk seeks**, **disk throughput**, **disk capacity**, and **network IO**. 
Google has evolved to overcome a number of these bottlenecks during various operations. 
- Google's major data structures make efficient use of available storage space. 
- Furthermore, the crawling, indexing, and sorting operations are efficient enough to be able to build an index of a substantial portion of the web -- 24 million pages, in less than one week. 
We expect to be able to build an index of 100 million pages in less than a month.

# 9 Scalability
At 100 million web pages we will be very close up against all sorts of operating system limits in the common operating systems (currently we run on both Solaris and Linux). These include things like 
- addressable memory, 
- number of open file descriptors, 
- network sockets and bandwidth, 
- and many others. 
We believe expanding to a lot more than 100 million pages would greatly increase the complexity of our system.

# 10 Future Growth
As the capabilities of computers increase, it becomes possible to index a very large amount of text for a reasonable cost. Of course, other more bandwidth intensive media such as video is likely to become more pervasive. But, because the cost of production of text is low compared to media like video, text is likely to remain very pervasive. Also, it is likely that soon we will have speech recognition that does a reasonable job converting speech into text, expanding the amount of text available. All of this provides amazing possibilities for centralized indexing. 

Here is an illustrative example. 
- We assume we want to index everything everyone in the US has written for a year. We assume that there are 250 million people in the US and they write an average of 10k per day. That **works out to be** about 850 terabytes. 
- Also assume that indexing a terabyte can be done now for a reasonable cost. 
- We also assume that the indexing methods used over the text are linear, or nearly linear in their complexity. 

Given all these assumptions we can compute how long it would take before we could index our 850 terabytes for a reasonable cost assuming certain growth factors. 

Moore's Law was defined in 1965 as a doubling every 18 months in processor power. It has held remarkably true, not just for processors, but for other important system parameters such as disk as well. 
If we assume that Moore's law holds for the future, we need only 10 more doublings, or 15 years to reach our goal of indexing everything everyone in the US has written for a year for a price that a small company could afford. 
Of course, hardware experts are somewhat concerned Moore's Law may not continue to hold for the next 15 years, but there are certainly a lot of interesting centralized applications even if we only get part of the way to our hypothetical example.


# Sentence

- there have already been several papers using databases generated by Google, and **many others are underway**.
- "PageRank", an objective measure of its citation importance that corresponds well with people's subjective idea of importance
- The web is a vast collection of completely uncontrolled heterogeneous documents.


