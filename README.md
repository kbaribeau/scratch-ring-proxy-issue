# scratch-ring-proxy-issue

A Clojure library designed to ... well, that part is up to you.

## Usage

I'm perpplexed by the behaviour I see here.

Try the following:

Run `lein repl`
Try to `(require 'compojure.handler)` -- you should get the following error:

    CompilerException java.lang.IllegalAccessError: assoc-conj does not exist, compiling:(ring/middleware/multipart_params.clj:1:1)

Now comment out the dependency on ring-proxy in project.clj (line 8) and try it
again, note that there's no error.

How is it that ring-proxy is interfering with the ability to require
compojure.handler? I've noticed that the ring-proxy jar contains 1.3MB worth of
stuff, it seems to me like a ~60 line clojure lib should produce a much smaller
jar, but I'm at a loss to explain why this happens.


## License

Copyright © 2014 FIXME

Distributed under the Eclipse Public License, the same as Clojure.
