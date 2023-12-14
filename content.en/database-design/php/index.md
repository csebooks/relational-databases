---
title: PHP
weight: 31
---

PHP## PHP

PHP is a scripting language that is widely used for server-side scripting. PHP code can be intermixed with HTML in a manner similar to JSP. The characters “_<_?php” indicate the start of PHP code, while the characters “?_\>_” indicate the end of PHP code. The following code performs the same actions as the JSP code in Figure 9.9.
```
<html>
<head> <title> Hello </title> </head>
<body>
<?php if (!isset($ REQUEST[’name’]))
{ echo ’Hello World’; }
else { echo ’Hello, ’ . $ REQUEST[’name’]; }
?>
</body>
</html>
```

The array $ REQUEST contains the request parameters. Note that the array is indexed by the parameter name; in PHP arrays can be indexed by arbitrary strings, not just numbers. The function isset checks if the element of the array has been initialized. The echo function prints its argument to the output HTML. The operator “.” between two strings concatenates the strings.

A suitably configured Web server would interpret any file whose name ends in “.php” to be a PHP file. If the file is requested, the Web server process it in a manner similar to how JSP files are processed, and returns the generated HTML to the browser.

A number of libraries are available for the PHP language, including libraries for database access using ODBC (similar to JDBC in Java).

the code is replaced in the HTML output by the text it prints to the object out. In the JSP code in the above figure, if no value was entered for the form parameter name, the script prints “Hello World”; if a value was entered, the script prints “Hello” followed by the name.

A more realistic example may perform more complex actions, such as looking up values from a database using JDBC.

JSP also supports the concept of a _tag library_, which allows the use of tags that look much like HTML tags, but are interpreted at the server, and are replaced by appropriately generated HTML code. JSP provides a standard set of tags that de- fine variables and control flow (iterators, if-then-else), along with an expression language based on JavaScript (but interpreted at the server). The set of tags is extensible, and a number of tag libraries have been implemented. For example, there is a tag library that supports paginated display of large data sets, and a li- brary that simplifies display and parsing of dates and times. See the bibliographic notes for references to more information on JSP tag libraries.  

### 9.3.6 Client-Side Scripting

Embedding of program code in documents allows Web pages to be **active**, car- rying out activities such as animation by executing programs at the local site, instead of just presenting passive text and graphics. The primary use of such pro- grams is flexible interaction with the user, beyond the limited interaction power provided by HTML and HTML forms. Further, executing programs at the client site speeds up interaction greatly, compared to every interaction being sent to a server site for processing.

A danger in supporting such programs is that, if the design of the system is done carelessly, program code embedded in a Web page (or equivalently, in an email message) can perform malicious actions on the user’s computer. The malicious actions could range from reading private information, to deleting or modifying information on the computer, up to taking control of the computer and propagating the code to other computers (through email, for example). A number of email viruses have spread widely in recent years in this way.

One of the reasons that the Java language became very popular is that it provides a safe mode for executing programs on users’ computers. Java code can be compiled into platform-independent “byte-code” that can be executed on any browser that supports Java. Unlike local programs, Java programs (applets) downloaded as part of a Web page have no authority to perform any actions that could be destructive. They are permitted to display data on the screen, or to make a network connection to the server from which the Web page was downloaded, in order to fetch more information. However, they are not permitted to access local files, to execute any system programs, or to make network connections to any other computers.

While Java is a full-fledged programming language, there are simpler lan- guages, called **scripting languages**, that can enrich user interaction, while pro- viding the same protection as Java. These languages provide constructs that can be embedded with an HTML document. **Client-side scripting languages** are lan- guages designed to be executed on the client’s Web browser.

Of these, the _JavaScript_ language is by far the most widely used. The current generation of Web interfaces uses the JavaScript scripting language extensively to construct sophisticated user interfaces. JavaScript is used for a variety of tasks. For example, functions written in JavaScript can be used to perform error checks (validation) on user input, such as a date string being properly formatted, or a value entered (such as age) being in an appropriate range. These checks are carried out on the browser as data is entered, even before the data are sent to the Web server.

Figure 9.10 shows an example of a JavaScript function used to validate a form input. The function is declared in the head section of the HTML document. The function checks that the credits entered for a course is a number greater than 0, and less than 16. The form tag specifies that the validation function is to be invoked when the form is submitted. If the validation fails, an alert box is shown to the user, and if it succeeds, the form is submitted to the server.

JavaScript can be used to modify dynamically the HTML code being displayed. The browser parses HTML code into an in-memory tree structure defined by 
```
<html>
<head>
<script type="text/javascript">
function validate() {
var credits=document.getElementById("credits").value;
if (isNaN(credits)|| credits<=0 || credits>=16) {
alert("Credits must be a number greater than 0 and less than 16");
return false
}
}
</script>
</head>
<body>
<form action="createCourse" onsubmit="return validate()">
Title: <input type="text" id="title" size="20"><br />
Credits: <input type="text" id="credits" size="2"><br />
<input type="submit" value="Submit">
</form>
</body>
</html>

```

**Figure 9.10** Example of JavaScript used to validate form input

a standard called the **Document Object Model** (**DOM**). JavaScript code can modify the tree structure to carry out certain operations. For example, suppose a user needs to enter a number of rows of data, for example multiple items in a single bill. A table containing text boxes and other form input methods can be used to gather user input. The table may have a default size, but if more rows are needed, the user may click on a button labeled (for example) “Add Item.” This button can be set up to invoke a JavaScript function that modifies the DOM tree by adding an extra row in the table.

Although the JavaScript language has been standardized, there are differ- ences between browsers, particularly in the details of the DOM model. As a result, JavaScript code that works on one browser may not work on another. To avoid such problems, it is best to use a JavaScript library, such as Yahoo’s YUI library, which allows code to be written in a browser independent way. Internally, the functions in the library can find out which browser is in use, and send appropri- ately generated JavaScript to the browser. See the Tools section at the end of the chapter for more information on YUI and other libraries.

Today, JavaScript is widely used to create dynamic Web pages, using several technologies that are collectively called **Ajax**. Programs written in JavaScript communicate with the Web server asynchronously (that is, in the background, without blocking user interaction with the Web browser), and can fetch data and display it.  

As an example of the use of Ajax, consider a Web site with a form that allows you to select a country, and once a country has been selected, you are allowed to select a state from a list of states in that country. Until the country is selected, the drop-down list of states is empty. The Ajax framework allows the list of states to be downloaded from the Web site in the background when the country is selected, and as soon as the list has been fetched, it is added to the drop-down list, which allows you to select the state.

There are also special-purpose scripting languages for specialized tasks such as animation (for example, Flash and Shockwave) and three-dimensional model- ing (Virtual Reality Markup Language (VRML)). Flash is very widely used today not only for animation, but also for handling streaming video content.

