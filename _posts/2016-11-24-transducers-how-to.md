---
layout: post
title:  "Learning Clojure: Transducers how-to"
date:   2016-11-24
tags:
 - clojure
 - transducer
categories: update
---

Clojure 1.8 introduced transducers to the world. They are quite an interesting idea that has 
many possible uses; but as they are very flexible and have a scary name, it is sometimes
hard to get started with them.

# So - what are transducers?

Transducers are funny "objects" that receive values, one by one, and for each value "in" can return
zero or more values "out". They decouple the logic of computing new values from the logic that iterates through
a set of them and feeds the transducers - so they don't really care if it's a lazy sequence, a vector,
a channel or whatever you want.

# And why should I care?

This means transducers can.....

* work like a "map"
* work like a "filter"
* work like a fancy "reducer"
* filter-and-aggregate elements
* be composed together without a need for intermediate sequences
* work on anything, be it a sequence or a `core.async` channel

# Writing your first transducer

Transducers have a fixed form: and this is what you need to copy and paste to get started.

{% highlight clojure %}

(defn template-transducer [xf]
  (fn 
    	;; SET-UP
    	([] 	         (xf))
      	;; PROCESS
    	([result input]  (xf result input))))
      	;; TEAR-DOWN
    	([result]        (xf result))

{% endhighlight %}

This is the basic form:

* A function of an external function "xf" - you don't know and do not care about it. This is what 
  makes them composable. This function allows composition - you call it to "move forward" with  processing.
* A zero-arity call: starting a processing chain
* A two-arity call: receives a current result and a value to add to it. Returns a call to "xf"
  passing the current result and (usually) some transformation on the input. If you don't want to 
  return any new value, return "result" as it is.
* A one-arity call: transducer is terminating. Return an application of "xf" to the final result,
  or whatever the "result" should be.

As you can see this is very simple, but you're left wondering. But practice makes perfect, so 
let's start.

So, say we want to write a transducer that retains only odd numbers in a sequence, and returns them
incremented. Something like:

{% highlight clojure %}

(->> (range 10)
     (filter odd?)
     (map inc))

; '(2 4 6 8 10)
{% endhighlight %}


So here is what we do:

{% highlight clojure %}

(defn plain-filter-odd [xf]
  (fn 
    	;; START
    	([]          (xf))
      	;; STOP
    	([result]    (xf result))
      	;; PROCESS
    	([result input] 
         (cond 
           (odd? input) (xf result (inc input))      ;; apply xf to result adding 1 to input
           :ELSE        result )                     ;; do nothing - return result
           )))


(into [] plain-filter-odd (range 10))
; [2 4 6 8 10]
{% endhighlight %}

In this case we do not need the set-up and tear-down parts, so we are basically write some logic only 
in the two arity version. Look how we return "result" as-is if we do not want to change it, while we 
"return" values by calling xf with a (new) intermediate value.

# Using transducers

There are a few simple ways to "run" transducers:

* `into`: as the vanilla version does, appends to an existing collection the transducer applied to a collection
* `transduce`: applies transducer to a collection, and then using a "reducing" function to create our final result
* `sequence`: returns a new sequence by applying the transducer to a sequence. This sequence is lazy, so if it's
not needed, it's not computed either. 
* `educe`: captures the transduction process into a function.

{% highlight clojure %}
(into [] plain-filter-odd (range 10))
; [2 4 6 8 10]

(transduce plain-filter-odd conj (range 10))
; [2 4 6 8 10]

(sequence plain-filter-odd (range 10))
; '(2 4 6 8 10)

{% endhighlight %}

Note that as you have no idea of how many items of the source sequence 
need to be consumed to produce your final sequence, there is no 1:1 equivalence.


# Duplicating values

As we said above, you can return 0 or more values from a transducer:

* To return no new values, just return "result" as it is
* To return one value, return "(xf result value)"
* To return multiple values, just call "(xf result value1)", then "(xf result value2)", and so on.

To show how thos works, this transducer duplicates odd values and removes even ones:

{% highlight clojure %}

(defn duplicates-odd-vals [xf]
  (fn 
    	([] (xf))
    	([result] (xf result))
    	([result input] 
         (cond 
           (odd? input) 
           	(-> result               
              (xf input)             ; first odd value
              (xf input))            ; second odd value
           :ELSE        result ))))  ; do nothing

(transduce duplicates-odd-vals conj (range 11))
; [1 1 3 3 5 5 7 7 9 9]
{% endhighlight %}



# Stateful transducers

Having the ability to control when a value is emitted is especially 
important for trasducers that must keep track of a current (inner) state. 

Our transducer will return a sequence of odd numbers intertwined with their unique 
sequence number for each item in our sequence, starting from 100 onwards 
(we count all numbers for a change).

{% highlight clojure %}

(defn stateful-transducer [xf]
  (let [state (atom 100)]
	 (fn 
    	([] (xf))
    	([result] (xf result))
    	([result input] 
         (swap! state inc)
         (cond 
           (odd? input) 
	           	(-> result
	              (xf input)
	              (xf (str "S:" @state)))
	           :ELSE        result )                     ;; do nothing - return result
         ))))

(transduce stateful-transducer conj (range 10))
;[1 "S:102" 3 "S:104" 5 "S:106" 7 "S:108" 9 "S:110"]
{% endhighlight %}

To do this we use an atom (or possibly a transient) that we use as a 
keeper of current state. See how this atom is unique per function
invocation, so it's local mutable state (and how this starts to look a bit like
an "object" encapsulating state).

Of course, a stateful transducer depends on its internal state and is not
inherently parallelizable if run in parallel, so beware!

# Transducers as reducers

You can easily turn a stateful transducer into a reducer, just like this:

{% highlight clojure %}
(defn reducer-transducer [xf]
  (let [state (atom 1)]
	 (fn 
    	([] (xf))
      	([result] (xf @state))
      	([result input] 
         (swap! state #(* input %))
          result ) )))

(transduce reducer-transducer conj [2 3 7])
; 42
{% endhighlight %}

See how we basically ignore result and use a result of our own making at the 
end of the eduction.


# Parametrized transducers

You can easily create your own parametrized transducers with "partial",
so that you capture all parameters but "xf":

{% highlight clojure %}

(defn string-transducer [prefix xf]
  (fn 
    ([] (xf))
    ([result] (xf result))
    ([result input] 
		(xf result (str prefix ":" input)) )))

(transduce (partial string-transducer "x") conj (range 5))
; ["x:0" "x:1" "x:2" "x:3" "x:4"]
{% endhighlight %}

# Composing transducers

One of the best things abut transducers is that they are composable;
you can create a new transducer out of a set of existing ones. 

This is more efficient than mapping/filtering over multiple sequences
in turn.

{% highlight clojure %}
(transduce (comp (partial string-transducer "B") 
                 (partial string-transducer "A"))
           conj 
           (range 3))

; ["A:B:0" "A:B:1" "A:B:2"]
{% endhighlight %}

See how B was applied first, and then A.

# Putting it all together - separating text paragraphs

We want to create a transducer that receives a sequence of
lines (maybe over a core.async channel) and separates them
into paragraphs. 

This is a typical stateful transducer, and something that 
we cannot do easily with just map and filter.

{% highlight clojure %}
(defn line-wrapper [xf]
  (let [state (atom [])]
    (fn 
      ([] (xf))
      ([result] 
       	(let [final-result (xf result @state)]     ; (A)
          (xf final-result)))                      ; (C)
      ([result input]
        ;; if empty line, push accumlated lines at once
       	(cond 
          (= input "") 
			  (let [v @state]
              (reset! state [])
              (xf result v))                       ; (B)
        
          :ELSE 
			   (do 
            	(swap! state #(conj % input))
            	result) )))))       
       
(transduce line-wrapper 
           conj 
           ["A" "B" "" "C" "" "" "D" ""])
;[["A" "B"] ["C"] [] ["D"] []]
{% endhighlight %}

In this case it is important that if we have open stanzas when the sequence terminates,
we reprocess them (see A, and see it's the very same thing we're doing in B) and then we generate the 
final result, that will in turn be sent down the pipeline (C).

This is pretty cool, as a lot of problems can be expressed in a way that  is similar 
to this; and IMHO this alone makes transducers worth learning.

# But the  very best thing about transducers is.....

The really best thing about transducers is that (often) you don't need to write them.
If you had to write transducers from scratch every time like in the examples above,
they would be still  be cool,  but in 99%  of the cases you would prefer the old
map/filter/reduce - it might not be as efficient or powerful, but is concise and compact
and easy to read.

Truth is that you can have the best of both worlds - you can use your classic
friends map&co. and use transducers. This is because many functions in core that used to 
have a collection as their last parameter now have an arity that produces a transducer
by just omoiiting that collection. So if you say:

{% highlight clojure %}
(map inc)
{% endhighlight %}

you get a transducer for free. In Clojure 1.8, it's actually the other way around - when you run
your plainest Miss Apple Pie function:

{% highlight clojure %}
(map inc some-seq)
{% endhighlight %}

what Clojure internally does is:

{% highlight clojure %}
(sequence (map inc) some-seq)
{% endhighlight %}  


So you can use transducers everywhere and create highly-efficient and easy to read pipelines
by composing some custom transducers and plenty of common Clojure functions, like in:

{% highlight clojure %}
; Remove empty stanzas
(transduce (comp 
            	line-wrapper
            	(filter (complement empty?)) )
           conj 
           ["A" "B" "" "C" "" "" "D" ""] )
; [["A" "B"] ["C"] ["D"]]
{% endhighlight %}

In the example above, for example, we could add a check within our newly created transducer `line-wrapper` 
that makes sure that if we have multiple empty lines,
then only the first one is counted. Sure, it would be easy. But filtering in the pipeline is easier.

{% highlight clojure %}
; Remember: order counts! This is likely *not* what you want.
(transduce (comp 
				(filter (complement empty?))
				line-wrapper)
           conj 
           ["A" "B" "" "C" "" "" "D" ""] )
; [["A" "B" "C" "D"]]
{% endhighlight %}

# Final thoughts

Transducers take a while getting used to; so make sure you open your REPL and experiment a bit.

The following functions have a transducer arity:  `map` `cat` `mapcat` `filter` `remove` `take` `take-while` `take-nth` `drop` `drop-while` `replace` `partition-by` `partition-all` `keep` `keep-indexed` `map-indexed` `distinct` `interpose` `dedupe` `random-sample`. 

By looking at their source, they can be used as a reference as hot to write your own transducers if you get lost.



# See also

* [http://clojure.org/reference/transducers]()

