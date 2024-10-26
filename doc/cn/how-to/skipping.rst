.. _`skip and xfail`:

.. _skipping:

如何使用 skip 和 xfail 处理无法成功的测试
=================================================================

**How to use skip and xfail to deal with tests that cannot succeed**

.. tabs::

    .. tab:: 中文

        你可以标记在某些平台上无法运行的测试函数或预期会失败的测试，以便 pytest 可以相应地处理它们，并在保持测试套件 *绿色* 的同时呈现测试会话的摘要。

        **skip** 表示你期望你的测试仅在满足某些条件时通过，否则 pytest 应该完全跳过运行该测试。常见的示例是，在非 Windows 平台上跳过仅适用于 Windows 的测试，或者跳过依赖于当前不可用的外部资源（例如数据库）的测试。

        **xfail** 表示你预期某个测试会因某种原因失败。常见示例是测试尚未实现的功能或尚未修复的错误。当一个预期会失败的测试（标记为 ``pytest.mark.xfail``）却意外通过时，它被称为 **xpass**，并将在测试摘要中报告。

        ``pytest`` 会单独统计和列出 *skip* 和 *xfail* 测试。默认情况下，不会显示跳过/失败测试的详细信息，以避免输出混乱。你可以使用 ``-r`` 选项查看与测试进度中显示的“简短”字母相对应的详细信息：

        .. code-block:: bash

            pytest -rxXs  # 显示 xfailed、xpassed 和 skipped 测试的额外信息

        有关 ``-r`` 选项的更多详细信息，可以通过运行 ``pytest -h`` 来找到。

        （见 :ref:`如何更改命令行选项默认值`）

    .. tab:: 英文

        You can mark test functions that cannot be run on certain platforms
        or that you expect to fail so pytest can deal with them accordingly and
        present a summary of the test session, while keeping the test suite *green*.

        A **skip** means that you expect your test to pass only if some conditions are met,
        otherwise pytest should skip running the test altogether. Common examples are skipping
        windows-only tests on non-windows platforms, or skipping tests that depend on an external
        resource which is not available at the moment (for example a database).

        An **xfail** means that you expect a test to fail for some reason.
        A common example is a test for a feature not yet implemented, or a bug not yet fixed.
        When a test passes despite being expected to fail (marked with ``pytest.mark.xfail``),
        it's an **xpass** and will be reported in the test summary.

        ``pytest`` counts and lists *skip* and *xfail* tests separately. Detailed
        information about skipped/xfailed tests is not shown by default to avoid
        cluttering the output.  You can use the ``-r`` option to see details
        corresponding to the "short" letters shown in the test progress:

        .. code-block:: bash

            pytest -rxXs  # show extra info on xfailed, xpassed, and skipped tests

        More details on the ``-r`` option can be found by running ``pytest -h``.

        (See :ref:`how to change command line options defaults`)

.. _skipif:
.. _skip:
.. _`condition booleans`:

跳过测试函数
-----------------------

**Skipping test functions**

.. tabs::

    .. tab:: 中文

        跳过测试函数的最简单方法是使用 ``skip`` 装饰器标记它，装饰器可以传递一个可选的 ``reason``：

        .. code-block:: python

            @pytest.mark.skip(reason="no way of currently testing this")
            def test_the_unknown(): ...

        另外，也可以在测试执行或设置过程中通过调用 ``pytest.skip(reason)`` 函数进行强制跳过：

        .. code-block:: python

            def test_function():
                if not valid_config():
                    pytest.skip("unsupported configuration")

        当无法在导入时评估跳过条件时，强制方法非常有用。

        也可以使用 ``pytest.skip(reason, allow_module_level=True)`` 在模块级别跳过整个模块：

        .. code-block:: python

            import sys

            import pytest

            if not sys.platform.startswith("win"):
                pytest.skip("skipping windows-only tests", allow_module_level=True)

    .. tab:: 英文

        The simplest way to skip a test function is to mark it with the ``skip`` decorator
        which may be passed an optional ``reason``:

        .. code-block:: python

            @pytest.mark.skip(reason="no way of currently testing this")
            def test_the_unknown(): ...


        Alternatively, it is also possible to skip imperatively during test execution or setup
        by calling the ``pytest.skip(reason)`` function:

        .. code-block:: python

            def test_function():
                if not valid_config():
                    pytest.skip("unsupported configuration")

        The imperative method is useful when it is not possible to evaluate the skip condition
        during import time.

        It is also possible to skip the whole module using
        ``pytest.skip(reason, allow_module_level=True)`` at the module level:

        .. code-block:: python

            import sys

            import pytest

            if not sys.platform.startswith("win"):
                pytest.skip("skipping windows-only tests", allow_module_level=True)


**Reference**: :ref:`pytest.mark.skip ref`

``skipif``
~~~~~~~~~~

**``skipif``**

.. tabs::

    .. tab:: 中文

        如果你希望有条件地跳过某些测试，可以使用 ``skipif``。以下是一个标记测试函数的示例，当在早于 Python 3.10 的解释器上运行时将被跳过：

        .. code-block:: python

            import sys


            @pytest.mark.skipif(sys.version_info < (3, 10), reason="requires python3.10 or higher")
            def test_function(): ...

        如果条件在收集期间评估为 ``True``，则测试函数将被跳过，指定的原因将在使用 ``-rs`` 时出现在摘要中。

        你可以在模块之间共享 ``skipif`` 标记。考虑这个测试模块：

        .. code-block:: python

            # content of test_mymodule.py
            import mymodule

            minversion = pytest.mark.skipif(
                mymodule.__versioninfo__ < (1, 1), reason="at least mymodule-1.1 required"
            )


            @minversion
            def test_function(): ...

        你可以导入标记并在另一个测试模块中重用它：

        .. code-block:: python

            # test_myothermodule.py
            from test_mymodule import minversion


            @minversion
            def test_anotherfunction(): ...

        对于较大的测试套件，通常最好有一个文件来定义标记，然后在整个测试套件中一致地应用这些标记。

        另外，你可以使用 :ref:`condition strings <string conditions>` 而不是布尔值，但它们在模块之间不容易共享，因此主要是出于向后兼容性原因而支持。

        **参考**: :ref:`pytest.mark.skipif ref`

    .. tab:: 英文


        If you wish to skip something conditionally then you can use ``skipif`` instead.
        Here is an example of marking a test function to be skipped
        when run on an interpreter earlier than Python3.10:

        .. code-block:: python

            import sys


            @pytest.mark.skipif(sys.version_info < (3, 10), reason="requires python3.10 or higher")
            def test_function(): ...

        If the condition evaluates to ``True`` during collection, the test function will be skipped,
        with the specified reason appearing in the summary when using ``-rs``.

        You can share ``skipif`` markers between modules.  Consider this test module:

        .. code-block:: python

            # content of test_mymodule.py
            import mymodule

            minversion = pytest.mark.skipif(
                mymodule.__versioninfo__ < (1, 1), reason="at least mymodule-1.1 required"
            )


            @minversion
            def test_function(): ...

        You can import the marker and reuse it in another test module:

        .. code-block:: python

            # test_myothermodule.py
            from test_mymodule import minversion


            @minversion
            def test_anotherfunction(): ...

        For larger test suites it's usually a good idea to have one file
        where you define the markers which you then consistently apply
        throughout your test suite.

        Alternatively, you can use :ref:`condition strings
        <string conditions>` instead of booleans, but they can't be shared between modules easily
        so they are supported mainly for backward compatibility reasons.

        **Reference**: :ref:`pytest.mark.skipif ref`


跳过类或模块的所有测试函数
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Skip all test functions of a class or module**

.. tabs::

    .. tab:: 中文

        你可以在类上使用 ``skipif`` 标记（与其他标记一样）：

        .. code-block:: python

            @pytest.mark.skipif(sys.platform == "win32", reason="does not run on windows")
            class TestPosixCalls:
                def test_function(self):
                    "will not be setup or run under 'win32' platform"

        如果条件为 ``True``，该标记将为该类的每个测试方法产生一个跳过结果。

        如果你想跳过模块的所有测试函数，可以使用 :globalvar:`pytestmark` 全局变量：

        .. code-block:: python

            # test_module.py
            pytestmark = pytest.mark.skipif(...)

        如果多个 ``skipif`` 装饰器应用于同一个测试函数，只要任何一个跳过条件为真，该测试函数将被跳过。

    .. tab:: 英文

        You can use the ``skipif`` marker (as any other marker) on classes:

        .. code-block:: python

            @pytest.mark.skipif(sys.platform == "win32", reason="does not run on windows")
            class TestPosixCalls:
                def test_function(self):
                    "will not be setup or run under 'win32' platform"

        If the condition is ``True``, this marker will produce a skip result for
        each of the test methods of that class.

        If you want to skip all test functions of a module, you may use the
        :globalvar:`pytestmark` global:

        .. code-block:: python

            # test_module.py
            pytestmark = pytest.mark.skipif(...)

        If multiple ``skipif`` decorators are applied to a test function, it
        will be skipped if any of the skip conditions is true.

.. _`whole class- or module level`: mark.html#scoped-marking


跳过文件或目录
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Skipping files or directories**

.. tabs::

    .. tab:: 中文

        有时你可能需要跳过整个文件或目录，例如如果测试依赖于特定于 Python 版本的特性或包含你不希望 pytest 运行的代码。在这种情况下，你必须将文件和目录排除在收集之外。有关更多信息，请参阅 :ref:`customizing-test-collection`。

    .. tab:: 英文

        Sometimes you may need to skip an entire file or directory, for example if the
        tests rely on Python version-specific features or contain code that you do not
        wish pytest to run. In this case, you must exclude the files and directories
        from collection. Refer to :ref:`customizing-test-collection` for more
        information.


跳过缺少的导入依赖项
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Skipping on a missing import dependency**

.. tabs::

    .. tab:: 中文

        你可以通过在模块级别、测试内部或测试设置函数中使用 :ref:`pytest.importorskip ref` 跳过缺失的导入测试。

        .. code-block:: python

            docutils = pytest.importorskip("docutils")

        如果在此处无法导入 ``docutils``，这将导致测试的跳过结果。你也可以根据库的版本号跳过测试：

        .. code-block:: python

            docutils = pytest.importorskip("docutils", minversion="0.3")

        版本将从指定模块的 ``__version__`` 属性中读取。

    .. tab:: 英文

        You can skip tests on a missing import by using :ref:`pytest.importorskip ref`
        at module level, within a test, or test setup function.

        .. code-block:: python

            docutils = pytest.importorskip("docutils")

        If ``docutils`` cannot be imported here, this will lead to a skip outcome of
        the test. You can also skip based on the version number of a library:

        .. code-block:: python

            docutils = pytest.importorskip("docutils", minversion="0.3")

        The version will be read from the specified
        module's ``__version__`` attribute.

摘要
~~~~~~~

**Summary**

.. tabs::

    .. tab:: 中文

        以下是在不同情况下如何跳过模块中的测试的快速指南：

        1. 无条件跳过模块中的所有测试：

        .. code-block:: python

                pytestmark = pytest.mark.skip("all tests still WIP")

        2. 根据某些条件跳过模块中的所有测试：

        .. code-block:: python

                pytestmark = pytest.mark.skipif(sys.platform == "win32", reason="tests for linux only")

        3. 如果缺少某个导入，则跳过模块中的所有测试：

        .. code-block:: python

                pexpect = pytest.importorskip("pexpect")

    .. tab:: 英文

        Here's a quick guide on how to skip tests in a module in different situations:

        1. Skip all tests in a module unconditionally:

        .. code-block:: python

                pytestmark = pytest.mark.skip("all tests still WIP")

        2. Skip all tests in a module based on some condition:

        .. code-block:: python

                pytestmark = pytest.mark.skipif(sys.platform == "win32", reason="tests for linux only")

        3. Skip all tests in a module if some import is missing:

        .. code-block:: python

                pexpect = pytest.importorskip("pexpect")


.. _xfail:

XFail：将测试函数标记为预期失败
----------------------------------------------

**XFail: mark test functions as expected to fail**

.. tabs::

    .. tab:: 中文

        你可以使用 ``xfail`` 标记来指示你期望一个测试失败：

        .. code-block:: python

            @pytest.mark.xfail
            def test_function(): ...

        该测试将会运行，但当它失败时不会报告任何回溯信息。相反，终端报告将其列在“预期失败”（``XFAIL``）或“意外通过”（``XPASS``）部分。

        另外，你也可以在测试或其设置函数内部以强制方式将测试标记为 ``XFAIL``：

        .. code-block:: python

            def test_function():
                if not valid_config():
                    pytest.xfail("failing configuration (but should work)")

        .. code-block:: python

            def test_function2():
                import slow_module

                if slow_module.slow_function():
                    pytest.xfail("slow_module taking too long")

        这两个示例说明了在模块级别不想检查条件的情况，此时条件会被评估以标记。

        这将使 ``test_function`` 成为 ``XFAIL``。请注意，在调用 :func:`pytest.xfail` 后不会执行其他代码，这与标记不同。这是因为它通过抛出已知异常在内部实现。

        **参考**: :ref:`pytest.mark.xfail ref`

    .. tab:: 英文

        You can use the ``xfail`` marker to indicate that you
        expect a test to fail:

        .. code-block:: python

            @pytest.mark.xfail
            def test_function(): ...

        This test will run but no traceback will be reported when it fails. Instead, terminal
        reporting will list it in the "expected to fail" (``XFAIL``) or "unexpectedly
        passing" (``XPASS``) sections.

        Alternatively, you can also mark a test as ``XFAIL`` from within the test or its setup function
        imperatively:

        .. code-block:: python

            def test_function():
                if not valid_config():
                    pytest.xfail("failing configuration (but should work)")

        .. code-block:: python

            def test_function2():
                import slow_module

                if slow_module.slow_function():
                    pytest.xfail("slow_module taking too long")

        These two examples illustrate situations where you don't want to check for a condition
        at the module level, which is when a condition would otherwise be evaluated for marks.

        This will make ``test_function`` ``XFAIL``. Note that no other code is executed after
        the :func:`pytest.xfail` call, differently from the marker. That's because it is implemented
        internally by raising a known exception.

        **Reference**: :ref:`pytest.mark.xfail ref`


``condition`` 参数
~~~~~~~~~~~~~~~~~~~~~~~

``condition`` **parameter**

.. tabs::

    .. tab:: 中文

        如果一个测试只在某个条件下预期失败，你可以将该条件作为第一个参数传递：

        .. code-block:: python

            @pytest.mark.xfail(sys.platform == "win32", reason="bug in a 3rd party library")
            def test_function(): ...

        请注意，你还必须传递一个原因（请参见 :ref:`pytest.mark.xfail ref` 中的参数说明）。

    .. tab:: 英文

        If a test is only expected to fail under a certain condition, you can pass
        that condition as the first parameter:

        .. code-block:: python

            @pytest.mark.xfail(sys.platform == "win32", reason="bug in a 3rd party library")
            def test_function(): ...

        Note that you have to pass a reason as well (see the parameter description at
        :ref:`pytest.mark.xfail ref`).

``reason`` 参数
~~~~~~~~~~~~~~~~~~~~

``reason`` **parameter**

.. tabs::

    .. tab:: 中文

        你可以使用 ``reason`` 参数指定预期失败的原因：

        .. code-block:: python

            @pytest.mark.xfail(reason="known parser issue")
            def test_function(): ...

    .. tab:: 英文

        You can specify the motive of an expected failure with the ``reason`` parameter:

        .. code-block:: python

            @pytest.mark.xfail(reason="known parser issue")
            def test_function(): ...


``raises`` 参数
~~~~~~~~~~~~~~~~~~~~

``raises`` **parameter**

.. tabs::

    .. tab:: 中文

        如果你想更具体地说明测试失败的原因，可以在 ``raises`` 参数中指定单个异常或异常元组。

        .. code-block:: python

            @pytest.mark.xfail(raises=RuntimeError)
            def test_function(): ...

        如果测试失败时引发的异常不在 ``raises`` 中，则将其报告为常规失败。

    .. tab:: 英文

        If you want to be more specific as to why the test is failing, you can specify
        a single exception, or a tuple of exceptions, in the ``raises`` argument.

        .. code-block:: python

            @pytest.mark.xfail(raises=RuntimeError)
            def test_function(): ...

        Then the test will be reported as a regular failure if it fails with an
        exception not mentioned in ``raises``.

``run`` 参数
~~~~~~~~~~~~~~~~~

``run`` **parameter**

.. tabs::

    .. tab:: 中文

        如果一个测试应该标记为 xfail 并按此报告，但不应该执行，使用 ``run`` 参数设置为 ``False``：

        .. code-block:: python

            @pytest.mark.xfail(run=False)
            def test_function(): ...

        这对于导致解释器崩溃的 xfailing 测试特别有用，这些测试应该在后续进行调查。

    .. tab:: 英文

        If a test should be marked as xfail and reported as such but should not be
        even executed, use the ``run`` parameter as ``False``:

        .. code-block:: python

            @pytest.mark.xfail(run=False)
            def test_function(): ...

        This is specially useful for xfailing tests that are crashing the interpreter and should be
        investigated later.

.. _`xfail strict tutorial`:

``strict`` 参数
~~~~~~~~~~~~~~~~~~~~

``strict`` **parameter**

.. tabs::

    .. tab:: 中文

        默认情况下， ``XFAIL`` 和 ``XPASS`` 不会导致测试套件失败。你可以通过将 ``strict`` 关键字参数设置为 ``True`` 来更改此行为：

        .. code-block:: python

            @pytest.mark.xfail(strict=True)
            def test_function(): ...


        这将使来自该测试的 ``XPASS`` （“意外通过”）结果导致测试套件失败。

        你可以使用 ``xfail_strict`` ini 选项更改 ``strict`` 参数的默认值：

        .. code-block:: ini

            [pytest]
            xfail_strict=true

    .. tab:: 英文

        Both ``XFAIL`` and ``XPASS`` don't fail the test suite by default.
        You can change this by setting the ``strict`` keyword-only parameter to ``True``:

        .. code-block:: python

            @pytest.mark.xfail(strict=True)
            def test_function(): ...


        This will make ``XPASS`` ("unexpectedly passing") results from this test to fail the test suite.

        You can change the default value of the ``strict`` parameter using the
        ``xfail_strict`` ini option:

        .. code-block:: ini

            [pytest]
            xfail_strict=true


忽略 xfail
~~~~~~~~~~~~~~

**Ignoring xfail**

.. tabs::

    .. tab:: 中文

        通过在命令行中指定：

        .. code-block:: bash

            pytest --runxfail

        你可以强制运行和报告标记为 ``xfail`` 的测试，就好像它根本没有被标记一样。这也会导致 :func:`pytest.xfail` 不产生任何效果。

    .. tab:: 英文

        By specifying on the commandline:

        .. code-block:: bash

            pytest --runxfail

        you can force the running and reporting of an ``xfail`` marked test
        as if it weren't marked at all. This also causes :func:`pytest.xfail` to produce no effect.

示例
~~~~~~~~

**Examples**

.. tabs::

    .. tab:: 中文

        这是一个包含多种用法的简单测试文件：

        .. literalinclude:: /example/xfail_demo.py

        使用报告 xfail 选项运行它会产生以下输出：

        .. FIXME: Use $ instead of ! again to re-enable regendoc once it's fixed:
        https://github.com/pytest-dev/pytest/issues/8807

        .. code-block:: pytest

            ! pytest -rx xfail_demo.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-6.x.y, py-1.x.y, pluggy-1.x.y
            cachedir: $PYTHON_PREFIX/.pytest_cache
            rootdir: $REGENDOC_TMPDIR/example
            collected 7 items

            xfail_demo.py xxxxxxx                                                [100%]

            ========================= short test summary info ==========================
            XFAIL xfail_demo.py::test_hello
            XFAIL xfail_demo.py::test_hello2
            reason: [NOTRUN]
            XFAIL xfail_demo.py::test_hello3
            condition: hasattr(os, 'sep')
            XFAIL xfail_demo.py::test_hello4
            bug 110
            XFAIL xfail_demo.py::test_hello5
            condition: pytest.__version__[0] != "17"
            XFAIL xfail_demo.py::test_hello6
            reason: reason
            XFAIL xfail_demo.py::test_hello7
            ============================ 7 xfailed in 0.12s ============================

    .. tab:: 英文

        Here is a simple test file with the several usages:

        .. literalinclude:: /example/xfail_demo.py

        Running it with the report-on-xfail option gives this output:

        .. FIXME: Use $ instead of ! again to re-enable regendoc once it's fixed:
        https://github.com/pytest-dev/pytest/issues/8807

        .. code-block:: pytest

            ! pytest -rx xfail_demo.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-6.x.y, py-1.x.y, pluggy-1.x.y
            cachedir: $PYTHON_PREFIX/.pytest_cache
            rootdir: $REGENDOC_TMPDIR/example
            collected 7 items

            xfail_demo.py xxxxxxx                                                [100%]

            ========================= short test summary info ==========================
            XFAIL xfail_demo.py::test_hello
            XFAIL xfail_demo.py::test_hello2
            reason: [NOTRUN]
            XFAIL xfail_demo.py::test_hello3
            condition: hasattr(os, 'sep')
            XFAIL xfail_demo.py::test_hello4
            bug 110
            XFAIL xfail_demo.py::test_hello5
            condition: pytest.__version__[0] != "17"
            XFAIL xfail_demo.py::test_hello6
            reason: reason
            XFAIL xfail_demo.py::test_hello7
            ============================ 7 xfailed in 0.12s ============================

.. _`skip/xfail with parametrize`:

使用 parametrize 跳过/xfail
---------------------------

**Skip/xfail with parametrize**

.. tabs::

    .. tab:: 中文

        可以在使用参数化时将标记（如 skip 和 xfail）应用于单个测试实例：

        .. code-block:: python

            import sys

            import pytest


            @pytest.mark.parametrize(
                ("n", "expected"),
                [
                    (1, 2),
                    pytest.param(1, 0, marks=pytest.mark.xfail),
                    pytest.param(1, 3, marks=pytest.mark.xfail(reason="some bug")),
                    (2, 3),
                    (3, 4),
                    (4, 5),
                    pytest.param(
                        10, 11, marks=pytest.mark.skipif(sys.version_info >= (3, 0), reason="py2k")
                    ),
                ],
            )
            def test_increment(n, expected):
                assert n + 1 == expected

    .. tab:: 英文

        It is possible to apply markers like skip and xfail to individual
        test instances when using parametrize:

        .. code-block:: python

            import sys

            import pytest


            @pytest.mark.parametrize(
                ("n", "expected"),
                [
                    (1, 2),
                    pytest.param(1, 0, marks=pytest.mark.xfail),
                    pytest.param(1, 3, marks=pytest.mark.xfail(reason="some bug")),
                    (2, 3),
                    (3, 4),
                    (4, 5),
                    pytest.param(
                        10, 11, marks=pytest.mark.skipif(sys.version_info >= (3, 0), reason="py2k")
                    ),
                ],
            )
            def test_increment(n, expected):
                assert n + 1 == expected
