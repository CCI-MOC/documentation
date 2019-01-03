# Documentation

This is where we host all MOC documentation.

## Validation

There are scripts in `docs/scripts` that can be used to validate the Markdown sources in this repository.  You will need to install the requirements in `requirements-testing.txt` before you can run theses cripts.

To validate all Markdown files:

    ./docs/scripts/check-all-links

To validate a single Markdown file:

  ./docs/scripts/markdown-check-links path/to/file.md

See the `check-all-links` scripts for an example of how to tell `markdown-check-links` about the `_static` directory.
