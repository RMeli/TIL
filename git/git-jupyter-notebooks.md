# `git` and Jupyter Notebooks

## nbstripout

`nbstripout` can be used as a `git` filter or pre-commit hook:

```bash
nbstripout --install --attributes .gitattributes
```

Installing `nbstripout` adds the following to the `.gitattributes` file:

```text
*.ipynb filter=nbstripout
*.ipynb diff=ipynb
```
