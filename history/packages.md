# Documentation

https://web.archive.org/web/20120421035749/http://erlang.org/doc/man/packages.html

http://www.erlang.se/publications/packages.html

[Report](http://www.it.uu.se/research/publications/reports/2000-001/)

# 2001

[Richard Carlsson announces packages and Report on the Erlang Mailing List](http://erlang.org/pipermail/erlang-questions/2001-March/003017.html)


# 2003

## Package support in Erlang: Status ?

http://erlang.org/pipermail/erlang-questions/2003-April/008572.html

http://erlang.org/pipermail/erlang-questions/2003-April/008576.html

```
So, if you want to move code from the current "flat" namespace into a
package namespace, you have to:

        1. rename the module, by prefixing a package name
        2. add an '-import(Mod)' for each non-package Mod that is being
called.

and that's it. Note that existing code can call package modules without any
changes - for example, you can give a module name as
'com.bigcorp.mycallback'
to the good old gen_server, and everything works just as normal.

```

http://erlang.org/pipermail/erlang-questions/2003-April/008585.html

```
the package concept does solve the problem of module
name space -- I find that a great relief.
```

```
I have also privately suggested that Erlang allow "dotted atoms" (that is,
without single quotes) in other places as well, e.g. in registered names,
ets tables, etc. This would make package support feel a little less like an
afterthought, and more like an integrated part of the language.
```

http://erlang.org/pipermail/erlang-questions/2003-April/008609.html

```
There goes the dot - I'd have liked the dot for "proper" structs -
(fortunately there is still ^ and ?) 

/Joe
```

Good point about handling ambiguity with Elixir struct paths

http://erlang.org/pipermail/erlang-questions/2003-April/008587.html

```
the following two functions are not
equivalent in a "packaged" module foo.bar 
(try erlc -E):

-module(foo.bar).
-compile(export_all).

f1() -> m:f(X).

f2() -> Mod = m, Mod:f(X).

As I understand your papers, this is intentional. But
it also breaks referential transparency: one (compiler
or developer) can no longer just replace occurrences
of Mod with its value m (or in general, a variable
occurrence X with the value of X).

That spells needless semantic trouble for all
involved, in my experience. So, my suggestion is to
fix that undesirable feature interaction.
```

## Packages in Erlang: new documentation

http://erlang.org/pipermail/erlang-questions/2003-September/009783.html

The url http://www.csd.uu.se/projects/hipe/packages.html is not available and on internet archive all snapshots are 404 errors

http://erlang.org/pipermail/erlang-questions/2003-September/009786.html

```
There is just one problem, from my point of view: they force one to
import everything global (lists, io, etc.), or add a . in front of
these modules (I couldn't get import_all to work for the global
package). As I am sick of keeping track of #includes and imports, I
enjoyed this not being necessary in Erlang. And I did not much like
the look of code peppered with the . prefix (just a matter of taste).
```

http://erlang.org/pipermail/erlang-questions/2003-September/009788.html

```
The main points with a package system are:

	- you can use short, natural names for "local things"
	- you don't have to import local names

and my motivation was to not have to see any more horrible module
names such as:

	CosEventChannelAdmin_ProxyPushConsumer_impl
	oe_CosEventComm_CAdmin_impl
	sys_core_fold
	sasl_report_file_h
	orber_iiop_tracer_silent
	mnesia_frag_old_hash
	tool_file_dialog
	io_lib_fread
	inet_gethost_native
	hipe_sparc_ra_post_ls_fp
	hipe_x86_ra_ls_postconditions

(also the long file names - add a ".beam" to the above),
not to mention obscurities such as:

	pg2
	sofs
	c
	si
	rb
	i
	uri

(Hands up all of you who knew that these short names were already
in use, and extra points if you can tell me what applications they
belong to - no peeking!)

For instance, how is anyone supposed to know that the name
"tool_file_dialog" is already being used by the GS application,
so you can't use it in your new wonderful 'tool' application?

And why shouldn't I be able to use a local module called
"vector" just because that name is already used in stdlib?
I don't _want_ to have to name it "my_bloody_own_vector" (and
type that name each time I make a call to it).

Or reversely, why should the Erlang/OTP people not be able to
add a module called "array" (for example) to stdlib, just because
some customer already has a module by that name, and does not want
to change their code in order to upgrade to the latest OTP release?

I know that you can get a long way without a package system, but
at the same time, these naming issues have always been annoying me.


> And I did not much like the look of code peppered with the . prefix
> (just a matter of taste).

I absolutely agree: the . prefix notation is not recommended unless you
really need it. Using -import(Module) is much better. Maybe we should
also add a declaration -import_as(Module, Alias).

```

http://erlang.org/pipermail/erlang-questions/2003-September/009789.html

```
Yes, but it does not work in Erlang, because there your _cannot_
rename a module, since you cannot know if someone is going to
call it via apply(M, F, [...]) or spawn(M, F, [...]). It is not
possible to know in general what the M:s are, or even to which
M the caller is actually referring (the first M, or the later added
one that was renamed?)

```

http://erlang.org/pipermail/erlang-questions/2003-September/009791.html talks about module aliases `-import_as()`

http://erlang.org/pipermail/erlang-questions/2003-September/009796.html

```
I note that Haskell has moved from a flat module namespace to a
hierarchical module namespace comparatively recently.
Ada 95 introduced nested packages to Ada, with some interesting and
complex visibility rules.

I note that Common Lisp moved in the other direction:  the original package
design had nested packages, but the one adopted for the standard didn't.
Similarly, the original book on SETL had a complex nested package system,
but that was later dropped.
```

```
In short, from the inside, there appears to be no significant difference
between a module name like foo_bar_baz and a module name like foo.bar.baz.

That's the first thing that bothers me:  language that confuses me by
talking about packages when there are only dotted module names.

The second is that there is a fixed relationship between a module
name and a set of places to look for it.  This has proven to be a real
pain in Java, where you want a development version of a package and
a stable version, but because they are versions of the same package,
they get the same mapping onto directories.

Amongst other extremely nasty consequences, as far as Erlang is
concerned, 'forx' and 'forX' are two different module names,
but on some file systems (Windows) they must map to the same file
name.
```

```
Erlang is one language, with one set of rules about what can be a symbol.
ANY symbol can in principle be used as a module name.  But there are
several file systems:  UNIX has one set of rules for what can be used
in a file name ('/' may not, '\\0' may not, but any other character may
be), Windows has another set of rules, RiscOS has yet another set of
rules, other operating systems have yet other rules.  Since the set of
operating systems is open-ended, if you insist on mapping Erlang module
names directly to file names, NOBODY CAN EVER KNOW WHAT THE SET OF
PORTABLE ERLANG MODULE NAMES IS!  Nobody can even know how long an
Erlang module name may be:  is the limit 8 or 10 or 27 or 251 or what?

The only programming languages I've seen where this problem is
satisfactorily addressed are Common Lisp (the "defsystem" facility,
although it didn't make it into the standard) and Eiffel (the "LACE"
facility, which I \_hope\_ will make it into the ECMA standard for Eiffel).

Let me put this as bluntly as I possibly can:
    While there may be a default mapping from Erlang module names
    to files, it must be possible for someone installing a module
    or package of modules to put each file exactly where s/he wants
    without \_any\_ constraint on how files are named.
    
Amongst other things, as soon as you step outside ASCII, it's entirely
possible that the file system used by the author of a module will encode
characters one way (Latin-1, say) while the file system used by the
installed may encode characters another way (UTF-8, say).


There are a couple of other things that bother me, but those are the
big ones.  I've always regarded the mapping from module names to file
names as a short-term makeshift, and with the introduction of dotted
names the idea of a fixed mapping from module names to file names is
now very definitely an idea whose time has \*GONE\*.  (The possible
loopholes caused by a search path make me queasy.  That's an idea whose
time has gone too.)
```

http://erlang.org/pipermail/erlang-questions/2003-September/009799.html

```
I'm not thrilled by the idea of tying module names to directory
structure closer than before. I have yet to come up with a better 
scheme, though.
```

http://erlang.org/pipermail/erlang-questions/2003-September/009820.html

```

(1) Module names must be completely decoupled from file names.
    [This does not mean that you cannot have a _default_ mapping
    from module names to file names, only that you cannot have a
    _fixed_ mapping.]

(2) It is a bad idea to introduce names that don't actually name
    anything.

    Erlang modules are somewhat anomalous in the language.  There really
    are module "objects", with many properties, but there are not module
    "values".  If you don't believe that modules are objects in Erlang,
    take a look at the 'c' and 'code' modules, and you'll see what I mean.
    While you cannot ask a module *itself* for its properties, you can
    ask the Erlang system for the properties of a module.

    It is possible to have a system of hierarchical modules.  That's
    how Pop-2 does it, and that's how Ada does it, and that's how Mercury
    does it.  In those languages, modules may contain types, functions,
    _and other modules_.

    In contrast, this package proposal introduces a class of names
    that do not name anything.  Where are the operations to
        - list all loaded packages
        - list all available packages
        - list the contents of a package
        - load a package
        - delete a package
        - purge a packge
        - ensure that a package is replicated on another node
    and so on?

    These operations may exist, but I didn't see them in the document
    we were asked to look at.  If they do, I would be interested in
    reading a fuller version of the document.

(3) Having packages that do not package is unhelpful.

    Java is notorious for this:  packages are accessibility scopes,
    but anybody can sneak new classes and subpackages into a package
    without the package having any say about it.

    This is really a fundamental problem with Java packages, and the
    scheme under consideration is far too Java-like for my taste.

    If there are to be packages, then a package should be able to list
    its children and insist that no other purported children are legitimate.

(4) A satisfactory solution to the "package" problem must deal with the
    issues of
    - including a module at more than one place in the space of packages
    - including more than one version of a module in a complete system
    If you consider these issues, you discover that the one thing that
    a module must NOT do is to know its exact place in a hierarchy of
    packages.

(5) For large scale systems, the rules for binding modules together
    have to be outside the modules.

    For a long time SASL felt wrong to me:  I thought most of the
    information in SASL files belonged in the modules.  But then I
    studied LACE, and understood what problems the LACE design was
    trying to solve, and realised that I was wrong about where the
    information belonged.  It's also worth looking at the
    configuration/distribution language that was developed for MESA.

(6) Global name spaces are a pain for really large systems.
    The global process registry (ANY global process registry) is
    an idea whose time has gone.
    The global module registry (ANY global module registry, whether
    module names are simple or dotted) is also an idea whose time
    has gone.

    Yes, this means that apply/3 and spawn/3 and so on need rethinking.
    Now that Erlang has closures, there are alternatives...

(7) JAM was not the last word in Erlang implementations.
    BEAM will not be the last word in Erlang implementations.
    "BEAM does so-and-so in such-and-such a manner" may decide what
    designs are easy to experiment with atop BEAM, but must not be
    allowed to dictate which designs are considered at all.
```

http://erlang.org/pipermail/erlang-questions/2003-September/009851.html

```
The thing that doesn't scale is the global namespace for modules.
Packages postpone the collapse, but do not prevent it; they are
*still* a global namespace for modules.  They just plain don't go
to the root of the problem.
```

# 2006

Package Support/Use thread on ML

http://erlang.org/pipermail/erlang-questions/2006-November/023770.html

# 2007

packages woe

http://erlang.org/pipermail/erlang-questions/2007-March/025550.html

# 2009

package namespaces question

http://erlang.org/pipermail/erlang-questions/2009-November/047444.html

# 2010

Package Support/Use thread on ML

http://erlang.org/pipermail/erlang-questions/2010-May/051274.html

Stack Overflow Question: 

Why aren't erlang packages used? 
https://stackoverflow.com/questions/4503131/why-arent-erlang-packages-used
