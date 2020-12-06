Latex
=====

Resources
---------

### Equation editor

<http://www.codecogs.com/latex/eqneditor.php>

### Required/optional bibliography fields

<http://en.wikibooks.org/wiki/LaTeX/Bibliography_Management#BibTeX>


Best practices
--------------

- __Don't worry about formatting until you're done with the paper__
- One line per sentence
- Use Bibtool to help manage the bibtex file.
- Use `\centering` instead of `\begin{center} \end{center}` to center things inside tables/figures etc. `\centering` doesn't add any additional vertical space.
- Encode data or terms that might change as variables


Formatting and typography
-------------------------

### Emphasis

- Minor emphasis: smallcaps or italics
- Severe emphasis (stands out on page): bold or underline

### Remove margins from a PDF figure automatically

Use the PDFCrop tool

### Float placement

It is currently impossible to force 2-column floats to appear on the same page as when they are referenced. The best method is to declare the figure as a separate file via `/input`, move this input higher in the paper as necessary. This should be one of the last steps before submission, since adding/removing text impacts this placement.


Bibliography and citations
--------------------------

### Automatic section/author refs

```
\citeauthor{paperlabel} % to get the paper's authors
% (crefs requires cleveref package)
\Cref{reflabel} % to get "Section 2.2"
\cref{reflabel} % to get "section 2.2"
```

### Bibtool commands

```bash
# Make bibliography with just citations in the paper
bibtool -r bibtool.rsc -x paper.aux -o papernew.bib
# Make bibliography with all citations
bibtool -r bibtool.rsc -i paper.bib -o papernew.bib
```

### Bibtool config

Use the `bibtool.rsc` config file to specify how the output bib file will be formatted. Can also specify the sort order for various document types. "\#" is a separator, not a comment.

```
print.line.length = 10000
print.use.tab = Off
print.terminal.comma = On
sort = On
check.double = On

sort.order{article =
     author
    #title
    #journal
    #year
    #volume
    #number
    #pages
    #month
    #note }
```

Issues
------

### Cref not being highlighted correctly in vim

/usr/share/vim/vim74/syntax/tex.vim

```
syn region texRefZone     matchgroup=texStatement start="\\Cref{"     end="}\|%stopzone\>"    contains=@texRefGroup
syn region texRefZone     matchgroup=texStatement start="\\cref{"     end="}\|%stopzone\>"    contains=@texRefGroup
```

### Random build errors

hyperref can fail randomly; comment the package if having trouble
