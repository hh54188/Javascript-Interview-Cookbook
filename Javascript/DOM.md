

## Node

### Overview

-  12 NodeTypes -- Internet Explorer 8 and earlier doesn’t expose the Nodetype constructor:

```
if (someNode.nodeType == Node.ELEMENT_NODE){ //won’t work in IE < 9
    alert(“Node is an element.”);
}
```

- For elements, nodeNameis always equal to the element’s tag name, and nodeValueis always null.

- Node Relationships

![Alt text](images/node-rel.jpg)

- insertBefore(): The insertBefore() method accepts two arguments: the **node to insert** and **a reference node**. The node to insert becomes the previous sibling of the reference node and is ultimately returned by the method. **If the reference node is null, then insertBefore() acts the same as appendChild()**.

### Document Type

- `var body = document.body; //get reference to <body>`: the documentobject also has a bodyproperty that points to the \<body\> element directly.

- `var html = document.documentElement;`: The 
first is the documentElementproperty, which always points to the <html>element in an HTML page.

- `document.domain`: the domainproperty is the only one that can be set.

>Pages from different subdomains can’t communicate with one another via 
JavaScript because of cross-domain security restrictions. By setting document.domainin each page 
to the same value, the pages can access JavaScript objects from each other.

- `document.getElementById`: Internet Explorer prior to version 8 considered IDs to be case-insensitive, so “myDiv”and “mydiv” are considered to be the same element ID. 

### The Element Type

- tag name: An element’s tag name is accessed via the nodeNameproperty or by using the tagName property;

When used with HTML, the tag name is always represented in all uppercase; 

```
if (element.tagName == “div”){ //AVOID! Error prone!
    //do something here
}
if (element.tagName.toLowerCase() == “div”){ //Preferred - works in all documents
    //do something here
}
```
- Attributes: Note that attribute names are case-insensitive, so “ID”and “id”are considered the same attribute. 
Also note that, according to HTML5, custom attributes should be prepended with data-in order to 
validate.

