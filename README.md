# Introduction to Backbone

Instructions for General Assembly class *Introduction to Backbone.js for Front-end Developers and Designers*.

<http://generalassemb.ly/education/582/introduction-to-backbone-js-for-front-end-developers-and-designers/955>

<http://www.eventbrite.com/event/5326760484/estw>

## Set up Backbone.js App From Scratch

Download required libraries:

* [jQuery 1.9 development source file](http://code.jquery.com/jquery-1.9.0.js)

* [Underscore development source file](http://underscorejs.org/underscore.js)

* [Backbone development source file](http://backbonejs.org/backbone.js)

index.html:

```html
<!DOCTYPE>
<html>
<head>
  <script src="jquery.js"></script>
  <script src="underscore.js"></script>
  <script src="backbone.js"></script>

  <script>

  </script>
</head>
<body>
</body>
</html>
```

Let's define a simple Router inside of `<script>` tags:

```javascript
...
    var router = Backbone.Router.extend({
    });
...
```

Then set up special `routes` property in side of extend call:

```javascript
    var router = Backbone.Router.extend({
      routes: {
      }
    });
```

Routes need to be in the following format: `'path/:param':'action' with will result in `path/param` calling function action which we'll set up as a property to **extend** later. For now we add only home route:

```javascript
    var router = Backbone.Router.extend({
      routes: {
        "": "home"
      }
    });
```

This is good but now we need to add home function:

```javascript
    var router = Backbone.Router.extend({
      routes: {
        "": "home"
      },
      home: function(){
				//TODO render html
      }
    });
```

We'll come back to later to add logic to create and render View, before we do it we should define our homeView:

```javascript
    var homeView = Backbone.View.extend({
    });
```

Looks similar, right? Backbone uses similar approach with extend and JSON structure. There are a multiple way to proceed from now on but the best practice is to use **el** and ***template*** properties:

```javascript
    var homeView = Backbone.View.extend({
      el: 'body',
      template: _.template('Hello World')   
    });
```
el is just a sting holding jQuery selector (you can use class with '.' and id with '#'). Template property is assigned Underscore function **template** with just a plain text 'Hello World'. To render all this we can use `this.$el` which is a compiled jQuery object referencing element in **el** property and jQuery `.html()` function to replace html with `this.template()` value, here is how it looks:

```javascript
    var homeView = Backbone.View.extend({
      el: 'body',
      template: _.template('Hello World'),
      render: function(){
        this.$el.html(this.template({}));
      }
    });
```

Now if we go back to router we can add these two lines to home function:

```javascript
    var router = Backbone.Router.extend({
      routes: {
        "": "home"
      },
      initialize: function(){
        
      },
      home: function(){
        this.homeView = new homeView;
        this.homeView.render();
      }
    });
```

Finally to start a Backbone app we call `new Router` inside of a document ready wrap to make sure DOM is loaded:

```javascript
		var app;
    $(document).ready(function(){
      app = new router;
      Backbone.history.start();      
    })
```

Here is the full code of index.html file:

```javascript
<!DOCTYPE>
<html>
<head>
  <script src="jquery.js"></script>
  <script src="underscore.js"></script>
  <script src="backbone.js"></script>

  <script>
  
    var app;
    var router = Backbone.Router.extend({
      routes: {
        "": "home"
      },
      initialize: function(){
        
      },
      home: function(){
        this.homeView = new homeView;
        this.homeView.render();
      }
    });

    var homeView = Backbone.View.extend({
      el: 'body',
      template: _.template('Hello World'),
      render: function(){
        this.$el.html(this.template({}));
      }
    });


    $(document).ready(function(){
      app = new router;
      Backbone.history.start();      
    })


  </script>
</head>
<body>
  <div></div>
</body>
</html>
```

## Collections

## ADM and Super Simple Backbone Starter Kit

## Using View and Subviews

TODO

