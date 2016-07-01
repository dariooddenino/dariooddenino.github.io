---
layout:     post
title:      Clojure Examples
date:       2016-02-12 23:32:11
summary:    
---

I've just written a small library for myself that could be useful to anyone who's learning Clojure: [Examples](https://github.com/dariooddenino/examples).

It prints examples for the function passed as argument, taking them from [clojuredocs.org](http://clojuredocs.org).

The library is on [Clojars](https://clojars.org/examples), so to use it just add the dependecy to your lein `project.clj`:

```clojure
(defproject ...
    :dependencies [[examples \"0.3.0\"]]}
```

Then add it to your requires:

```clojure
(:require [examples.core :refer [examples clear-examples-cache])
```

The usage is pretty simple: just call `(examples function)` in your REPL, and you'll get all the examples printed.

E.g. `(examples map)`

```clojure
-----------------------------------------------
clojure.core/map
-----------------------------------------------
(map inc [1 2 3 4 5])
;;=> (2 3 4 5 6)

;; map can be used with multiple collections. Collections will be consumed
;; and passed to the mapping function in parallel:
(map + [1 2 3] [4 5 6])
;;=> (5 7 9)

;; When map is passed more than one collection, the mapping function will
;; be applied until one of the collections runs out:
(map + [1 2 3] (iterate inc 1))
;;=> (2 4 6)

;; map is often used in conjunction with the # reader macro:
(map #(str \"Hello \" % \"!\" ) [\"Ford\" \"Arthur\" \"Tricia\"])
;;=> (\"Hello Ford!\" \"Hello Arthur!\" \"Hello Tricia!\")

;; A useful idiom to pull \"columns\" out of a collection of collections. 
;; Note, it is equivalent to:
;; user=> (map vector [:a :b :c] [:d :e :f] [:g :h :i])

(apply map vector [[:a :b :c]
                   [:d :e :f]
                   [:g :h :i]])
;;=> ([:a :d :g] [:b :e :h] [:c :f :i])

;; From http://clojure-examples.appspot.com/clojure.core/map

;;-----------

user=> (map #(vector (first %) (* 2 (second %)))
            {:a 1 :b 2 :c 3})
            ([:a 2] [:b 4] [:c 6])
user=> (into {} *1)
{:a 2, :b 4, :c 6}

...
```
 
 Cache can be cleared with `(clear-examples-cache [fun])`. If name is omitted, the entire cache will be cleared.
 
 I hope it will useful to someone!