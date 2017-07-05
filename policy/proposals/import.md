# import
Make importing standard library easier/more readable.

Syntactic sugar so this:
```python
import "stdlib.cf"
```
expands to:
```cfengine
body file control { inputs => { "$(sys.libdir)/stdlib.cf" }; }
```
if specified at start of line, outside of other declarations.

It is important to stress that while this is new syntax it is **just a shortcut**.
It will not replace body file control.

This can also be a nice way to "guide" developers to the correct docs.
They are used to other languages, try to search for `import .cf file` or similar.
They find the docs for this and the most common use case, plus links to `body file control`.
