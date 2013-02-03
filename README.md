# Introduction to Backbone

Instructions for General Assembly class *Introduction to Backbone.js for Front-end Developers and Designers*.

<http://generalassemb.ly/education/582/introduction-to-backbone-js-for-front-end-developers-and-designers/955>

<http://www.eventbrite.com/event/5326760484/estw>

## Setting up Backbone.js App From Scratch

Full source code is under [ga-backbone/hello-world](https://github.com/azat-co/ga-backbone/tree/master/hello-world).

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

## Working with Collections

Full source code is under [ga-backbone/collections](https://github.com/azat-co/ga-backbone/tree/master/collections).

Follow all the steps from **Setting up Backbone.js App From Scratch** exercise or download app from [ga-backbone/hello-world](https://github.com/azat-co/ga-backbone/tree/master/hello-world).

We should add some data to play around and to hydrate our views so add this right after **script** tag and before other code:

```json
   var appleData = [
      {
        name: "fuji",
        url: 'img/fuji.jpg'
      },
      {
        name: "gala",
        url: 'img/gala.jpg'
      }      
    ];
```

This will be our apple *database* or REST API endpoint substitute to provide us with names and URLs to the images.

Now to make UX little bit better we can add a new route to the routes object in Backbone Route:
	
```javascript
    ...  
	  routes: {
      "": "home",
      "apples/:appleName": "loadApple"
    },
    ...
```
This will allow users to go to index.html#apples/SOMENAME and expect to see and information about an apple fetching and rendering of which we will implement in the **loadApple** function in the Backbone Router definition:

```javascript
      loadApple: function(appleName){
        this.appleView.render(appleName);
      }
```
Have you noticed *appleName* variable? It's exactly the same name as in route. This is how we can access query string parameters in Backbone.

Now we'll need to refactor some more code to create Backbone Collection and populate it with data in our **appleData** variable, and to pass collection to **homeView** and **appleView**, and we do it all in Router constructor method **initialize**:

```javascript
      initialize: function(){
        var apples = new Apples();
        apples.reset(appleData);
        this.homeView = new homeView({collection: apples});
        this.appleView = new appleView({collection: apples});
      },
```

At this point we're pretty much done with the Router class and it should look like this:

```javascript
    var router = Backbone.Router.extend({
      routes: {
        "": "home",
        "apples/:appleName": "loadApple"
      },
      initialize: function(){
        var apples = new Apples();
        apples.reset(appleData);
        this.homeView = new homeView({collection: apples});
        this.appleView = new appleView({collection: apples});
      },
      home: function(){        
        this.homeView.render();
      },
      loadApple: function(appleName){
        this.appleView.render(appleName);
      }
    });
```

Let's modify our **homeView** a bit to see the whole *database*:

```javascript
    var homeView = Backbone.View.extend({
      el: 'body',
      template: _.template('Apple data: <%= data %>'),
      render: function(){
        this.$el.html(this.template({data: JSON.stringify(this.collection.models)}));
      }
    });
```

For now we out put JSON object which is not user-friendly at all, the best thing to do would be to use subviews. 
 
Apple Backbone Collections is clean and simple:

```javascript
    var Apples = Backbone.Collection.extend({

    });
```

Apple view is not any more complex, it has only two properties, template and render. In template we want to display **figure**, **img** and **figcaption** tags with specific values, Underscore template engine is handy at this:

```javascript
    var appleView = Backbone.View.extend({
      template: _.template(
						'<figure>\
               <img src="<%= attributes.url%>"/>\
               <figcaption><%= attributes.name %></figcaption>\
             </figure>'),
   ...
    });
```
To make JavaScript string, which has HTML tags in it, more readable we can use backslash line breaker escape ("\") symbol or close strings and concatenate them with plus sign ("+").

```javascript
    var appleView = Backbone.View.extend({
      template: _.template(
          '<figure>'+
            +'<img src="<%= attributes.url%>"/>'+
            +'<figcaption><%= attributes.name %></figcaption>'+
          +'</figure>'),
   ...
```

Please note the '<%=' and '%>' symbols, that instructions for Undescore.js to print values in properties **url** and **name** of **attributes** object.

Finally, we adding render function to the **appleView** class:

```javascript
      render: function(appleName){
        var appleModel = this.collection.where({name:appleName})[0];
        var appleHtml = this.template(appleModel);
        $('body').html(appleHtml);
      }
```

Right now **render** function is responsible for both loading the data and rendering it, later it will be a good idea to separate these two functionalities into different methods.

The whole app, which is in ga-backbone/collection/index.html, looks like this:

```html
<!DOCTYPE>
<html>
<head>
  <script src="jquery.js"></script>
  <script src="underscore.js"></script>
  <script src="backbone.js"></script>

  <script>
   var appleData = [
      {
        name: "fuji",
        url: 'img/fuji.jpg'
      },
      {
        name: "gala",
        url: 'img/gala.jpg'
      }      
    ];
    var app;
    var router = Backbone.Router.extend({
      routes: {
        "": "home",
        "apples/:appleName": "loadApple"
      },
      initialize: function(){
        var apples = new Apples();
        apples.reset(appleData);
        this.homeView = new homeView({collection: apples});
        this.appleView = new appleView({collection: apples});
      },
      home: function(){        
        this.homeView.render();
      },
      loadApple: function(appleName){
        this.appleView.render(appleName);
      }
    });

    var homeView = Backbone.View.extend({
      el: 'body',
      template: _.template('Apple data: <%= data %>'),
      render: function(){
        this.$el.html(this.template({data: JSON.stringify(this.collection.models)}));
      }
      //TODO subviews
    });

    var Apples = Backbone.Collection.extend({

    });
    var appleView = Backbone.View.extend({
      template: _.template('<figure>\
                              <img src="<%= attributes.url%>"/>\
                              <figcaption><%= attributes.name %></figcaption>\
                            </figure>'),

      //re-write with load apple and event binding

      render: function(appleName){

        var appleModel = this.collection.where({name:appleName})[0];
        var appleHtml = this.template(appleModel);
        $('body').html(appleHtml);
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


## Event Binding

TODO: use load apple function and even listeners

## Views and Subviews

TODO: use subview to render list of apples

## ADM and Super Simple Backbone Starter Kit

Load local HTTP server, e.g., [MAMP](http://www.mamp.info/en/index.html), and open you browser at the folder in which you downloaded/cloned SSBSK.


