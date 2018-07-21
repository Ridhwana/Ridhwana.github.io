---
layout: post
title: "Part 1: Setting up a basic react app with create-react-app"
description: "Learn with me how to use create react app basics"
date: 2017-06-08 11:59:20
comments: false
keywords: "react"
reading_time: '5 min'
show_excerpts: true
excerpt: "I've been investing a lot of time recently in using React and I'd like to share with you how to build a simple react app from the basics..."
categories: articles
---

I've been investing a lot of time recently in using React and I'd like to share with you how to build a simple react app from the basics. Since React does not follow strong setup conventions, there are many starter kits to assist in getting your first React app up and running. However, I firmly believe in starting simple and thereafter improving as one gets more familiar with a tool or library.

For this reason, I chose create-react-app to give me the most simplest folder structure with the bare necessities to get up and running.

The first step is to install ```create-react-app```. This can be done by following the documentation for [create-react-app](https://github.com/facebookincubator/create-react-app). It simply asks you to do the following to get started:


{% highlight shell %}
$ npm install -g create-react-app
$ create-react-app my-app
$ cd my-app
{% endhighlight %}

You will also need to ensure that you have a dependency management tool installed in order to install any dependancies that you may require to develop your app. My dependency manager of choice at the moment is  ```yarn```.

To install yarn, follow the [yarn documentation](https://yarnpkg.com/en/docs/getting-started), however if you using a mac it will be as simple as executing

{% highlight shell %}
$ brew install yarn
{% endhighlight %}

Once you have yarn installed, you can start your server by running:

{% highlight shell %}
$ yarn start
{% endhighlight %}

Thereafter navigate to ```http://localhost:3000/``` to see your application.

### What are we building?

We're going to start off with a basic app that contains a header and a main component that will render static data.


### Creating our header and main components

Once you've generated the react app, navigate to your app directory and let the fun begin!

In order to edit your home page navigate to src/App.js. You'll notice that this is the component that gets rendered when you start up your server. We know this because in the index.js we see the following code which indicates that the index route renders the App component.

{% highlight javascript %}
ReactDOM.render(<App/>, document.getElementById('root'));
{% endhighlight %}

We're going to amend the component slightly to start building out the layout for our application. Most applications, always contain a header and a main container. So, lets start by editing the App.js to create a header and main container. We'll be replacing the code in the render method within the App.js file to achieve our goal.

Lets start by removing the static text that create-react-app has generated for us, to make our component look more like this:

*src/App.js*

{% highlight javascript %}
import React from 'react';
import logo from './logo.svg';
import './App.css';

class App extends React.Component {
  render() {
    return (
      /* remove code here */
    )
  }
}

export default App;
{% endhighlight %}

You'll notice above that our App extends React.Component, which we import from the React package in the first line of the file.

React has introduced a simpler way to define stateless components using plain Javascript ES2015 syntax (read [the ffg article](https://hackernoon.com/react-stateless-functional-components-nine-wins-you-might-have-overlooked-997b0d933dbc) to understand the advantages of using this syntax instead of the class definition when working with stateless components.

Since we don't expect our Header to contain any state at the moment, we can define our header as a stateless component:

*src/App.js*
{% highlight javascript %}
const Header = () => (
  <header>
    <nav>
      <ul>
        <li>Home</li>
        <li>Store</li>
      </ul>
    </nav>
  </header>
)
{% endhighlight %}

The above code is simply a function that returns markup. The markup contains two navigation elements which we will link to in the next post when we introduce react router.

We can follow the same pattern for the Main Component. Please note, that it is a good practice utilise the stateless components only until such time when whereby there is no need to introduce state into a component. Thereafter we will be required to use a class that extends React.Component.

*src/App.js*
{% highlight javascript %}
const Main = () => (
  Welcome to your first react app!
)
{% endhighlight %}

Now that we have defined these two components, we can reference them within our App component, so that they render when we navigate to the index route.

We render them by referencing the components inside the App components render function. ```<Header \> ``` is the jsx syntax we use to reference the Header Component.

*src/App.js*
{% highlight javascript %}
class App extends React.Component {
  render() {
    return (
      <div>
       <Header/>
       <Main/>
      </div>
    )
  }
}
export default App;
{% endhighlight %}

Notice how we encompass the two components into a div, this is because React enforces that  adjacent JSX elements must be wrapped in an enclosing tag. This ensures that we are only returning one element from our render function, thus ensuring that our jsx code gets transformed correctly.

All these components that we have defined can be simply placed in the App.js file for now. In a future post, we will discuss, importing files and folder structures.

For the code related to the post please see this [JSBin](https://jsbin.com/hasajef/8/edit?html,js,output)
