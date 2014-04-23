## Problem Statement

I'm perpplexed by the behaviour I see here.

Try the following:

1. Run `lein repl`
1. Try to `(require 'compojure.handler)` -- you should get the following error:

    ```CompilerException java.lang.IllegalAccessError: assoc-conj does not exist, compiling:(ring/middleware/multipart_params.clj:1:1)```

1. Now comment out the dependency on ring-proxy in project.clj (line 8) and try it
again, note that there's no error.

**How is it that ring-proxy is interfering with the ability to require
compojure.handler?** I've noticed that the ring-proxy jar contains 1.3MB worth of
stuff, it seems to me like a ~60 line clojure lib should produce a much smaller
jar, but I'm at a loss to explain why this happens.

## Explanation

This is apparently because of AOT (ahead of time) compilation. By default, when you compile a clojure library, you'll just end up with a .jar file that depends on the clojure runtime, and contains a bunch of .clj files. However, if you specify a main method (as ring-proxy has done [here]( https://github.com/tailrecursion/ring-proxy/commit/85741f27150aec234347353ce3b490e68679d7dd)), you'll get a bunch of funky .class files instead. It looks to me like there is some transitive AOT compilation happening, and we're also getting class files for the dependencies of ring-proxy. 

The whole thing is complected by a couple of other factors:

* In this project we're depending on compojure 1.1.6, which depends on ring 1.2.1, but ring-proxy depends on ring 1.1.8.
* In ring 1.1.8, assoc-conj was in ring.util.data, in ring 1.2.1 it was moved to ring.util.codec

What happens is that when we depend on ring-proxy, even if we try to exclude the ring dependency, some ring 1.1.8 code is included because of the (transitive?) AOT compilation, which means that assoc-conj isn't in the namespace where expect to find it.

I don't think I'm completely clear of all of the details of what's going on here yet, but I think I've got the gist of it.
