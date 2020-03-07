    Author: Mariano Guerra <mariano(at)marianoguerra(dot)org>
    Status: Draft
    Type: Standards Track
    Created: 07-Mar-2020
    Erlang-Version: 24
    Post-History:
    Replaces: 
****
EEP LI-WG 02: Value Metadata
----



Abstract
========

This EEP proposes a standard way to attach metadata to any value, allowing
to have information about data at runtime that at the moment is only available at
compile time and only for the language that is being compiled.

Rationale
=========

Many language design problems are solved at the compiler level instead of
solving them at the VM level, this means that compilers translate features
in languages into data structures supported by the VM but lossing the information
at runtime.

Over the years this has generated problems like:

* Checking if a list is a string or an IO list
* Checking if a binary is a utf-8 or latin-1 encoded string
* Knowing if a tuple is a record
* Inhability to do generic serialization/deserialization of records at runtime
* Inhability to do generic manipulation of records at runtime
* Attaching extra functions to a module to track metadata about something else in the module
* Duck-type checking, that is, assuming something is of a type if it has a similar shape
* Carrying metadata in "special fields": `__struct__` field in Elixir, `__meta__` field in Ecto

Some of them could be solved by the introduction of a new data type that 
allows to support structs and tagged unions (See `eep-liwg-0003`) but others will still persist.

This EEP proposes the introduction of an API to attach and retrieve metadata from values 
and their corresponding guard functions.

API Draft
---------

The API follows the `maps` API when possible

### get(Key, Data) -> Value

Returns metadata value `Value` associated with `Key` if `Data` contains metadata `Key`.

The call fails with a {badkey,Key} exception if no value is associated with Key.

### get(Key, Data, Default) -> Value | Default

Returns metadata value `Value` associated with `Key` if `Data` contains metadata `Key`, `Default` otherwise.

### get(Data) -> Values

Returns all metadata associaged with `Data` as a map.

### put(Key, Value, Data1) -> Data2

Associates `Key` with value `Value` and inserts the association into the metadata of `Data2`. 
If key `Key` already exists in `Data1`'s metadata, the old associated value is replaced by value `Value`. 

The function returns a new map `Data2` containing the new association and the old associations in `Data1`.

### remove(Key, Data1) -> Data2

Removes the `Key`, if it exists, and its associated value from `Data1`'s metadata and returns a new value `Data2`
without key `Key`.

###

See Also
--------

* [Clojure Metadata](https://clojure.org/reference/metadata)
* [Elisp Symbol Properties](https://www.gnu.org/software/emacs/manual/html_node/elisp/Symbol-Properties.html#Symbol-Properties)
* [R Data Attributes](http://adv-r.had.co.nz/Data-structures.html#attributes)

TODO
----

[] Introduction of "well known" keys?
[] Split between "system" values and "user" values?
[] Have readonly values instead of system values?
[] Define how equality works on values that are equal but have different metadata

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
