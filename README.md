# Documentation

This is where we host all MOC documentation.

## Contributing

Documentation files are markdown and are stored in docs/source

docs/source/home.md is the index for MOC documentation.

For each section in home.md there's a sub directory in docs/source where files relevant to that section are stored. For example files in "How Tos" section are stored in docs/source/how-tos.

We also have an section for archived files. These are stored in docs/source/archive-files and are listed in docs/source/Archives-page.md

Non-markdown files are stored in `docs/souce/_static/<type>/` where sub directory type depends on the file's type. For example all limages are in `docs/source/_static/img`.

To update MOC documentationi, fork the documentation repository and submit a pull request. This will trigger a series of travis-ci tasks, that validate the markdown syntax, check document links, and make sure sphinx can build the docs.

## Validation

There are scripts in `docs/scripts` that can be used to validate the Markdown sources in this repository. These are the link checker scripts used by tracis-ci to validate documentation changes.

You will need to install the requirements in `requirements-testing.txt` before you can run theses scripts.

To validate all Markdown files:

    ./docs/scripts/check-all-links

To validate a single Markdown file:

  ./docs/scripts/markdown-check-links path/to/file.md

See the `check-all-links` scripts for an example of how to tell `markdown-check-links` about the `_static` directory.

To check markdown syntax, we use the node-js markdown linter remark-lint. This will lint documentation markdown files according to [following syntax rules](https://github.com/remarkjs/remark-lint/blob/master/doc/rules.md)

You can install remark link on your own machine by following [these instructions](https://github.com/remarkjs/remark-lint). MOC's Remark configuration is in `.remarkrc`

## How Paths Work

MOC Docs are built with Sphinx using markdown parsing plugin called [recommonmark](https://recommonmark.readthedocs.io/en/latest/).

One component of recommonmark is AutoStructify which converts references in markdown files to other markdown files into url references when the markdown files are converted to html.

This allows the correct link to be generated when the base url is changed (eg. building docs for a different version)

The url resolver can't parse extensions other than `.html`, so a link should be the relative path to the markdown file with the '.md` extention replaced with `.html`.

For example, to reference `contacts/MOC-Developers-Contact-Information.md` in home.md, use a markdown link to `contacts/MOC-Developers-Contact-Information.html`.

This will have the url `https://moc-documents.readthedocs.io/en/latest/contacts/MOC-Developers-Contact-Information.html` when the docs are built.

Paths used as links must be **relative to the file where the link is**. For example to link to an image in `contacts/MOC-Developers-Contact-Information.md` the correct path would be `../_static/img/<img_name>`
