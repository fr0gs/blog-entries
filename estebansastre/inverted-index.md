# Writing a simple Inverted Index in Python


# Introduction

Nowadays is not uncommon that web applications include full text search features. There are already well known solutions working out-of-the-box that provide the needed functionalities, such as [ElasticSearch](https://www.elastic.co/ "ElasticSearch") or [Apache Solr](https://lucene.apache.org/solr/ "Solr"). 

Having used **ElasticSearch** at work a couple of times I wondered how it achieved fast searches and what mechanism empowered that, so reading up a little *Inverted Index* appears as the cornerstone of full text search algorithms.

Thus, what better way to understand how something works than writing my own toy *Inverted Index*?


# What is an Inverted Index?

The *Inverted Index* is the data structure used to support full text search over a set of documents. It is constituted by a big table where there is one entry per word that appear in all the documents processed, and for each different word, a list of all the documents that word appear, along with how frequently that word appears in the document. 


# How does it work?

Let's imagine we have two documents with different texts:


1. The big sharks of Belgium drink beer.
2. Belgium has great beer. They drink beer all the time.


In order to create the *Inverted Index*, each text is sliced into different units or **terms**. The rule is to use whitespace as the natural separator between words, although in most engines as **ElasticSearch** you can specify the string used as a separator. And then per each term there is a list of pairs (**document id**, **occurrences**), showing the document's ID where the term is found, and the number of times the term appears in the text. 

Therefore, the Inverted Index after processing previous two documents would be:



| Term    | Appearances (DocId, Occurrences) |
|---------|----------------------------------|
| The     | (1, 1)                           |
| big     | (1, 1)                           |
| sharks  | (1, 1)                           |
| of      | (1, 1)                           |
| Belgium | (1, 1) (2, 1)                    |
| drink   | (1, 1)                           |
| beer    | (1, 1) (2, 2)                    |
| has     | (1, 1)                           |
| great   | (1, 1)                           |
| They    | (1, 1)                           |
| all     | (1, 1)                           |
| the     | (1, 1)                           |
| time    | (1, 1)                           |



As seen, the term *Belgium* appears once in both documents, while the term *beer* appears once in the first and twice in the second document. Whenever a search is issued, the index will be looked up and the corresponding documents retrieved automatically. 

This in turn makes processing the documents (indexing) and thus creating & updating the index a slow process, since each document needs to be parsed, sliced and analyzed. Conversely, once the index is created search becomes an extremely cheap operation since it literally entails only looking up an entry in a table.


As it happens with everything, this mechanism is not a silver bullet and it has it's quirks and drawbacks, being some of them:

* **Case**: The words *Belgium* and *belgium* are indexed as two different terms in the table, while a user writing "belgium" in lowercase letters most likely would want to see all the occurrences regardless case.
* **


# Example

# References


* [Inverted Index in quora](https://www.quora.com/What-is-inverted-index-It-is-a-well-known-fact-that-you-need-to-build-indexes-to-implement-efficient-searches-What-is-the-difference-between-index-and-inverted-index-and-how-does-one-build-inverted-index)

* [Inverted Index for information Retrieval](http://www.dcs.bbk.ac.uk/~dell/teaching/cc/book/ditp/ditp_ch4.pdf)

* [Inverted Index Elasticsearch website](https://www.elastic.co/guide/en/elasticsearch/guide/current/inverted-index.html)

* [Inverted Index in StackOverflow](https://stackoverflow.com/questions/12511543/how-to-build-a-simple-inverted-index)

* [Inverted Index implementation in scala](https://github.com/felipehummel/TinySearchEngine/blob/master/scala/tinySearch.scala)

* [Building an Inverted Index with NTLK](https://nlpforhackers.io/building-a-simple-inverted-index-using-nltk/)
