# Documentation

This is the [Mass Open Cloud][moc] documentation repository.

[moc]: https://massopen.cloud

## Organzation

Documentation files are written in Markdown and are stored in the [`docs/source`](docs/source) directory.

### Adding a new section

Major sections are defined in `docs/source/index.rst` in the `toctree` block:

```
.. toctree::
   :maxdepth: 2

   networking/index.rst
   hardware/index.rst
```

To add a new section:

1. Create the corresponding directory as a subdirectory of `docs/source` (e.g., `docs/source/mysection`).

1. Create an `index.rst` file in the new directory (so, `docs/source/mysection/index.rst`) that looks like this:

    ```
    My Section
    ==========

    .. toctree::
       :maxdepth: 2
       :glob:

       *
    ```

1. Update the main `docs/source/index.rst` to include your new section:

    ```
    .. toctree::
       :maxdepth: 2

       networking/index.rst
       hardware/index.rst
       mysection/index.rst
    ```

### Adding a new document

Create your document in an existing section directory. The document should start with a level 1 heading:

```
# This is my document
```

## Rendering

You can render the documentation locally by installing [Sphinx][] and using the `Makefile`. These instructions are appropriate for Linux and MacOS users; if you are using Windows, please see the [Windows README](README.windows.md).

[sphinx]: https://www.sphinx-doc.org/

### Requirements

In order to build the documentation you will need a recent version of Python and the `make` utility.

### If this is your first time rendering the documentation

Start in the top level of this repository.

1. Create and activate a Python virtual environment:

    ```
    $ python -mvenv .venv
    $ . .venv/bin/activate
    ```

1. Install the required software:

    ```
    $ pip install -r requirements.txt
    ```

1. Build the documentation using `make`:

    ```
    $ make -C docs html
    ```

### If you previously run the installation steps

If you have already run the above steps and you have an existing `.venv` directory, you can skip the installation steps:

```
$ . .venv/bin/activate
$ make -C docs html
```

### Opening the documentation in your browser

1. You can directly open the file `docs/build/html/index.html` in your browser. Under Linux, this will probably work:

    ```
    $ xdg-open docs/build/html/index.html
    ```

    Under MacOS, you can use the `open` command:

    ```
    $ open docs/build/html/index.html
    ```

    And of course you can just use the "Open..." menu item in your browser.

1. You can start a simple http server and then point your browser at a local URL. To start a server on port 8000:

    ```
    $ cd docs/build/html
    $ python -m http.server 8000
    ```

    Now point your browser at http://localhost:8000
