

**Regex** is used to find specific text within large text documents.

| Regex | Description |
| --- | --- |
| ? | matches zero or one of the preceding group. |
| * | matches zero or more of the preceding group. |
| + |  matches one or more of the preceding group. |
| {n} | matches exactly *n* of the preceding group. |
| {n,} | matches *n* or more of the preceding group. |
| {,m} | matches 0 to *m* of the preceding group. |
| {n,m} | matches at least *n* and at most *m* of the preceding group. |
| {n,m}? or \*? or +? | performs a non-greedy match of the preceding group. |
| \^spam | means the string must begin with *spam* |
| spam$ | means the string must end with *spam* |
| . | wildcard, matches with any character, except newline characters |
| \\d, \\w, and \\s | match a digit, word, or space character, respectively. |
| \\D, \\W, and \\S | match anything except a digit, word, or space character, respectively. |
| [abc] | matches any character between the brackets (such as a, b, or c) |
| [\^abc] | matches any character that isn't between the brackets |


