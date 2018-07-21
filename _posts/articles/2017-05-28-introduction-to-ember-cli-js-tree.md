---
layout: post
title: "Introduction to ember-cli-jstree"
description: "The nitty gritty details of hooking up ember-cli-jstree"
date: 2017-05-28 11:50:20
comments: false
keywords: "ember"
reading_time: '10 min'
show_excerpts: true
excerpt: "Recently, I'd been tasked with creating a sidebar. Sounds easy right? Wellllll not actually... let me elaborate..."
categories: articles

---


Recently, I'd been tasked with creating a sidebar. Sounds easy right? Wellllll not actually... let me elaborate.


### Background

This particular sidebar contains the following challenges:

- It contains a nested ui and data structure, three levels deep to be exact
- It requires ajax (or in my case Ember asynchronous calls) to three separate endpoints to receive the data (promises are the way to go to solve this issue)
- Lastly, and most importantly it requires a search input.



Now, usually search inputs are pretty easy right? Most definitely, but more so when they are implemented on a flat structure. However, my search is implemented on a **tree structure**, which in turn means that I need to give the user experience special consideration. To elaborate, if I search for a match on a word that is nested two levels deep on one path of a tree, and three level deeps on another path of the tree, I want the user to be able to see the path leading up to the matching keyword, as well be able see the the matched word being highlighted. So, basically just like this:

![ember-cli-jstreejs](/images/search.png)

After much frustration, my team and I discovered a very feature filled [jsTree jquery plugin](https://www.jstree.com/). And as a bonus, I found an [ember addon]( https://github.com/ritesh83/ember-cli-jstree) that wrapped the jquery plugin, and was perfect for my Ember project.

Now, ofcourse I did my mandatory checks on the ember addon, these include:

- is it being maintained? (i.e. when was the last commit and are issues being addressed on the github repo)
- is the addon being used? (i.e the stats on the [npm package](https://www.npmjs.com/package/ember-cli-jstree) )

I use these checks to determine whether I would receive the authors assistance should I encounter any issues, and also whether this addon would match any updates to Ember, and...... This addon passed the test!

The [author](https://github.com/ritesh83) of this addon provides some good documentation on the features exposed through the addon that map to the initial [jquery jsTree plugin](https://www.jstree.com/), as well as some [examples](http://ritesh83.github.io/ember-cli-jstree/#/static). Like with every addon, I use the docs to come up with a solution to solve my unique problem onhand. The capabilities of this addon turns out to be numerous, there is support for features like searching, state management, drag and drop, checkboxes, etc.,all on tree structures. However, for the introductory purposes of this post, I'll simply concentrate on rendering a tree and providing the user with the ability to search that tree whilst utilising some actions and events.

As mentioned above, the tree will contain a three level deep structure. It will contain companies at the root level, head-offices at level two, and branches at level three.

Lets get started!


### Installation

In order to use this ember addon, we install it using:

{% highlight html %}
$ ember install ember-cli-jstree
{% endhighlight %}

This will add the necessary npm and bower packages to our Ember app.
(note for newbies: restart your server after the installation is necessary).

Once installed we have access to a ember-js-tree helper/component. As the author mentions, the bare minimum required in order to configure a tree structure is to pass through the data attribute to the helper.


### Creating a tree structure with json data

ember-js-tree requires a very specific format to work with JSON. We define our root level nodes (i.e in our example 'companies') at the top level in the array.

{% highlight javascript %}
[
  { 'text': 'Company 1' },
  { 'text': 'Company 2' }
]
{% endhighlight %}

In order to add children for the root level nodes, we then expand the object to contain a key called ```children``` with the array of children nodes (at a second level nesting) which will serve as the 'headoffice' for each 'company'.

{% highlight javascript %}
[
  { "text": "Company 1",
    "children": [
      { "text" : "Headoffice"}
    ]
  },
  { "text": "Company 2",
    "children": [
      {"text": "Headoffice" }
    ]
  }
]
{% endhighlight %}

In the above example Company 1 and Company 2 contain one headoffice each. We then apply the same concept to provide a third level of nesting which is to map out the branches that are contained under each headoffice.

{% highlight javascript %}
[
  { "text": "Company 1",
    "children": [
      { "text" : "Headoffice",
        "children": [
          { "text" : "Branch 1" },
          { "text" : "Branch 2" },
        ]
      },
    ]
  },
  { "text": "Company 2",
    "children": [
      {"text": "Headoffice" }
    ]
  }
]
{% endhighlight %}

In the illustration above, Headoffice for Company 1 has two branches i.e Branch 1 and Branch 2, whilst Company 2 has no branches.

Now that we've figured out how our data will look, we need to set the data attribute in on our Ember Controller so that we can pass our data to the ember-js-tree helper.

*controllers/application.js*

{% highlight javascript %}
import Ember from 'ember';

export default Ember.Controller.extend({
  data: [
    { "text": "Company 1",
      "children": [
        { "text" : "Headoffice",
          "children": [
            { "text" : "Branch 1" },
            { "text" : "Branch 2" },
          ]
        },
      ]
    },
    { "text": "Company 2",
      "children": [
        {"text": "Headoffice" }
      ]
    }
  ]
})
{% endhighlight %}

Furthermore, ember-js-tree allows you to expose a state object to determine whether a particular node should be open, disabled, or selected. As an example, in order to open one of the root nodes ('Company 1') we add a state object on the root node containing the opened attribute set to true.

We are also able to set any additional attributes in the JSON through a_attr, as well as set our own custom id's at each node instead of using the default generated ember-js-tree one.

*controllers/application.js*

{% highlight javascript %}
import Ember from 'ember';

export default Ember.Controller.extend({
  data: [
	  {
      'text': 'Company 1',
	  	'id': 'company-1',
	    'state': {
	      'opened': true
	    },
	    'a_attr': {
	      'people': 100
	    },
	    'children': [
	      { 'text' : 'Headoffice',
	        'children': [
	          { 'text' : 'Branch 1' },
	          { 'text' : 'Branch 2' },
	        ]
	      }
	    ]
	  },
	  { 'text': 'Company 2',
	    'children': [
	      {'text': 'Headoffice' }
	    ]
	  }
	]
{% endhighlight %}

Once we have set a data property on the controller (or perhaps in the model since controllers may be deprecated in Ember), we can then pass the data attribute to ember-js-tree to render.

*templates/application.hbs*


{% highlight html %}
{% raw %}
  {{ember-jstree
     data=data
  }}
{% endraw %}
{% endhighlight %}



And, voila!, just like that (very simply) we have our tree structure.
Now that we have our data, the real ease of use comes in play with the search functionality.

### Using the search plugin

Integrating the search plugin is extremely straight-forward. We simply send through an intent to use the search plugin to the helper, and if required we can attach some searchOptions.

We also need to create our search input and provide it with an empty/default value.

*controllers/application.js*

{% highlight javascript %}
import Ember from 'ember';

export default Ember.Controller.extend({
  searchTerm: '',
  plugins: 'search',
  searchOptions: {
    'show_only_matches' : true
  },

  data: [
  ....
  ]

})
{% endhighlight %}

*templates/application.hbs*

{% highlight html %}
{% raw %}
<div class="ui icon input input--sidebar-clone__search">
  {{input type="text" placeholder="Search" class="prompt search-input form-control" value=searchTerm}}
  <i class="search icon"></i>
</div>

{{ember-jstree
   data          = data
   plugins       = plugins
   searchOptions = searchOptions
   searchTerm    = searchTerm
}}
{% endraw %}
{% endhighlight %}

### Calling events

Now, that we have our tree setup and we're able to search, we might want to use events to determine when a node is opened or closed. We can find a [list of events](https://github.com/ritesh83/ember-cli-jstree) in the documentation for the addon.

To illustrate events, when a user clicks on a node to open it, we will just show an alert message. In order to do so, we'd need to use the event ```eventDidOpen```. We pass this event to our ember-js-tree helper and provide a callback for it as follows:

*templates/application.hbs*

{% highlight html %}
{% raw %}
<div class="ui icon input">
  {{input type="text" placeholder="Search" class="prompt search-input form-control" value=searchTerm}}
  <i class="search icon"></i>
</div>

{{ember-jstree
  data                = data
  plugins             = plugins
  searchOptions       = searchOptions
  searchTerm          = searchTerm
  eventDidOpen        = 'handleJstreeEventDidOpen'
}}
{% endraw %}
{% endhighlight %}

*controller/application.js*


{% highlight javascript %}
import Ember from 'ember';

export default Ember.Controller.extend({
  ....

  actions: {
    handleJstreeEventDidOpen(e, data) {
      alert('opened node');
    }
  }
})
{% endhighlight %}

### Sending actions

Sometimes we may want to manually send an action to the tree to tell it to execute on behalf of the user. As an example, we may want a button on the interface to allow the user to close all nodes instead of him/her having to manually go through each node to close them.

We do this by using the ```actionReceiver``` property and sending along the intended action.

We need to register the action receiver on the plugin by setting the ```actionReceiver ``` property on the helper, and thereafter referance the corresponding ember action ```closeAllNodes``` from the button click as follows:


*templates/application.hbs*

{% highlight html %}
{% raw %}
<div class="ui icon input input--sidebar-clone__search">
  {{input type="text" placeholder="Search" class="prompt search-input form-control" value=searchTerm}}
  <i class="search icon"></i>
</div>

{{ember-jstree
  data                = data
  plugins             = plugins
  searchOptions       = searchOptions
  searchTerm          = searchTerm
  eventDidOpen        = 'handleJstreeEventDidOpen'
  actionReceiver      = jstreeActionReceiver
}}

<button {{action 'closeAllNodes'}}>Close all nodes</button>
{% endraw %}
{% endhighlight %}

*controller/application.js*

{% highlight javascript %}
import Ember from 'ember';

export default Ember.Controller.extend({

  .....

  actions: {
    handleJstreeEventDidOpen(e, data) {
       alert('opened node');
    },

    closeAllNodes() {
      this.get('jstreeActionReceiver').send('closeAll');
    }
  }
})
{% endhighlight %}

When clicking on the button, all nodes in the tree that were open will then be automatically closed.

In conclusion, I hope that this post is useful in terms of providing you with an idea on how to use the ember addon. This was simply an introduction to the addon, but a lot more is possible in terms of styling the tree and providing additional functionality. Please find all code for this post on [this Ember Twiddle](https://ember-twiddle.com/4d4d919fa716f94a6eb90bf3a239c6a0).

I'd like to thank the [author](https://github.com/ritesh83) for this great ember addon which has made my life so much easier, and my pairing partner Micheal who worked with me in order to integrate this setup.
