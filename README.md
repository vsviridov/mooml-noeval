﻿Mooml-NoEval
===========

Mooml-NoEval is a fork of Mooml. Minor changes were introduced so that it doesn't use eval internally. Also, it solves some scoping issues.

Mooml is a port of Jaml. It let's you build html from Javascript using a very nice and clean templating system.
**Mooml creates the dom elements** in the template, returning a single element if the template has one root or an array of elements if there are multiple roots.

![Screenshot](http://github.com/eneko/mooml/raw/master/screenshot.jpg)

How to use
----------

Creating Mooml templates is very easy.
Make sure your class extends Moomlable, then do following:
	this.register('mytemplate', function(engine)
	{
		with(engine)
		div({id: 'mydivid'},
			h2('Title'),
			p('Lorem Ipsum'),
			p(a({ href: 'http://example.com' }, 'click here'))
		);
	});

This template will generate the following HTML structure, creating the dom elements for you:

	<div id="mydivid">
		<h2>Title</h2>
		<p>Lorem Ipsum</p>
		<p><a href="http://example.com">click here</a></p>
	</div>

To render the template and generate the dom elements:

	var el = this.render('mytemplate');

Passing variables to a template can be done easily too:

	this.register('mytemplate', function(engine,params) {
		with(engine)
		div({id: params.myDivId}
			// more code here
		);
	});
	var el = this.render('mytemplate', { myDivId: 'newid' });

Finally, we can render a template N times by passing an array as argument to the render method:

	var elements = this.render('mytemplate', [
		{ myDivId: 'div1' },
		{ myDivId: 'div2' },
		{ myDivId: 'div3' }
	]);

Will generate:

	<div id="div1">
	</div>
	<div id="div2">
	</div>
	<div id="div3">
	</div>


Evaluating templates on the fly
-----------------

With Mooml you can evaluate templates without having to register/save them for later reuse.

	Mooml.evaluate(function(engine) {
		with(engine)
		div(
			p(a({ href: 'http://example.com' }, 'click here'))
		);
	});

Will create the html elements:

	<div>
		<p><a href="http://example.com">click here</a></p>
	</div>


Globalizing Mooml
-----------------

Since version 1.0.9, Mooml includes a new feature: globalize. It maybe handy for some websites to globalize all the Mooml template functions (div, a, p, span...) to the window object scope, so they can be used anywhere in the code without the need of defining or evaluating a template.

For example, in normal Mootools code we would create a div like this:

	var mydiv = new Element('div', options); // options can have attributes, css, events and more

With Mooml.globalize we can do this:

	Mooml.globalize(); // Only need to call this once
	var mydiv = div(options); // Same options as Mootools new Element()

Mooml globalized functions can also have nested elements, which makes very easy to create dom elements:

	var mydiv = div(options, 
		p('First paragraph'),
		p('Second paragraph'),
		div('Nested div:',
			span('div content')
		),
		Mooml.render('nested_template'),
		'Some <b>inline</b> <em>html</em> too'
	);

Please be aware that using Mooml.globalize() feature will pollute the window object scope, overriding any methods with the same name and/or possibly conflicting with other javascript libraries.

Not want to globalize? Still can run Mooml inline
-----------------

With version 1.0.10, Mooml can be used directly inline to create dom elements:

	var mydiv = Mooml.engine.div({id:'mydiv'}, 'Inline div');

Better yet, you can build elements with children like this:

	var el;
	with (Mooml.engine) {
		el = div({id:'mydiv',
			p('one paragraph'),
			p('another paragraph'),
			input({name:'email', type:'text', defaultValue:'test@example.com'})
		);
	}


Differences between Mooml and Jaml
-----------------

Mooml is based in Jaml, but it has some differences:

* Mooml code takes advantage of Mootools using classes, elements, etc
* Mooml does not return text as Jaml. Instead, it creates the dom elements in the template
* Mooml alows passing css in json format and events, like Mootools Element.set function does
