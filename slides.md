# jQuery 202 & Web components

---

## Our Presenters

## David Luecke

* GitHub: [daffl.github.com](http://daffl.github.com), Twitter: [@daffl](http://twitter.com/daffl)

## Eric Kryski

* GitHub: [ekryski.github.com](http://ekryski.github.com), Twitter: [@ekryski](http://twitter.com/ekryski)

---

## Our Sponsors

## Assembly Co-working Space

![Assembly](images/sponsors/assembly_logo.png)

## PetroFeed

![PetroFeed](images/sponsors/pf-logo.png)

---

## Our Sponsors

## Village Brewery

![Village Brewery](images/sponsors/village_brewery_logo_inverted.png)

---

# jQuery 202

---

## What are we going to do?

- Un-spaghetti and modularize your code
- Making your code faster
- Advanced selectors and DOM traversal
- Writing your own plugins

---

## Scope in JavaScript

__JavaScript only knows function scope__

	!javascript
	for(var i = 0; i < 10; i++) {
		var x = 'testing';

		(function(arg) {
			var y = 42;
		})(i);
	}

__Global variables__

	!javascript
	function test() {
		var local = 42;
		global = 'global';
	}

	test();

---

## Namespacing

	!javascript
	var App = {
		init : function() {
			/* ... */
		},
		Dummy : {
			sayHi : function(name) {
				return 'Hi ' + name;
			}
		}
	}

	App.Blog = {
        getPosts : function() { /* ... */ }
    }

	console.log(App.Dummy.sayHi('David'));

---

## Modules

	!javascript
	var APP = (function() {
		// Do stuff
		var privateVariable = 'Hello ',
			sayHi = function(name) {
				return privateVariable + name;
			};
		// Return API
		return {
			init : function() { /* ... */ },
			hi : sayHi
		}
	})();

	console.log(APP.sayHi('David'));

---

## __A__synchronous __M__odule __D__efinition

__AMD__: [CommonJS](http://www.commonjs.org/) specification for
[asynchronously loading dependencies](https://github.com/amdjs/amdjs-api/wiki/AMD).

	!javascript
	// say_hi.js
	define(function() {
		var privateVariable = 'Hello ';
		return {
			sayHi : function(name) {
                return privateVariable + name;
            }
		}
	});

	// module.js
	define(['say_hi.js'], function(hisayer) {
		return {
			result : hisayer.sayHi('David'),
			sayHi : hisayer
		}
	});

	// app.js
	var module = require('module.js', function(module) {
		module.sayHi('You'); // Hello You
		module.result; // -> Hello David
	});

---

## jQuery performance

With an empty list

    !html
    <ul></ul>

We want to create 2000 list elements using jQuery:

    !javascript
    for(var i = 0; i < 2000; i++) {
        $('ul').append('<li>Entry ' + i + '</li>');
    }

Storing the selector:

    !javascript
    var ul = $('ul');
    for(var i = 0; i < 2000; i++) {
        ul.append('<li>Entry ' + i + '</li>');
    }

---

## Even faster...

DOM manipulation is expensive. Avoid it whenever possible.

Create a string with 2000 list elements, then append it:

    !javascript
    var html = '';

    for(var i = 0; i < 2000; i++) {
        html += '<li>Entry ' + i + '</li>';
    }

    $('ul').append(html);

The best:

Separate HTML from your JavaScript and use a client side templating language like [\_.template](http://underscorejs.org/#template), [Mustache](http://mustache.github.io/), [Handlebars](http://handlebarsjs.com/), [EJS](https://github.com/visionmedia/ejs) or [Jade](http://jade-lang.com/).

---

## Advanced [selectors](http://api.jquery.com/category/selectors/)

- Attribute `[attributeName]`
    - Attribute contains `[attributeName*="something"]`
    - Attribute equals `[attributeName="something"]`
- Parent child `parent > child`
- Next adjacent `prev + next`
- Contains text `:contains("text")`
- First and last `:first`, `:last`
- Not selector `:not(otherSelector)`

---

## A jQuery plugin

It's as simple as:

    !javascript
    // myplugin.js
    (function($, undefined) {
       $.fn.myButton = function() {
        this.each(function(button) {
          var text = button.text();
          button.click(function() {
            alert(text);
          });
        });

        return this;
       }
    })(jQuery);

And can be used like this:

    !javascript
    $('button').myButton();

---

## Accordion

Building your own jQuery plugin to turn [Bootstrap panels with heading](http://getbootstrap.com/components/#panels-heading) into an accordion:

    !javascript
    // myplugin.js
    (function($, undefined) {
       $.fn.accordion = function() {
         var element = this;

         element.find('.panel-body:not(:first)').hide();

         element.find('.panel-heading').on('click', function() {
           element.find('.panel-body:visible').slideUp();
           $(this).next('.panel-body').slideDown();
         });
       }
    })(jQuery);

Use it like this:

    !javascript
    $(function() {
      $('#accordion').accordion();
    });

---

# Web components

---
## What are we going to do?

- Introduction to web components
- Custom elements
- Data driven views
- Build web component style applications now with CanJS

---

## The [web component model](http://www.w3.org/TR/components-intro)

The component model for the Web ("Web Components") consists of five pieces:

- [Templates](http://www.w3.org/TR/components-intro/#template-section), which define chunks of markup that are inert but can be activated for use later.
- [Decorators](http://www.w3.org/TR/components-intro/#decorator-section), which apply templates based on CSS selectors to affect rich visual and behavioral changes to documents.
- [Custom Elements](http://www.w3.org/TR/components-intro/#custom-element-section), which let authors define their own elements, with new tag names and new script interfaces.
- [Shadow DOM](http://www.w3.org/TR/components-intro/#shadow-dom-section), which encapsulates a DOM subtree for more reliable composition of user interface elements.
- [Imports](http://www.w3.org/TR/components-intro/#imports-section), which defines how templates, decorators and custom elements are packaged and loaded as a resource.

---

## Polyfills

### [Google Polymer](http://www.polymer-project.org/)

<img src="images/polymer-logo.svg" style="height: 200px;" alt="Polymer Logo" />

### [Mozilla X-Tags](http://www.x-tags.org/)

<img src="images/x-tags-logo.png" style="height: 200px;" alt="X-Tags Logo" />

---

## Custom elements

    !javascript
    document.registerElement('todo-list');

Lets you do:

    !html
    <todo-list id="todos"></todo-list>

Which is great because it also allows to:

    !javascript
    var todo = Object.create(HTMLElement.prototype);

    todo.addTodo = function(description) {
        var paragraph = document.createElement('p');
        paragraph.innerHTML = description;
        this.appendChild(paragraph);
    };

    document.registerElement('todo-list', {
        prototype: todo
    });

And then work with it:

    !javascript
    document.getElementById('todos').addTodo('Hello there');

---

## The bright future

Directly load third party components and customize them.

    !html
    <link rel="import" href="http://yycjs.com/davids-awesome-grid.html">

    <davids-awesome-grid class="grid">
        <grid-row>
            <grid-column from="name"></grid-column>
            <grid-column>Delete</grid-column>
        </grid-row>
    </davids-awesome-grid>

    <script>
        document.querySelector('davids-awesome-grid').setData([{
            name: 'Eric'
        }, {
            name: 'David'
        }]);
    </script>

---

## Data driven views

Use HTML style declarations

    !html
    <ul>
      <li ng-repeat="todo in todos | filter:statusFilter track by $index"
        ng-class="{completed: todo.completed, editing: todo == editedTodo}">
            ...
      </li>
    </ul>

Or a templating language like [Handlebars](http://handlebarsjs.com/):

    !html
    {{#each filteredTodos}}
    <ul>
      <li class="todo{{#if complete}} completed{{/if}}
        {{#if editing}} editing{{/if}}">
        ...
      </li>
    </ul>
    {{/each}}

That __updates automatically__ when its data changes.

---

## can.Component

The [CanJS](http://canjs.us/) approach of controller + custom elements + data driven templates:

    !javascript
    can.Component.extend({
      tag: "todos-app",
      scope: {
        selectedTodo: null,
        todos: new Todo.List({}),
        select: function(todo){
          this.attr('selectedTodo', todo);
        },
        save: function(todo) {
          todo.save();
          this.removeAttr('selectedTodo');
        }
      }
    });

---

## can.view.mustache

    !html
    <todos-app>
      <h2>Todays to-dos</h2>
      {{#selectedTodo}}
      <input type="text"
             can-value="description"
             can-change="save">
      {{/selectedTodo}}
      <ul>
        {{#each todos}}
        <li>
          <input type="checkbox"
                 can-value="complete">
          <span class="{{#if complete}}done{{/if}}"
                can-click="select">
            {{description}}
          </span>
          <button can-click="destroy"></button>
        </li>
        {{/each}}
      </ul>
    </todos-app>

---

## Next Month - Node time

We partner up with [Startup Calgary](http://startupcalgary.ca/) and take over the [Hacknight]() on Tuesday, April 29th.

1. Node for noobs
2. Real time mobile web apps

![NodeJS logo](images/node_logo.png)