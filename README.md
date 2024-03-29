# ljt - Little JSON Tool 

`ljt` is a shell utility written in JavaScript that can quickly extract values from JSON data. It can be used standalone or embedded as a function into _your_ shell scripts and only requires `jsc` the [JavaScriptCore](https://trac.webkit.org/wiki/JavaScriptCore) binary, which is standard on every Mac and available on many \*nix distributions. If you need advanced JSON manipulation and querying, see my other project [jpt](https://github.com/brunerd/jpt) the JSON Power Tool.

### Usage: 
`ljt [query] [filepath]`

`[query]` is optional and may be either [JSON Pointer](https://datatracker.ietf.org/doc/html/rfc6901), [JSONPath](https://datatracker.ietf.org/doc/draft-ietf-jsonpath-base/) ("canonical" only, no filters, unions, recursive descenders, etc) or `plutil`-style "keypath" notation. An empty query will output the entire JSON document with the default of 2 spaces per level of indent. 

**Note:** If a `keypath` resembles a JSON Pointer or JSONPath/jq-style expression by **beginning** with the character dollar sign ($), period(.), left square bracket ([), or solidus (/) it will **not** be resolved. It will be treated as if it were JSONPath and JSON Pointer. Standards win.

`[filepath]` can be any valid file path. Input via file redirection, here docs, here texts and Unix pipe (via cat) are all accepted.

String values are output as regular text and are _not_ double quoted JSON. All other values are output as JSON (arrays, objects, booleans, numbers and null). If the query path is not found an error is sent to `/dev/stderr` and ljt will exit with a status of `1`.

There are no other input or output options.

See my blog for articles, examples, and musing on the ljt: https://www.brunerd.com/blog/category/projects/ljt/

## Examples
```
#JSON Pointer example, strings are output as text not JSON
% ljt /obj/0 <<< '{"obj":["string",42,true]}'
string

#keypath example: No leading characters before propery name, both array elements and property names are period delimited
% ljt obj.0 <<< '{"obj":["string",42,true]}'
string

#keypath example if property name contains periods escape them with \
% ljt 'com\.keypaths\.are kinda\.wonky' <<< '{"com.keypaths.are kinda.wonky":true}'
true

#if you don't single quote you must escape your escape (leaning toothpick syndrome? :)
% ljt "com\\.keypaths\\.are kinda\\.wonky" <<< '{"com.keypaths.are kinda.wonky":true}'
true 

#use JSON Pointer to avoid escaping periods, if you have space you still have to quote
% ljt '/com.json pointer.is.easier' <<< '{"com.json pointer.is.easier":true}'
true

#JSONPath example
% ljt '$["obj"][2]' <<< '{"obj":["string",42,true]}'
true

#JSONPath without leading $ is also allowed
% ljt '.obj[1]' <<< '{"obj":["string",42,true]}'
42

#objects are output as JSON
% ljt <<< '{"obj":["string",42,true]}'                                                                               
{
  "obj": [
    "string",
    42,
    true
  ]
}

#arrays are output as JSON
% ljt /obj <<< '{"obj":["string",42,true]}'
[
  "string",
  42,
  true
]

#unlike jpt there are no output options, rely on standard tools to modify output
 % ljt /obj <<< '{"obj":["string",42,true]}' | sed -e 's/^ *//g' | tr -d $'\n'
["string",42,true]

#example of piped input
% ljt /obj <<< '{"obj":["string",42,true]}' | ljt '/0'  
string
```

### Requirements:
* macOS 10.11+ or \*nix with jsc installed

### Limitations:
* Max JSON input size is 2GB
* Max output is 720MB

### Installation
A macOS pkg is available in [Releases](https://github.com/brunerd/ljt/releases) it will install both the commented `ljt` and the minified version `ljt.min` into `/usr/local/ljt` and create a symlink to `ljt` in `/usr/local/bin`.
