# Array of extended objects in python using list comprehensions and lambda functions.

## Problem

It's been a while since I don't write any posts, so I thought that even though the idea might be initially quite silly, it will help me to kickoff again the habit by writing about a small problem I encountered the other day.

I was developing a very simple microservice that would receive a *GET* request with two parameters, issue a *SPARQL* query to a **Virtuoso** store and then, transform the returned array of objects by extending each object with the same additional meta information per object. Say:

```python
res = [{ 'title': 'Oh boy' }, { 'title': 'Oh girl'}]
```

And then add some additional metadata like `{ 'meta': { 'author': 'Myself'}}`

Ending up with

```python
res = [ {
        'title': 'Oh boy',
        'meta':  {
          'author': 'Myself'
          }
        },
        {
          'title': 'Oh girl',
          'meta': {
            'author': 'Myself'
          }
        }]
```

## Solution

I wanted to do something self contained and as functional as possible, by using list comprehensions for example. Unfortunately, there is no method in python to update a dictionary and return the new dictionary updated. The regular way is like:

```python
a = { 'b': 3 }
a.update({'c': 5}) # Does not return anything
print(a) # {'c': 5, 'b': 3}
```


Ultimately I came up with a small solution:

```python
result = [(lambda x, y=z.copy(): (y.update(x), y))({ 'meta': { 'author': 'Myself' } })[1] for z in res]
```

Tada! Combining list comprehensions, lambda functions and the built-in dictionary **copy()** function we can return a new array with a copy of each object already extended.

By using a lambda function that accepts a tuple we can specify that the first argument is passed as a parameter and the second one will be a copy of each element in the array (assuming it is an object). Then, the object is extended with the argument and the newly extended object is returned as the second element of the tuple.

We could even bake this into a function:

```python
def map_extend(array=[], ext={}):
  return [(lambda x, y=z.copy(): (y.update(x), y))(ext)[1] for z in array]
```


```bash
>>> res
[{'title': 'Oh boy'}, {'title': 'Oh girl'}]
>>> ext = { 'meta': {'author': 'Hola'}}                                                     
>>> map_extend(res, ext)
[{'meta': {'author': 'Hola'}, 'title': 'Oh boy'}, {'meta': {'author': 'Hola'}, 'title': 'Oh girl'}]
>>> map_extend(res, {})                                                                     
[{'title': 'Oh boy'}, {'title': 'Oh girl'}]
>>> map_extend([], {})                                                                      
[]
```


Have fun!
