# NeatJSON

[![Gem Version](https://badge.fury.io/rb/neatjson.svg)](http://badge.fury.io/rb/neatjson)
[![Gem Downloads](http://ruby-gem-downloads-badge.herokuapp.com/neatjson?type=total&color=brightgreen)](https://rubygems.org/gems/neatjson)

Pretty-print your JSON in Ruby with more power than is provided by `JSON.pretty_generate`. In particular, like Ruby's `pp` (pretty print), NeatJSON will keep objects on one line if they fit, but break them over multiple lines if needed.

Here's an excerpt (from a much larger JSON):

~~~ json
{
  "navigation.createroute.poi":[
    {"text":"Lay in a course to the Hilton","params":{"poi":"Hilton"}},
    {"text":"Take me to the airport","params":{"poi":"airport"}},
    {"text":"Let's go to IHOP","params":{"poi":"IHOP"}},
    {"text":"Show me how to get to The Med","params":{"poi":"The Med"}},
    {"text":"Create a route to Arby's","params":{"poi":"Arby's"}},
    {
      "text":"Go to the Hilton by the Airport",
      "params":{"poi":"Hilton","location":"Airport"}
    },
    {
      "text":"Take me to the Fry's in Fresno",
      "params":{"poi":"Fry's","location":"Fresno"}
    }
  ],
  "navigation.eta":[
    {"text":"When will we get there?"},
    {"text":"When will I arrive?"},
    {"text":"What time will I get to the destination?"},
    {"text":"What time will I reach the destination?"},
    {"text":"What time will it be when I arrive?"}
  ]
}
~~~

## Installation

`gem install neatjson`

## Examples

~~~ ruby
require 'neatjson'

o = { b:42.005, a:[42,17], longer:true, str:"yes
please" }

puts JSON.neat_generate(o)
#=> {"b":42.005,"a":[42,17],"longer":true,"str":"yes\nplease"}

puts JSON.neat_generate(o,sort:true)
#=> {"a":[42,17],"b":42.005,"longer":true,"str":"yes\nplease"}

puts JSON.neat_generate(o,sort:true,padding:1,after_comma:1)
#=> { "a":[ 42, 17 ], "b":42.005, "longer":true, "str":"yes\nplease" }

puts JSON.neat_generate(o,sort:true,wrap:40)
#=> {
#=>   "a":[42,17],
#=>   "b":42.005,
#=>   "longer":true,
#=>   "str":"yes\nplease"
#=> }

puts JSON.neat_generate(o,sort:true,wrap:40,decimals:2)
#=> {
#=>   "a":[42,17],
#=>   "b":42.01,
#=>   "longer":true,
#=>   "str":"yes\nplease"
#=> }

puts JSON.neat_generate(o,sort:->(k){ k.length },wrap:40,aligned:true)
#=> {
#=>   "a"     :[42,17],
#=>   "b"     :42.005,
#=>   "str"   :"yes\nplease",
#=>   "longer":true
#=> }

puts JSON.neat_generate(o,sort:true,wrap:40,aligned:true,around_colon:1)
#=> {
#=>   "a"      : [42,17],
#=>   "b"      : 42.005,
#=>   "longer" : true,
#=>   "str"    : "yes\nplease"
#=> }

puts JSON.neat_generate(o,sort:true,wrap:40,aligned:true,around_colon:1,short:true)
#=> {"a"      : [42,17],
#=>  "b"      : 42.005,
#=>  "longer" : true,
#=>  "str"    : "yes\nplease"}

a = [1,2,[3,4,[5]]]
puts JSON.neat_generate(a)
#=> [1,2,[3,4,[5]]]

puts JSON.pretty_generate(a) # oof!
#=> [
#=>   1,
#=>   2,
#=>   [
#=>     3,
#=>     4,
#=>     [
#=>       5
#=>     ]
#=>   ]
#=> ]

puts JSON.neat_generate( a, wrap:true, short:true )
#=> [1,
#=>  2,
#=>  [3,
#=>   4,
#=>   [5]]]
~~~

## Options
You may pass any of the following option symbols to `neat_generate`:

* `:wrap`           — The maximum line width before wrapping. Use `false` to never wrap, or `true` (or `-1`) to always wrap. Default: `80`
* `:indent`         — Whitespace used to indent each level when wrapping. Default: `"  "` (two spaces)
* `:indent_last`    — Indent the closing bracket/brace for arrays and objects? Default: `false`
* `:short`          — Keep the output 'short' when wrapping? This puts opening brackets on the same line as the first value, and closing brackets on the same line as the last. Default: `false`
  * _This causes the `:indent` and `:indent_last` options to be ignored, instead basing indentation on array and object padding._
* `:sort`           — Sort the keys for objects to be in alphabetical order (`true`), or supply a lambda for customized sort order. Default: `false`
* `:aligned`        — When wrapping objects, line up the colons (per object)? Default: `false`
* `:decimals`       — Decimal precision to use for non-integer numbers; use `false` to keep float values precise. Default: `false`
* `:array_padding`  — Number of spaces to put inside brackets for arrays. Default: `0`
* `:object_padding` — Number of spaces to put inside braces for objects.  Default: `0`
* `:padding`        — Shorthand to set both `:array_padding` and `:object_padding`. Default: `0`
* `:before_comma`   — Number of spaces to put before commas (for both arrays and objects). Default: `0`
* `:after_comma`    — Number of spaces to put after commas (for both arrays and objects). Default: `0`
* `:around_comma`   — Shorthand to set both `:before_comma` and `:after_comma`. Default: `0`
* `:before_colon_1` — Number of spaces before a colon when the object is on one line. Default: `0`
* `:after_colon_1`  — Number of spaces after a colon when the object is on one line. Default: `0`
* `:before_colon_n` — Number of spaces before a colon when the object is on multiple lines. Default: `0`
* `:after_colon_n`  — Number of spaces after a colon when the object is on multiple lines. Default: `0`
* `:before_colon`   — Shorthand to set both `:before_colon_1` and `:before_colon_n`. Default: `0`
* `:after_colon`    — Shorthand to set both `:after_colon_1` and `:after_colon_n`. Default: `0`
* `:around_colon`   — Shorthand to set both `:before_colon` and `:after_colon`. Default: `0`

If you supply a lambda to the `sort` option, it will be passed three values:

* The string name of the object key.
* The associated value.
* The object being sorted.

For example:

~~~ ruby
# Ruby sorting examples
obj = {e:3, a:2, c:3, b:2, d:1, f:3}

JSON.neat_generate obj, sort:true                              # sort by key name
#=> {"a":2,"b":2,"c":3,"d":1,"e":3,"f":3}

JSON.neat_generate obj, sort:->(k){ k }                        # sort by key name (long way)
#=> {"a":2,"b":2,"c":3,"d":1,"e":3,"f":3}

JSON.neat_generate obj, sort:->(k,v){ [-v,k] }                 # sort by descending value, ascending key
#=> {"c":3,"e":3,"f":3,"a":2,"b":2,"d":1}

JSON.neat_generate obj, sort:->(k,v,h){ h.values.count(v) }    # sort by count of keys with same value
#=> {"d":1,"a":2,"b":2,"e":3,"c":3,"f":3}
~~~

~~~ js
// JavaScript sorting examples
var obj = {e:3, a:2, c:3, b:2, d:1, f:3};

neatJSON( obj, {sort:true} );                                              // sort by key name
// {"a":2,"b":2,"c":3,"d":1,"e":3,"f":3}

neatJSON( obj, { sort:function(k){ return k }} );                          // sort by key name (long way)
// {"a":2,"b":2,"c":3,"d":1,"e":3,"f":3}

neatJSON( obj, { sort:function(k,v){ return -v }} );                       // sort by descending value
// {"e":3,"c":3,"f":3,"a":2,"b":2,"d":1}

var countByValue = {};
for (var k in obj) countByValue[obj[k]] = (countByValue[obj[k]]||0) + 1;
neatJSON( obj, { sort:function(k,v,h){ return countByValue[v] } } );       // sort by count of same value
// {"d":1,"a":2,"b":2,"e":3,"c":3,"f":3}
~~~

_Note that the JavaScript version of NeatJSON does not provide a mechanism for cascading sort in the same manner as Ruby._


## License & Contact

NeatJSON is copyright ©2015–2016 by Gavin Kistner and is released under
the [MIT License](http://www.opensource.org/licenses/mit-license.php).
See the LICENSE.txt file for more details.

For bugs or feature requests please open [issues on GitHub][1].
For other communication you can [email the author directly](mailto:!@phrogz.net?subject=NeatJSON).

## TODO (aka Known Limitations)

* Figure out the best way to play with custom objects that use `to_json` for their representation.
* Detect circular references.
* Possibly allow illegal JSON values like `NaN` or `Infinity`.
* Possibly allow "JSON5" output (legal identifiers unquoted, etc.)

## HISTORY

* **v0.8.1** - April 22nd, 2016
  * Make NeatJSON work with [Opal](http://opalrb.org) (by removing all in-place string mutations)

* **v0.8** - April 21st, 2016
  * Allow `sort` to take a lambda for customized sorting of object key/values.

* **v0.7.2** - April 14th, 2016
  * Fix JavaScript library to support objects without an `Object` constructor (e.g. `location`).
  * Online HTML converter accepts arbitrary JavaScript values as input in addition to JSON.

* **v0.7.1** - April 6th, 2016
  * Fix Ruby library to work around bug in Opal.

* **v0.7** - March 26th, 2016
  * Add `indentLast`/`indent_last` feature.

* **v0.6.2** - February 8th, 2016
  * Use memoization to avoid performance stalls when wrapping deeply-nested objects/arrays.  
    _Thanks @chroche_

* **v0.6.1** - October 12th, 2015
  * Fix handling of nested empty objects and arrays. (Would cause a runtime error in many cases.)
    * _This change causes empty arrays in a tight wrapping scenario to appear on a single line where they would previously take up three lines._

* **v0.6** - April 26th, 2015
  * Added `before_colon_1` and `before_colon_n` to distinguish between single-line and multi-line objects.

* **v0.5** - April 19th, 2015
  * Do not format integers (or floats that equal their integer) using `decimals` option.
  * Make `neatJSON()` JavaScript available to Node.js as well as web browsers.
  * Add (Node-based) testing for the JavaScript version.

* **v0.4** - April 18th, 2015
  * Add JavaScript version with online runner.

* **v0.3.2** - April 16th, 2015
  * Force YARD to use Markdown for documentation.

* **v0.3.1** - April 16th, 2015
  * Remove some debugging code accidentally left in.

* **v0.3** - April 16th, 2015
  * Fix another bug with `short:true` and wrapping array values inside objects.

* **v0.2** - April 16th, 2015
  * Fix bug with `short:true` and wrapping values inside objects.

* **v0.1** - April 15th, 2015
  * Initial release.

[1]: https://github.com/Phrogz/NeatJSON/issues
