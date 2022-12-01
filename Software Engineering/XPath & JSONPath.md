

**XPath/JSONPath**: How to traverse [XML/JSON](Serialization.md) respectively


|XPath | JSONPath | Description |
| ---- | --- | --- |
| / | $ | the root object/element |
| . | @ | the current object/element |
| / | . or [] | child operator |
| .. | n/a | parent operator |
| // | .. | nested descendants. JSONPath borrows this syntax from E4X |
| * | * | wildcard. All objects/elements regardless of their names. |
| @ | n/a | attribute access. JSON data items don't have attributes |
| [] | [] | subscript operator. XPath uses it to iterate over element collections and for predicates. In JavaScript and JSON it is the native array operator |
| \| | [,] | Union operator in XPath results in a combination of node sets. JSONPath allows alternate names or array indices as a set. |
| n/a | [start:end:step] | array slice operator borrowed from ES4 |
| [] | ?() | applies a filter (script) expression |
| n/a | () | script expression, using the underlying script engine. |
| () | n/a | grouping in XPath |



