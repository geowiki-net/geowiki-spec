#### Examples
Twig resp. TwigJS is a template language. Example:
```twig
Value of property "test": {{ test }}.
```

If-condition:
```twig
{% if test == "foo" %}
It's foo!
{% elseif test == "bar" %}
It's bar!
{% else %}
Other value: {{ test }}
{% endif %}
```

For-loop:
```twig
{% for k, v in tags %}
Tag {{ k }} has value {{ v }}
{% endfor %}
```

Assign value to variable:
```twig
{% set k = "foo" %}
```

For more information, please visit:
* [Page of the original Twig template language](https://twig.symfony.com/)
* [Wiki of the TwigJS template language which is almost identical to Twig](https://github.com/twigjs/twig.js/wiki)

#### TwigJS templates
When rendering map features, the following properties are available:
* `id` (the id of the object is always available, prefixed 'n' for nodes, 'w' for ways and 'r' for relations; e.g. 'n1234')
* `osm_id` (the numerical id of the object)
* `layer_id` (the id of the category)
* `type` ('node', 'way' or 'relation')
* `tags.*` (all tags are available with the prefix tags., e.g. tags.amenity)
* `meta.timestamp` (timestamp of last modification)
* `meta.version` (version of the object)
* `meta.changeset` (ID of the changeset, the object was last modified in)
* `meta.user` (Username of the user, who changed the object last)
* `meta.uid` (UID of the user, who changed the object last)
* `map.zoom` (Current zoom level)
* `const.*` (Values from the 'const' option)
* `filter.*` (if filters are defined, the values of the selected filter options)
* `config.*` (if config options are defined, the values of the selected configuration options)
* `user.*` (Values from the user's options, e.g. `user.ui_lang`, `user.data_lang`, ...)

For the info-section of a category the following properties are available:
* `layer_id` (the id of the category)
* `map.zoom` (Current zoom level)
* `const.*` (Values from the 'const' option)

There are several extra functions defined for the TwigJS language:

Function name | Parameters | Description | Defined in module
--------------|------------|-------------|-------------------
`keyTrans` | key (required, e.g. 'amenity') | return the translation of the given key
`tagTrans` | key (required, e.g. 'amenity'), value (required, e.g. 'bar'), count (optional, default 1) | return the translation of the given tag.
`tagTransList` | key (required, e.g. 'cuisine'), value (required, e.g. 'kebab' or 'kebab;pizza;noodles;burger') | return the translations of the given tag for tags with multiple values separated by ';' (e.g. 'cuisine')
`localizedTag` | tags (the tags property), key prefix (e.g. 'name') | return a localized tag if available (e.g. 'name:de' for the german translation of the tag). Which language will be returned depends on the "data language" which can be set via Options. If no localized tag is available, the tag value itself will be returned (e.g. value of 'name').
`trans` | string (the string to translate) | return the translation of the given string (e.g. 'save', 'unknown', 'unnamed', ...)
`tagsPrefix` | tags, prefix | return all tags with the specified prefix. The result will be an array with `{ "en": "name:en", "de": "name:de" }` (for the input `{ "name": "foo", "name:en": "english foo", "name:de": "german foo" }` and the prefix "name:")
`openingHoursState` | opening_hours_definition | returns state of object as string: 'closed', 'open' or 'unknown'. | [geowiki-module-opening-hours](https://github.com/geowiki-net/geowiki-module-opening-hours)
`colorInterpolate` | map, value | interpolates between two or more colors. E.g. `colorInterpolate([ 'red', 'yellow', 'green' ], 0.75)` | [geowiki-module-color](https://github.com/geowiki-net/geowiki-module-color)
`enumerate` | list | enumerate the given list, e.g. "foo, bar, and bla". Input either an array (`enumerate([ "foo", "bar", "bla" ])`) or a string with `;` as separator (`enumerate("foo;bar;bla")`).
`debug` | ... | print all arguments to the javascript console (via `console.log()`)

Extra filters:
Function name | Input | Parameters | Description | Defined in module
--------------|-------|------------|-------------|-------------------
`websiteUrl` | url | - | return a valid http link. Example: `{{ "www.google.com"|websiteUrl }}` -> "http://www.google.com"; `{{ "https://google.com"|websiteUrl }}` -> "https://google.com"
`matches` | string | regular expression | regular expression match. e.g. `{{ "test"|matches("e(st)$") }}` returns `[ "est", "st" ]`. You can pass a second parameter with [RegExp flags](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/RegExp). Returns null if it does not match.
`osmParseDate` | date | | returns an array with the lower and upper boundary of the year of a `start_date` tag. See [openstreetmap-date-parser](https://github.com/plepe/openstreetmap-date-parser) for details.
`osmFormatDate` | date | options | returns the date as localized strings. Accept an object for options, e.g. `{{ tags.start_date|osmFormatDate({ format: 'short' }) }}`. See [openstreetmap-date-format](https://github.com/plepe/openstreetmap-date-format) for details.
`natsort` | string[] | | Sort an array naturally, see [natsort](https://www.npmjs.com/package/natsort) for details.
`ksort` | object | | Sort an associative array by key (alphabetic)
`unique` | string[] | | Remove duplicate elements from an array.
`md5` | string | | calculate md5 hash of a string.
`enumerate` | string|string[] | | enumerate the given list, e.g. "foo, bar, and bla". Input either an array (`[ "foo", "bar", "bla" ]|enumerate`) or a string with `;` as separator (`"foo;bar;bla"|enumerate`).
`debug` | * | ... | print the value (and further arguments) to the javascript console (via `console.log()`)
`wikidataEntity` | id | | returns the wikidata entity in structured form (or `null` if the entity is not cached or `false` if it does not exist). Example: `{{ 'Q42'|wikidataEntity|json_encode }}` -> https://www.wikidata.org/wiki/Special:EntityData/Q42.json
`json_pp` | * | options | JSON pretty print the object. As parameter to the filter, the following options can be passed: `indent`: indentation (default: 2)
`yaml` | * | options | YAML pretty print the object. As options the filter, all options to [yaml.dump of js-yaml](https://github.com/nodeca/js-yaml#dump-object---options-) can be used.
`formatUnit` | value | type of measurement | format a value in the selected unit system of the user. Use as parameter the type of measurement ('distance' (default), 'area', 'speed', 'height', 'coord'). Example: `{{ 2|formatUnit('distance') }}` -> '2 m'.

Notes:
* Variables will automatically be HTML escaped, unless the filter raw is used, e.g.: `{{ tags.name|raw }}`
* The templates will be rendered when the object becomes visible and when the zoom level changes.
* If you set an arbitrary value within a twig template (e.g.: `{% set foo = "bar" %}`), it will also be available in further templates of the same object by using (e.g.: `{{ foo }}`). The templates will be evaluated in the order as they are defined.
