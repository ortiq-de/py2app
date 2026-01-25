Structure of a py2app bundle
============================

Introduction
------------

The output of py2app consists of macOS application
or plugin bundles. The general structure of such
bundles is described in Apple's `Bundle Programming Guide`_.

This document describes how py2app packages python
code into such bundles.

XXX: This document is very much a work in progress.

Environment usage
-----------------

The stub executables that py2app includes in the bundle will
ignore the environment when configuring the python interpreter,
which means settings like ``PYTHONPATH`` will not work. This is
also true for ``sys.executable`` in the bundle.

A limited amount of configuration can be done through the ``Info.plist``
file as described in the next section.

Info.plist
----------

Py2app adds a number of new toplevel keys to ``Info.plist``:

* ``PyConfig``

  The value for this key is a dictionary with settings
  for the Python interpreter:

  * ``malloc_debug`` (boolean)

    Enable or disable Python's malloc debugger. Defaults to ``false``.

  * ``dev_mode`` (boolean)

    Enable or disable Python's dev mode. Defaults to ``false``.

  * ``optimization_level`` (int)

    Python's optimization level, defaults to ``0``.

  * ``verbose`` (int)

    Python's verbose mode, default to ``0``.

  In general these should only be changed through py2app's
  configuration when building the bundle, but they can be
  changed manually as well when debugging an application.

* ...

  To be determined, current code uses a number of other keys
  that likely aren't needed.


Python locations
----------------

The paths below are relative to the root of the bundle:

* ``Contents/MacOS/python3``, the binary used for ``sys.executable``.

  This binary can be used like the command-line ``python3`` executable,
  and uses the Python environment of the bundle. This executable cannot
  be copied outside the bundle, and does not support virtual environments.

  The ``python3`` executable supports the same command-line interface as
  a regular ``python3`` executable, but will not look at environment variables
  during startup (as if the ``-E`` option is always specified) and will
  not use user site packages.

* ``Contents/Resources/python-libraries.zip``

  Python libraries marked as zip safe.

  This file also contains the scripts in the folder
  ``XXX`` in the root of the zip file.

* ``Contents/Resources/python-libraries``

  Python libraries that are marked as being not zipsafe.

* ``Contents/Resources/lib-dynload``

  Native extensions for libraries stored in ``python-libraries.zip``.

  The filenames in this folder are the full name of the extension
  module followed by ``.so``.

* ``Contents/Frameworks`` (optional)

  This folder is used to store shared libraries used by
  extension modules.


Py2app Introspection
--------------------

During launch py2app will inject the following values into
the interpreter:

* ``sys.py2app_argv0`` -  the executable path resolved with realpath(3)

  This is primarily meant to be used by the helper code injected by py2app to
  ensure application launching works when using a symbolic link to the stub
  executables in a generated bundle.

  Only set for app bundles (or helper scripts in plugin bundles), not for plugin bundles.

* ``sys.py2app_bundle_resources`` - absolute path of the ``Contents/Resources`` folder

* ``sys.py2app_bundle_address`` - integer with the address of the NSBundle

  The ``NSBundle*`` for the bundle representing this plugin bundle as a Python
  integer.

  Only set for plugin bundles, not for app bundles.

.. _`Bundle Programming Guide`: https://developer.apple.com/library/archive/documentation/CoreFoundation/Conceptual/CFBundles/Introduction/Introduction.html#//apple_ref/doc/uid/10000123i
