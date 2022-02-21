# ljt
Little JSON Tool a smaller compliment to [jpt](https://github.com/brunerd/jpt) the JSON Power Tool

Extract values from JSON in your shell script (bash or zsh) using either [JSON Pointer](https://datatracker.ietf.org/doc/html/rfc6901) or canonical [JSONPath](https://datatracker.ietf.org/doc/draft-ietf-jsonpath-base/).

JSONPath with or without a leading $ is allowed. Canonical JSONPath means no filters, negative indexes, unions, or recursive search, etc... basically JavaScript syntax.

String values are output in their native encoding (not JSON double quoted), all other values are output as JSON (arrays, objects, booleans, numbers and null)

There are no input or output options, both the query and the file arguments are also optional.

An empty query will output the entire JSON document with the default 2 spaces per level of indent.

A file argument can be any valid file path. Additionally file redirection, here docs and here texts are accepted, as well as input via pipe (via cat).

Usage: `jpt [query] [fileArg]`
```
% ljt /obj/0 <<< '{"obj":["string",42,true]}'
string

% ljt '.obj[1]' <<< '{"obj":["string",42,true]}'
42

% ljt '$["obj"][2]' <<< '{"obj":["string",42,true]}'
true
```
