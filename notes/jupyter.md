

# Jupyter Styling


[Cheat Sheet](https://jupyterbook.org/en/stable/reference/cheatsheet.html#reference-documents)

### Cell tags

 - Start hiden similar to `hide-input` but for text
```{toggle}
Some hidden toggle content!

![](../images/cool.jpg)
```

- Add to admonition - classs :dropdown:
```{admonition} Click the button to reveal!
:class: dropdown
Some hidden toggle content!

```

Code visibility
- remove-input: no code shown at all
- hide-input: toggled closed
- remove-output: suppress all outputs of code

### Add variables to markdown

in code
```{python}
from myst_nb import glue
var = 5
glue("my_var", var, display=False) #dont print on run display=False

```

now use with {glue:}`my_var`
Use with a table:
https://jupyterbook.org/en/stable/content/executable/output-insert.html#pasting-into-tables

Glue fig into table row


scroll-output - scroll long outputs

### Margin code

````{margin}
```{admonition} This is a title
:class: warning
An example of an admonition with a title and a warning style.
With code block:

```python
print("Hello, world!")
```


````




Using mpl fig obj:
from myst_nb import glue
glue('my_fig', fig, display=False)

| name                            |       plot                    |
|:-------------------------------:|:-----------------------------:|
| histogram and raw text          | {glue:}`my_fig`             | 
| sorted means and formatted text | {glue:}`my_fig`     | 