# So you want to build the documentation on Windows

## Install chocolatey

[Chocolatey][] is a command line package manager for Windows. We're going to use that to install the tools necessary for building the documentation. Following the Chocolatey [installation instructions][].

[chocolatey]: https://chocolatey.org/
[installation instructions]: https://chocolatey.org/install

## Install the required software

Open up an administrative shell on your Windows sytem and run the following command:

```
choco install python git make
```

Allow Chocolatey to run scripts when prompted.

Close the administrative shell when you are finished installing software.

## Create a virtual environment

Create and activate a Python virtual environment in the `documentation` directory. Assuming that you're using PowerShell:

```
$ python -mvenv .venv
$ . .venv/Scripts/Activate.ps1
```

If you're using `Bash`:

```
$ python -mvenv .venv
$ . .venv/Scripts/activate

```

## Build the documentation

You should now be able to run the necessary `make` command to build the documentation:

```
make -C docs html
```
