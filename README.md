Mustache-Wax
============

**Disclaimer: I do not own this nor I deserve credit for this library. The only reason why I'm forking it and publishing it under a different name is because the version on NPM Registry is too outdated and I didn't want to copy the JavaScript artefact and paste it manually on my SCM. Once the original author decides to update the mustache-wax version on NPM registry, this should no longer be used**

Wax is an extension for Mustache.js, it enables the use of formatters inside of Mustache expressions in a similar fasion of Angular filters.

This extension requires that **String.prototype.trim()** method exists, you can grab a Polyfill from 
[MDN site](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/Trim):

Quick example
=============

First you need to define some filters:
```javascript
	Mustache.Formatters = {
		"uppercase": function (str) {
			return str.toUpperCase();
		},
		"lpad": function (str, num, sep) {
			sep = sep || " ";
			str = "" + str;
			var filler = "";
			while ((filler.length + str.length) < num) { filler += sep };
			return (filler + str).slice(-num);
		},
		"date": function (dt) {
			var lpad  = Mustache.Formatters.lpad, 
				day   = lpad(dt.getDate(), 2, "0"),
				month = lpad(dt.getMonth()+1, 2, "0");
			return  day + "/" + month + "/" + dt.getFullYear();
		}
	};
```

Then create a template and pass some data to render:
```javascript
	Mustache.render(
		"{{ name | uppercase }}, {{ dob | date }}, {{ ssnum | lpad : 10 : '0' }}", 
		{
			name: 	"john doe",
			dob: 	new Date(1981, 2, 24),
			ssnum:  12345
		}
	);
```

Result:
```
	JOHN DOE, 24/03/1981, 0000012345
```


A formatter is a function that modifies the value of an expression for display to the user. 
They are used in templates and it is easy to define your own formatters.

Using formatters in templates
=============================

Formatters can be applied to expressions in templates using the following syntax:
```
{{ expression | formatter }}
```

Formatters can be applied to the result of another formatter. This is called "chaining" and uses the following syntax:
```
{{ expression | formatter1 | formatter2 | ... }}
```

Formatters may have arguments. The syntax for this is
```
{{ expression | formatter : argument1 : argument2 : ... }}
```

Arguments can be either an integer, a real, a string or another expression.
Integer arguments example: 
* 123 
* -123 
* +123

Real arguments example:	
* 1.2
* 0.1
* .1
* -1.2
* -.1

String arguments example:
* "Hello World"
* 'Hello World'
* 'Hello "World"'
* "Hello 'World'"

Chaining formatters and passing arguments
=========================================
For example, assuming we have the following formatter and data:

```javascript
	Mustache.Formatters = {
		"add": function (one, two) {
			return one + two;
		}
	}

	var data = {
		ten:  	10,
		twenty: 20
	};
```

We could use the following templates
```
	{{ ten | add: 5}}
	{{ twenty | add: 5.25 | add:-.25 }}
	{{ ten | add : 3.14159 | add : twenty | add:-3}}
```

The result will be:
```
	15
	25
	30.14159
```
