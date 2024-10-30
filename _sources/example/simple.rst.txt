
基本模式和示例
==========================================================

**Basic patterns and examples**

如何更改命令行选项默认值
-------------------------------------------

**How to change command line options defaults**

.. tabs::

    .. tab:: 中文

        每次使用 ``pytest`` 时输入相同的命令行选项可能会很繁琐。例如，如果您总是想查看被跳过和 xfailed 测试的详细信息，以及更简洁的“点”进度输出，您可以将其写入配置文件中：

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            addopts = -ra -q


        另外，您可以设置一个 ``PYTEST_ADDOPTS`` 环境变量，以便在环境使用时添加命令行选项：

        .. code-block:: bash

            export PYTEST_ADDOPTS="-v"

        以下是当存在 ``addopts`` 或环境变量时，命令行是如何构建的：

        .. code-block:: text

            <pytest.ini:addopts> $PYTEST_ADDOPTS <extra command-line arguments>

        因此，如果用户在命令行中执行：

        .. code-block:: bash

            pytest -m slow

        实际执行的命令行是：

        .. code-block:: bash

            pytest -ra -q -v -m slow

        请注意，和其他命令行应用程序一样，在选项冲突的情况下，最后一个选项优先，因此上述示例将显示详细输出，因为 ``-v`` 会覆盖 ``-q``。

    .. tab:: 英文

        It can be tedious to type the same series of command line options
        every time you use ``pytest``.  For example, if you always want to see
        detailed info on skipped and xfailed tests, as well as have terser "dot"
        progress output, you can write it into a configuration file:

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            addopts = -ra -q


        Alternatively, you can set a ``PYTEST_ADDOPTS`` environment variable to add command
        line options while the environment is in use:

        .. code-block:: bash

            export PYTEST_ADDOPTS="-v"

        Here's how the command-line is built in the presence of ``addopts`` or the environment variable:

        .. code-block:: text

            <pytest.ini:addopts> $PYTEST_ADDOPTS <extra command-line arguments>

        So if the user executes in the command-line:

        .. code-block:: bash

            pytest -m slow

        The actual command line executed is:

        .. code-block:: bash

            pytest -ra -q -v -m slow

        Note that as usual for other command-line applications, in case of conflicting options the last one wins, so the example
        above will show verbose output because ``-v`` overwrites ``-q``.


.. _request example:

根据命令行选项将不同的值传递给测试函数
----------------------------------------------------------------------------

**Pass different values to a test function, depending on command line options**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        假设我们想写一个依赖于命令行选项的测试。这里有一个基本的模式来实现这一点：

        .. code-block:: python

            # content of test_sample.py
            def test_answer(cmdopt):
                if cmdopt == "type1":
                    print("first")
                elif cmdopt == "type2":
                    print("second")
                assert 0  # to see what was printed


        为了使其工作，我们需要添加一个命令行选项，并通过 :ref:`fixture function <fixture>` 提供 ``cmdopt``：

        .. code-block:: python

            # content of conftest.py
            import pytest


            def pytest_addoption(parser):
                parser.addoption(
                    "--cmdopt", action="store", default="type1", help="my option: type1 or type2"
                )


            @pytest.fixture
            def cmdopt(request):
                return request.config.getoption("--cmdopt")

        让我们在不提供新选项的情况下运行这个测试：

        .. code-block:: pytest

            $ pytest -q test_sample.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            _______________________________ test_answer ________________________________

            cmdopt = 'type1'

                def test_answer(cmdopt):
                    if cmdopt == "type1":
                        print("first")
                    elif cmdopt == "type2":
                        print("second")
            >       assert 0  # to see what was printed
            E       assert 0

            test_sample.py:6: AssertionError
            --------------------------- Captured stdout call ---------------------------
            first
            ========================= short test summary info ==========================
            FAILED test_sample.py::test_answer - assert 0
            1 failed in 0.12s

        现在，让我们在提供命令行选项的情况下运行：

        .. code-block:: pytest

            $ pytest -q --cmdopt=type2
            F                                                                    [100%]
            ================================= FAILURES =================================
            _______________________________ test_answer ________________________________

            cmdopt = 'type2'

                def test_answer(cmdopt):
                    if cmdopt == "type1":
                        print("first")
                    elif cmdopt == "type2":
                        print("second")
            >       assert 0  # to see what was printed
            E       assert 0

            test_sample.py:6: AssertionError
            --------------------------- Captured stdout call ---------------------------
            second
            ========================= short test summary info ==========================
            FAILED test_sample.py::test_answer - assert 0
            1 failed in 0.12s

        您可以看到命令行选项已经传递到我们的测试中。

        我们可以通过列出选项来对输入进行简单验证：

        .. code-block:: python

            # content of conftest.py
            import pytest


            def pytest_addoption(parser):
                parser.addoption(
                    "--cmdopt",
                    action="store",
                    default="type1",
                    help="my option: type1 or type2",
                    choices=("type1", "type2"),
                )

        现在，如果提供了错误的参数，我们将得到反馈：

        .. code-block:: pytest

            $ pytest -q --cmdopt=type3
            ERROR: usage: pytest [options] [file_or_dir] [file_or_dir] [...]
            pytest: error: argument --cmdopt: invalid choice: 'type3' (choose from 'type1', 'type2')


        如果您需要提供更详细的错误消息，可以使用 ``type`` 参数并引发 :exc:`pytest.UsageError`：

        .. code-block:: python

            # content of conftest.py
            import pytest


            def type_checker(value):
                msg = "cmdopt must specify a numeric type as typeNNN"
                if not value.startswith("type"):
                    raise pytest.UsageError(msg)
                try:
                    int(value[4:])
                except ValueError:
                    raise pytest.UsageError(msg)

                return value


            def pytest_addoption(parser):
                parser.addoption(
                    "--cmdopt",
                    action="store",
                    default="type1",
                    help="my option: type1 or type2",
                    type=type_checker,
                )

        这完成了基本模式。然而，通常人们希望在测试外部处理命令行选项，而是传递不同或更复杂的对象。

    .. tab:: 英文

        .. regendoc:wipe

        Suppose we want to write a test that depends on a command line option.
        Here is a basic pattern to achieve this:

        .. code-block:: python

            # content of test_sample.py
            def test_answer(cmdopt):
                if cmdopt == "type1":
                    print("first")
                elif cmdopt == "type2":
                    print("second")
                assert 0  # to see what was printed


        For this to work we need to add a command line option and
        provide the ``cmdopt`` through a :ref:`fixture function <fixture>`:

        .. code-block:: python

            # content of conftest.py
            import pytest


            def pytest_addoption(parser):
                parser.addoption(
                    "--cmdopt", action="store", default="type1", help="my option: type1 or type2"
                )


            @pytest.fixture
            def cmdopt(request):
                return request.config.getoption("--cmdopt")

        Let's run this without supplying our new option:

        .. code-block:: pytest

            $ pytest -q test_sample.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            _______________________________ test_answer ________________________________

            cmdopt = 'type1'

                def test_answer(cmdopt):
                    if cmdopt == "type1":
                        print("first")
                    elif cmdopt == "type2":
                        print("second")
            >       assert 0  # to see what was printed
            E       assert 0

            test_sample.py:6: AssertionError
            --------------------------- Captured stdout call ---------------------------
            first
            ========================= short test summary info ==========================
            FAILED test_sample.py::test_answer - assert 0
            1 failed in 0.12s

        And now with supplying a command line option:

        .. code-block:: pytest

            $ pytest -q --cmdopt=type2
            F                                                                    [100%]
            ================================= FAILURES =================================
            _______________________________ test_answer ________________________________

            cmdopt = 'type2'

                def test_answer(cmdopt):
                    if cmdopt == "type1":
                        print("first")
                    elif cmdopt == "type2":
                        print("second")
            >       assert 0  # to see what was printed
            E       assert 0

            test_sample.py:6: AssertionError
            --------------------------- Captured stdout call ---------------------------
            second
            ========================= short test summary info ==========================
            FAILED test_sample.py::test_answer - assert 0
            1 failed in 0.12s

        You can see that the command line option arrived in our test.

        We could add simple validation for the input by listing the choices:

        .. code-block:: python

            # content of conftest.py
            import pytest


            def pytest_addoption(parser):
                parser.addoption(
                    "--cmdopt",
                    action="store",
                    default="type1",
                    help="my option: type1 or type2",
                    choices=("type1", "type2"),
                )

        Now we'll get feedback on a bad argument:

        .. code-block:: pytest

            $ pytest -q --cmdopt=type3
            ERROR: usage: pytest [options] [file_or_dir] [file_or_dir] [...]
            pytest: error: argument --cmdopt: invalid choice: 'type3' (choose from 'type1', 'type2')


        If you need to provide more detailed error messages, you can use the
        ``type`` parameter and raise :exc:`pytest.UsageError`:

        .. code-block:: python

            # content of conftest.py
            import pytest


            def type_checker(value):
                msg = "cmdopt must specify a numeric type as typeNNN"
                if not value.startswith("type"):
                    raise pytest.UsageError(msg)
                try:
                    int(value[4:])
                except ValueError:
                    raise pytest.UsageError(msg)

                return value


            def pytest_addoption(parser):
                parser.addoption(
                    "--cmdopt",
                    action="store",
                    default="type1",
                    help="my option: type1 or type2",
                    type=type_checker,
                )

        This completes the basic pattern.  However, one often rather wants to
        process command line options outside of the test and rather pass in
        different or more complex objects.

动态添加命令行选项
--------------------------------------------------------------

**Dynamically adding command line options**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        通过 :confval:`addopts`，您可以静态地为您的项目添加命令行选项。您还可以在命令行参数被处理之前动态修改它们：

        .. code-block:: python

            # installable external plugin
            import sys


            def pytest_load_initial_conftests(args):
                if "xdist" in sys.modules:  # pytest-xdist 插件
                    import multiprocessing

                    num = max(multiprocessing.cpu_count() / 2, 1)
                    args[:] = ["-n", str(num)] + args

        如果您安装了 :pypi:`xdist plugin <pytest-xdist>`，您现在将始终使用接近您 CPU 数量的子进程执行测试运行。在一个空目录中运行上述 conftest.py：

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 0 items

            ========================== no tests ran in 0.12s ===========================

    .. tab:: 英文

        .. regendoc:wipe

        Through :confval:`addopts` you can statically add command line
        options for your project.  You can also dynamically modify
        the command line arguments before they get processed:

        .. code-block:: python

            # installable external plugin
            import sys


            def pytest_load_initial_conftests(args):
                if "xdist" in sys.modules:  # pytest-xdist plugin
                    import multiprocessing

                    num = max(multiprocessing.cpu_count() / 2, 1)
                    args[:] = ["-n", str(num)] + args

        If you have the :pypi:`xdist plugin <pytest-xdist>` installed
        you will now always perform test runs using a number
        of subprocesses close to your CPU. Running in an empty
        directory with the above conftest.py:

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 0 items

            ========================== no tests ran in 0.12s ===========================

.. _`excontrolskip`:

根据命令行选项控制跳过测试
--------------------------------------------------------------

**Control skipping of tests according to command line option**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        这是一个 ``conftest.py`` 文件，它添加了一个 ``--runslow`` 命令行选项，用于控制跳过标记为 ``pytest.mark.slow`` 的测试：

        .. code-block:: python

            # content of conftest.py

            import pytest


            def pytest_addoption(parser):
                parser.addoption(
                    "--runslow", action="store_true", default=False, help="运行慢测试"
                )


            def pytest_configure(config):
                config.addinivalue_line("markers", "slow: 将测试标记为慢测试")


            def pytest_collection_modifyitems(config, items):
                if config.getoption("--runslow"):
                    # 在命令行中给定了 --runslow：不跳过慢测试
                    return
                skip_slow = pytest.mark.skip(reason="需要 --runslow 选项才能运行")
                for item in items:
                    if "slow" in item.keywords:
                        item.add_marker(skip_slow)

        现在我们可以编写一个测试模块，如下所示：

        .. code-block:: python

            # content of test_module.py
            import pytest


            def test_func_fast():
                pass


            @pytest.mark.slow
            def test_func_slow():
                pass

        当运行时，它将看到一个被跳过的“慢”测试：

        .. code-block:: pytest

            $ pytest -rs    # "-rs" 表示报告小写 's' 的详细信息
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py .s                                                    [100%]

            ========================= short test summary info ==========================
            SKIPPED [1] test_module.py:8: 需要 --runslow 选项才能运行
            ======================= 1 passed, 1 skipped in 0.12s =======================

        或者运行它，包括标记为 ``slow`` 的测试：

        .. code-block:: pytest

            $ pytest --runslow
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py ..                                                    [100%]

            ============================ 2 passed in 0.12s =============================

    .. tab:: 英文

        .. regendoc:wipe

        Here is a ``conftest.py`` file adding a ``--runslow`` command
        line option to control skipping of ``pytest.mark.slow`` marked tests:

        .. code-block:: python

            # content of conftest.py

            import pytest


            def pytest_addoption(parser):
                parser.addoption(
                    "--runslow", action="store_true", default=False, help="run slow tests"
                )


            def pytest_configure(config):
                config.addinivalue_line("markers", "slow: mark test as slow to run")


            def pytest_collection_modifyitems(config, items):
                if config.getoption("--runslow"):
                    # --runslow given in cli: do not skip slow tests
                    return
                skip_slow = pytest.mark.skip(reason="need --runslow option to run")
                for item in items:
                    if "slow" in item.keywords:
                        item.add_marker(skip_slow)

        We can now write a test module like this:

        .. code-block:: python

            # content of test_module.py
            import pytest


            def test_func_fast():
                pass


            @pytest.mark.slow
            def test_func_slow():
                pass

        and when running it will see a skipped "slow" test:

        .. code-block:: pytest

            $ pytest -rs    # "-rs" means report details on the little 's'
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py .s                                                    [100%]

            ========================= short test summary info ==========================
            SKIPPED [1] test_module.py:8: need --runslow option to run
            ======================= 1 passed, 1 skipped in 0.12s =======================

        Or run it including the ``slow`` marked test:

        .. code-block:: pytest

            $ pytest --runslow
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py ..                                                    [100%]

            ============================ 2 passed in 0.12s =============================

.. _`__tracebackhide__`:

编写良好的集成断言助手
-----------------------------------------

**Writing well integrated assertion helpers**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        如果你有一个在测试中调用的测试辅助函数，可以使用 ``pytest.fail`` 标记来以特定消息使测试失败。如果在辅助函数中设置 ``__tracebackhide__`` 选项，测试支持函数将不会出现在 traceback 中。示例：

        .. code-block:: python

            # content of test_checkconfig.py
            import pytest


            def checkconfig(x):
                __tracebackhide__ = True
                if not hasattr(x, "config"):
                    pytest.fail(f"未配置: {x}")


            def test_something():
                checkconfig(42)

        ``__tracebackhide__`` 设置影响 ``pytest`` 显示 traceback 的方式：除非指定 ``--full-trace`` 命令行选项，否则 ``checkconfig`` 函数不会显示。让我们运行这个小函数：

        .. code-block:: pytest

            $ pytest -q test_checkconfig.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            ______________________________ test_something ______________________________

                def test_something():
            >       checkconfig(42)
            E       Failed: 未配置: 42

            test_checkconfig.py:11: Failed
            ========================= short test summary info ==========================
            FAILED test_checkconfig.py::test_something - Failed: 未配置: 42
            1 failed in 0.12s

        如果你只想隐藏某些异常，可以将 ``__tracebackhide__`` 设置为一个可调用对象，该对象接收 ``ExceptionInfo`` 对象。例如，你可以使用它来确保未预期的异常类型不会被隐藏：

        .. code-block:: python

            import operator

            import pytest


            class ConfigException(Exception):
                pass


            def checkconfig(x):
                __tracebackhide__ = operator.methodcaller("errisinstance", ConfigException)
                if not hasattr(x, "config"):
                    raise ConfigException(f"未配置: {x}")


            def test_something():
                checkconfig(42)

        这样可以避免在与不相关的异常（即断言辅助函数中的错误）时隐藏异常的 traceback。

    .. tab:: 英文

        .. regendoc:wipe

        If you have a test helper function called from a test you can
        use the ``pytest.fail`` marker to fail a test with a certain message.
        The test support function will not show up in the traceback if you
        set the ``__tracebackhide__`` option somewhere in the helper function.
        Example:

        .. code-block:: python

            # content of test_checkconfig.py
            import pytest


            def checkconfig(x):
                __tracebackhide__ = True
                if not hasattr(x, "config"):
                    pytest.fail(f"not configured: {x}")


            def test_something():
                checkconfig(42)

        The ``__tracebackhide__`` setting influences ``pytest`` showing
        of tracebacks: the ``checkconfig`` function will not be shown
        unless the ``--full-trace`` command line option is specified.
        Let's run our little function:

        .. code-block:: pytest

            $ pytest -q test_checkconfig.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            ______________________________ test_something ______________________________

                def test_something():
            >       checkconfig(42)
            E       Failed: not configured: 42

            test_checkconfig.py:11: Failed
            ========================= short test summary info ==========================
            FAILED test_checkconfig.py::test_something - Failed: not configured: 42
            1 failed in 0.12s

        If you only want to hide certain exceptions, you can set ``__tracebackhide__``
        to a callable which gets the ``ExceptionInfo`` object. You can for example use
        this to make sure unexpected exception types aren't hidden:

        .. code-block:: python

            import operator

            import pytest


            class ConfigException(Exception):
                pass


            def checkconfig(x):
                __tracebackhide__ = operator.methodcaller("errisinstance", ConfigException)
                if not hasattr(x, "config"):
                    raise ConfigException(f"not configured: {x}")


            def test_something():
                checkconfig(42)

        This will avoid hiding the exception traceback on unrelated exceptions (i.e.
        bugs in assertion helpers).


检测是否在 pytest 运行中运行
--------------------------------------------------------------

**Detect if running from within a pytest run**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        通常来说，让应用程序代码在测试中被调用时表现不同是个坏主意。但是，如果你确实需要确定你的应用程序代码是否是在测试中运行，你可以这样做：

        .. code-block:: python

            import os


            if os.environ.get("PYTEST_VERSION") is not None:
                # 如果你的代码是由 pytest 调用，你想要做的事情。
                ...
            else:
                # 如果你的代码不是由 pytest 调用，你想要做的事情。
                ...

    .. tab:: 英文

        .. regendoc:wipe

        Usually it is a bad idea to make application code
        behave differently if called from a test.  But if you
        absolutely must find out if your application code is
        running from a test you can do this:

        .. code-block:: python

            import os


            if os.environ.get("PYTEST_VERSION") is not None:
                # Things you want to to do if your code is called by pytest.
                ...
            else:
                # Things you want to to do if your code is not called by pytest.
                ...


向测试报告标题添加信息
--------------------------------------------------------------

**Adding info to test report header**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        在 ``pytest`` 运行中呈现额外信息很简单：

        .. code-block:: python

            # content of conftest.py

            def pytest_report_header(config):
                return "project deps: mylib-1.1"

        这将相应地将字符串添加到测试头中：

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            project deps: mylib-1.1
            rootdir: /home/sweet/project
            collected 0 items

            ========================== no tests ran in 0.12s ===========================

        .. regendoc:wipe

        也可以返回一个字符串列表，这些字符串将被视为多行信息。你可以考虑使用 ``config.getoption('verbose')`` 来显示更多信息（如果适用）：

        .. code-block:: python

            # content of conftest.py

            def pytest_report_header(config):
                if config.get_verbosity() > 0:
                    return ["info1: did you know that ...", "did you?"]

        这将仅在使用 "--v" 运行时添加信息：

        .. code-block:: pytest

            $ pytest -v
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            info1: did you know that ...
            did you?
            rootdir: /home/sweet/project
            collecting ... collected 0 items

            ========================== no tests ran in 0.12s ===========================

        而在普通运行时则没有任何信息：

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 0 items

            ========================== no tests ran in 0.12s ===========================

    .. tab:: 英文

        .. regendoc:wipe

        It's easy to present extra information in a ``pytest`` run:

        .. code-block:: python

            # content of conftest.py


            def pytest_report_header(config):
                return "project deps: mylib-1.1"

        which will add the string to the test header accordingly:

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            project deps: mylib-1.1
            rootdir: /home/sweet/project
            collected 0 items

            ========================== no tests ran in 0.12s ===========================

        .. regendoc:wipe

        It is also possible to return a list of strings which will be considered as several
        lines of information. You may consider ``config.getoption('verbose')`` in order to
        display more information if applicable:

        .. code-block:: python

            # content of conftest.py


            def pytest_report_header(config):
                if config.get_verbosity() > 0:
                    return ["info1: did you know that ...", "did you?"]

        which will add info only when run with "--v":

        .. code-block:: pytest

            $ pytest -v
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            info1: did you know that ...
            did you?
            rootdir: /home/sweet/project
            collecting ... collected 0 items

            ========================== no tests ran in 0.12s ===========================

        and nothing when run plainly:

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 0 items

            ========================== no tests ran in 0.12s ===========================

分析测试持续时间
--------------------------

**Profiling test duration**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        .. versionadded: 2.2

        如果你有一个运行缓慢的大型测试套件，你可能想找出哪些测试是最慢的。让我们创建一个人工测试套件：

        .. code-block:: python

            # content of test_some_are_slow.py
            import time


            def test_funcfast():
                time.sleep(0.1)


            def test_funcslow1():
                time.sleep(0.2)


            def test_funcslow2():
                time.sleep(0.3)

        现在我们可以分析哪些测试函数执行得最慢：

        .. code-block:: pytest

            $ pytest --durations=3
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 3 items

            test_some_are_slow.py ...                                            [100%]

            =========================== slowest 3 durations ============================
            0.30s call     test_some_are_slow.py::test_funcslow2
            0.20s call     test_some_are_slow.py::test_funcslow1
            0.10s call     test_some_are_slow.py::test_funcfast
            ============================ 3 passed in 0.12s =============================

    .. tab:: 英文

        .. regendoc:wipe

        .. versionadded: 2.2

        If you have a slow running large test suite you might want to find
        out which tests are the slowest. Let's make an artificial test suite:

        .. code-block:: python

            # content of test_some_are_slow.py
            import time


            def test_funcfast():
                time.sleep(0.1)


            def test_funcslow1():
                time.sleep(0.2)


            def test_funcslow2():
                time.sleep(0.3)

        Now we can profile which test functions execute the slowest:

        .. code-block:: pytest

            $ pytest --durations=3
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 3 items

            test_some_are_slow.py ...                                            [100%]

            =========================== slowest 3 durations ============================
            0.30s call     test_some_are_slow.py::test_funcslow2
            0.20s call     test_some_are_slow.py::test_funcslow1
            0.10s call     test_some_are_slow.py::test_funcfast
            ============================ 3 passed in 0.12s =============================

增量测试-测试步骤
---------------------------------------------------

**Incremental testing - test steps**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        有时候你可能会遇到一个测试场景，它由一系列测试步骤组成。如果一个步骤失败了，那么继续执行后面的步骤是没有意义的，因为它们都预计会失败，而它们的 traceback 也不会提供任何有用的信息。这里有一个简单的 ``conftest.py`` 文件，它引入了一个 ``incremental`` 标记，供类使用：

        .. code-block:: python

            # content of conftest.py

            from typing import Dict, Tuple

            import pytest

            # 存储每个测试类名称和参数索引的失败历史（如果使用了参数化）
            _test_failed_incremental: Dict[str, Dict[Tuple[int, ...], str]] = {}


            def pytest_runtest_makereport(item, call):
                if "incremental" in item.keywords:
                    # 使用了增量标记
                    if call.excinfo is not None:
                        # 测试失败
                        # 获取测试的类名
                        cls_name = str(item.cls)
                        # 获取测试的索引（如果使用了参数化并与增量结合）
                        parametrize_index = (
                            tuple(item.callspec.indices.values())
                            if hasattr(item, "callspec")
                            else ()
                        )
                        # 获取测试函数的名称
                        test_name = item.originalname or item.name
                        # 在 _test_failed_incremental 中存储失败测试的原始名称
                        _test_failed_incremental.setdefault(cls_name, {}).setdefault(
                            parametrize_index, test_name
                        )


            def pytest_runtest_setup(item):
                if "incremental" in item.keywords:
                    # 获取测试的类名
                    cls_name = str(item.cls)
                    # 检查该类是否有前一个测试失败
                    if cls_name in _test_failed_incremental:
                        # 获取测试的索引（如果使用了参数化并与增量结合）
                        parametrize_index = (
                            tuple(item.callspec.indices.values())
                            if hasattr(item, "callspec")
                            else ()
                        )
                        # 获取该类名和索引的第一个失败测试函数名称
                        test_name = _test_failed_incremental[cls_name].get(parametrize_index, None)
                        # 如果找到名称，则该测试因类名和测试名称组合而失败
                        if test_name is not None:
                            pytest.xfail(f"previous test failed ({test_name})")


        这两个钩子实现协同工作，以中止标记为增量的类中的测试。以下是一个测试模块示例：

        .. code-block:: python

            # content of test_step.py

            import pytest


            @pytest.mark.incremental
            class TestUserHandling:
                def test_login(self):
                    pass

                def test_modification(self):
                    assert 0

                def test_deletion(self):
                    pass


            def test_normal():
                pass

        如果我们运行这个：

        .. code-block:: pytest

            $ pytest -rx
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items

            test_step.py .Fx.                                                    [100%]

            ================================= FAILURES =================================
            ____________________ TestUserHandling.test_modification ____________________

            self = <test_step.TestUserHandling object at 0xdeadbeef0001>

                def test_modification(self):
            >       assert 0
            E       assert 0

            test_step.py:11: AssertionError
            ========================= short test summary info ==========================
            XFAIL test_step.py::TestUserHandling::test_deletion - reason: previous test failed (test_modification)
            ================== 1 failed, 2 passed, 1 xfailed in 0.12s ==================

        我们会看到 ``test_deletion`` 没有被执行，因为 ``test_modification`` 失败了。它被报告为“预期失败”。

    .. tab:: 英文

        .. regendoc:wipe

        Sometimes you may have a testing situation which consists of a series
        of test steps.  If one step fails it makes no sense to execute further
        steps as they are all expected to fail anyway and their tracebacks
        add no insight.  Here is a simple ``conftest.py`` file which introduces
        an ``incremental`` marker which is to be used on classes:

        .. code-block:: python

            # content of conftest.py

            from typing import Dict, Tuple

            import pytest

            # store history of failures per test class name and per index in parametrize (if parametrize used)
            _test_failed_incremental: Dict[str, Dict[Tuple[int, ...], str]] = {}


            def pytest_runtest_makereport(item, call):
                if "incremental" in item.keywords:
                    # incremental marker is used
                    if call.excinfo is not None:
                        # the test has failed
                        # retrieve the class name of the test
                        cls_name = str(item.cls)
                        # retrieve the index of the test (if parametrize is used in combination with incremental)
                        parametrize_index = (
                            tuple(item.callspec.indices.values())
                            if hasattr(item, "callspec")
                            else ()
                        )
                        # retrieve the name of the test function
                        test_name = item.originalname or item.name
                        # store in _test_failed_incremental the original name of the failed test
                        _test_failed_incremental.setdefault(cls_name, {}).setdefault(
                            parametrize_index, test_name
                        )


            def pytest_runtest_setup(item):
                if "incremental" in item.keywords:
                    # retrieve the class name of the test
                    cls_name = str(item.cls)
                    # check if a previous test has failed for this class
                    if cls_name in _test_failed_incremental:
                        # retrieve the index of the test (if parametrize is used in combination with incremental)
                        parametrize_index = (
                            tuple(item.callspec.indices.values())
                            if hasattr(item, "callspec")
                            else ()
                        )
                        # retrieve the name of the first test function to fail for this class name and index
                        test_name = _test_failed_incremental[cls_name].get(parametrize_index, None)
                        # if name found, test has failed for the combination of class name & test name
                        if test_name is not None:
                            pytest.xfail(f"previous test failed ({test_name})")


        These two hook implementations work together to abort incremental-marked
        tests in a class.  Here is a test module example:

        .. code-block:: python

            # content of test_step.py

            import pytest


            @pytest.mark.incremental
            class TestUserHandling:
                def test_login(self):
                    pass

                def test_modification(self):
                    assert 0

                def test_deletion(self):
                    pass


            def test_normal():
                pass

        If we run this:

        .. code-block:: pytest

            $ pytest -rx
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items

            test_step.py .Fx.                                                    [100%]

            ================================= FAILURES =================================
            ____________________ TestUserHandling.test_modification ____________________

            self = <test_step.TestUserHandling object at 0xdeadbeef0001>

                def test_modification(self):
            >       assert 0
            E       assert 0

            test_step.py:11: AssertionError
            ========================= short test summary info ==========================
            XFAIL test_step.py::TestUserHandling::test_deletion - reason: previous test failed (test_modification)
            ================== 1 failed, 2 passed, 1 xfailed in 0.12s ==================

        We'll see that ``test_deletion`` was not executed because ``test_modification``
        failed.  It is reported as an "expected failure".


包/目录级别的装置（设置）
-------------------------------------------------------

**Package/Directory-level fixtures (setups)**

.. tabs::

    .. tab:: 中文

        如果你有嵌套的测试目录，可以通过在该目录中放置 ``conftest.py`` 文件来实现每个目录的夹具作用域。你可以使用所有类型的夹具，包括 :ref:`autouse fixtures <autouse fixtures>`，它们相当于 xUnit 的 setup/teardown 概念。然而，建议在测试或测试类中显式引用夹具，而不是依赖于隐式执行的 setup/teardown 函数，尤其是当它们与实际测试相距较远时。

        以下是使 ``db`` 夹具在一个目录中可用的示例：

        .. code-block:: python

            # content of a/conftest.py
            import pytest


            class DB:
                pass


            @pytest.fixture(scope="package")
            def db():
                return DB()

        然后在该目录中的一个测试模块：

        .. code-block:: python

            # content of a/test_db.py
            def test_a1(db):
                assert 0, db  # 显示值

        另一个测试模块：

        .. code-block:: python

            # content of a/test_db2.py
            def test_a2(db):
                assert 0, db  # 显示值

        然后在一个兄弟目录中的模块，它将无法看到 ``db`` 夹具：

        .. code-block:: python

            # content of b/test_error.py
            def test_root(db):  # 这里没有 db，将会出错
                pass

        我们可以运行这个：

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 7 items

            a/test_db.py F                                                       [ 14%]
            a/test_db2.py F                                                      [ 28%]
            b/test_error.py E                                                    [ 42%]
            test_step.py .Fx.                                                    [100%]

            ================================== ERRORS ==================================
            _______________________ ERROR at setup of test_root ________________________
            file /home/sweet/project/b/test_error.py, line 1
            def test_root(db):  # 这里没有 db，将会出错
            E       fixture 'db' not found
            >       available fixtures: cache, capfd, capfdbinary, caplog, capsys, capsysbinary, doctest_namespace, monkeypatch, pytestconfig, record_property, record_testsuite_property, record_xml_attribute, recwarn, tmp_path, tmp_path_factory, tmpdir, tmpdir_factory
            >       use 'pytest --fixtures [testpath]' for help on them.

            /home/sweet/project/b/test_error.py:1
            ================================= FAILURES =================================
            _________________________________ test_a1 __________________________________

            db = <conftest.DB object at 0xdeadbeef0002>

                def test_a1(db):
            >       assert 0, db  # 显示值
            E       AssertionError: <conftest.DB object at 0xdeadbeef0002>
            E       assert 0

            a/test_db.py:2: AssertionError
            _________________________________ test_a2 __________________________________

            db = <conftest.DB object at 0xdeadbeef0002>

                def test_a2(db):
            >       assert 0, db  # 显示值
            E       AssertionError: <conftest.DB object at 0xdeadbeef0002>
            E       assert 0

            a/test_db2.py:2: AssertionError
            ____________________ TestUserHandling.test_modification ____________________

            self = <test_step.TestUserHandling object at 0xdeadbeef0003>

                def test_modification(self):
            >       assert 0
            E       assert 0

            test_step.py:11: AssertionError
            ========================= short test summary info ==========================
            FAILED a/test_db.py::test_a1 - AssertionError: <conftest.DB object at 0x7...
            FAILED a/test_db2.py::test_a2 - AssertionError: <conftest.DB object at 0x...
            FAILED test_step.py::TestUserHandling::test_modification - assert 0
            ============= 3 failed, 2 passed, 1 xfailed, 1 error in 0.12s ==============

        ``a`` 目录中的两个测试模块看到了相同的 ``db`` 夹具实例，而兄弟目录 ``b`` 中的一个测试则看不到。我们当然也可以在那个兄弟目录的 ``conftest.py`` 文件中定义一个 ``db`` 夹具。注意，每个夹具仅在有测试实际需要它时才会被实例化（除非使用“autouse”夹具，这些夹具总是在第一个测试执行之前被执行）。

    .. tab:: 英文

        If you have nested test directories, you can have per-directory fixture scopes
        by placing fixture functions in a ``conftest.py`` file in that directory.
        You can use all types of fixtures including :ref:`autouse fixtures
        <autouse fixtures>` which are the equivalent of xUnit's setup/teardown
        concept.  It's however recommended to have explicit fixture references in your
        tests or test classes rather than relying on implicitly executing
        setup/teardown functions, especially if they are far away from the actual tests.

        Here is an example for making a ``db`` fixture available in a directory:

        .. code-block:: python

            # content of a/conftest.py
            import pytest


            class DB:
                pass


            @pytest.fixture(scope="package")
            def db():
                return DB()

        and then a test module in that directory:

        .. code-block:: python

            # content of a/test_db.py
            def test_a1(db):
                assert 0, db  # to show value

        another test module:

        .. code-block:: python

            # content of a/test_db2.py
            def test_a2(db):
                assert 0, db  # to show value

        and then a module in a sister directory which will not see
        the ``db`` fixture:

        .. code-block:: python

            # content of b/test_error.py
            def test_root(db):  # no db here, will error out
                pass

        We can run this:

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 7 items

            a/test_db.py F                                                       [ 14%]
            a/test_db2.py F                                                      [ 28%]
            b/test_error.py E                                                    [ 42%]
            test_step.py .Fx.                                                    [100%]

            ================================== ERRORS ==================================
            _______________________ ERROR at setup of test_root ________________________
            file /home/sweet/project/b/test_error.py, line 1
            def test_root(db):  # no db here, will error out
            E       fixture 'db' not found
            >       available fixtures: cache, capfd, capfdbinary, caplog, capsys, capsysbinary, doctest_namespace, monkeypatch, pytestconfig, record_property, record_testsuite_property, record_xml_attribute, recwarn, tmp_path, tmp_path_factory, tmpdir, tmpdir_factory
            >       use 'pytest --fixtures [testpath]' for help on them.

            /home/sweet/project/b/test_error.py:1
            ================================= FAILURES =================================
            _________________________________ test_a1 __________________________________

            db = <conftest.DB object at 0xdeadbeef0002>

                def test_a1(db):
            >       assert 0, db  # to show value
            E       AssertionError: <conftest.DB object at 0xdeadbeef0002>
            E       assert 0

            a/test_db.py:2: AssertionError
            _________________________________ test_a2 __________________________________

            db = <conftest.DB object at 0xdeadbeef0002>

                def test_a2(db):
            >       assert 0, db  # to show value
            E       AssertionError: <conftest.DB object at 0xdeadbeef0002>
            E       assert 0

            a/test_db2.py:2: AssertionError
            ____________________ TestUserHandling.test_modification ____________________

            self = <test_step.TestUserHandling object at 0xdeadbeef0003>

                def test_modification(self):
            >       assert 0
            E       assert 0

            test_step.py:11: AssertionError
            ========================= short test summary info ==========================
            FAILED a/test_db.py::test_a1 - AssertionError: <conftest.DB object at 0x7...
            FAILED a/test_db2.py::test_a2 - AssertionError: <conftest.DB object at 0x...
            FAILED test_step.py::TestUserHandling::test_modification - assert 0
            ERROR b/test_error.py::test_root
            ============= 3 failed, 2 passed, 1 xfailed, 1 error in 0.12s ==============

        The two test modules in the ``a`` directory see the same ``db`` fixture instance
        while the one test in the sister-directory ``b`` doesn't see it.  We could of course
        also define a ``db`` fixture in that sister directory's ``conftest.py`` file.
        Note that each fixture is only instantiated if there is a test actually needing
        it (unless you use "autouse" fixture which are always executed ahead of the first test
        executing).


后处理测试报告/故障
---------------------------------------

**Post-process test reports / failures**

.. tabs::

    .. tab:: 中文

        如果你想后处理测试报告并需要访问执行环境，可以实现一个钩子，在测试“报告”对象即将创建时调用它。在这里，我们写出所有失败的测试调用，并且在需要时访问一个夹具（如果它被测试使用了），以便在后处理时查询或查看它。在我们的例子中，我们只将一些信息写入一个 ``failures`` 文件：

        .. code-block:: python

            # content of conftest.py

            import os.path

            import pytest


            @pytest.hookimpl(wrapper=True, tryfirst=True)
            def pytest_runtest_makereport(item, call):
                # 执行所有其他钩子以获取报告对象
                rep = yield

                # 我们只关注实际失败的测试调用，而不是 setup/teardown
                if rep.when == "call" and rep.failed:
                    mode = "a" if os.path.exists("failures") else "w"
                    with open("failures", mode, encoding="utf-8") as f:
                        # 让我们也访问一个夹具以增加趣味
                        if "tmp_path" in item.fixturenames:
                            extra = " ({})".format(item.funcargs["tmp_path"])
                        else:
                            extra = ""

                        f.write(rep.nodeid + extra + "\n")

                return rep


        如果你有失败的测试：

        .. code-block:: python

            # content of test_module.py
            def test_fail1(tmp_path):
                assert 0


            def test_fail2():
                assert 0

        并运行它们：

        .. code-block:: pytest

            $ pytest test_module.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py FF                                                    [100%]

            ================================= FAILURES =================================
            ________________________________ test_fail1 ________________________________

            tmp_path = PosixPath('PYTEST_TMPDIR/test_fail10')

                def test_fail1(tmp_path):
            >       assert 0
            E       assert 0

            test_module.py:2: AssertionError
            ________________________________ test_fail2 ________________________________

                def test_fail2():
            >       assert 0
            E       assert 0

            test_module.py:6: AssertionError
            ========================= short test summary info ==========================
            FAILED test_module.py::test_fail1 - assert 0
            FAILED test_module.py::test_fail2 - assert 0
            ============================ 2 failed in 0.12s =============================

        你将会有一个包含失败测试 ID 的 ``failures`` 文件：

        .. code-block:: bash

            $ cat failures
            test_module.py::test_fail1 (PYTEST_TMPDIR/test_fail10)
            test_module.py::test_fail2

    .. tab:: 英文

        If you want to postprocess test reports and need access to the executing
        environment you can implement a hook that gets called when the test
        "report" object is about to be created.  Here we write out all failing
        test calls and also access a fixture (if it was used by the test) in
        case you want to query/look at it during your post processing.  In our
        case we just write some information out to a ``failures`` file:

        .. code-block:: python

            # content of conftest.py

            import os.path

            import pytest


            @pytest.hookimpl(wrapper=True, tryfirst=True)
            def pytest_runtest_makereport(item, call):
                # execute all other hooks to obtain the report object
                rep = yield

                # we only look at actual failing test calls, not setup/teardown
                if rep.when == "call" and rep.failed:
                    mode = "a" if os.path.exists("failures") else "w"
                    with open("failures", mode, encoding="utf-8") as f:
                        # let's also access a fixture for the fun of it
                        if "tmp_path" in item.fixturenames:
                            extra = " ({})".format(item.funcargs["tmp_path"])
                        else:
                            extra = ""

                        f.write(rep.nodeid + extra + "\n")

                return rep


        if you then have failing tests:

        .. code-block:: python

            # content of test_module.py
            def test_fail1(tmp_path):
                assert 0


            def test_fail2():
                assert 0

        and run them:

        .. code-block:: pytest

            $ pytest test_module.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py FF                                                    [100%]

            ================================= FAILURES =================================
            ________________________________ test_fail1 ________________________________

            tmp_path = PosixPath('PYTEST_TMPDIR/test_fail10')

                def test_fail1(tmp_path):
            >       assert 0
            E       assert 0

            test_module.py:2: AssertionError
            ________________________________ test_fail2 ________________________________

                def test_fail2():
            >       assert 0
            E       assert 0

            test_module.py:6: AssertionError
            ========================= short test summary info ==========================
            FAILED test_module.py::test_fail1 - assert 0
            FAILED test_module.py::test_fail2 - assert 0
            ============================ 2 failed in 0.12s =============================

        you will have a "failures" file which contains the failing test ids:

        .. code-block:: bash

            $ cat failures
            test_module.py::test_fail1 (PYTEST_TMPDIR/test_fail10)
            test_module.py::test_fail2

在装置中提供测试结果信息
-----------------------------------------------------------

**Making test result information available in fixtures**

.. tabs::

    .. tab:: 中文

        如果你想在夹具终结器中使测试结果报告可用，这里有一个通过本地插件实现的小示例：

        .. code-block:: python

            # content of conftest.py
            from typing import Dict
            import pytest
            from pytest import StashKey, CollectReport

            phase_report_key = StashKey[Dict[str, CollectReport]]()


            @pytest.hookimpl(wrapper=True, tryfirst=True)
            def pytest_runtest_makereport(item, call):
                # 执行所有其他钩子以获取报告对象
                rep = yield

                # 存储每个调用阶段的测试结果，可以是
                # "setup"、"call"、"teardown"
                item.stash.setdefault(phase_report_key, {})[rep.when] = rep

                return rep


            @pytest.fixture
            def something(request):
                yield
                # request.node 是一个“项”，因为我们使用默认的
                # “函数”范围
                report = request.node.stash[phase_report_key]
                if report["setup"].failed:
                    print("设置测试失败或跳过", request.node.nodeid)
                elif ("call" not in report) or report["call"].failed:
                    print("执行测试失败或跳过", request.node.nodeid)


        如果你有失败的测试：

        .. code-block:: python

            # content of test_module.py

            import pytest


            @pytest.fixture
            def other():
                assert 0


            def test_setup_fails(something, other):
                pass


            def test_call_fails(something):
                assert 0


            def test_fail2():
                assert 0

        并运行它：

        .. code-block:: pytest

            $ pytest -s test_module.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 3 items

            test_module.py Esetting up a test failed or skipped test_module.py::test_setup_fails
            Fexecuting test failed or skipped test_module.py::test_call_fails
            F

            ================================== ERRORS ==================================
            ____________________ ERROR at setup of test_setup_fails ____________________

                @pytest.fixture
                def other():
            >       assert 0
            E       assert 0

            test_module.py:7: AssertionError
            ================================= FAILURES =================================
            _____________________________ test_call_fails ______________________________

            something = None

                def test_call_fails(something):
            >       assert 0
            E       assert 0

            test_module.py:15: AssertionError
            ________________________________ test_fail2 ________________________________

                def test_fail2():
            >       assert 0
            E       assert 0

            test_module.py:19: AssertionError
            ========================= short test summary info ==========================
            FAILED test_module.py::test_call_fails - assert 0
            FAILED test_module.py::test_fail2 - assert 0
            ERROR test_module.py::test_setup_fails - assert 0
            ======================== 2 failed, 1 error in 0.12s ========================

        你将看到夹具终结器可以使用精确的报告信息。

    .. tab:: 英文

        .. regendoc:wipe

        If you want to make test result reports available in fixture finalizers
        here is a little example implemented via a local plugin:

        .. code-block:: python

            # content of conftest.py
            from typing import Dict
            import pytest
            from pytest import StashKey, CollectReport

            phase_report_key = StashKey[Dict[str, CollectReport]]()


            @pytest.hookimpl(wrapper=True, tryfirst=True)
            def pytest_runtest_makereport(item, call):
                # execute all other hooks to obtain the report object
                rep = yield

                # store test results for each phase of a call, which can
                # be "setup", "call", "teardown"
                item.stash.setdefault(phase_report_key, {})[rep.when] = rep

                return rep


            @pytest.fixture
            def something(request):
                yield
                # request.node is an "item" because we use the default
                # "function" scope
                report = request.node.stash[phase_report_key]
                if report["setup"].failed:
                    print("setting up a test failed or skipped", request.node.nodeid)
                elif ("call" not in report) or report["call"].failed:
                    print("executing test failed or skipped", request.node.nodeid)


        if you then have failing tests:

        .. code-block:: python

            # content of test_module.py

            import pytest


            @pytest.fixture
            def other():
                assert 0


            def test_setup_fails(something, other):
                pass


            def test_call_fails(something):
                assert 0


            def test_fail2():
                assert 0

        and run it:

        .. code-block:: pytest

            $ pytest -s test_module.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 3 items

            test_module.py Esetting up a test failed or skipped test_module.py::test_setup_fails
            Fexecuting test failed or skipped test_module.py::test_call_fails
            F

            ================================== ERRORS ==================================
            ____________________ ERROR at setup of test_setup_fails ____________________

                @pytest.fixture
                def other():
            >       assert 0
            E       assert 0

            test_module.py:7: AssertionError
            ================================= FAILURES =================================
            _____________________________ test_call_fails ______________________________

            something = None

                def test_call_fails(something):
            >       assert 0
            E       assert 0

            test_module.py:15: AssertionError
            ________________________________ test_fail2 ________________________________

                def test_fail2():
            >       assert 0
            E       assert 0

            test_module.py:19: AssertionError
            ========================= short test summary info ==========================
            FAILED test_module.py::test_call_fails - assert 0
            FAILED test_module.py::test_fail2 - assert 0
            ERROR test_module.py::test_setup_fails - assert 0
            ======================== 2 failed, 1 error in 0.12s ========================

        You'll see that the fixture finalizers could use the precise reporting
        information.

.. _pytest current test env:

``PYTEST_CURRENT_TEST`` 环境变量
--------------------------------------------

``PYTEST_CURRENT_TEST`` **environment variable**

.. tabs::

    .. tab:: 中文

        有时测试会话可能会卡住，可能没有简单的方法来找出哪个测试卡住了，例如在安静模式（``-q``）下运行pytest时，或者你无法访问控制台输出。这在问题只偶尔发生时尤其成问题，通常被称为“易波动”测试。

        ``pytest`` 在运行测试时设置 :envvar:`PYTEST_CURRENT_TEST` 环境变量，这可以通过进程监控工具或像 :pypi:`psutil` 这样的库检查，以便在必要时发现哪个测试卡住了：

        .. code-block:: python

            import psutil

            for pid in psutil.pids():
                environ = psutil.Process(pid).environ()
                if "PYTEST_CURRENT_TEST" in environ:
                    print(f'pytest process {pid} running: {environ["PYTEST_CURRENT_TEST"]}')

        在测试会话期间，pytest 会将 ``PYTEST_CURRENT_TEST`` 设置为当前测试的
        :ref:`nodeid <nodeids>` 及当前阶段，这可以是 ``setup``、``call`` 或 ``teardown``。

        例如，当从 ``foo_module.py`` 运行名为 ``test_foo`` 的单个测试函数时，
        ``PYTEST_CURRENT_TEST`` 将被设置为：

        #. ``foo_module.py::test_foo (setup)``
        #. ``foo_module.py::test_foo (call)``
        #. ``foo_module.py::test_foo (teardown)``

        按此顺序。

        .. note::

            ``PYTEST_CURRENT_TEST`` 的内容旨在便于人类阅读，实际格式可能会在版本之间（甚至是修复错误）发生变化，因此不应依赖于其进行脚本或自动化。

    .. tab:: 英文

        Sometimes a test session might get stuck and there might be no easy way to figure out
        which test got stuck, for example if pytest was run in quiet mode (``-q``) or you don't have access to the console
        output. This is particularly a problem if the problem happens only sporadically, the famous "flaky" kind of tests.

        ``pytest`` sets the :envvar:`PYTEST_CURRENT_TEST` environment variable when running tests, which can be inspected
        by process monitoring utilities or libraries like :pypi:`psutil` to discover which test got stuck if necessary:

        .. code-block:: python

            import psutil

            for pid in psutil.pids():
                environ = psutil.Process(pid).environ()
                if "PYTEST_CURRENT_TEST" in environ:
                    print(f'pytest process {pid} running: {environ["PYTEST_CURRENT_TEST"]}')

        During the test session pytest will set ``PYTEST_CURRENT_TEST`` to the current test
        :ref:`nodeid <nodeids>` and the current stage, which can be ``setup``, ``call``,
        or ``teardown``.

        For example, when running a single test function named ``test_foo`` from ``foo_module.py``,
        ``PYTEST_CURRENT_TEST`` will be set to:

        #. ``foo_module.py::test_foo (setup)``
        #. ``foo_module.py::test_foo (call)``
        #. ``foo_module.py::test_foo (teardown)``

        In that order.

        .. note::

            The contents of ``PYTEST_CURRENT_TEST`` is meant to be human readable and the actual format
            can be changed between releases (even bug fixes) so it shouldn't be relied on for scripting
            or automation.

.. _freezing-pytest:

冻结 pytest
---------------

**Freezing pytest**

.. tabs::

    .. tab:: 中文

        如果你使用像 `PyInstaller <https://pyinstaller.readthedocs.io>`_ 这样的工具来冻结你的应用程序，以便分发给最终用户，最好也将你的测试运行器打包，并使用冻结后的应用程序运行测试。这样可以及早检测到打包错误，例如依赖项未包含在可执行文件中，同时还允许你将测试文件发送给用户，以便他们在自己的机器上运行，这对于获取难以重现的错误的更多信息非常有用。

        幸运的是，最近的 ``PyInstaller`` 版本已经为 pytest 提供了自定义钩子，但如果你使用其他工具来冻结可执行文件，如 ``cx_freeze`` 或 ``py2exe``，可以使用 ``pytest.freeze_includes()`` 来获取完整的内部 pytest 模块列表。不过，如何配置这些工具以查找内部模块因工具而异。

        你可以通过在程序启动期间巧妙地处理参数，使你的冻结程序作为 pytest 运行器工作，而不是将 pytest 运行器冻结为单独的可执行文件。这使你可以拥有一个单一的可执行文件，通常更方便。请注意，pytest 使用的插件发现机制（:ref:`entry points <pip-installable plugins>`）不适用于冻结的可执行文件，因此 pytest 不能自动找到任何第三方插件。要包含像 ``pytest-timeout`` 这样的第三方插件，必须显式导入它们并将其传递给 pytest.main。

        .. code-block:: python

            # contents of app_main.py
            import sys

            import pytest_timeout  # 第三方插件

            if len(sys.argv) > 1 and sys.argv[1] == "--pytest":
                import pytest

                sys.exit(pytest.main(sys.argv[2:], plugins=[pytest_timeout]))
            else:
                # 正常的应用程序执行：此时 argv 可以像往常一样由你选择的参数解析库解析
                ...


        这允许你使用标准的 ``pytest`` 命令行选项通过冻结的应用程序执行测试：

        .. code-block:: bash

            ./app_main --pytest --verbose --tb=long --junit=xml=results.xml test-suite/

    .. tab:: 英文

        If you freeze your application using a tool like
        `PyInstaller <https://pyinstaller.readthedocs.io>`_
        in order to distribute it to your end-users, it is a good idea to also package
        your test runner and run your tests using the frozen application. This way packaging
        errors such as dependencies not being included into the executable can be detected early
        while also allowing you to send test files to users so they can run them in their
        machines, which can be useful to obtain more information about a hard to reproduce bug.

        Fortunately recent ``PyInstaller`` releases already have a custom hook
        for pytest, but if you are using another tool to freeze executables
        such as ``cx_freeze`` or ``py2exe``, you can use ``pytest.freeze_includes()``
        to obtain the full list of internal pytest modules. How to configure the tools
        to find the internal modules varies from tool to tool, however.

        Instead of freezing the pytest runner as a separate executable, you can make
        your frozen program work as the pytest runner by some clever
        argument handling during program startup. This allows you to
        have a single executable, which is usually more convenient.
        Please note that the mechanism for plugin discovery used by pytest (:ref:`entry
        points <pip-installable plugins>`) doesn't work with frozen executables so pytest
        can't find any third party plugins automatically. To include third party plugins
        like ``pytest-timeout`` they must be imported explicitly and passed on to pytest.main.

        .. code-block:: python

            # contents of app_main.py
            import sys

            import pytest_timeout  # Third party plugin

            if len(sys.argv) > 1 and sys.argv[1] == "--pytest":
                import pytest

                sys.exit(pytest.main(sys.argv[2:], plugins=[pytest_timeout]))
            else:
                # normal application execution: at this point argv can be parsed
                # by your argument-parsing library of choice as usual
                ...


        This allows you to execute tests using the frozen
        application with standard ``pytest`` command-line options:

        .. code-block:: bash

            ./app_main --pytest --verbose --tb=long --junit=xml=results.xml test-suite/
