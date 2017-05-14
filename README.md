# Welcome to Calliope

Calliope is a purely functional web application framework, using Clojure and ClojureScript.
It's highly inspired by [Elm](http://elm-lang.org/).

## The Programming Model

Single-page web applications are not general purpose applications. The browser defines, that a web application is started once (e.g. when the page loads), interacts with the user via DOM, and never ends in the sense that it would complete its work. Also, web application mostly do any work only upon request of the user, e.g. a 'click' or other input. Additionally, most applications want to react to asynchronous responses from server communication, unsolicited push notifications, or timed animations.

The programming model calliope enforces, embraces that concept, by letting the user implement a few pure functions based on a single application state, the model, which changes over time.

### Your Application Model

Your application model can be any date structure that suits your needs. The built-in clojure data structures are highly recommended as they are immutable and persistent. Usually, you will define an initial model for your application in your source code:

```clj
(def my-initial-model "foobar")
```

### Doing your application work

In the Calliope application model, your application can only make progress by changing your model in response to *messages*. A message can be any value except `nil`. Usually, you will define a pure update function like this:

```clj
(defn my-update [model message]
  (case (first message)
    :set-text (second message)
    (assert false)))
```

### Your Application View

The view of your application defines the user interface for a given model, consisting of visual dom elements and the interactions a user can make via them.

The view can be implemented with various libraries and the `calliope-core` library actually does not include an implementation. We recommend Orpheus, which was developed alongside Calliope, that allows to define the user interface as a purely functional virtual dom. Using Orpheus, you will usually define a pure view function like this:

```clj
(defn set-text-message [ev]
  [:set-text (.-value (.-target ev))])

(defn my-view [model]
  (html/input {:type "text" :value model :onInput set-text-message})
```

In order to use Orpheus, you need to include the `calliope-orpheus` library, and define an *orpheus canvas* for your application:

```
(def my-canvas (orpheus/canvas my-view))
```

### Your Application can change the World

Hardly any applications lives in its own, closed world. You will usually want to manipulate some browser storage, or communicate with one or more servers. In Calliope all those side-effects are kept out of your pure application functions, by encapsulating them in *commands*. They are only ever actually executed by the application engine. You only instruct it to do so, with your `update` function introduced above.

Besides returning an updated model, your `update` function can also return one or more commands. You will usually add commands with the `model->` macro, which works a bit like clojure's threading macro:

```
(defn my-update [model message]
  (case (first message)
    :set-text (second message)
    :store-text (model-> model
                  (add-cmd (send-input-to-server model)))
    (assert false)))
```

The `calliope-basics` library already contains a lot of useful commands, but the underlying protocol can be extended for various other forms of side-effectful actions.

### The World can change your Application

The last part of the Calliope application model are *subscriptions*. Your application can subscribe to various *facilities*, which may send unsolicited messages to your application. For example this can be a timer, which sends you a message every now and then, or a websocket channel over which a server informs you of some event.

You will usually define your subscriptions in a separate pure function, which may return a different set of subscriptions depending on your current model:

```
(defn my-subscriptions [model]
  [sub-1 sub-2])
```

The application engine will take care of all side-effectful registrations and de-registrations needed when your functions returns something new.

The `calliope-basics` library already contains a lot of useful subscriptions, but the underlying protocol can also be extended for various other forms of sources.
