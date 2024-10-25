
.. _usage:

如何调用 pytest
==========================================

**How to invoke pytest**

.. tabs::

    .. tab:: 中文

        ..  seealso:: :ref:`完整的 pytest 命令行标志参考 <command-line-flags>`

        通常，pytest 使用命令 ``pytest`` 调用（请参阅下文 :ref:`调用 pytest 的其他方法 <invoke-other>` ）。这将执行当前目录及其子目录中所有名称遵循 ``test_*.py`` 或 ``\*_test.py`` 格式的文件中的所有测试。更一般地，pytest 遵循 :ref:`标准测试发现规则 <test discovery>`。

    .. tab:: 英文

        ..  seealso:: :ref:`Complete pytest command-line flag reference <command-line-flags>`

        In general, pytest is invoked with the command ``pytest`` (see below for :ref:`other ways to invoke pytest
        <invoke-other>`). This will execute all tests in all files whose names follow the form ``test_*.py`` or ``\*_test.py``
        in the current directory and its subdirectories. More generally, pytest follows :ref:`standard test discovery rules
        <test discovery>`.


.. _select-tests:

指定要运行的测试
------------------------------

**Specifying which tests to run**

.. tabs::

    .. tab:: 中文

        Pytest 支持多种方式从命令行或文件中运行和选择测试（有关 :ref:`从文件读取参数 <args-from-file>` 的详细信息，请参见下面）。

        **在模块中运行测试**

        .. code-block:: bash

            pytest test_mod.py

        **在目录中运行测试**

        .. code-block:: bash

            pytest testing/

        **通过关键字表达式运行测试**

        .. code-block:: bash

            pytest -k 'MyClass and not method'

        这将运行包含与给定 *字符串表达式*（不区分大小写）匹配的名称的测试，其中可以包含使用文件名、类名和函数名作为变量的 Python 运算符。上述示例将运行 ``TestMyClass.test_something`` 但不运行 ``TestMyClass.test_method_simple``。在 Windows 上运行时，在表达式中使用 ``""`` 而不是 ``''``。

        .. _nodeids:

        **通过集合参数运行测试**

        传递相对于工作目录的模块文件名，后跟用 ``::`` 字符分隔的类名和函数名，以及用 ``[]`` 括起来的参数。

        要在模块中运行特定测试：

        .. code-block:: bash

            pytest tests/test_mod.py::test_func

        要在类中运行所有测试：

        .. code-block:: bash

            pytest tests/test_mod.py::TestClass

        指定特定测试方法：

        .. code-block:: bash

            pytest tests/test_mod.py::TestClass::test_method

        指定测试的特定参数化：

        .. code-block:: bash

            pytest tests/test_mod.py::test_func[x1,y2]

        **通过标记表达式运行测试**

        要运行所有使用 ``@pytest.mark.slow`` 装饰器装饰的测试：

        .. code-block:: bash

            pytest -m slow

        要运行所有使用带注解的 ``@pytest.mark.slow(phase=1)`` 装饰器装饰的测试，其中 ``phase`` 关键字参数设置为 ``1``：

        .. code-block:: bash

            pytest -m "slow(phase=1)"

        有关更多信息，请参见 :ref:`标记 <mark>`。

        **从包中运行测试**

        .. code-block:: bash

            pytest --pyargs pkg.testing

        这将导入 ``pkg.testing`` 并使用其文件系统位置查找并运行测试。

        .. _args-from-file:

        **从文件读取参数**

        .. versionadded:: 8.2

        以上所有内容都可以使用 ``@`` 前缀从文件中读取：

        .. code-block:: bash

            pytest @tests_to_run.txt

        其中 ``tests_to_run.txt`` 每行包含一个条目，例如：

        .. code-block:: text

            tests/test_file.py
            tests/test_mod.py::test_func[x1,y2]
            tests/test_mod.py::TestClass
            -m slow

        此文件也可以使用 ``pytest --collect-only -q`` 生成，并根据需要进行修改。

    .. tab:: 英文

        Pytest supports several ways to run and select tests from the command-line or from a file
        (see below for :ref:`reading arguments from file <args-from-file>`).

        **Run tests in a module**

        .. code-block:: bash

            pytest test_mod.py

        **Run tests in a directory**

        .. code-block:: bash

            pytest testing/

        **Run tests by keyword expressions**

        .. code-block:: bash

            pytest -k 'MyClass and not method'

        This will run tests which contain names that match the given *string expression* (case-insensitive),
        which can include Python operators that use filenames, class names and function names as variables.
        The example above will run ``TestMyClass.test_something``  but not ``TestMyClass.test_method_simple``.
        Use ``""`` instead of ``''`` in expression when running this on Windows

        .. _nodeids:

        **Run tests by collection arguments**

        Pass the module filename relative to the working directory, followed by specifiers like the class name and function name
        separated by ``::`` characters, and parameters from parameterization enclosed in ``[]``.

        To run a specific test within a module:

        .. code-block:: bash

            pytest tests/test_mod.py::test_func

        To run all tests in a class:

        .. code-block:: bash

            pytest tests/test_mod.py::TestClass

        Specifying a specific test method:

        .. code-block:: bash

            pytest tests/test_mod.py::TestClass::test_method

        Specifying a specific parametrization of a test:

        .. code-block:: bash

            pytest tests/test_mod.py::test_func[x1,y2]

        **Run tests by marker expressions**

        To run all tests which are decorated with the ``@pytest.mark.slow`` decorator:

        .. code-block:: bash

            pytest -m slow


        To run all tests which are decorated with the annotated ``@pytest.mark.slow(phase=1)`` decorator,
        with the ``phase`` keyword argument set to ``1``:

        .. code-block:: bash

            pytest -m "slow(phase=1)"

        For more information see :ref:`marks <mark>`.

        **Run tests from packages**

        .. code-block:: bash

            pytest --pyargs pkg.testing

        This will import ``pkg.testing`` and use its filesystem location to find and run tests from.

        .. _args-from-file:

        **Read arguments from file**

        .. versionadded:: 8.2

        All of the above can be read from a file using the ``@`` prefix:

        .. code-block:: bash

            pytest @tests_to_run.txt

        where ``tests_to_run.txt`` contains an entry per line, e.g.:

        .. code-block:: text

            tests/test_file.py
            tests/test_mod.py::test_func[x1,y2]
            tests/test_mod.py::TestClass
            -m slow

        This file can also be generated using ``pytest --collect-only -q`` and modified as needed.

获取有关版本、选项名称、环境变量的帮助
--------------------------------------------------------------

**Getting help on version, option names, environment variables**

.. tabs::

    .. tab:: 中文

        .. code-block:: bash

            pytest --version # 显示 pytest 的导入位置
            pytest --fixtures # 显示可用的内置函数参数
            pytest -h | --help # 显示命令行和配置文件选项的帮助

    .. tab:: 英文

        .. code-block:: bash

            pytest --version   # shows where pytest was imported from
            pytest --fixtures  # show available builtin function arguments
            pytest -h | --help # show help on command line and config file options


.. _durations:

分析测试执行持续时间
-------------------------------------

**Profiling test execution duration**

.. tabs::

    .. tab:: 中文

        .. versionchanged:: 6.0

        要获取持续时间超过 1.0 秒的最慢 10 个测试持续时间列表：

        .. code-block:: bash

            pytest --durations=10 --durations-min=1.0

        默认情况下，除非在命令行上传递 ``-vv``，否则 pytest 不会显示太短的测试持续时间（<0.005 秒）。

    .. tab:: 英文

        .. versionchanged:: 6.0

        To get a list of the slowest 10 test durations over 1.0s long:

        .. code-block:: bash

            pytest --durations=10 --durations-min=1.0

        By default, pytest will not show test durations that are too small (<0.005s) unless ``-vv`` is passed on the command-line.


管理插件的加载
-------------------------------

**Managing loading of plugins**

提前加载插件
~~~~~~~~~~~~~~~~~~~~~~~

**Early loading plugins**

.. tabs::

    .. tab:: 中文

        您可以使用 ``-p`` 选项在命令行中显式地提前加载插件（内部和外部）::

            pytest -p mypluginmodule

        该选项接收 ``name`` 参数，可以是：

        * 完整模块的点名，例如 ``myproject.plugins``。此点名必须是可导入的。
        * 插件的入口点名称。这是在注册插件时传递给 ``importlib`` 的名称。例如，要提前加载 :pypi:`pytest-cov` 插件，您可以使用::

            pytest -p pytest_cov

    .. tab:: 英文

        You can early-load plugins (internal and external) explicitly in the command-line with the ``-p`` option::

            pytest -p mypluginmodule

        The option receives a ``name`` parameter, which can be:

        * A full module dotted name, for example ``myproject.plugins``. This dotted name must be importable.
        * The entry-point name of a plugin. This is the name passed to ``importlib`` when the plugin is
        registered. For example to early-load the :pypi:`pytest-cov` plugin you can use::

            pytest -p pytest_cov


禁用插件
~~~~~~~~~~~~~~~~~~

**Disabling plugins**

.. tabs::

    .. tab:: 中文

        要在调用时禁用加载特定插件，请使用 ``-p`` 选项以及前缀 ``no:``。

        示例：要禁用加载插件 ``doctest``（该插件负责从文本文件执行 doctest 测试），请像这样调用 pytest:

        .. code-block:: bash

            pytest -p no:doctest

    .. tab:: 英文

        To disable loading specific plugins at invocation time, use the ``-p`` option
        together with the prefix ``no:``.

        Example: to disable loading the plugin ``doctest``, which is responsible for
        executing doctest tests from text files, invoke pytest like this:

        .. code-block:: bash

            pytest -p no:doctest


.. _invoke-other:

调用 pytest 的其他方式
-----------------------------------------------------

**Other ways of calling pytest**

.. _invoke-python:

通过 ``python -m pytest`` 调用 pytest
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Calling pytest through** ``python -m pytest``

.. tabs::

    .. tab:: 中文

        您可以从命令行通过 Python 解释器调用测试：

        .. code-block:: text

            python -m pytest [...]

        这几乎相当于直接调用命令行脚本 ``pytest [...]`` ，只是通过 ``python`` 调用也会将当前目录添加到 ``sys.path``。

    .. tab:: 英文

        You can invoke testing through the Python interpreter from the command line:

        .. code-block:: text

            python -m pytest [...]

        This is almost equivalent to invoking the command line script ``pytest [...]``
        directly, except that calling via ``python`` will also add the current directory to ``sys.path``.


.. _`pytest.main-usage`:

从 Python 代码调用 pytest
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Calling pytest from Python code**

.. tabs::

    .. tab:: 中文

        您可以直接从 Python 代码中调用 ``pytest``：

        .. code-block:: python

            retcode = pytest.main()

        这就像从命令行调用 "pytest" 一样。
        它不会引发 :class:`SystemExit`，而是返回 :ref:`退出代码 <exit-codes>`。
        如果您没有传递任何参数，``main`` 会从进程的命令行参数 (:data:`sys.argv`) 读取参数，这可能是不希望的。
        您可以显式传递选项和参数：

        .. code-block:: python

            retcode = pytest.main(["-x", "mytestdir"])

        您可以为 ``pytest.main`` 指定额外的插件：

        .. code-block:: python

            # myinvoke.py 的内容
            import sys

            import pytest


            class MyPlugin:
                def pytest_sessionfinish(self):
                    print("*** 测试运行报告结束")


            if __name__ == "__main__":
                sys.exit(pytest.main(["-qq"], plugins=[MyPlugin()]))

        运行它将显示 ``MyPlugin`` 被添加并且其
        钩子被调用：

        .. code-block:: pytest

            $ python myinvoke.py
            *** 测试运行报告结束


        .. note::

            调用 ``pytest.main()`` 将导致导入您的测试及其导入的任何模块。
            由于 Python 导入系统的缓存机制，
            从同一进程中对 ``pytest.main()`` 的后续调用不会反映调用之间对这些文件的更改。因此，从同一进程中多次调用 ``pytest.main()``（例如，为了重新运行测试）是不推荐的。

    .. tab:: 英文

        You can invoke ``pytest`` from Python code directly:

        .. code-block:: python

            retcode = pytest.main()

        this acts as if you would call "pytest" from the command line.
        It will not raise :class:`SystemExit` but return the :ref:`exit code <exit-codes>` instead.
        If you don't pass it any arguments, ``main`` reads the arguments from the command line arguments of the process (:data:`sys.argv`), which may be undesirable.
        You can pass in options and arguments explicitly:

        .. code-block:: python

            retcode = pytest.main(["-x", "mytestdir"])

        You can specify additional plugins to ``pytest.main``:

        .. code-block:: python

            # content of myinvoke.py
            import sys

            import pytest


            class MyPlugin:
                def pytest_sessionfinish(self):
                    print("*** test run reporting finishing")


            if __name__ == "__main__":
                sys.exit(pytest.main(["-qq"], plugins=[MyPlugin()]))

        Running it will show that ``MyPlugin`` was added and its
        hook was invoked:

        .. code-block:: pytest

            $ python myinvoke.py
            *** test run reporting finishing


        .. note::

            Calling ``pytest.main()`` will result in importing your tests and any modules
            that they import. Due to the caching mechanism of python's import system,
            making subsequent calls to ``pytest.main()`` from the same process will not
            reflect changes to those files between the calls. For this reason, making
            multiple calls to ``pytest.main()`` from the same process (in order to re-run
            tests, for example) is not recommended.
