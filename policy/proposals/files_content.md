# Easily write string to file
**Description:**
A common example beginners will look for is how to write a string to file.
In any UNIX shell this can be done in one short line:

```bash
echo "Hello world" > /tmp/out.txt
```

## Idea
I want this to work without masterfiles:
```cfengine
bundle agent main
{
    files:
        "/tmp/out.txt"
            content => "Hello world";
}
```
Create a file if necessary and write the string to it.
Ignoring the bundle syntax this is 5 words, compared to the 4 word shell command.
It uses no new syntax, just a new promise attribute.
## Name suggestions
* `content`
* `is_exactly`
* `force_content`
* `contains_only`
* `overwrite_with`


## Equivalent
The proposed policy should have similar results as:
```cfengine
body edit_defaults empty
{
      empty_file_before_editing => "true";
      edit_backup => "false";
}

bundle edit_line insert_lines(lines)
{
  insert_lines:
      "$(lines)";
}

bundle agent main
{
    files:
        "/tmp/out.txt"
            create => "true",
            edit_line => insert_lines("Hello world"),
            edit_defaults => empty;
}

```
