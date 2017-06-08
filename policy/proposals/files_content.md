# I want this to work, without masterfiles:
```cfengine
bundle agent main
{
    files:
        "/tmp/out.txt"
            content => "Hello world";
}
```

Other name suggestions:
* `force_content`
* `is_exactly`
* `contains_only`
* `overwrite_with`

Also for beginners it would be best if `create => "true"` was default for this.
