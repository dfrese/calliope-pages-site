# Welcome to Calliope

Calliope is a purely functional web application framework, using Clojure and ClojureScript.
It's highly inspired by [Elm](http://elm-lang.org/).

## The Programming Model

Single-page web applications are not general purpose applications. The browser defines, that a web application is started once (e.g. when the page loads), interacts with the user via DOM, and never ends in the sense that it would complete its work. Also, web application mostly do any work only upon request of the user, e.g. a 'click' or other input. Additionally, most applications want to react to asynchronous responses from server communication, unsolicited push notifications, or timed animations.

The programming model calliope enforces, embraces that concept, by letting the user implement a few pure functions based on a single application state, the model, which changes over time.

### Your Application Model

Your application model can be any date structure that suits your needs. The built-in clojure data structures are highly recommended as they are immutable and persistent. Usually, you will define an initial model for your application in your source code:

```
(define my-initial-model "foobar")
```

### Doing your application work

In the Calliope application model, your application can only make progress by changing your model in response to messages. A message can be any value except `nil`. Usually, you will define a pure update function like this:

```
(define my-update [model message]
  ...)
```

### Your Application View

The view of your application defines the user interface for a given model, consisting of visual dom elements and the interactions a user can make via them.

The view can be implemented with various libraries and the `calliope-core` library actually does not include an implementation. We recommend Orpheus, which was developed alongside Calliope, that allows to define the user interface as a purely function virtual dom. Using Orpheus, you will usually define a view function like this:

```
(define my-view [model] (html/input {:type "text" :onInput :set-text :value model})
```

### Your Application can change the World

TODO ...returning commands from update

### The World can change your Application

TODO ...subscribing to unsolicited messages



