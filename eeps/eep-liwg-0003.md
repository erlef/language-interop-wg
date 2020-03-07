    Author: Mariano Guerra <mariano(at)marianoguerra(dot)org>
    Status: Draft
    Type: Standards Track
    Created: 07-Mar-2020
    Erlang-Version: 24
    Post-History:
    Replaces: 
****
EEP LI-WG 03: Tagged Union Data Type
----



Abstract
========

This EEP proposes a new BEAM data type that supports data structures that
until now are implemented at the language level and "compiled out" of the
generated code, such as structs and tagged unions. This new data type should
allow all data types in existing languages to interoperate between languages
and provide runtime information about themselves.

Rationale
=========

Many programming languages have user defined data types, such as classes, objects, structs, tagged unions.

Some of those are implemented in languages that target the BEAM by compiling to data structures that the
VM supports and recognizing them at runtime either by its shape or by knowledge gattered at compile time.

This custom layouts and the loss of information at runtime makes it hard to interoperate between languages.

The proposal is for a new data type that will support structs and tagged unions as a single data type, where
a struct is simply a tagged union with one variant. It also proposes a new set of guards to check for information
about the type at runtime.

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
