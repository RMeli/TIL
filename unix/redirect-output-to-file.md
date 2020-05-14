#  Redirect Output to File

## Redirect STDOUT

```bash
COMMAND > FILE
```

## Redirect STDERR

```bash
COMMAND 2> FILE
```

## Redirect STDOUT and STDERR to Different Files

```bash
COMMAND > FILE.OUT 2> FILE.ERR
```

## Redirect STDOUT and STDERR to the Same File

```bash
COMMAND > FILE 2>&1
```
