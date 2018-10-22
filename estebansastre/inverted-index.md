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


In order to create the *Inverted Index*, each text is sliced into different units or **terms**. The rule is to use whitespace as the natural separator between words, although it can be changed. And then per each term there is a list of pairs (**document id**, **occurrences**), showing the document's ID where the term is found, and the number of times the term appears in the text. 

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

* **Case**: The words `Belgium` and `belgium` are indexed as two different terms in the table, while a user writing "belgium" in lowercase letters most likely would want to see all the occurrences regardless case.
* **Stopwords**: keywords considered irrelevant, like prepositions, articles or conjunctions.
* **Stemming**: Reducing plural words to their common root (e.g: `shark` and `sharks` to `shark`).
* **Synonyms**: Gathering terms that share a common meaning to a single one, in order to prevent an explosion in Inverted Indexe's size. Terms like `car`, `automobile`, `plane` and `truck` could be mapped into a single `vehicle` word in the index.



# Example

The *Inverted Index* can be understood as a simple key/value dictionary where per each term we store a list of appearances of those terms in the documents and their frequency.

Thus, an Appearance class represents a single *Appearance* of a term in a document:

```python
class Appearance:
    """
    Represents the appearance of a term in a given document, along with the
    frequency of appearances in the same one.
    """
    def __init__(self, docId, frequency):
        self.docId = docId
        self.frequency = frequency

        
    def __repr__(self):
        """
        String representation of the Appearance object
        """
        return str(self.__dict__)

```

The *Database* class is a fake in-memory DB used to persist the documents after they have been indexed.

```python
class Database:
    """
    In memory database representing the already indexed documents.
    """
    def __init__(self):
        self.db = dict()


    def __repr__(self):
        """
        String representation of the Database object
        """
        return str(self.__dict__)

    
    def get(self, id):
        return self.db.get(id, None)

    
    def add(self, document):
        """
        Adds a document to the DB.
        """
        return self.db.update({document['id']: document})


    def remove(self, document):
        """
        Removes document from DB.
        """
        return self.db.pop(document['id'], None)

```

And finally, the *InvertedIndex* class.

```python
class InvertedIndex:
    """
    Inverted Index class.
    """
    def __init__(self, db):
        self.index = dict()
        self.db = db


    def __repr__(self):
        """
        String representation of the Database object
        """
        return str(self.index)

        
    def index_document(self, document):
        """
        Process a given document, save it to the DB and update the index.
        """
        
        # Remove punctuation from the text.
        clean_text = re.sub(r'[^\w\s]','', document['text'])
        terms = clean_text.split(' ')
        appearances_dict = dict()

        # Dictionary with each term and the frequency it appears in the text.
        for term in terms:
            term_frequency = appearances_dict[term].frequency if term in appearances_dict else 0
            appearances_dict[term] = Appearance(document['id'], term_frequency + 1)
            
        # Update the inverted index
        update_dict = { key: [appearance]
                       if key not in self.index
                       else self.index[key] + [appearance]
                       for (key, appearance) in appearances_dict.items() }

        self.index.update(update_dict)

        # Add the document into the database
        self.db.add(document)

        return document

    
    def lookup_query(self, query):
        """
        Returns the dictionary of terms with their correspondent Appearances. 
        This is a very naive search since it will just split the terms and show
        the documents where they appear.
        """
        return { term: self.index[term] for term in query.split(' ') if term in self.index }

```


In order to test the execution of the index, I just create a couple documents and perform some searches.


```python
def highlight_term(id, term, text):
    replaced_text = text.replace(term, "\033[1;32;40m {term} \033[0;0m".format(term=term))
    return "--- document {id}: {replaced}".format(id=id, replaced=replaced_text)


def main():
    db = Database()
    index = InvertedIndex(db)

    document1 = {
        'id': '1',
        'text': 'The big sharks of Belgium drink beer.'
    }

    document2 = {
        'id': '2',
        'text': 'Belgium has great beer. They drink beer all the time.'
    }

    index.index_document(document1)
    index.index_document(document2)
    
    
    search_term = raw_input("Enter term(s) to search: ")
    result = index.lookup_query(search_term)

    
    for term in result.keys():
        for appearance in result[term]:
            # Belgium: { docId: 1, frequency: 1}
            document = db.get(appearance.docId)
            print(highlight_term(appearance.docId, term, document['text']))
        print("-----------------------------")    
    
main()

```


Doing a couple searches we can see the result:





# References


* [Inverted Index in quora](https://www.quora.com/What-is-inverted-index-It-is-a-well-known-fact-that-you-need-to-build-indexes-to-implement-efficient-searches-What-is-the-difference-between-index-and-inverted-index-and-how-does-one-build-inverted-index)

* [Inverted Index for information Retrieval](http://www.dcs.bbk.ac.uk/~dell/teaching/cc/book/ditp/ditp_ch4.pdf)

* [Inverted Index Elasticsearch website](https://www.elastic.co/guide/en/elasticsearch/guide/current/inverted-index.html)

* [Inverted Index in StackOverflow](https://stackoverflow.com/questions/12511543/how-to-build-a-simple-inverted-index)

* [Inverted Index implementation in scala](https://github.com/felipehummel/TinySearchEngine/blob/master/scala/tinySearch.scala)

* [Building an Inverted Index with NTLK](https://nlpforhackers.io/building-a-simple-inverted-index-using-nltk/)

* [Legacy Python Workshop Inverted Index](https://legacy.python.org/workshops/1996-11/papers/InvertedIndex.html)
