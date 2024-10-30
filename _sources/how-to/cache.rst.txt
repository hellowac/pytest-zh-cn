.. _`cache_provider`:
.. _cache:


如何重新运行失败的测试并在测试运行之间保持状态
===============================================================

**How to re-run failed tests and maintain state between test runs**


用法
---------

**Usage**

.. tabs::

    .. tab:: 中文

        该插件提供了两个命令行选项，以重新运行上次 ``pytest`` 调用中的失败测试：

        * ``--lf``，``--last-failed`` - 仅重新运行失败的测试。
        * ``--ff``，``--failed-first`` - 优先运行失败的测试，然后运行其余的测试。

        对于清理（通常不需要），``--cache-clear`` 选项允许在测试运行前删除所有跨会话的缓存内容。

        其他插件可以访问 `config.cache`_ 对象，以在 ``pytest`` 调用之间设置/获取 **可 JSON 编码** 的值。

        .. note::

            该插件默认启用，但如有需要可以禁用：参见 :ref:`cmdunregister` （该插件的内部名称是 ``cacheprovider``）。

    .. tab:: 英文

        The plugin provides two command line options to rerun failures from the last ``pytest`` invocation:

        * ``--lf``, ``--last-failed`` - to only re-run the failures.
        * ``--ff``, ``--failed-first`` - to run the failures first and then the rest of the tests.

        For cleanup (usually not needed), a ``--cache-clear`` option allows to remove all cross-session cache contents ahead of a test run.

        Other plugins may access the `config.cache`_ object to set/get **json encodable** values between ``pytest`` invocations.

        .. note::

            This plugin is enabled by default, but can be disabled if needed: see :ref:`cmdunregister` (the internal name for this plugin is ``cacheprovider``).


仅重新运行失败或首次失败
-----------------------------------------------

**Rerunning only failures or failures first**

.. tabs::

    .. tab:: 中文

        首先，让我们创建 50 个测试调用，其中只有 2 个失败：

        .. code-block:: python

            # content of test_50.py
            import pytest


            @pytest.mark.parametrize("i", range(50))
            def test_num(i):
                if i in (17, 25):
                    pytest.fail("bad luck")

        如果您第一次运行该程序，您将看到两个失败：

        .. code-block:: pytest

            $ pytest -q
            .................F.......F........................                   [100%]
            ================================= FAILURES =================================
            _______________________________ test_num[17] _______________________________

            i = 17

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            _______________________________ test_num[25] _______________________________

            i = 25

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            ========================= short test summary info ==========================
            FAILED test_50.py::test_num[17] - Failed: bad luck
            FAILED test_50.py::test_num[25] - Failed: bad luck
            2 failed, 48 passed in 0.12s

        如果你随后使用 ``--lf`` 运行它:

        .. code-block:: pytest

            $ pytest --lf
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items
            run-last-failure: rerun previous 2 failures

            test_50.py FF                                                        [100%]

            ================================= FAILURES =================================
            _______________________________ test_num[17] _______________________________

            i = 17

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            _______________________________ test_num[25] _______________________________

            i = 25

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            ========================= short test summary info ==========================
            FAILED test_50.py::test_num[17] - Failed: bad luck
            FAILED test_50.py::test_num[25] - Failed: bad luck
            ============================ 2 failed in 0.12s =============================

        您只运行了上次运行中的两个失败测试，​​而 48 个通过的测试尚未运行（“取消选择(deselected)”）。

        现在，如果您使用 ``--ff`` 选项运行，则将运行所有测试，但将首先执行先前失败的测试（从一系列 ``FF`` 和点可以看出）：

        .. code-block:: pytest

            $ pytest --ff
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 50 items
            run-last-failure: rerun previous 2 failures first

            test_50.py FF................................................        [100%]

            ================================= FAILURES =================================
            _______________________________ test_num[17] _______________________________

            i = 17

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            _______________________________ test_num[25] _______________________________

            i = 25

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            ========================= short test summary info ==========================
            FAILED test_50.py::test_num[17] - Failed: bad luck
            FAILED test_50.py::test_num[25] - Failed: bad luck
            ======================= 2 failed, 48 passed in 0.12s =======================

        .. _`config.cache`:

        新的 ``--nf``、 ``--new-first`` 选项：首先运行新测试，然后运行其余测试，在这两种情况下，测试也按文件修改时间排序，较新的文件排在最前面。

    .. tab:: 英文

        First, let's create 50 test invocation of which only 2 fail:

        .. code-block:: python

            # content of test_50.py
            import pytest


            @pytest.mark.parametrize("i", range(50))
            def test_num(i):
                if i in (17, 25):
                    pytest.fail("bad luck")

        If you run this for the first time you will see two failures:

        .. code-block:: pytest

            $ pytest -q
            .................F.......F........................                   [100%]
            ================================= FAILURES =================================
            _______________________________ test_num[17] _______________________________

            i = 17

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            _______________________________ test_num[25] _______________________________

            i = 25

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            ========================= short test summary info ==========================
            FAILED test_50.py::test_num[17] - Failed: bad luck
            FAILED test_50.py::test_num[25] - Failed: bad luck
            2 failed, 48 passed in 0.12s

        If you then run it with ``--lf``:

        .. code-block:: pytest

            $ pytest --lf
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items
            run-last-failure: rerun previous 2 failures

            test_50.py FF                                                        [100%]

            ================================= FAILURES =================================
            _______________________________ test_num[17] _______________________________

            i = 17

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            _______________________________ test_num[25] _______________________________

            i = 25

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            ========================= short test summary info ==========================
            FAILED test_50.py::test_num[17] - Failed: bad luck
            FAILED test_50.py::test_num[25] - Failed: bad luck
            ============================ 2 failed in 0.12s =============================

        You have run only the two failing tests from the last run, while the 48 passing
        tests have not been run ("deselected").

        Now, if you run with the ``--ff`` option, all tests will be run but the first
        previous failures will be executed first (as can be seen from the series
        of ``FF`` and dots):

        .. code-block:: pytest

            $ pytest --ff
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 50 items
            run-last-failure: rerun previous 2 failures first

            test_50.py FF................................................        [100%]

            ================================= FAILURES =================================
            _______________________________ test_num[17] _______________________________

            i = 17

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            _______________________________ test_num[25] _______________________________

            i = 25

                @pytest.mark.parametrize("i", range(50))
                def test_num(i):
                    if i in (17, 25):
            >           pytest.fail("bad luck")
            E           Failed: bad luck

            test_50.py:7: Failed
            ========================= short test summary info ==========================
            FAILED test_50.py::test_num[17] - Failed: bad luck
            FAILED test_50.py::test_num[25] - Failed: bad luck
            ======================= 2 failed, 48 passed in 0.12s =======================

        New ``--nf``, ``--new-first`` options: run new tests first followed by the rest of the tests, in both cases tests are also sorted by the file modified time, with more recent files coming first.

上次运行中没有测试失败时的行为
---------------------------------------------

**Behavior when no tests failed in the last run**

.. tabs::

    .. tab:: 中文

        ``--lfnf/--last-failed-no-failures`` 选项控制 ``--last-failed`` 的行为。它决定在没有先前（已知）失败或未找到缓存的 ``lastfailed`` 数据时是否执行测试。

        有两个选项：

        * ``all``：当没有已知的测试失败时，运行所有测试（完整的测试套件）。这是默认设置。
        * ``none``：当没有已知的测试失败时，仅发出一条消息说明这一点并成功退出。

        示例：

        .. code-block:: bash

            pytest --last-failed --last-failed-no-failures all    # 运行完整的测试套件（默认行为）
            pytest --last-failed --last-failed-no-failures none   # 不运行任何测试并成功退出

    .. tab:: 英文

        The ``--lfnf/--last-failed-no-failures`` option governs the behavior of ``--last-failed``.
        Determines whether to execute tests when there are no previously (known)
        failures or when no cached ``lastfailed`` data was found.

        There are two options:

        * ``all``:  when there are no known test failures, runs all tests (the full test suite). This is the default.
        * ``none``: when there are no known test failures, just emits a message stating this and exit successfully.

        Example:

        .. code-block:: bash

            pytest --last-failed --last-failed-no-failures all    # runs the full test suite (default behavior)
            pytest --last-failed --last-failed-no-failures none   # runs no tests and exits successfully

新的 config.cache 对象
--------------------------------

**The new config.cache object**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        插件或 conftest.py 支持代码可以使用 pytest ``config`` 对象获取缓存值。下面是一个基本的示例插件，它实现了一个 :ref:`fixture <fixture>`，可以在 pytest 调用之间重用先前创建的状态：

        .. code-block:: python

            # content of test_caching.py
            import pytest


            def expensive_computation():
                print("running expensive computation...")


            @pytest.fixture
            def mydata(pytestconfig):
                val = pytestconfig.cache.get("example/value", None)
                if val is None:
                    expensive_computation()
                    val = 42
                    pytestconfig.cache.set("example/value", val)
                return val


            def test_function(mydata):
                assert mydata == 23

        第一次运行该命令时，可以看到打印语句：

        .. code-block:: pytest

            $ pytest -q
            F                                                                    [100%]
            ================================= FAILURES =================================
            ______________________________ test_function _______________________________

            mydata = 42

                def test_function(mydata):
            >       assert mydata == 23
            E       assert 42 == 23

            test_caching.py:19: AssertionError
            -------------------------- Captured stdout setup ---------------------------
            running expensive computation...
            ========================= short test summary info ==========================
            FAILED test_caching.py::test_function - assert 42 == 23
            1 failed in 0.12s

        如果第二次运行，将从缓存中检索值，且不会打印任何内容：

        .. code-block:: pytest

            $ pytest -q
            F                                                                    [100%]
            ================================= FAILURES =================================
            ______________________________ test_function _______________________________

            mydata = 42

                def test_function(mydata):
            >       assert mydata == 23
            E       assert 42 == 23

            test_caching.py:19: AssertionError
            ========================= short test summary info ==========================
            FAILED test_caching.py::test_function - assert 42 == 23
            1 failed in 0.12s

        有关更多详细信息，请参见 :fixture:`config.cache fixture <cache>`。

    .. tab:: 英文

        .. regendoc:wipe

        Plugins or conftest.py support code can get a cached value using the
        pytest ``config`` object.  Here is a basic example plugin which
        implements a :ref:`fixture <fixture>` which re-uses previously created state
        across pytest invocations:

        .. code-block:: python

            # content of test_caching.py
            import pytest


            def expensive_computation():
                print("running expensive computation...")


            @pytest.fixture
            def mydata(pytestconfig):
                val = pytestconfig.cache.get("example/value", None)
                if val is None:
                    expensive_computation()
                    val = 42
                    pytestconfig.cache.set("example/value", val)
                return val


            def test_function(mydata):
                assert mydata == 23

        If you run this command for the first time, you can see the print statement:

        .. code-block:: pytest

            $ pytest -q
            F                                                                    [100%]
            ================================= FAILURES =================================
            ______________________________ test_function _______________________________

            mydata = 42

                def test_function(mydata):
            >       assert mydata == 23
            E       assert 42 == 23

            test_caching.py:19: AssertionError
            -------------------------- Captured stdout setup ---------------------------
            running expensive computation...
            ========================= short test summary info ==========================
            FAILED test_caching.py::test_function - assert 42 == 23
            1 failed in 0.12s

        If you run it a second time, the value will be retrieved from
        the cache and nothing will be printed:

        .. code-block:: pytest

            $ pytest -q
            F                                                                    [100%]
            ================================= FAILURES =================================
            ______________________________ test_function _______________________________

            mydata = 42

                def test_function(mydata):
            >       assert mydata == 23
            E       assert 42 == 23

            test_caching.py:19: AssertionError
            ========================= short test summary info ==========================
            FAILED test_caching.py::test_function - assert 42 == 23
            1 failed in 0.12s

        See the :fixture:`config.cache fixture <cache>` for more details.


审查缓存内容
------------------------

**Inspecting Cache content**

.. tabs::

    .. tab:: 中文

        你可以随时使用 ``--cache-show`` 命令行选项查看缓存的内容：

        .. code-block:: pytest

            $ pytest --cache-show
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            cachedir: /home/sweet/project/.pytest_cache
            --------------------------- cache values for '*' ---------------------------
            cache/lastfailed contains:
            {'test_caching.py::test_function': True}
            cache/nodeids contains:
            ['test_caching.py::test_function']
            cache/stepwise contains:
            []
            example/value contains:
            42

            ========================== no tests ran in 0.12s ===========================

        ``--cache-show`` 还接受一个可选参数，以指定用于过滤的 glob 模式：

        .. code-block:: pytest

            $ pytest --cache-show example/*
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            cachedir: /home/sweet/project/.pytest_cache
            ----------------------- cache values for 'example/*' -----------------------
            example/value contains:
            42

            ========================== no tests ran in 0.12s ===========================

    .. tab:: 英文

        You can always peek at the content of the cache using the
        ``--cache-show`` command line option:

        .. code-block:: pytest

            $ pytest --cache-show
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            cachedir: /home/sweet/project/.pytest_cache
            --------------------------- cache values for '*' ---------------------------
            cache/lastfailed contains:
            {'test_caching.py::test_function': True}
            cache/nodeids contains:
            ['test_caching.py::test_function']
            cache/stepwise contains:
            []
            example/value contains:
            42

            ========================== no tests ran in 0.12s ===========================

        ``--cache-show`` takes an optional argument to specify a glob pattern for
        filtering:

        .. code-block:: pytest

            $ pytest --cache-show example/*
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            cachedir: /home/sweet/project/.pytest_cache
            ----------------------- cache values for 'example/*' -----------------------
            example/value contains:
            42

            ========================== no tests ran in 0.12s ===========================

清除缓存内容
----------------------

**Clearing Cache content**

.. tabs::

    .. tab:: 中文

        你可以通过添加 ``--cache-clear`` 选项来指示 pytest 清除所有缓存文件和值，如下所示：

        .. code-block:: bash

            pytest --cache-clear

        这在来自持续集成服务器的调用中是推荐的，因为在这些情况下，隔离和正确性比速度更为重要。

    .. tab:: 英文

        You can instruct pytest to clear all cache files and values
        by adding the ``--cache-clear`` option like this:

        .. code-block:: bash

            pytest --cache-clear

        This is recommended for invocations from Continuous Integration
        servers where isolation and correctness is more important
        than speed.


.. _cache stepwise:

逐步
--------

**Stepwise**

.. tabs::

    .. tab:: 中文

        作为 ``--lf -x`` 的替代方案，特别是在你预期测试套件的大部分会失败的情况下，``--sw``，``--stepwise`` 允许你一次修复一个问题。测试套件将运行直到第一个失败，然后停止。在下次调用时，测试将从上一个失败的测试继续，然后运行直到下一个失败的测试。你可以使用 ``--stepwise-skip`` 选项来忽略一个失败的测试，并在第二个失败的测试上停止测试执行。这在你被一个失败的测试卡住时非常有用，只想在稍后忽略它。提供 ``--stepwise-skip`` 也会隐式启用 ``--stepwise``。

    .. tab:: 英文

        As an alternative to ``--lf -x``, especially for cases where you expect a large part of the test suite will fail, ``--sw``, ``--stepwise`` allows you to fix them one at a time. The test suite will run until the first failure and then stop. At the next invocation, tests will continue from the last failing test and then run until the next failing test. You may use the ``--stepwise-skip`` option to ignore one failing test and stop the test execution on the second failing test instead. This is useful if you get stuck on a failing test and just want to ignore it until later.  Providing ``--stepwise-skip`` will also enable ``--stepwise`` implicitly.
