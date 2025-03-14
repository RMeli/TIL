# Open Babel

# GZipped Files

Open Babel deals with GZipped input files automatically, whether the `.gz` extension is present or not.

For writing files, however, the `.gz` extension is not enough, and the `-z` option need to be used explicitly:

```bash
obabel -i<INPUT_EXTENSION> <INPUT_FILE> -o<OUTPUT_EXTENSION> -O <OUTPUT_FILE> -z
```

`<OUTPUT_FILE>` needs to specify the `.gz` extension explicitly, since it is not appended automatically.
