virtualcandy
============

## Smarter Shell Integration for [Python's](http://www.python.org/) [Virtualenv](http://www.virtualenv.org/en/latest/index.html)  

This not an attempt to create another, or even better, set of wrapper functions
for [Virtualenv](http://www.virtualenv.org/en/latest/index.html). This is a set of wrappers that I've built, like, and use
everyday.

* [Installation](#installation)
* [Philosophy of Virtualenv](#philosophy-of-virtualenv)
* [Configuration](#configuration)
* [Functions](#functions)
    * [vcstart](#vcstart)
    * [vcactivate](#vcactivate)
    * [vcin](#vcin)
    * [vcpkgup](#vcpkgup)
    * [vcpkgskel](#vcpkgskel)
    * [vctags](#vctags)
    * [vcfreeze](#vcfreeze)
    * [vcbundle](#vcbundle)
    * [vcclean](#vcclean)
    * [vc_auto_activate](#vc_auto_activate)
    * [vcfindenv](#vcfindenv)
    * [vcfinddir](#vcfinddir)


# Installation

Just clone the repo. I use a ~/.virtualcandy directory to hold the code, but the
location doesn't matter much.

    cd; git clone git://github.com/jeffbuttars/virtualcandy.git .virtualcandy 

To enable VirtualCandy, you just source it in your ~/.bashrc file. Add the
following line into your ~/.bashrc file:

    . ~/.virtualcandy/src/virtualcandy.sh

or add the following line to your ~/.zshrc, as appropriate:

    source ~/.virtualcandy/src/virtualcandy.zsh

That's it, VirtualCandy is installed!

## Philosophy of Virtualenv

My usage of [Virtualenv](http://www.virtualenv.org/en/latest/index.html) is very similar to how one uses Git or [Hg](https://mercurial.selenic.com/).
I create one [Virtualenv](http://www.virtualenv.org/en/latest/index.html) environment per project and that [Virtualenv](http://www.virtualenv.org/en/latest/index.html) environment
is located at the top of the project's directory tree. I also name
all of my [Virtualenv](http://www.virtualenv.org/en/latest/index.html) directories the same name, `.venv`, and this project
uses that as the default [Virtualenv](http://www.virtualenv.org/en/latest/index.html) directory name. But that is configurable.

Most VirtualCandy functions can be used from anywhere within a project using a
[Virtualenv](http://www.virtualenv.org/en/latest/index.html). VirtualCandy will find the nearest install of [Virtualenv](http://www.virtualenv.org/en/latest/index.html) by traversing
up the directory tree until one or no [Virtualenv](http://www.virtualenv.org/en/latest/index.html) are found.

## Configuration

Set the following environmental variables in your ~/.bashrc, before
you source the virtualcandy.sh file, to configure VirtualCandy settings.

### Naming it:

Set the name of your [Virtualenv](http://www.virtualenv.org/en/latest/index.html) directory created by and used by VirtualCandy

    VC_DEFAULT_VENV_NAME='.venv'


### Requirements files:

Set the name of the requirements file used by [Pip](http://pypi.python.org/pypi/pip) freeze and VirtualCandy to store your installed package information

    VC_DEFAULT_VENV_REQFILE='requirements.txt'

### Auto activation:

The auto activation (when set to 'true', it's off by default) of a [Virtualenv](http://www.virtualenv.org/en/latest/index.html) when you enter its containing directory. If you use [Virtualenv](http://www.virtualenv.org/en/latest/index.html) often, this is a very handy option. Example: If you have a directory named ~/Dev1 that has a [Virtualenv](http://www.virtualenv.org/en/latest/index.html) in it. Then upon changing into the ~/Dev1 directory that [Virtualenv](http://www.virtualenv.org/en/latest/index.html) will be activated. If you a [Virtualenv](http://www.virtualenv.org/en/latest/index.html) activated and cd into a directory containing a [Virtualenv](http://www.virtualenv.org/en/latest/index.html) that is different from the currently activated [Virtualenv](http://www.virtualenv.org/en/latest/index.html), then the current [Virtualenv](http://www.virtualenv.org/en/latest/index.html) will be deactivated and the new one will be activated.

    VC_AUTO_ACTIVATION=false

## Functions

### vcstart

Start a new virtualenv, or build one from a requirements file. This
function only works on your current working directory(all other functions work
anywhere within a [Virtualenv](http://www.virtualenv.org/en/latest/index.html) project). If you run `vcstart` in a
directory without a [Virtualenv](http://www.virtualenv.org/en/latest/index.html) of the name defined by `$VC_DEFAULT_VENV_NAME` ,
then a new [Virtualenv](http://www.virtualenv.org/en/latest/index.html) will be created. After the [Virtualenv](http://www.virtualenv.org/en/latest/index.html) is created, if a
requirements file is present, all of the packages listed in the
requirements file will be installed. If a [Virtualenv](http://www.virtualenv.org/en/latest/index.html) defined by the name
`$VC_DEFAULT_VENV_NAME` already exists and a requirements file exists then no
new [Virtualenv](http://www.virtualenv.org/en/latest/index.html) will be created, the packages listed in a present requirements file will be
installed/updated if necessary.

Any arguments given to the `vcstart` command will be considered package names and
will be installed after the virtualenv is created. If package parameters are given
and there is an existing requirements.txt file, the requirements.txt file we be
updated to include the additional packages.

### vcactivate

`vcactivate` will activate the [Virtualenv](http://www.virtualenv.org/en/latest/index.html) of the current project. `vcactivate` finds
the current project by using the `vcfindenv` command.

### vcin
Install a package into the current
[Virtualenv](http://www.virtualenv.org/en/latest/index.html)
and update the requirements file. 

Ex:

    # install the latest versions of Django and djnagorestframework
    # and update the requirements file
    vcin Django djnagorestframework


### vcpkgup

This will upgrade all of the packages listed in the requirements file to their
latest version and then re-write the requirements file to reflect the update.

### vcpkgskel

Create a Python package skeleton of the specified name. This includes some
boilerplate code for `Makefile`, `setup.py` and `pkg_name.__init__.py`

    vcpkgskel <package-name>

Will create a folder structure:

    <package-name>
      LICENSE.txt
      MANIFEST.in
      README.rst
      Makefile
      requirements.txt
      setup.py
      \
       <package_name> (directory for package sources)
         __init__.py

The `README.md` and `setup.py` will include boilerplate. Also the `__init__.py`
includes default version variables:

    __version__ = "0.1.0.dev1"
    __version_info__ = (0, 1, 0, 'dev1')

### vctags

* TODO: Make the inotify watch optional with a command line flag
* TODO: Make the [Virtualenv](http://www.virtualenv.org/en/latest/index.html) name option a command line flag

Runs ctags and creates a tags file in your current working directory. The
[Virtualenv](http://www.virtualenv.org/en/latest/index.html) directory of the current project will be explicitly scanned by ctags
and included in the tags file. If no parameters are given to `vctags` then the
current working directory will also be recursively added to the tags file. Any
parameters given to the `vctags` command will be treated as files and/or
directories that should be scanned by ctags.

### vcfreeze

Write a new requirements file for the current [Virtualenv](http://www.virtualenv.org/en/latest/index.html). The
requirements file contents are the result of the `pip freeze` command. The
requirements file is written in the same directory that contains the
[Virtualenv](http://www.virtualenv.org/en/latest/index.html) directory, even if the command is ran in a subdirectory.
If you don't want to name the output file to be `requirements.txt`, you can
change the name of the output file with the `$VC_DEFAULT_VENV_REQFILE`
environmental variable.

### vcbundle

Creates a package bundle containing all of the packages listed in the current [Virtualenv](http://www.virtualenv.org/en/latest/index.html)'s VC\_DEFAULT\_VENV\_REQFILE file. The name of the bundle output will be 'VC\_DEFAULT\_VENV\_NAME.pybundle', but with any leading '.' stripped from the [Virtualenv](http://www.virtualenv.org/en/latest/index.html) name. For instance, if VC\_DEFAULT\_VENV\_NAME is '.myenv' the bundle will be named 'myenv.pybundle'.

### vcclean
Recursively clean files matching a set of patterns.  
**Be careful using this. It's very convenient and very destructive**  
By default the file patterns `*.pyc` and `*.pyo` will be matched by default and
without question. You can add additional patterns as parameters:

    # Ex: clean out all files ending in .txt and .md
    vcclean '*.txt' '*.md'

    # Ex: clean out all tags files.
    vcclean tags

If additional patterns are given you will be prompted to confirm the use of the
additional patterns. 
`vcclean` is just a wrapper around:

    find . -iname "<pattern>" | xargs rm -fv


### vc\_auto\_activate

Checks the current directory for a [Virtualenv](http://www.virtualenv.org/en/latest/index.html) named VC\_DEFAULT\_VENV\_NAME. If it exists it is activated. This function is put into the PROMPT\_COMMAND variable and executed on every changed of directory.
This function is intended for internal use by VirtualCandy itself, but it is
available to the user.

### vcfindenv

This will find and print the full path of the current project's [Virtualenv](http://www.virtualenv.org/en/latest/index.html)
location.
This function is intended for internal use by VirtualCandy itself, but it is
available to the user.

### vcfinddir

This is used to find the nearest directory containing the [Virtualenv](http://www.virtualenv.org/en/latest/index.html) named by
the `$VC_DEFAULT_VENV_NAME` bash variable. For instance you have [Virtualenv](http://www.virtualenv.org/en/latest/index.html)
located at:
    `/home/user/project`
and you run vcfinddir from the directory:
    `/home/user/project/a/subdir`
the result will be:
    `/home/user/project`
This function is intended for internal use by VirtualCandy itself, but it is
available to the user.



## Per project settings via `.vc_proj` file

You can use per project Virtualcandy settings by adding a file named `.vc_proj` in
the same directory as your `requirements.txt` file. The `.vc_proj` file will be sourced
every time a Virtualcandy command is used. Settings in the `.vc_proj` file is a simple matter
of setting shell variables.

Example `.vc_proj` file that sets the Python executable to Python3 and sets the name of the
Virtualenv directory to `.vc_venv`

```sh
VC_PYTHON_EXE=python3
VC_DEFAULT_VENV_NAME='.vc_venv'
```

### Available config variables

* `VC_DEFAULT_VENV_NAME` Name of the Virtualenv directory, Default is '.venv'
* `VC_DEFAULT_VENV_REQFILE` Name of the requirements file, Default is 'requirements.txt'
* `VC_AUTO_ACTIVATION` Enable auto Virtualenv activation, Default is true
* `VC_PYTHON_EXE` Python executable to use for the Virtualenv, Default is $(basename $(which python))
* `VC_VIRTUALENV_EXE` Virtualenv command to use, Default is virtualenv

A wrapper around `pip install`. All arguments to `vcin` are passed to `pip
install`. After `pip install` is run `vcfreeze` is run.

## References

* [Python](http://www.python.org/)
* [Virtualenv](http://www.virtualenv.org/en/latest/index.html)
* [Pip](http://pypi.python.org/pypi/pip)
