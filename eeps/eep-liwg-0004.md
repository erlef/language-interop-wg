    Author: Mariano Guerra <mariano(at)marianoguerra(dot)org>
    Status: Draft
    Type: Draft
    Created: 26-Jul-2020
    Erlang-Version: 24
    Post-History:
    Replaces: 

# EEP LI-WG 04: Runtime Records

## Abstract

This EEP proposes a runtime representation for [Erlang Records](http://erlang.org/doc/reference_manual/records.html)
with extensions to cover use cases in [Elixir Structs](https://elixir-lang.org/getting-started/structs.html)
and multiple incompatible Union Types/Tagged Tuples across BEAM languages.

## Rationale

Due to the lack of first class support for record types in the BEAM each
language, including Erlang, has its own runtime representation for them.

The lack of standard support on the BEAM makes interoperability harder by
having to know the internal representation for data created in other
languages and what language it is coming from.

It also adds extra code since other languages don't have the syntax available
and have to match the internal representation, since the representation is not
compact in the current language it may require writing encoding/decoding
functions to transform it to a native representation and then back again when
calling functions in the original language.

Some languages choose to follow Erlang and compile records to Tagged Tuples,
losing information at runtime or requiring others to load the record
definition to recover some of the lost information.

Another problem of records as Tagged Tuples is that they only keep the record
name and loose the module where they were defined, making it easy to clash
records with common names or have to resort to prefixing records if they mean
to be used by others.

Other languages choose to define their own representations, usually after maps
where available by tagging maps with the type information, while keeping most
of the information at runtime, there's no simple way to tell them apart from
maps other than looking for special fields in them.

An additional case that is solved in an ad-hoc fashion in dynamically typed
languages on the BEAM but has a strict definition in statically typed ones is
the introduction of Union Types.

Union Types allow a type to have more than one variant, this are really similar
to records and in some cases they are solved with a unified type/syntax like in
[Gleam's custom types](https://gleam.run/tour/custom-types.html), types like
`ok | {error, reason()}` or `{ok, value()} | error` or `undefined | {ok, Val}`
are cases that solve the same problem.

Given the fact that all statically typed languages on the BEAM have support for
Union Types and they are used in dynamically typed languages too, it makes
sense to add an extension to records to support them and at the same time
standardizing common variant return types seen in Erlang and Elixir codebases.

The support for Union Types in records adds the benefit of being able to do
exhaustive checking of case clauses when matching against record variants, even
in dynamically typed languages.

## Record Extension

### Namespace

To avoid name clashes and avoid the need to prefix record names, runtime
records have an extra field (namespace) that holds the module where they were
created.

### Variant

To support Union Types a runtime record can have more than one variant, in
case of records with only one variant the name of the record and the variant
is the same.

## Syntax

Syntax for runtime records is an extension to the map syntax and similar to the
record syntax, by adding the namespace, record name and variant after the `#`
and before the fields:

### Local Single Variant

```erl
% a record with name person, no variants defined in the current module
#person{name := "Joe"}
```

Notice that to differentiate from records the field assignment symbol is `:=`
instead of `=` like in current records and matches the meaning of `:=` in maps.

Like with records, fields that are not defined get default values provided in
the record's declaration.

### Local Multi Variant

```erl
% a local record with name option that has two variants: some and none
#option/some{value := Value}
#option/none{}
```

```erl
% a local record with name result that has two variants: ok and error
#result/ok{value := Value}
#result/error{reason := Reason}
```

### Remote Records

Using records defined in other modules only require prefixing the record name
with the module where they are defined:

```erl
#person:person{name := "Joe"}

#option:option/some{value := Value}
#option:option/none{}

#result:result/ok{value := Value}
#result:result/error{reason := Reason}
```

### Syntax Expansion

```erl
#record_name{...}
```

Expands to:

```erl
#ModName:record_name/record_name{...}
```

`ModName` is `__MODULE__` if record\_name is not included via [include](http://erlang.org/doc/reference_manual/modules.html#pre-defined-module-attributes),
if it is then the included module name is used.

### Potential Conflicting Syntax

If a local record with no variants and all default values is created the syntax
is the same as that of a record, since there's no field with `:=` to differentiate them.

The proposed solution for this is to disambiguate based on the modules' declarations.

### Syntax Extension: Default Variant

If a record has variants but no variant is specified, the compiler picks the
first one.

```erl
#option:option{value := Value}
```

Becomes:

```erl
#option:option/some{value := Value}
```

Alternatively, it can pick the variant whose fields match:

```erl
#result:result{value := Value}
#result:result{reason := Reason}
```

Becomes:

```erl
#result:result/ok{value := Value}
#result:result/error{reason := Reason}
```

### Syntax Extension: Default Single Field

If a record has a single field, the name of the field is not required:

```erl
#result:result/ok{value := Value}
#result:result/error{reason := Reason}
```

Becomes:

```erl
#result:result/ok{Value}
#result:result/error{Reason}
```

### Case Handling

Different BEAM languages use different naming conventions, for example, Erlang
uses `snake\_case`, Elixir uses `CamelCase` and LFE uses `lisp-case`, to avoid
foreign syntax or identifier quoting when dealing with record names, variants
and modules with different naming conventions, the new syntax should provide a
way to specify the syntax transformation, compile them to a canonical
representation or support all three cases in the parser to avoid the need for
quoting names in different cases.

### Spec Syntax

Runtime records's syntax in specs should be the same as a runtime record.

## Proposed Types

The inclusion of common types made possible by the introduction of runtime
records would increase the interoperability across languages but also across
libraries and modules in the same language, here are two proposed types to be
included in the standard library.

### Option

```erl
#option:option/some{value := value()}
#option:option/none{}
```
### Result

```erl
#result:result/ok{value := value()}
#result:result/error{reason := reason()}
```

## Record Definition Changes and Hot Core Reloading

TBD

## Language Support for Records/Union Types

### Languages that compile record types to erlang records

* Gleam: [Generates hrl files](https://gleam.run/tour/custom-types.html#erlang-interop)
* Fez: [Compiles to Erlang records](https://github.com/kjnilsson/fez/blob/HEAD/thoughts.md#records)
* Erlang
* Efene: [Same as Erlang](http://efene.org/language-reference.html#records)
* LFE: [Same as Erlang](https://lfe.gitbooks.io/reference-guide/content/16.html)
* Elixir: [Supports Erlang records](https://hexdocs.pm/elixir/Record.html), but for compatibility only

### Languages that compile record types to erlang maps

* Alpaca: `_\_struct_\_` field for records, no extra field for anonymous records
* Clojerl: `_\_type_\_` field
* Elixir: `_\_struct_\_` field for structs
* Purerl: [No extra field](https://github.com/purescript/documentation/blob/master/language/Records.md#records)
* Hamler : [No extra field](https://github.com/hamler-lang/documentation/blob/master/guides/09_DataTypesMapping.md#records-mapping)

### Languages with Union Types

* Alpaca: Tagged tuple if it has values, atom if not
* Gleam: [Tagged tuple if it has values, atom if not](https://gleam.run/tour/custom-types.html#erlang-interop)
* Fez: [Tagged tuple if it has values, atom if not](https://github.com/kjnilsson/fez/blob/HEAD/thoughts.md#discriminated-unions-implemented)
* Purerl: [Tagged tuple (not sure about variants with no values, should be like Hamler)](https://clojerl.github.io/differences-with-clojure/#datatypes)
* Hanler: [Tagged tuple even with variant with no values](https://github.com/hamler-lang/documentation/blob/master/guides/09_DataTypesMapping.md#user-defined-data-types-mapping)

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
