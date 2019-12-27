# mypy notes
source: [an article on Zulip's blog](https://blog.zulip.org/2016/10/13/static-types-in-python-oh-mypy/) - this article has entirely too many exclamation points

mypy is a type-checker. It can enforce static type consistency in your python program
- it can provide a strong consistency check when it has complete type annotations

## Quotes
```
You run mypy on your codebase like a linterj
```

## Links
- [mypy syntax cheatsheet](http://mypy.readthedocs.io/en/latest/cheat_sheet.html)
- [mypy docs](https://www.python.org/dev/peps/pep-0484/)

## Benifits of mypy
- readability
- easier to refactor
- bug catching

## Phases of mypy migration
1. Annotate the core library - find modules that get imported a lot, and annotate them first
2. Annotate the bulk of the codebase -
3. Annotate the last of the codebase - this is a little more work, because you generally find the bugs you made in phase 2

## Tips
- `Make sure to handle str vs. Text correctly.`
- `Bytes vs. str and str vs. unicode errors`
- `annotate class variables correctly`
- avoid using `Any`, if you can


