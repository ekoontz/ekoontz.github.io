---
layout: post
title: "The Joy of Clojail"
category: clojure
tags: [clojure, security]
---
{% include JB/setup %}

I'm glad to have learned enough
[clojail](http://github.com/flatland/clojail) to use it
practically. Below is a short file that demonstrates its
capabilities. 

Clojail lets you safely* embed a Clojure REPL inside an untrusted UI (for
example, a web browser). It comes to us courtesy of [Anthony
Grimes](http://github.com/iorayne), whom I saw [present this work in 2011](http://clojure-conj.org), and
[the flatland crew](https://github.com/flatland?tab=members).

* The Clojail authors [explicitly do not
promise](https://github.com/flatland/clojail#warning) 
that clojail is entirely secure.

It's great that it exists because Clojure really shines in its
abililty to embed Clojure repls in remote applications, such as
[4clojure.org](4clojure.org).

4clojure is an amazing application that I think heralds a new era of
repl-based web interfaces. Teaching Clojure through examples, like
4clojure does, is one such application, but one can imagine many
others.

As an API, Clojail lets you specify a `tester`, which is a list of a
set of symbols, a set of objects, a set of packages, and a set of
namespaces. These are all sets of entities that are
"blacklisted". These symbols are denied access within the sandbox that
you are specifying: attempting to access them throws a `SecurityException`.

 The usage of testers and the syntax of the blacklists may seems a bit
complex at first. Hopefully my example below is enough to get over the
initial confusion that I, at least, felt. A good source of examples is
[`testers.clj`](https://github.com/flatland/clojail/blob/master/src/clojail/testers.clj)
in the Clojail source code. I speculate that the usage of `testers` as
a first-class term outside of the environment of the standard `test/`
directory gradually evolved as testers became recognized.

The trick for you as an application programmer and security auditor is
figuring out exactly *what* set of symbols, objects, etc., to
blacklist. It's better to err on the side of specifying too many
things than too few - better to have to deal with usability problems
than exploits. 

I wonder whether one could specify a whitelist rather than a
blacklist. Perhaps one could transform a whitelist into a blacklist,
thus ensuring API compatibility with the existing clojail
library. This would work if one had a Universal list: a list of all
symbols, packages, etc. If so, one could generate a whitelist by
simply taking the set difference between the universal set and the
given whitelist. Now that I think about it, though, such a Universal
list (and the blacklist) would be enormous. It might be better looking
into how to add explicit whitelists instead within Clojail.

If you know of other web-based Clojure repls, let me know in the
comments, email me at [ekoontz@hiro-tan.org](mailto:ekoontz@hiro-tan.org) or 
[Twitter](http://twitter.com/hadooplearnings).

<script src="https://gist.github.com/4489368.js"></script>
