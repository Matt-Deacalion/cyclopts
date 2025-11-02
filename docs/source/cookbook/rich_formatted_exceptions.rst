=========================
Rich Formatted Exceptions
=========================
Tracebacks of uncaught exceptions provide valuable feedback for debugging. This guide demonstrates how to enhance your error messages using rich formatting.

-------------------------
Standard Python Traceback
-------------------------
Consider the following example:

.. code-block:: python

    from cyclopts import App

    app = App()

    @app.default
    def main(name: str):
        print(name + 3)

    if __name__ == "__main__":
        app()

Running this script will produce a standard Python traceback:

.. code-block:: console

   $ python my-script.py foo
   Traceback (most recent call last):
     File "/cyclopts/my-script.py", line 12, in <module>
       app()
     File "/cyclopts/cyclopts/core.py", line 903, in __call__
       return command(*bound.args, **bound.kwargs)
              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
     File "/cyclopts/my-script.py", line 8, in main
       print(name + 3)
             ~~~~~^~~
   TypeError: can only concatenate str (not "int") to str

------------------------
Rich Formatted Traceback
------------------------
To create a more visually appealing and informative traceback, you can use the `Rich library's traceback handler`_. Here's how to modify your script:

.. code-block:: python

    import sys
    from cyclopts import App
    from rich.console import Console
    from rich.traceback import install as install_rich_traceback

    error_console = Console(stderr=True)
    app = App(console=error_console, error_console=error_console)

    # Install rich traceback handler using the error console
    install_rich_traceback(console=error_console)

    @app.default
    def main(name: str):
        print(name + 3)

    if __name__ == "__main__":
        app()

Now, running the updated script will display a rich-formatted traceback:

.. code-block:: console

   $ python my-script.py foo
    ╭─────────────────────────────── Traceback (most recent call last) ────────────────────────────────╮
    │ /cyclopts/my-script.py:17 in <module>                                                            │
    │                                                                                                  │
    │   14 │   print(name + 3)                                                                         │
    │   15                                                                                             │
    │   16 if __name__ == "__main__":                                                                  │
    │ ❱ 17 │   app()                                                                                   │
    │   18                                                                                             │
    │                                                                                                  │
    │ /cyclopts/cyclopts/core.py:1860 in __call__                                                      │
    │                                                                                                  │
    │   1857 │   │   │                                                                                 │
    │   1858 │   │   │   resolved_backend = cast(Literal["asyncio", "trio"], self.app_stack.resolve("  │
    │   1859 │   │   │   try:                                                                          │
    │ ❱ 1860 │   │   │   │   result = _run_maybe_async_command(command, bound, resolved_backend)       │
    │   1861 │   │   │   │   return self._handle_result_action(result)                                 │
    │   1862 │   │   │   except KeyboardInterrupt:                                                     │
    │   1863 │   │   │   │   if self.suppress_keyboard_interrupt:                                      │
    │                                                                                                  │
    │ /cyclopts/cyclopts/_run.py:50 in _run_maybe_async_command                                        │
    │                                                                                                  │
    │    47 │   │   if bound is None:                                                                  │
    │    48 │   │   │   return command()                                                               │
    │    49 │   │   else:                                                                              │
    │ ❱  50 │   │   │   return command(*bound.args, **bound.kwargs)                                    │
    │    51 │                                                                                          │
    │    52 │   if backend == "asyncio":                                                               │
    │    53 │   │   import asyncio                                                                     │
    │                                                                                                  │
    │ /cyclopts/my-script:14 in main                                                                   │
    │                                                                                                  │
    │   11                                                                                             │
    │   12 @app.default                                                                                │
    │   13 def main(name: str):                                                                        │
    │ ❱ 14 │   print(name + 3)                                                                         │
    │   15                                                                                             │
    │   16 if __name__ == "__main__":                                                                  │
    │   17 │   app()                                                                                   │
    ╰──────────────────────────────────────────────────────────────────────────────────────────────────╯
    TypeError: can only concatenate str (not "int") to str

This rich-formatted traceback provides a more readable and visually appealing representation of the error, but may make copy/pasting for sharing a bit more cumbersome.

.. _Rich library's traceback handler: https://rich.readthedocs.io/en/stable/traceback.html#printing-tracebacks
