改变标准 (Python) 测试的发现机制
===============================================

**Changing standard (Python) test discovery**

在测试收集期间忽略路径
-----------------------------------

**Ignore paths during test collection**

.. tabs::

    .. tab:: 中文

        你可以通过在命令行中传递 ``--ignore=path`` 选项，轻松忽略某些测试目录和模块。``pytest`` 允许多个 ``--ignore`` 选项。示例：

        .. code-block:: text

            tests/
            |-- example
            |   |-- test_example_01.py
            |   |-- test_example_02.py
            |   '-- test_example_03.py
            |-- foobar
            |   |-- test_foobar_01.py
            |   |-- test_foobar_02.py
            |   '-- test_foobar_03.py
            '-- hello
                '-- world
                    |-- test_world_01.py
                    |-- test_world_02.py
                    '-- test_world_03.py

        现在，如果你使用 ``--ignore=tests/foobar/test_foobar_03.py --ignore=tests/hello/`` 调用 ``pytest``，你会看到 ``pytest`` 仅收集不匹配指定模式的测试模块：

        .. code-block:: pytest

            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-5.x.y, py-1.x.y, pluggy-0.x.y
            rootdir: $REGENDOC_TMPDIR, inifile:
            collected 5 items

            tests/example/test_example_01.py .                                   [ 20%]
            tests/example/test_example_02.py .                                   [ 40%]
            tests/example/test_example_03.py .                                   [ 60%]
            tests/foobar/test_foobar_01.py .                                     [ 80%]
            tests/foobar/test_foobar_02.py .                                     [100%]

            ========================= 5 passed in 0.02 seconds =========================

        ``--ignore-glob`` 选项允许根据 Unix shell 风格的通配符忽略测试文件路径。如果你想排除以 ``_01.py`` 结尾的测试模块，可以使用 ``--ignore-glob='*_01.py'`` 执行 ``pytest``。

    .. tab:: 英文

        You can easily ignore certain test directories and modules during collection
        by passing the ``--ignore=path`` option on the cli. ``pytest`` allows multiple
        ``--ignore`` options. Example:

        .. code-block:: text

            tests/
            |-- example
            |   |-- test_example_01.py
            |   |-- test_example_02.py
            |   '-- test_example_03.py
            |-- foobar
            |   |-- test_foobar_01.py
            |   |-- test_foobar_02.py
            |   '-- test_foobar_03.py
            '-- hello
                '-- world
                    |-- test_world_01.py
                    |-- test_world_02.py
                    '-- test_world_03.py

        Now if you invoke ``pytest`` with ``--ignore=tests/foobar/test_foobar_03.py --ignore=tests/hello/``,
        you will see that ``pytest`` only collects test-modules, which do not match the patterns specified:

        .. code-block:: pytest

            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-5.x.y, py-1.x.y, pluggy-0.x.y
            rootdir: $REGENDOC_TMPDIR, inifile:
            collected 5 items

            tests/example/test_example_01.py .                                   [ 20%]
            tests/example/test_example_02.py .                                   [ 40%]
            tests/example/test_example_03.py .                                   [ 60%]
            tests/foobar/test_foobar_01.py .                                     [ 80%]
            tests/foobar/test_foobar_02.py .                                     [100%]

            ========================= 5 passed in 0.02 seconds =========================

        The ``--ignore-glob`` option allows to ignore test file paths based on Unix shell-style wildcards.
        If you want to exclude test-modules that end with ``_01.py``, execute ``pytest`` with ``--ignore-glob='*_01.py'``.

在测试收集期间取消选择测试
-------------------------------------

**Deselect tests during test collection**

.. tabs::

    .. tab:: 中文

        测试在收集期间可以通过传递 ``--deselect=item`` 选项单独取消选择。例如，假设 ``tests/foobar/test_foobar_01.py`` 包含 ``test_a`` 和 ``test_b``。你可以运行 ``tests/`` 中的所有测试，*除了* ``tests/foobar/test_foobar_01.py::test_a``，通过使用 ``--deselect tests/foobar/test_foobar_01.py::test_a`` 调用 ``pytest``。``pytest`` 允许多个 ``--deselect`` 选项。

    .. tab:: 英文

        Tests can individually be deselected during collection by passing the ``--deselect=item`` option.
        For example, say ``tests/foobar/test_foobar_01.py`` contains ``test_a`` and ``test_b``.
        You can run all of the tests within ``tests/`` *except* for ``tests/foobar/test_foobar_01.py::test_a``
        by invoking ``pytest`` with ``--deselect tests/foobar/test_foobar_01.py::test_a``.
        ``pytest`` allows multiple ``--deselect`` options.

保留从命令行指定的重复路径
----------------------------------------------------

**Keeping duplicate paths specified from command line**

.. tabs::

    .. tab:: 中文

        ``pytest`` 的默认行为是忽略从命令行指定的重复路径。示例：

        .. code-block:: pytest

            pytest path_a path_a

            ...
            collected 1 item
            ...

        只收集一次测试。

        要收集重复测试，请在命令行上使用 ``--keep-duplicates`` 选项。示例：

        .. code-block:: pytest

            pytest --keep-duplicates path_a path_a

            ...
            collected 2 items
            ...

        由于收集器只对目录工作，如果你指定了同一个测试文件两次，``pytest`` 仍然会收集两次，无论是否指定了 ``--keep-duplicates``。示例：

        .. code-block:: pytest

            pytest test_a.py test_a.py

            ...
            collected 2 items
            ...

    .. tab:: 英文

        Default behavior of ``pytest`` is to ignore duplicate paths specified from the command line.
        Example:

        .. code-block:: pytest

            pytest path_a path_a

            ...
            collected 1 item
            ...

        Just collect tests once.

        To collect duplicate tests, use the ``--keep-duplicates`` option on the cli.
        Example:

        .. code-block:: pytest

            pytest --keep-duplicates path_a path_a

            ...
            collected 2 items
            ...

        As the collector just works on directories, if you specify twice a single test file, ``pytest`` will
        still collect it twice, no matter if the ``--keep-duplicates`` is not specified.
        Example:

        .. code-block:: pytest

            pytest test_a.py test_a.py

            ...
            collected 2 items
            ...


更改目录递归
-----------------------------------------------------

**Changing directory recursion**

.. tabs::

    .. tab:: 中文

        你可以在 ini 文件中设置 :confval:`norecursedirs` 选项，例如在项目根目录中的 ``pytest.ini``：

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            norecursedirs = .svn _build tmp*

        这将告诉 ``pytest`` 不要递归进入典型的 Subversion 或 Sphinx 构建目录，或者任何以 ``tmp`` 开头的目录。

    .. tab:: 英文

        You can set the :confval:`norecursedirs` option in an ini-file, for example your ``pytest.ini`` in the project root directory:

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            norecursedirs = .svn _build tmp*

        This would tell ``pytest`` to not recurse into typical subversion or sphinx-build directories or into any ``tmp`` prefixed directory.

.. _`change naming conventions`:

更改命名约定
-----------------------------------------------------

**Changing naming conventions**

.. tabs::

    .. tab:: 中文

        你可以通过在 :ref:`配置文件 <config file formats>` 中设置 :confval:`python_files`、:confval:`python_classes` 和 :confval:`python_functions` 来配置不同的命名约定。以下是一个示例：

        .. code-block:: ini

            # content of pytest.ini
            # 示例 1：让 pytest 查找 "check" 而不是 "test"
            [pytest]
            python_files = check_*.py
            python_classes = Check
            python_functions = *_check

        这将使 ``pytest`` 查找匹配 ``check_*.py`` 通配符模式的文件、以 ``Check`` 开头的类，以及匹配 ``*_check`` 的函数和方法。例如，如果我们有：

        .. code-block:: python

            # content of check_myapp.py
            class CheckMyApp:
                def simple_check(self):
                    pass

                def complex_check(self):
                    pass

        测试收集将如下所示：

        .. code-block:: pytest

            $ pytest --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            configfile: pytest.ini
            collected 2 items

            <Dir pythoncollection.rst-205>
            <Module check_myapp.py>
                <Class CheckMyApp>
                <Function simple_check>
                <Function complex_check>

            ======================== 2 tests collected in 0.12s ========================

        你可以通过在模式之间添加空格来检查多个通配符模式：

        .. code-block:: ini

            # 示例 2：让 pytest 查找包含 "test" 和 "example" 的文件
            # content of pytest.ini
            [pytest]
            python_files = test_*.py example_*.py

        .. note::

            ``python_functions`` 和 ``python_classes`` 选项对 ``unittest.TestCase`` 测试发现没有效果，因为 pytest 将测试用例方法的发现委托给 unittest 代码。

    .. tab:: 英文

        You can configure different naming conventions by setting
        the :confval:`python_files`, :confval:`python_classes` and
        :confval:`python_functions` in your :ref:`configuration file <config file formats>`.
        Here is an example:

        .. code-block:: ini

            # content of pytest.ini
            # Example 1: have pytest look for "check" instead of "test"
            [pytest]
            python_files = check_*.py
            python_classes = Check
            python_functions = *_check

        This would make ``pytest`` look for tests in files that match the ``check_*
        .py`` glob-pattern, ``Check`` prefixes in classes, and functions and methods
        that match ``*_check``. For example, if we have:

        .. code-block:: python

            # content of check_myapp.py
            class CheckMyApp:
                def simple_check(self):
                    pass

                def complex_check(self):
                    pass

        The test collection would look like this:

        .. code-block:: pytest

            $ pytest --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            configfile: pytest.ini
            collected 2 items

            <Dir pythoncollection.rst-205>
            <Module check_myapp.py>
                <Class CheckMyApp>
                <Function simple_check>
                <Function complex_check>

            ======================== 2 tests collected in 0.12s ========================

        You can check for multiple glob patterns by adding a space between the patterns:

        .. code-block:: ini

            # Example 2: have pytest look for files with "test" and "example"
            # content of pytest.ini
            [pytest]
            python_files = test_*.py example_*.py

        .. note::

            the ``python_functions`` and ``python_classes`` options has no effect
            for ``unittest.TestCase`` test discovery because pytest delegates
            discovery of test case methods to unittest code.

将命令行参数解释为 Python 包
-----------------------------------------------------

**Interpreting cmdline arguments as Python packages**

.. tabs::

    .. tab:: 中文

        你可以使用 ``--pyargs`` 选项来让 ``pytest`` 尝试将参数解释为 Python 包名，从而推导它们的文件系统路径，然后运行测试。例如，如果你安装了 unittest2，你可以输入：

        .. code-block:: bash

            pytest --pyargs unittest2.test.test_skipping -q

        这将运行相应的测试模块。与其他选项一样，通过 ini 文件和 :confval:`addopts` 选项，你可以使这个更改更加永久：

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            addopts = --pyargs

        现在，简单调用 ``pytest NAME`` 将检查 NAME 是否作为可导入的包/模块存在，否则将其视为文件系统路径。

    .. tab:: 英文

        You can use the ``--pyargs`` option to make ``pytest`` try
        interpreting arguments as python package names, deriving
        their file system path and then running the test. For
        example if you have unittest2 installed you can type:

        .. code-block:: bash

            pytest --pyargs unittest2.test.test_skipping -q

        which would run the respective test module.  Like with
        other options, through an ini-file and the :confval:`addopts` option you
        can make this change more permanently:

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            addopts = --pyargs

        Now a simple invocation of ``pytest NAME`` will check
        if NAME exists as an importable package/module and otherwise
        treat it as a filesystem path.

找出收集的内容
-----------------------------------------------

**Finding out what is collected**

.. tabs::

    .. tab:: 中文

        您随时可以查看集合树而无需运行这样的测试：

        .. code-block:: pytest

            . $ pytest --collect-only pythoncollection.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            configfile: pytest.ini
            collected 3 items

            <Dir pythoncollection.rst-205>
            <Dir CWD>
                <Module pythoncollection.py>
                <Function test_function>
                <Class TestClass>
                    <Function test_method>
                    <Function test_anothermethod>

            ======================== 3 tests collected in 0.12s ========================

    .. tab:: 英文

        You can always peek at the collection tree without running tests like this:

        .. code-block:: pytest

            . $ pytest --collect-only pythoncollection.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            configfile: pytest.ini
            collected 3 items

            <Dir pythoncollection.rst-205>
            <Dir CWD>
                <Module pythoncollection.py>
                <Function test_function>
                <Class TestClass>
                    <Function test_method>
                    <Function test_anothermethod>

            ======================== 3 tests collected in 0.12s ========================

.. _customizing-test-collection:

自定义测试收集
---------------------------

**Customizing test collection**

.. tabs::

    .. tab:: 中文

        你可以轻松指示 ``pytest`` 从每个 Python 文件中发现测试：

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            python_files = *.py

        然而，许多项目会有一个 ``setup.py``，不希望被导入。此外，可能还有一些文件只能被特定版本的 Python 导入。对于这种情况，你可以通过在 ``conftest.py`` 文件中动态定义要忽略的文件：

        .. code-block:: python

            # content of conftest.py
            import sys

            collect_ignore = ["setup.py"]
            if sys.version_info[0] > 2:
                collect_ignore.append("pkg/module_py2.py")

        然后，如果你有一个模块文件如下：

        .. code-block:: python

            # content of pkg/module_py2.py
            def test_only_on_python2():
                try:
                    assert 0
                except Exception as e:
                    pass

        以及一个 ``setup.py`` 的虚拟文件如下：

        .. code-block:: python

            # content of setup.py
            0 / 0  # will raise exception if imported

        如果你使用 Python 2 解释器运行，那么你将发现一个测试，并且会忽略 ``setup.py`` 文件：

        .. code-block:: pytest

            #$ pytest --collect-only
            ====== test session starts ======
            platform linux2 -- Python 2.7.10, pytest-2.9.1, py-1.4.31, pluggy-0.3.1
            rootdir: $REGENDOC_TMPDIR, inifile: pytest.ini
            collected 1 items
            <Module 'pkg/module_py2.py'>
            <Function 'test_only_on_python2'>

            ====== 1 tests found in 0.04 seconds ======

        如果你使用 Python 3 解释器，那个测试和 ``setup.py`` 文件都将被忽略：

        .. code-block:: pytest

            $ pytest --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            configfile: pytest.ini
            collected 0 items

            ======================= no tests collected in 0.12s ========================

        你还可以通过将模式添加到 :globalvar:`collect_ignore_glob`，基于 Unix shell 风格的通配符来忽略文件。

        以下示例 ``conftest.py`` 在使用 Python 3 解释器执行时，忽略 ``setup.py`` 文件以及所有以 ``*_py2.py`` 结尾的文件：

        .. code-block:: python

            # content of conftest.py
            import sys

            collect_ignore = ["setup.py"]
            if sys.version_info[0] > 2:
                collect_ignore_glob = ["*_py2.py"]

        自 Pytest 2.6 起，用户可以通过将布尔 ``__test__`` 属性设置为 ``False`` 来防止 pytest 发现以 ``Test`` 开头的类。

        .. code-block:: python

            # Will not be discovered as a test
            class TestClass:
                __test__ = False

    .. tab:: 英文

        .. regendoc:wipe

        You can easily instruct ``pytest`` to discover tests from every Python file:

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            python_files = *.py

        However, many projects will have a ``setup.py`` which they don't want to be
        imported. Moreover, there may files only importable by a specific python
        version. For such cases you can dynamically define files to be ignored by
        listing them in a ``conftest.py`` file:

        .. code-block:: python

            # content of conftest.py
            import sys

            collect_ignore = ["setup.py"]
            if sys.version_info[0] > 2:
                collect_ignore.append("pkg/module_py2.py")

        and then if you have a module file like this:

        .. code-block:: python

            # content of pkg/module_py2.py
            def test_only_on_python2():
                try:
                    assert 0
                except Exception, e:
                    pass

        and a ``setup.py`` dummy file like this:

        .. code-block:: python

            # content of setup.py
            0 / 0  # will raise exception if imported

        If you run with a Python 2 interpreter then you will find the one test and will
        leave out the ``setup.py`` file:

        .. code-block:: pytest

            #$ pytest --collect-only
            ====== test session starts ======
            platform linux2 -- Python 2.7.10, pytest-2.9.1, py-1.4.31, pluggy-0.3.1
            rootdir: $REGENDOC_TMPDIR, inifile: pytest.ini
            collected 1 items
            <Module 'pkg/module_py2.py'>
            <Function 'test_only_on_python2'>

            ====== 1 tests found in 0.04 seconds ======

        If you run with a Python 3 interpreter both the one test and the ``setup.py``
        file will be left out:

        .. code-block:: pytest

            $ pytest --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            configfile: pytest.ini
            collected 0 items

            ======================= no tests collected in 0.12s ========================

        It's also possible to ignore files based on Unix shell-style wildcards by adding
        patterns to :globalvar:`collect_ignore_glob`.

        The following example ``conftest.py`` ignores the file ``setup.py`` and in
        addition all files that end with ``*_py2.py`` when executed with a Python 3
        interpreter:

        .. code-block:: python

            # content of conftest.py
            import sys

            collect_ignore = ["setup.py"]
            if sys.version_info[0] > 2:
                collect_ignore_glob = ["*_py2.py"]

        Since Pytest 2.6, users can prevent pytest from discovering classes that start
        with ``Test`` by setting a boolean ``__test__`` attribute to ``False``.

        .. code-block:: python

            # Will not be discovered as a test
            class TestClass:
                __test__ = False
