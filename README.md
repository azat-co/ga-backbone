# Introduction to Backbone

Working material for a new chapter of [Rapid Prototyping with JS](leanpub.com/rapid-prototyping-with-js/): "Backbone.js in details".

Originally developed as instructions for General Assembly class *Introduction to Backbone.js for Front-end Developers and Designers*: <http://generalassemb.ly/education/582/introduction-to-backbone-js-for-front-end-developers-and-designers/955> and <http://www.eventbrite.com/event/5326760484/estw>.


## Content

1. [Setting up Backbone.js App From Scratch](https://github.com/azat-co/ga-backbone#setting-up-backbonejs-app-from-scratch)
1. [Working with Collections](https://github.com/azat-co/ga-backbone#working-with-collections)
1. [Event Binding](https://github.com/azat-co/ga-backbone#event-binding)
1. [View and Subviews with Underscore.js](https://github.com/azat-co/ga-backbone#event-binding)
1. [ADM and Require.js](https://github.com/azat-co/ga-backbone#event-binding)
1. [Super Simple Backbone Starter Kit](https://github.com/azat-co/ga-backbone#event-binding)

## Setting up Backbone.js App From Scratch

We're going to build typical starter  "Hello World" application using Backbone.js and MVC architecture. I know it might sound like an overkill in the beginning, but as we go along with each step we'll add more and more complexity, things like Models, Subviews and Collections. 

A full source code for "Hello World" app is available at GitHub under [github.com/azat-co/ga-backbone/hello-world](https://github.com/azat-co/ga-backbone/tree/master/hello-world).

Download required libraries:

* [jQuery 1.9 development source file](http://code.jquery.com/jquery-1.9.0.js)

* [Underscore development source file](http://underscorejs.org/underscore.js)

* [Backbone development source file](http://backbonejs.org/backbone.js)

And include them in the index.html file like this:

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

Then set up special `routes` property inside of an **extend** call:

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

This is good, but now we need to add a **home** function:

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

We'll come back to the **home** function later to add more logic for creating and rendering of a View. Right now we should define our **homeView**:

```javascript
    var homeView = Backbone.View.extend({
    });
```

Looks familiar, right? Backbone.js uses similar syntax for for all of its components: extend function and a JSON object as a parameter to it. There are a multiple ways to proceed from now on, but the best practice is to use **el** and ***template*** properties which are "magical", i.e., special in Backbone.js:

```javascript
    var homeView = Backbone.View.extend({
      el: 'body',
      template: _.template('Hello World')   
    });
```

Property **el** is just a string that holds jQuery selector (you can use class name with '.' and id name with '#'). Template property has been assigned an Underscore function **template** with just a plain text 'Hello World'. 

To render our **homeView** we use `this.$el` which is a compiled jQuery object referencing element in an **el** property, and jQuery `.html()` function to replace HTML with `this.template()` value. Here is how the full code for our Backbone.js view looks like:

```javascript
    var homeView = Backbone.View.extend({
      el: 'body',
      template: _.template('Hello World'),
      render: function(){
        this.$el.html(this.template({}));
      }
    });
```

Now, if we go back to the **router** we can add these two lines to the **home** function:

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
The first line will create homeView object and assign it to homeView property of the router. The second line will call **render** method in **homeView** object triggering 'Hello World' output.

Finally, to start a Backbone app we call `new Router` inside of a document ready wrap to make sure that file's DOM is fully loaded:

```javascript
  var app;
    $(document).ready(function(){
      app = new router;
      Backbone.history.start();      
    })
```

Here is the full code of index.html file:

```html

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

Open **index.html** in the browser to see if it works, i.e., 'Hello World' message should be on the page.

## Working with Collections

Full source code is under [ga-backbone/collections](https://github.com/azat-co/ga-backbone/tree/master/collections).

Follow all the steps from **Setting up Backbone.js App From Scratch** exercise or download app from [ga-backbone/hello-world](https://github.com/azat-co/ga-backbone/tree/master/hello-world).

We should add some data to play around and to hydrate our views so add this right after **script** tag and before other code:

```javascript
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

Open collections/index.html file in your browser. You should see data from our "database", i.e., `Apple data: [{"name":"fuji","url":"img/fuji.jpg"},{"name":"gala","url":"img/gala.jpg"}]`. It's not very user friendly but will do for now (we'll make it prettier in the section on subviews).

Now, let' go to `collections/index.html#apples/fuji` or `collections/index.html#apples/gala` in your browser. We expect to see on image with a caption. It's a detailed view of an item which in this case is an apple. Nice work!

## Event Binding

In real life getting data does not happen instantaneously so we need to refactor our code. For the better UI/UX we'll also have to show loading icon (a.k.a. spinner) to users to make sure that they know that the page is loading.

Without Backbone.js we'll have to pass a function that renders as a callback to data loading function to make sure rendering function is not executed before we have actual data to display. 

It's good that we have event binding in Backbone. Therefore, when a user goes to detailed view (`apples/:id`) we only call function that loads the data. Then with the proper even listeners our view will automagically update itself when there is a new data (or a data change, Backbone supported multiple and even custom events).

Let's change what we call in the router:

```javascript
  ...
    loadApple: function(appleName){
      this.appleView.loadApple(appleName);
    }
  ...
```

Everything else is the same till we get to the **appleView** class. We'll need to to add constructor or an initialize function. **initialize** is a special word/property in Backbone.js framework. It's called each time we create an instane of an object, i.e., `var someObj = new SomeObject()`. We can also pass extra parameters to **initialize** function, as we did with our views (we passed object with key **collection** and the value of apples Backbone Collection). More on Backbone constructors at <http://backbonejs.org/#View-constructor>.

```javascript
  ...
  var appleView = Backbone.View.extend({
    initialize: function(){
      //TODO: create and setup model (aka apple)
    },
  ...
```  

Great, we have our **initialize** function. Now we need to create a model which will represent a single apple and set up propert event listeners to the model. We'll use two types of events, `change` and a custom event `spinner`. To do that we are going to use **on** function which takes these properties `event, actions, context`, more at <http://backbonejs.org/#Events-on>:

```javascript
  ...
  var appleView = Backbone.View.extend({
      this.model = new (Backbone.Model.extend({}));
      this.model.bind('change', this.render, this);
      this.bind('spinner',this.showSpinner, this);
    },
  ...      
```

The code above basically boils down to two simple things:

1. when model changes call render function on this object (appleView)
1. when this object (appleView) has event spinner, call showSpinner

So far so good, right? But what about spinner, or loading GIF icon? Let's create a new property in **appleView**:

```javascript
  ...
      templateSpinner: '<img src="img/spinner.gif" width="30"/>',
  ...    
```

Remember loadApple funciton in the router? This is how we can implement it in appleView:

```javascript
  ...
  loadApple:function(appleName){
    this.trigger('spinner'); //show spinner GIF image
    var view = this; //we'll need to access that inside of a closure
    setTimeout(function(){ //simulates real time lag when fetching data from the remote server
      view.model.set(view.collection.where({name:appleName})[0].attributes);  
    },1000);    
  },
  ...
```

The first line will trigger `spinner` event (the function for which we still have to write). Second line is just for scoping issues (so we can use appleView inside of the closure). **setTimeout** function is simulating time lag of a real remote server response. Inside of it we assign attributes of selected model to our view's model by using **model.set()** function and **model.attributes** property (which return properties of a model). 

Now we can remove extra code from **render** function and implement **showSpinner** function:

```javascript
  render: function(appleName){
    var appleHtml = this.template(this.model);
    $('body').html(appleHtml);
  },
  showSpinner: function(){
    $('body').html(this.templateSpinner);        
  }
  ...
```      

That's all! Open `index.html#apples/gala` or `index.html#apples/fuji` in your browser and enjoy loading animation while waiting for an apple image to load. The full code of **index.html** file:

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
        this.appleView.loadApple(appleName);

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
      initialize: function(){
        this.model = new (Backbone.Model.extend({}));
        this.model.on('change', this.render, this);
        this.on('spinner',this.showSpinner, this);
      },
      template: _.template('<figure>\
                              <img src="<%= attributes.url%>"/>\
                              <figcaption><%= attributes.name %></figcaption>\
                            </figure>'),
      templateSpinner: '<img src="img/spinner.gif" width="30"/>',

      loadApple:function(appleName){
        this.trigger('spinner');
        var view = this; //we'll need to access that inside of a closure
        setTimeout(function(){ //simulates real time lag when fetching data from the remote server
          view.model.set(view.collection.where({name:appleName})[0].attributes);  
        },1000);
        
      },

      render: function(appleName){
        var appleHtml = this.template(this.model);
        $('body').html(appleHtml);
      },
      showSpinner: function(){
        $('body').html(this.templateSpinner);        
      }

    });
    $(document).ready(function(){
      app = new router;
      Backbone.history.start();      
    })


  </script>
</head>
<body>
  <a href="#apples/fuji">fuji</a>
  <div></div>
</body>
</html>
```

## Views and Subviews with Underscore.js

TODO: use subview to render list of apples

## ADM and Require.js

TODO: simple require.js example, split template into separate files. Shim?

## Super Simple Backbone Starter Kit
Load local HTTP server, e.g., [MAMP](http://www.mamp.info/en/index.html), and open you browser at the folder in which you downloaded/cloned SSBSK.


