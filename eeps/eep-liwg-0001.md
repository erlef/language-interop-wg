    Author: Mariano Guerra <mariano(at)marianoguerra(dot)org>
    Status: Draft
    Type: Standards Track
    Created: 07-Mar-2020
    Erlang-Version: 24
    Post-History:
    Replaces: 
****
EEP LI-WG 01: Language Compiler Hooks
----



Abstract
========

This EEP proposes a standard interface for BEAM Lang compilers to expose 
information during compile time to allow modules in other languages to
use that information to interoperate.

Rationale
=========

When interoperating with modules implemented in other languages, sometimes
extra information is required to compile valid and/or performat code.

For example functions with default parameters are not supported in the VM,
languages that implement them need to expose the default parameters to other
languages to allow the calls to pass the default values.

Struct-like data structures need to expose the default values for fields to
allow compiling struct literals with missing fields with the default values
at compile time and avoid having to initiaize them at runtime or create
invalid initializations.

To be able to have access to this information at compile time, it would be
useful to standardize an interface that all compilers expose to allow other
compilers in the project to access that information during compilation.

The interface could be based on Elixir's  [Compilation tracers ](https://hexdocs.pm/elixir/Code.html#module-compilation-tracers)

Copyright
=========

This document has been placed in the public domain.



[EmacsVar]: <> "Local Variables:"
[EmacsVar]: <> "mode: indented-text"
[EmacsVar]: <> "indent-tabs-mode: nil"
[EmacsVar]: <> "sentence-end-double-space: t"
[EmacsVar]: <> "fill-column: 70"
[EmacsVar]: <> "coding: utf-8"
[EmacsVar]: <> "End:"
[VimVar]: <> " vim: set fileencoding=utf-8 expandtab shiftwidth=4 softtabstop=4: "
