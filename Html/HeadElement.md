# Head Element

## Content

- [\<link\>](#link)
- [\<script\>](#script)

## [\<link\>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link)

>The HTML \<link\> Element specifies **relationships** between the **current document** and **external resource**. Possible uses for this element include defining a relational framework for navigation.This Element is most used to link to style sheets.

### [(Important)Attributes](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link#Attributes)

- href: This attribute specifies the URL of the linked resource. A URL might be absolute or relative.

- **rel**: This attribute names a **relationship** of the **linked document** to the **current document**. The attribute must be a space-separated list of the [link types values](https://developer.mozilla.org/en-US/docs/Web/HTML/Link_types). *WebTV also supports the use of the value next for rel to preload the next page in a document series.*

- type: This attribute is used to define the type of the content linked to. The value of the attribute should be a **MIME type** such as text/html, text/css, and so on.

### [(Important)Link types](https://developer.mozilla.org/en-US/docs/Web/HTML/Link_types)

**注意，部分的Link types对\<a\>和\<area\>同样有效**

- external(HTML5): Indicates that the hyperlink leads to a resource outside the site of the current page, i.e. that following the link will make the user leaves the site.

- stylesheet: Defines an external resource to be used as a stylesheet. **If the type is not set, the browser should assume it is a text/css stylesheet until further inspection**.

## [\<script\>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script)

>The HTML \<script\> element is used to embed or reference an executable script within an HTML or XHTML document.

### [(Important)Attributes](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#Attributes)

- type: This attribute identifies the scripting language of code embedded within a script element or referenced via the element’s src attribute. This is specified as a **MIME type**; 

**defer**和**async**详见性能或者脚本载入部分。
