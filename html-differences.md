# Understanding HTML terminology

# Introduction

I know, I know. "But Esteban, this article would have been useful 20 years ago, now it is a little outdated to say the least". I cannot disagree with that, but this kind of posts serve more as a reminder to me, and also perhaps to satisfy your curiosity. I don't have a comments section, but feel free to reach me out over twitter, email or linkedin.  

# History

When *The Web* was born, it did it as a system of internet servers that would allow to access documents via a *Web Browser*. From those documents you could access others via links, as well as other formats like graphics, or video, or audio, conforming a big network of interconnected documents called the **web**.

*Web Browsers* (at the time) were simple programs running on users' machines that would fetch the document from a web server, read it, and show it in user's screen.

**HTML** was born as a declarative way to structure those documents to tell the *web browsers* how they should paint and show the documents. HTML stands for HyperText Markup Language. Hypertext describes the ability to link to other documents from the current one, and markup defines the structure of a web page or a web application. An example document would be:

```html
<html>
  <head>
    <title>My Website</title>
  </head>
  <body>
    <section>
      <p>My paragraph</p>
      <p>My other paragraph</p>
    </section>
  </body>
</html>
```

So the web browser would fetch that document from some server somewhere, and start interpreting it's content: "Oh hey, this is an HTML document! and.. oh yes, it has a title of **My Title**, so I will write that into the tab title, and also I see you have a document body, with a paragraph inside a section!, so I will paint that paragraph. But then I have another paragraph, so this means they must be separated by an empty line, since they are different blocks."

One important characteristic about HTML is that it is not strictly parsed. It means that in the event of receiving wrong code, for instance an unclosed tag, the web browser won't fail to load and show the page but will do the best it can to correct the mistake and paint the document.

So the author of the document wrote that "code" seen before, and the user who visited the web page of the author would see this:

  ![simplesite](simplesite.png)

It really is as simple as that. Without taking into account CSS to style those documents, giving them colours, shapes, you name it, and javascript to interact with them, websites were just are text documents written in a concrete way.


# SGML

The Standard Generalized Markup Language came before HTML. One could say that HTML was derived from SGML although they were developed more or less in parallel. HTML would focus more on how the data reflected in the document looks. SGML is more generic, it is a (meta)language to define other markup languages, while in HTML you have a limited set of tags that define the structure of the document.

With SGML, you would need to specify:

  * The SGML declaration, enumerating the characters and delimiters that may appear in the application. You can find the charset declaration [for HTML 4.0 here.](https://www.w3.org/TR/WD-html40-970708/sgml/sgmldecl.html)
  * The **Document Type Definition**, defining the syntax of the markup constructs, for example:

```
<!DOCTYPE tvguide [
<!ELEMENT tvguide - - (date,channel+)>
<!ELEMENT date - - (#PCDATA)>
<!ELEMENT channel - - (channel_name,format?,program*)>
<!ATTLIST channel teletext (yes|no) "no">
<!ELEMENT format - - (#PCDATA)>
<!ELEMENT program - - (name,start_time,(end_time|duration))>
<!ATTLIST program
     min_age CDATA #REQUIRED
     lang CDATA "es">
<!ELEMENT name - - (#PCDATA)>
<!ELEMENT start_time - - (#PCDATA)>
<!ELEMENT end_time - - (#PCDATA)>
<!ELEMENT duration - - (#PCDATA)>
]>
```
  * A specification describing the semantics of the markup.
  * Document instances containing data and markup.

# XML

XML was based on SGML and was designed to describe a set of rules that encode data in both a human readable and machine-readable formats. It was thought to focus primarily on what the data is, rather on how it is represented.

That is why it is used often as the exchange data format accross services over the internet. An example of XML would be: (examples taken from [https://www.w3schools.com](https://www.w3schools.com))

```xml
<?xml version="1.0" encoding="UTF-8"?>
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>
<note>
  <to>John</to>
  <from>Smith</from>
  <heading>Reminder</heading>
  <body>Who cares right?</body>
</note>
```

The first line specifies the version and encoding. The rest of the document represents two notes, with information associated to them. A service can receive this xml document, parse it and do something with the data.

XML documents also can have a **DTD** just as the SGML documents, formalizing the structure of the document and describing a common ground to exchange the data for multiple users. You can add a DTD adding this line to the document: `<!DOCTYPE note SYSTEM "Note.dtd">`

**Note.dtd**:
```xml
<!DOCTYPE note
[
<!ELEMENT note (to,from,heading,body)>
<!ELEMENT to (#PCDATA)>
<!ELEMENT from (#PCDATA)>
<!ELEMENT heading (#PCDATA)>
<!ELEMENT body (#PCDATA)>
]>
```

XML documents have other features like **XML Schema**, **XML namespaces** or **XPath** to name a few, but they are specific mechanisms for the xml documents. More information on [w3schools](https://www.w3schools.com/xml/)


# XHTML

 XHTML is simply HTML but expressed as valid XML. It has the same functionality, but is compliant with the most strict representations of the XML standard. This means that rules that were overlooked for HTML if they when not followed, must adhere to the strict set of rules of XML. For example:

  * In HTML you can write `<br>`, in XHTML it must be `<br></br> or <br/> or <br />`.
  * In HTML you can write `<em><strong>Texto</em></strong>`, in XHTML it has to be `<em><strong>Texto</strong></em>`, following the correct opening/closing order.

# DHTML

This term was introduced by **Microsoft** when Internet Explorer 4 came out and has no clear meaning. DHTML (Dynamic HTML) encompasses the set of technologies that allow to create interactive and animated web sites. This means that a site made with **HTML**, styled with **CSS** and additional interactivity accomplished using **Javascript** would fall into the *DHTML* category.
