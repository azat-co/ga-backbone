# Introduction to Backbone

Working material for a new chapter of [Rapid Prototyping with JS](leanpub.com/rapid-prototyping-with-js/): "Backbone.js in details".

Originally developed as instructions for General Assembly class *Introduction to Backbone.js for Front-end Developers and Designers*: <http://generalassemb.ly/education/582/introduction-to-backbone-js-for-front-end-developers-and-designers/955> and <http://www.eventbrite.com/event/5326760484/estw>.


## Content

1. [Setting up Backbone.js App From Scratch](https://github.com/azat-co/ga-backbone#setting-up-backbonejs-app-from-scratch)
1. [Working with Collections](https://github.com/azat-co/ga-backbone#working-with-collections)
1. [Event Binding](https://github.com/azat-co/ga-backbone#event-binding)
1. [View and Subviews with Underscore.js](https://github.com/azat-co/ga-backbone#views-and-subviews-with-underscorejs)
1. [ADM and Require.js for Development](https://github.com/azat-co/ga-backbone#amd-and-requirejs-for-development)
1. [Require.js for Production](https://github.com/azat-co/ga-backbone#requirejs-for-production)
1. [Super Simple Backbone Starter Kit](https://github.com/azat-co/ga-backbone#super-simple-backbone-starter-kit)

## Setting up Backbone.js App From Scratch

We're going to build a typical starter  "Hello World" application using Backbone.js and Mode-View-Controller (MVC) architecture. I know it might sound like an overkill in the beginning, but as we go along we'll add more and more complexity, things like Models, Subviews and Collections. 

A full source code for "Hello World" app is available at GitHub under [github.com/azat-co/ga-backbone/hello-world](https://github.com/azat-co/ga-backbone/tree/master/hello-world).

### Dependancies

Download the following libraries:

* [jQuery 1.9 development source file](http://code.jquery.com/jquery-1.9.0.js)

* [Underscore.js development source file](http://underscorejs.org/underscore.js)

* [Backbone.js development source file](http://backbonejs.org/backbone.js)

And include these frameworks in the **index.html** file like this:

```html
<!DOCTYPE>
<html>
<head>
  <script src="jquery.js"></script>
  <script src="underscore.js"></script>
  <script src="backbone.js"></script>

  <script>
    //TODO write some awesome JS code!
  </script>

</head>
<body>
</body>
</html>
```

**Note**: We can also put `<script>` tags right after the `</body>` tag. This will change the order of impact performance in large files.

Let's define a simple Backbone.js Router inside of `<script>` tags:

```javascript
...
    var router = Backbone.Router.extend({
    });
...
```

**Note**: For now, to Keep It Simple Stupid (KISS) we'll be putting all JavaScript code right into our **index.html** file. This is not a good idea for a real development or production code. We'll refactor the code later.

Then set up a special `routes` property inside of the **extend** call:

```javascript
    var router = Backbone.Router.extend({
      routes: {
      }
    });
```

Backcone.js Routes need to be in the following format: `'path/:param':'action' with will result in `path/param` calling function action which we'll set up as a property to **extend** later. For now we add a single **home** route:

```javascript
    var router = Backbone.Router.extend({
      routes: {
        '': 'home'
      }
    });
```

This is good, but now we need to add a **home** function:

```javascript
    var router = Backbone.Router.extend({
      routes: {
        '': 'home'
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

Looks familiar, right? Backbone.js uses similar syntax for all of its components: extend function and a JSON object as a parameter to it. 

There are a multiple ways to proceed from now on, but the best practice is to use **el** and ***template*** properties which are "magical", i.e., special in Backbone.js:

```javascript
    var homeView = Backbone.View.extend({
      el: 'body',
      template: _.template('Hello World')   
    });
```

Property **el** is just a string that holds jQuery selector (you can use class name with '.' and id name with '#'). Template property has been assigned an Underscore.js function **template** with just a plain text 'Hello World'. 

To render our **homeView** we use `this.$el` which is a compiled jQuery object referencing element in an **el** property, and jQuery `.html()` function to replace HTML with `this.template()` value. Here is how the full code for our Backbone.js View looks like:

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
        '': 'home'
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

Here is the full code of the **index.html** file:

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
        '': 'home'
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

The full source code of this example is under [ga-backbone/collections](https://github.com/azat-co/ga-backbone/tree/master/collections).

Follow all the steps from **Setting up Backbone.js App From Scratch** exercise or download the app from [ga-backbone/hello-world](https://github.com/azat-co/ga-backbone/tree/master/hello-world).

We should add some data to play around with, and to hydrate our views. To do this, add right after **script** tag and before the other code:

```javascript
   var appleData = [
      {
        name: "fuji",
        url: "img/fuji.jpg"
      },
      {
        name: "gala",
        url: "img/gala.jpg"
      }      
    ];
```

This is our apple *database*. :-) Or to be more correct REST API endpoint-substitute which provides us with names and images URLs of the apples (data models).

**Note**: This mock dataset can be easily substituted by assigning REST API endpoints of your back-end to `url` properties in Backbone.js Collections and/or Models. 

Now to make the User Experience (UX) a little bit better, we can add a new route to the **routes** object in Backbone Route:
  
```javascript
    ...  
    routes: {
      '': 'home',
      'apples/:appleName': 'loadApple'
    },
    ...
```
This will allow users to go to `index.html#apples/SOMENAME` and expect to see the information about an apple. This information will be fetched and rendered by **loadApple** function in the Backbone Router definition:

```javascript
      loadApple: function(appleName){
        this.appleView.render(appleName);
      }
```
Have you noticed an **appleName** variable? It's exactly the same name as in the one the we've used in **route**. This is how we can access query string parameters (e.g, `?param=value&q=search`) in Backbone.

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
        '': 'home',
        'apples/:appleName': 'loadApple'
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

For now we just output JSON object in the browser. This is not user-friendly at all, later we'll improve it by using a list and subviews. 
 
Our apple Backbone Collection is clean and simple:

```javascript
    var Apples = Backbone.Collection.extend({
    });
```

**Note**: Backbone automatically creates models inside of a collection when we use `fetch()` or `reset()` functions.

Apple view is not any more complex, it has only two properties: template and render. In a template we want to display **figure**, **img** and **figcaption** tags with specific values. Underscore.js template engine is handy at this:

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

To make JavaScript string, which has HTML tags in it, more readable we can use backslash line breaker escape ("\") symbol or close strings and concatenate them with plus sign ("+"):

```javascript
  var appleView = Backbone.View.extend({
    template: _.template(
        '<figure>'+
          +'<img src="<%= attributes.url%>"/>'+
          +'<figcaption><%= attributes.name %></figcaption>'+
        +'</figure>'),
  ...
```

Please note the '<%=' and '%>' symbols, they are the instructions for Undescore.js to print values in properties **url** and **name** of the **attributes** object.

Finally, we're adding render function to the **appleView** class:

```javascript
  render: function(appleName){
    var appleModel = this.collection.where({name:appleName})[0];
    var appleHtml = this.template(appleModel);
    $('body').html(appleHtml);
  }
```

Right now **render** function is responsible for both loading the data and rendering it. Later we'll refactor the function to separate these two functionalities into different methods.

The whole app, which is in `ga-backbone/collection/index.html` folder, looks like this:

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
        url: "img/fuji.jpg"
      },
      {
        name: "gala",
        url: "img/gala.jpg"
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
      //TODO re-write with load apple and event binding
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

Open `collections/index.html` file in your browser. You should see the data from our "database", i.e., `Apple data: [{"name":"fuji","url":"img/fuji.jpg"},{"name":"gala","url":"img/gala.jpg"}]`. It's not very user friendly but will do for now (we'll make it prettier in the section on subviews).

Now, let' go to `collections/index.html#apples/fuji` or `collections/index.html#apples/gala` in your browser. We expect to see on image with a caption. It's a detailed view of an item which in this case is an apple. Nice work!

## Event Binding

In the real life getting data does not happen instantaneously so let's refactor our code. For a better UI/UX, we'll also have to show loading icon (a.k.a., spinner or ajax-loader) to users to make sure that they know that the information is being loaded.

Without Backbone.js we'll have to pass a function that renders HTML as a callback to the data loading function, to make sure rendering function is not executed before we have actual data to display. 

It's a good things that we have event binding in Backbone. Therefore, when a user goes to detailed view (`apples/:id`) we only call function that loads the data. Then with the proper even listeners our view will automagically update itself, when there is a new data (or on a data change, Backbone.js supports multiple and even custom events).

Let's change what we call in the router:

```javascript
  ...
    loadApple: function(appleName){
      this.appleView.loadApple(appleName);
    }
  ...
```

Everything else is the same till we get to the **appleView** class. We'll need to add a constructor or an initialize function. **initialize** is a special word/property in Backbone.js framework. It's called each time we create an instance of an object, i.e., `var someObj = new SomeObject()`. We can also pass extra parameters to **initialize** function, as we did with our views (we passed object with key **collection** and the value of apples Backbone Collection). Read more on Backbone constructors at <http://backbonejs.org/#View-constructor>.

```javascript
  ...
  var appleView = Backbone.View.extend({
    initialize: function(){
      //TODO: create and setup model (aka an apple)
    },
  ...
```  

Great, we have our **initialize** function. Now we need to create a model which will represent a single apple and set up proper event listeners on the model. We'll use two types of events, `change` and a custom event called `spinner`. To do that we are going to use `on()` function which takes these properties `on(event, actions, context)`, read more about it at at <http://backbonejs.org/#Events-on>:

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

1. Call render function on **this** object (appleView) when model changes
1. Call showSpinner when **this** object (appleView) event spinner is fired

So far so good, right? But what about spinner, or loading a GIF icon? Let's create a new property in **appleView**:

```javascript
  ...
    templateSpinner: '<img src="img/spinner.gif" width="30"/>',
  ...    
```

Remember `loadApple` function in the router? This is how we can implement it in **appleView**:

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

The first line will trigger the `spinner` event (the function for which we still have to write). 

Second line is just for scoping issues (so we can use **appleView** inside of the closure). 

`setTimeout` function is simulating a time lag of a real remote server response. Inside of it we assign attributes of selected model to our view's model by using `model.set()` function and **model.attributes** property (which returns properties of a model). 

Now we can remove extra code from `render` function and implement `showSpinner` function:

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

That's all! Open `index.html#apples/gala` or `index.html#apples/fuji` in your browser and enjoy loading animation while waiting for an apple image to load. 

The full code of **index.html** file:

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
        url: "img/fuji.jpg"
      },
      {
        name: "gala",
        url: "img/gala.jpg"
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

This example is available at <https://github.com/azat-co/ga-backbone/tree/master/subview>.

Subviews are Backbone Views that are created and used inside of another Backbone View. Subviews concept is a great way to abstract (separate) UI events (e.g., clicks) and templates for similarly structured elements (e.g., apples).

Use case of a Subview might include a row in a table, a list item in a list, a paragraph, a new line, etc. 

We'll refactor our home page to show a nice list of apples. Each list item will have an apple name and a **buy** link with onClick event. Let's start with creating a subview for a single apple with our standard Backbone `extend` function:

```javascript
  ...
  var appleItemView = Backbone.View.extend({
    tagName: 'li',
    template: _.template(''
           +'<a href="#apples/<%=name%>" target="_blank">'
          +'<%=name%>'
          +'</a>&nbsp;<a class="add-to-cart" href="#">buy</a>'),
    events: {
      'click .add-to-cart': 'addToCart'
    },
    render: function() {
      this.$el.html(this.template(this.model.attributes));
    },
    addToCart: function(){
      this.model.collection.trigger('addToCart', this.model);
    }
  });
  ...
```

Now we can populate the object with **tagName**, **template**, **events**, **render** and **addToCart** properties/methods. 

```javascript
  ...
  tagName: 'li',
  ...
``` 

**tagName** automatically lets Backbone to create HTML element with the specified tag name, in this case `<li>` — list item. This will be a representation of a single apple, a row in our list.

```javascript
  ...
  template: _.template(''
         +'<a href="#apples/<%=name%>" target="_blank">'
        +'<%=name%>'
        +'</a>&nbsp;<a class="add-to-cart" href="#">buy</a>'),
  ...
```

Template is just a sting with Undescore.js instructions. They are wrapped in `<%` and `%>` symbols. `<%=` simply means print the value. The same code can be written with backslash escapes:

```javascript
  ...
  template: _.template('\
         <a href="#apples/<%=name%>" target="_blank">\
        <%=name%>\
        </a>&nbsp;<a class="add-to-cart" href="#">buy</a>\
        '),
  ...
```

Each `<li>` will have two anchor elements (`<a>`), links to a detailed apple view (`#apples/:appleName`) and a **buy** button. Now we're going to attach an event listener to the **buy** button:

```javascript
  ...
  events: {
    'click .add-to-cart': 'addToCart'
  },
  ...
```

The syntax follows this rule: event + jQuery element selector: function name. Both, the key and the value (right and left part separated by the colon) are strings.

To render each item in the list we use jQuery html() function on this.$el jQuery object which is li (based on tagName):

```javascript 
  ...
  render: function() {
    this.$el.html(this.template(this.model.attributes));
  },
  ...
```

addToCart will use trigger() function to notify collection that this particular model is up for the purchase by the user:

```javascript
  ...
    addToCart: function(){
      this.model.collection.trigger('addToCart', this.model);
    }
  ...
```

Here is the full code of the appleItemView Backbone view class:

```javascript
  ...
  var appleItemView = Backbone.View.extend({
    tagName: 'li',
    template: _.template(''
           +'<a href="#apples/<%=name%>" target="_blank">'
          +'<%=name%>'
          +'</a>&nbsp;<a class="add-to-cart" href="#">buy</a>'),
    events: {
      'click .add-to-cart': 'addToCart'
    },
    render: function() {
      this.$el.html(this.template(this.model.attributes));
    },
    addToCart: function(){
      this.model.collection.trigger('addToCart', this.model);
    }
  });
  ...
```

That was easy, right? But what about the master view which is supposed to render all our items (apples) and provide wrapper container for li HTML elements (ul is our wrapper). We need to modify and enhance our homeView. 

For started we can add extra properties as string understandable by jQuery as selectors:

```javascript
  ...
  el: 'body',
  listEl: '.apples-list',
  cartEl: '.cart-box',
  ...
```

We can use properties from above in the template or just hard-code them (we'll refactor our code later):

```javascript      
  ...
  template: _.template('Apple data: \
    <ul class="apples-list">\
    </ul>\
    <div class="cart-box"></div>'),
  ...
```

Initialize function will be called when homeView is created (new homeView) and we render our template (with our favorite html function) and attach event listener to the collection (which is a set of apple models):

```javascript        
  ...
    initialize: function() {
      this.$el.html(this.template);
      this.collection.on('addToCart', this.showCart, this);
    },
  ...
```

The syntax for binding event is covered in the previous section. It boils down to calling showCart function of homeView. In this function we append appleName to the cart (along with a line break, br element):

```javascript  
  ...    
    showCart: function(appleModel) {
      $(this.cartEl).append(appleModel.attributes.name+'<br/>');
    },
  ...
```

Finally here is our glorious render function in which we iterate through each model in the collection (each apple), create appleItemView for each apple, create li element for each apple and append that elements to view.listEl which is ul element with a class apples-list in the DOM:

```javascript      
  ...
  render: function(){
    view = this; 
    //so we can use view inside of closure
    this.collection.each(function(apple){
      var appleSubView = new appleItemView({model:apple}); 
      // create subview with model apple
      appleSubView.render(); 
      // compiles tempalte and single apple data
      $(view.listEl).append(appleSubView.$el);   
      //append jQuery object from single apple to apples-list DOM element
    });
  }
  ...
```

    
Let's make sure we didn't miss anything in the homeView Backbone view:

```javascript
  ...
  var homeView = Backbone.View.extend({
    el: 'body',
    listEl: '.apples-list',
    cartEl: '.cart-box',
    template: _.template('Apple data: \
      <ul class="apples-list">\
      </ul>\
      <div class="cart-box"></div>'),
    initialize: function() {
      this.$el.html(this.template);
      this.collection.on('addToCart', this.showCart, this);
    },
    showCart: function(appleModel) {
      $(this.cartEl).append(appleModel.attributes.name+'<br/>');
    },      
    render: function(){
      view = this; //so we can use view inside of closure
      this.collection.each(function(apple){
        var appleSubView = new appleItemView({model:apple}); // create subview with model apple
        appleSubView.render(); // compiles tempalte and single apple data
        $(view.listEl).append(appleSubView.$el);   //append jQuery object from single apple to apples-list DOM element
      });
    }
  }); 
  ...
```

You should be able to click on the buy and cart will populate with the apples of your choice. Looking at an individual apple does not require typing its name in the URL address bar of the browser. We can click on the name and it opens a new window with a detailed view.

By using the subviews we re-used the template for all of the items, attached specific event to each item. Those event are smart enough to pass the information about the model to other objects: views and collections.

Just in case, here is the full code for the subview example:

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
        url: "img/fuji.jpg"
      },
      {
        name: "gala",
        url: "img/gala.jpg"
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
    var appleItemView = Backbone.View.extend({
      tagName: 'li',
      // template: _.template(''
      //        +'<a href="#apples/<%=name%>" target="_blank">'
      //       +'<%=name%>'
      //       +'</a>&nbsp;<a class="add-to-cart" href="#">buy</a>'),
      template: _.template('\
             <a href="#apples/<%=name%>" target="_blank">\
            <%=name%>\
            </a>&nbsp;<a class="add-to-cart" href="#">buy</a>\
            '),

      events: {
        'click .add-to-cart': 'addToCart'
      },
      render: function() {
        this.$el.html(this.template(this.model.attributes));
      },
      addToCart: function(){
        this.model.collection.trigger('addToCart', this.model);
      }
    });

    var homeView = Backbone.View.extend({
      el: 'body',
      listEl: '.apples-list',
      cartEl: '.cart-box',
      template: _.template('Apple data: \
        <ul class="apples-list">\
        </ul>\
        <div class="cart-box"></div>'),
      initialize: function() {
        this.$el.html(this.template);
        this.collection.on('addToCart', this.showCart, this);
      },
      showCart: function(appleModel) {
        $(this.cartEl).append(appleModel.attributes.name+'<br/>');
      },      
      render: function(){
        view = this; //so we can use view inside of closure
        this.collection.each(function(apple){
          var appleSubView = new appleItemView({model:apple}); // create subview with model apple
          appleSubView.render(); // compiles tempalte and single apple data
          $(view.listEl).append(appleSubView.$el);   //append jQuery object from single apple to apples-list DOM element
        });
      }
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
  <div></div>
</body>
</html>
```

Open `collections/index.html#apples/fuji`.

## Refactoring

At this point you are probably wondering what is the benefit of using a framework and still having multiple classes, objects and elements with different functionalities in one file. This was done for the purpose of *Keep it Simple Stupid* (KISS). The bigger is your application the more pain there is in unorganized code base. Let's break down our application into multiple files where each file will be one of this types:

* view
* template
* router
* collection
* model

Let write these script include tags into our **index.html** head: 

```html
  <script src="apple-item.view.js"></script>
  <script src="apple-home.view.js"></script>
  <script src="apple.view.js"></script>
  <script src="apples.js"></script>
  <script src="apple-app.js"></script>
```

The name don't have to follow the convention of dashes and dots as long as it's easy to tell what each file is supposed to do.

Now let's copy our objects/classes into corresponding files.
Our main **index.html** file should look like this:

```html
<!DOCTYPE>
<html>
<head>
  <script src="jquery.js"></script>
  <script src="underscore.js"></script>
  <script src="backbone.js"></script>

  <script src="apple-item.view.js"></script>
  <script src="apple-home.view.js"></script>
  <script src="apple.view.js"></script>
  <script src="apples.js"></script>
  <script src="apple-app.js"></script>
  
</head>
<body>
  <div></div>
</body>
</html>
```

Content of **apple-item.view.js**:

```javascript
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
```
    
**apple-home.view.js**: 
    
```javascipt
    

    var homeView = Backbone.View.extend({
      el: 'body',
      listEl: '.apples-list',
      cartEl: '.cart-box',
      template: _.template('Apple data: \
        <ul class="apples-list">\
        </ul>\
        <div class="cart-box"></div>'),
      initialize: function() {
        this.$el.html(this.template);
        this.collection.on('addToCart', this.showCart, this);
      },
      showCart: function(appleModel) {
        $(this.cartEl).append(appleModel.attributes.name+'<br/>');
      },      
      render: function(){
        view = this; //so we can use view inside of closure
        this.collection.each(function(apple){
          var appleSubView = new appleItemView({model:apple}); // create subview with model apple
          appleSubView.render(); // compiles tempalte and single apple data
          $(view.listEl).append(appleSubView.$el);   //append jQuery object from single apple to apples-list DOM element
        });
      }


    });
```
    
**apple.view.js**:

```javascript
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
```

**apples.js**

```javascript
 
    var Apples = Backbone.Collection.extend({

    });
```

**apple-app.js** :

```javascript
   var appleData = [
      {
        name: "fuji",
        url: "img/fuji.jpg"
      },
      {
        name: "gala",
        url: "img/gala.jpg"
      }      
    ];
    var app;
    var router = Backbone.Router.extend({
      routes: {
        '': 'home',
        'apples/:appleName': 'loadApple'
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

    $(document).ready(function(){
      app = new router;
      Backbone.history.start();      
    })
```

Now let's try to open the application. It should work exactly the same as in previous example **subview**.

It's better but not perfect because we still have html templates directly in JavaScript code. The problem with that is that designers and developers can't work on the same files and any change to the presentation requires a change in the main code base.

We can add a few more include to our index.html file:


```html
  <script src="apple-item.tpl.js"></script>
  <script src="apple-home.tpl.js"></script>
  <script src="apple-spinner.tpl.js"></script>
  <script src="apple.tpl.js"></script>
```

Usually one Backbone view has one templates but in the case of our **appleView** (detailed view of an apple in a separate window) we also have loading icon spinner.

The content of the files is just a global variable which is assign the string value. Later we use these varible in our views when we call Underscore.js helper method **_.template()**.

**apple-item.tpl.js**:

```javascript
var appleItemTpl = '\
             <a href="#apples/<%=name%>" target="_blank">\
            <%=name%>\
            </a>&nbsp;<a class="add-to-cart" href="#">buy</a>\
            ';
```

**apple-home.tpl.js**

```javascript
var appleHomeTpl = 'Apple data: \
        <ul class="apples-list">\
        </ul>\
        <div class="cart-box"></div>';
```

**apple-spinner.tpl.js**

```javascript
var appleSpinnerTpl = '<img src="img/spinner.gif" width="30"/>';
```

**apple.tpl.js**

```javascript
var appleTpl = '<figure>\
                              <img src="<%= attributes.url%>"/>\
                              <figcaption><%= attributes.name %></figcaption>\
                            </figure>';
```

Try to start the application now. The full code is under **refactor** folder. 



As you can see in previous example we used global scoped variables (without the keyword `window`). 

**Note:** Be carefull when you untroduce a lot of veriable into global namespace. There might be conflicts and other unpredicable concequences. For example, if you wrote an open source library and other developers started using methods and properties directly instead of using the interface, what happens later when you decide to finally remove/deprecate those global leacks? To prevent this properly written libraries and applications use [JavaScript closures](https://developer.mozilla.org/en-US/docs/JavaScript/Guide/Closures).

Example of using closure and global variable module definition:

```javascript
(function() {
  var apple= function() {
  ...//do something useful like return apple object
  };  
  window.Apple = apple;
}())
```

or in case we need an access to app object (which creates a dependency on that object):

```javascript
(function() {
  var app = this.app; //equivalent of window.appliation in case we need a dependency (app)
  this.apple = function() {
  ...//return apple object/class
  //use app variable
  }
  // eqivalent of window.apple = function(){...};
}())
```

As you can see we create function and call it immediatly while wrapping everything in paranthesis `()`.


## AMD and Require.js For Development



This article does a great job at explaining why AMD is a good thing, [WHY AMD?](http://requirejs.org/docs/whyamd.html).

Start you local HTTP server, e.g., [MAMP](http://www.mamp.info/en/index.html).


Let enhance our code by using Require.js library. 

Our index.html will look very minimalistic:

```html
<!DOCTYPE>
<html>
<head>
  <script src="jquery.js"></script>
  <script src="underscore.js"></script>
  <script src="backbone.js"></script>
  <script src="require.js"></script>
  <script src="apple-app.js"></script>  

</head>
<body>
  <div></div>
</body>
</html>
```

We only include libraries and one JavaScript file. This file has the following structure:

```javascript
require([...],function(...){...});
```

or, more explanatory:

```javascript
require([
  'name-of-the-module',
  ...
  'name-of-the-other-module'
  ],function(referenceToModule, ..., referenceToOtherModule){
  ...//some usefull code
  referenceToModule.someMethod();
});
```

Basically we tell browser to load files from the array of filenames (first parameter of require function) and then pass our modules from that files to the function as variables (second argument of require function). Inside of the main function we can use our module by referencing the variables.

So our **apple-app.js** will look like this:

<<(amd/apple-app.js)

We put all the code inside the function, required modules by their filenames and passed modules as parameters to the main function. To define module we need to use **define** function:

```javascript
define([...],function(...){...})
```

The meaning is similar to **require** function. We put dependencies as string of filenames (and paths) in the array and pass it as the first argument. Main function takes dependencies as parameters (the order of parameters and modules in the array is important):

```javascript
define(['name-of-the-module'],function(nameOfModule){
  var b = nameOfModule.render();
  return b; 
})
```
Note: there is no need to append **.js** to the filename. Require.js does it automatically.

Let's start with the templates and convert them into Require.js modules. This is **apple-item.tpl.js**:

<<(amd/apple-item.tpl.js)

**apple-home.tpl**:

<<(amd/apple-home.tpl.js)
    
**apple-spinner.tpl.js**

<<(amd/apple-spinner.tpl.js)

**apple.tpl.js**

<<(amd/apple.tpl.js)

**apple-item.view.js**:

<<(amd/apple-item.view.js)

**apple-home.view.js**:

<<(amd/apple-home.view.js)

**apple.view.js**:

<<(amd/apple.view.js)

**apples.js**  

<<(amd/apples.js)

I hope you can see a patern by now. All our code is split into separate files based on the logic (e.g., view class, collection class, template). Main file loads all the dependencies with **require** function. If we need some module in non-main file, then we can ask for it in **define** function. Usually in modules we want to return an object, e.g., in templates we return string and in views we return Backbone View class.

Try launching the examples. Visually there shouldn't be any changes. If you open Network tab in Developers Tool, you can see the difference in how files are loaded.

`amd/index.html`: <http://cl.ly/image/2W0I0O1q0V23>
`refactor/index.html`: <http://cl.ly/image/122X3q1M0P17>

Require.js has a lot of configuration options which are defined through **requirejs.config()** call in the top level HTML page. More information can be found at <http://requirejs.org/docs/api.html#config>.

Let add bust to our example. Bust argument will be appended to the url of each file preventing browser from caching the files. Perfect for development and terrible for production. :-)

Add this to the **apple-app.js** file infront of everything else:

```javascript
requirejs.config({
  urlArgs: "bust=" +  (new Date()).getTime()
}); 
require([
...
```

Network tab will look like this: <http://cl.ly/image/3W3U3v3D3p1t>. Please notice that each file has 200 instead of 304 (not modified).


## Require.js for Production

we'll used Node Package Manager (NPM) to install requirejs in your project folder. In your project folder run these commands in a terminal:

```bash
$ npm install requirejs
```

or add `-g ` for global installation:

```bash
$ npm install -g requirejs
```

Create file **app.build.js**:

<<(r/app.build.js)

For more information on build file check out this example: <https://github.com/jrburke/r.js/blob/master/build/example.build.js>.

Now everything should be ready for building one giagantic JavaScript file which will have all the dependencies:


```bash
$ r.js -o app.build.js
```
or

```bash
$ node_modules/requirejs/bin/r.js -o app.build.js
```
You should get list of files: <http://cl.ly/image/4123273V1806>.

Open index.html in build folder. The network tab shows much improvment with just one file to load:
<http://cl.ly/image/3l423v1C3o3r>.

For more information checkout official documentation: <http://requirejs.org/docs/optimization.html>.

Example code is available under `r` and `r/build` folders.

## Super Simple Backbone Starter Kit

To jump start your Backbone.js development consider using [Super Simple Backbone Starter Kit](https://github.com/azat-co/super-simple-backbone-starter-kit) or similar projects:

* [Backbone Boilerplate](http://backboneboilerplate.com/)
* [Sample App with Backbone.js and Twitter Bootstrap](http://coenraets.org/blog/2012/02/sample-app-with-backbone-js-and-twitter-bootstrap/)
* More tutorials <https://github.com/documentcloud/backbone/wiki/Tutorials%2C-blog-posts-and-example-sites>.
