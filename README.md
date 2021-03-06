# esbrowse
Use esprima parse and escodegen generate to get better line numbers in JS runtime errors by deminifying

## Purpose

javascript runtime errors with real line numbers even in minified code

One way that minified javascript gets in the way of debugging is that the line
number for a runtime will be reported as "line 1" or "line 3", where line 1 could
go on for pages.  So this makes it harder to understand what is happening
and the gross, unclear, opaque quality of this code suggests metaphors with tossed
cookies of the genus canine...

If you run javascript code through the esprima parser library, it turns the code
into an Abstract Syntax Tree stored in an object.  If you run the object through
escodegen.generate, it turns an abstract syntax tree isomorphically/losslessly
back into identical javascript code, but with linefeeds.  So if you do the two
in succession, it is no longer minified.  (You won't get back meaningful symbols,
like long variable names, unfortunately.)

So the purpose of this kit is to put together something modular so that you aren't
interfering with startwindow.js.  It can be plugged in as needed,
recompile with it added, use it for an hour and then remove it before committing
anything to the real edbrowse.

## Contents

(1) es.js - contains esprima parse and escodegen libraries

(2) GNUmakefile.diff - a proof of concept where es.js in brought into the makefile
the same way as we do it with startwindow.js

(3) ebjs.c - modified to bring in the es.js code in the same way as startwindow

(4) infinite.html - example of a web page which contains something that we want to
track down, and also has minified code that hinders our work.
To avoid a catch-22 in trying to use edbrowse to work on code
that is causing an infinite loop in edbrowse, I added a throw statement to the
top of the code.  So it raises a runtime error immediately, the js
environment is still available, and you can go to jdb, whereas if edbrowse
went into an infinite loop and you escaped with ^C, you wouldn't be able to
to go to jdb anymore.

(5) README

## Suggested usage

- Use the diff as a guide to modifying the makefile
- Compile edbrowse with es.js brought in
- launch edbrowse
- b infinite.html
- jdb to go to jdb mode
- take document.scripts.length
- if there are a few files, echo them with document.scripts[n].data until you find
the one you care about
- Pass this to esprima and escodegen:
newCode = escodegen.generate(esprima.parse(document.scripts[n].data))
- Echo your new code, save it out using logging, etc.
- Or, you can eval(newCode) and maybe you will learn something you can use
- Cobble together a new infinite.html with the code with linefeeds
- Carry on with your research

## References

https://github.com/jquery/esprima

https://github.com/estools/escodegen/wiki/API

