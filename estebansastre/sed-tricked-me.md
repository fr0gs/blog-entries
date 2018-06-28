# Introduction

Today I had some time free at work since I am between projects and I wait for some additional information,
and I took advantage of it to help a coworker that was new to **Ember.js**. For some reason all the calls
to the backend (a Virtuoso Database) failed.

Taking a look together, we discovered that the middleware that transformed JSON-API calls into SPARQL queries and vice-versa,
(the piece that was talking directly to the frontend) was consistently returning an error **500**, and this happened because
the triples that were introduced into the database using a script were generated wrong and had all the same *id* for every different model.

Let's say that you have a file with this structure:

```xml
<url1:concept1> <predicate1> <foo> ;
	<predicate2> <bar> ;
	<predicate3> <baz> .

<url1:concept2> <predicate1> <foo> ;
	<predicate2> <bar> ;
	<predicate3> <baz> .
```


And you want to detect each "foo" ocurrence and add a unique identifier, generated for example with the bash **uuidgen** utility, At the beginning this was the code:

```xml
blog λ cat example.txt | sed "s/foo/$(uuidgen)/g"
<url1:concept1> <predicate1> <66fa7661-889f-4ed5-b74d-540e18b9a83d> ;
        <predicate2> <bar> ;
        <predicate3> <baz> .

<url1:concept2> <predicate1> <66fa7661-889f-4ed5-b74d-540e18b9a83d> ;
        <predicate2> <bar> ;
        <predicate3> <baz> .

```

But then the *uuid* was generated only once, and substituted in all occurrences. We need to substitute "foo" happening by a different *uuid* each time! Ah but **sed** allows you to pass an external command per each match, so you could do this in theory:

```sh
blog λ cat a.txt
foo
foo
c
d

blog λ cat a.txt | sed "s/foo/echo $(uuidgen)/ge"
8b8cc7ac-b089-4339-875c-76a5278b594a
8b8cc7ac-b089-4339-875c-76a5278b594a
b
c
d
```

Damn!, **sed** still only evaluates the command call once and does the substitution for each occurrence!
But what if we manage to execute a command that depends on an external random source to generate the *uuid*? I found a little snippet [here](https://gist.github.com/earthgecko/3089509).

So we try adapting it to work with **sed**:

```sh
blog λ cat a.txt | sed "s^foo^cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w ${1:-32} | head -n 1^ge"
13YFcSxzshlFocig6AdA7yEbHeSKYq4r
6jhDEL3x3yDUsOf6mqScrea29YNDDURy
b
c
d

```

Nice, now each occurrence of "foo" is replaced by a random string. So let's try with the original file **example.txt**:


```sh
blog λ cat example.txt | sed "s^foo^cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w ${1:-32} | head -n 1^ge"
sh: 1: Syntax error: redirection unexpected

        <predicate2> <bar> ;
        <predicate3> <baz> .

sh: 1: Syntax error: redirection unexpected

        <predicate2> <bar> ;
        <predicate3> <baz> .
```

Argh, why the hell this happens?.. redirections are with the "<" character in the unix shell. Oh wait, could it be that **sed** is not only taking the exact match but the whole line? or the characters next to it? Let's verify it. Let's say that now this is **a.txt**:

```xml
blog λ cat a.txt
foo < /etc/passwd
foo
b
c
d

blog λ cat a.txt | sed "s/foo/cat/ge"
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
... etc ...
b
c
d
```

Yes, it is substituting "foo" by cat but receives also the "< /etc/passwd" and interprets not as text but as part of the commmand to execute inside **sed**.

## The Solution

The solution came using **awk**. This is the line that did the trick, it will add a specific triple with a new uuid for each **<url:concept>**.


```sh
blog λ cat example.txt | awk '1;/foo/{command="uuidgen";command | getline uuidgen;close(command); print "\t<http://our.namespace.url> \"" uuidgen "\" ;"}'
<url1:concept1> <predicate1> <foo> ;
        <http://our.namespace.url> "802a44bd-c28f-4856-b275-e24c666308c8" ;
        <predicate2> <bar> ;
        <predicate3> <baz> .

<url1:concept2> <predicate1> <foo> ;
        <http://our.namespace.url> "6b8cbac2-70c4-4769-b065-5aa36af797a4" ;
        <predicate2> <bar> ;
        <predicate3> <baz> .

```


Special thanks to my colleague [@wdullaer](https://wdullaer.com/) for asking me to help him out with Ember and we end up having fun with **sed** & **awk**.

Have fun!
