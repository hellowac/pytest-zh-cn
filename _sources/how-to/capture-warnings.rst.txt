.. _`warnings`:

如何捕获警告
=======================

**How to capture warnings**

.. tabs::

    .. tab:: 中文

        从版本 ``3.1`` 开始，pytest 现在会在测试执行期间自动捕获警告，并在会话结束时显示它们：

        .. code-block:: python

            # test_show_warnings.py 的内容
            import warnings


            def api_v1():
                warnings.warn(UserWarning("api v1, 应该使用 v2 中的函数"))
                return 1


            def test_one():
                assert api_v1() == 1

        运行 pytest 现在会产生以下输出：

        .. code-block:: pytest

            $ pytest test_show_warnings.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_show_warnings.py .                                              [100%]

            ============================= warnings summary =============================
            test_show_warnings.py::test_one
            /home/sweet/project/test_show_warnings.py:5: UserWarning: api v1, 应该使用 v2 中的函数
                warnings.warn(UserWarning("api v1, 应该使用 v2 中的函数"))

            -- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
            ======================= 1 passed, 1 warning in 0.12s =======================

    .. tab:: 英文

        Starting from version ``3.1``, pytest now automatically catches warnings during test execution
        and displays them at the end of the session:

        .. code-block:: python

            # content of test_show_warnings.py
            import warnings


            def api_v1():
                warnings.warn(UserWarning("api v1, should use functions from v2"))
                return 1


            def test_one():
                assert api_v1() == 1

        Running pytest now produces this output:

        .. code-block:: pytest

            $ pytest test_show_warnings.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_show_warnings.py .                                              [100%]

            ============================= warnings summary =============================
            test_show_warnings.py::test_one
            /home/sweet/project/test_show_warnings.py:5: UserWarning: api v1, should use functions from v2
                warnings.warn(UserWarning("api v1, should use functions from v2"))

            -- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
            ======================= 1 passed, 1 warning in 0.12s =======================

.. _`controlling-warnings`:

控制警告
--------------------

**Controlling warnings**

.. tabs::

    .. tab:: 中文

        类似于 Python 的 `warning filter`_ 和 :option:`-W option <python:-W>` 标志，pytest 提供了自己的 ``-W`` 标志，用于控制忽略、显示或将哪些警告转变为错误。有关更高级用例，请参见 `warning filter`_ 文档。

        .. _`warning filter`: https://docs.python.org/3/library/warnings.html#warning-filter

        以下代码示例展示了如何将任何 ``UserWarning`` 类别的警告视为错误：

        .. code-block:: pytest

            $ pytest -q test_show_warnings.py -W error::UserWarning
            F                                                                    [100%]
            ================================= FAILURES =================================
            _________________________________ test_one _________________________________

                def test_one():
            >       assert api_v1() == 1

            test_show_warnings.py:10:
            _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _

                def api_v1():
            >       warnings.warn(UserWarning("api v1, 应该使用 v2 中的函数"))
            E       UserWarning: api v1, 应该使用 v2 中的函数

            test_show_warnings.py:5: UserWarning
            ========================= short test summary info ==========================
            FAILED test_show_warnings.py::test_one - UserWarning: api v1, 应该使用 ...
            1 failed in 0.12s

        相同的选项可以通过 ``filterwarnings`` ini 选项在 ``pytest.ini`` 或 ``pyproject.toml`` 文件中设置。例如，以下配置将忽略所有用户警告和匹配正则表达式的特定弃用警告，但会将所有其他警告转变为错误。

        .. code-block:: ini

            # pytest.ini
            [pytest]
            filterwarnings =
                error
                ignore::UserWarning
                ignore:function ham\(\) is deprecated:DeprecationWarning

        .. code-block:: toml

            # pyproject.toml
            [tool.pytest.ini_options]
            filterwarnings = [
                "error",
                "ignore::UserWarning",
                # 注意下面使用单引号表示 TOML 中的“原始”字符串
                'ignore:function ham\(\) is deprecated:DeprecationWarning',
            ]

        当一个警告匹配列表中的多个选项时，将执行最后一个匹配选项的操作。

        .. note::

            ``-W`` 标志和 ``filterwarnings`` ini 选项使用的警告过滤器在结构上相似，但每个配置选项对其过滤器的解释不同。例如，``filterwarnings`` 中的 *message* 是一个包含正则表达式的字符串，该正则表达式必须不区分大小写地与警告消息的开头匹配，而 ``-W`` 中的 *message* 是一个字面字符串，该字符串必须不区分大小写地包含警告消息的开头（忽略消息开头或结尾的任何空格）。有关更多详细信息，请参阅 `warning filter`_ 文档。

    .. tab:: 英文

        Similar to Python's `warning filter`_ and :option:`-W option <python:-W>` flag, pytest provides
        its own ``-W`` flag to control which warnings are ignored, displayed, or turned into
        errors. See the `warning filter`_ documentation for more
        advanced use-cases.

        .. _`warning filter`: https://docs.python.org/3/library/warnings.html#warning-filter

        This code sample shows how to treat any ``UserWarning`` category class of warning
        as an error:

        .. code-block:: pytest

            $ pytest -q test_show_warnings.py -W error::UserWarning
            F                                                                    [100%]
            ================================= FAILURES =================================
            _________________________________ test_one _________________________________

                def test_one():
            >       assert api_v1() == 1

            test_show_warnings.py:10:
            _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _

                def api_v1():
            >       warnings.warn(UserWarning("api v1, should use functions from v2"))
            E       UserWarning: api v1, should use functions from v2

            test_show_warnings.py:5: UserWarning
            ========================= short test summary info ==========================
            FAILED test_show_warnings.py::test_one - UserWarning: api v1, should use ...
            1 failed in 0.12s

        The same option can be set in the ``pytest.ini`` or ``pyproject.toml`` file using the
        ``filterwarnings`` ini option. For example, the configuration below will ignore all
        user warnings and specific deprecation warnings matching a regex, but will transform
        all other warnings into errors.

        .. code-block:: ini

            # pytest.ini
            [pytest]
            filterwarnings =
                error
                ignore::UserWarning
                ignore:function ham\(\) is deprecated:DeprecationWarning

        .. code-block:: toml

            # pyproject.toml
            [tool.pytest.ini_options]
            filterwarnings = [
                "error",
                "ignore::UserWarning",
                # note the use of single quote below to denote "raw" strings in TOML
                'ignore:function ham\(\) is deprecated:DeprecationWarning',
            ]


        When a warning matches more than one option in the list, the action for the last matching option
        is performed.


        .. note::

            The ``-W`` flag and the ``filterwarnings`` ini option use warning filters that are
            similar in structure, but each configuration option interprets its filter
            differently. For example, *message* in ``filterwarnings`` is a string containing a
            regular expression that the start of the warning message must match,
            case-insensitively, while *message* in ``-W`` is a literal string that the start of
            the warning message must contain (case-insensitively), ignoring any whitespace at
            the start or end of message. Consult the `warning filter`_ documentation for more
            details.


.. _`filterwarnings`:

``@pytest.mark.filterwarnings``
-------------------------------

**``@pytest.mark.filterwarnings``**

.. tabs::

    .. tab:: 中文

        您可以使用 ``@pytest.mark.filterwarnings`` 为特定测试项添加警告过滤器，从而更精细地控制在测试、类或甚至模块级别应捕获哪些警告：

        .. code-block:: python

            import warnings


            def api_v1():
                warnings.warn(UserWarning("api v1, 应该使用 v2 中的函数"))
                return 1


            @pytest.mark.filterwarnings("ignore:api v1")
            def test_one():
                assert api_v1() == 1


        使用标记应用的过滤器优先于通过命令行传递或通过 ``filterwarnings`` ini 选项配置的过滤器。

        您可以通过将 ``filterwarnings`` 标记用作类装饰器，来对类中所有测试应用过滤器，或者通过设置 :globalvar:`pytestmark` 变量，对模块中的所有测试应用过滤器：

        .. code-block:: python

            # 将该模块中的所有警告转变为错误
            pytestmark = pytest.mark.filterwarnings("error")


        *感谢 Florian Schulze 在* `pytest-warnings`_
        *插件中的参考实现。*

    .. tab:: 英文

        You can use the ``@pytest.mark.filterwarnings`` to add warning filters to specific test items,
        allowing you to have finer control of which warnings should be captured at test, class or
        even module level:

        .. code-block:: python

            import warnings


            def api_v1():
                warnings.warn(UserWarning("api v1, should use functions from v2"))
                return 1


            @pytest.mark.filterwarnings("ignore:api v1")
            def test_one():
                assert api_v1() == 1


        Filters applied using a mark take precedence over filters passed on the command line or configured
        by the ``filterwarnings`` ini option.

        You may apply a filter to all tests of a class by using the ``filterwarnings`` mark as a class
        decorator or to all tests in a module by setting the :globalvar:`pytestmark` variable:

        .. code-block:: python

            # turns all warnings into errors for this module
            pytestmark = pytest.mark.filterwarnings("error")



        *Credits go to Florian Schulze for the reference implementation in the* `pytest-warnings`_
        *plugin.*

.. _`pytest-warnings`: https://github.com/fschulze/pytest-warnings

禁用警告摘要
--------------------------

**Disabling warnings summary**

.. tabs::

    .. tab:: 中文

        虽然不推荐，但您可以使用 ``--disable-warnings`` 命令行选项完全抑制测试运行输出中的警告摘要。

    .. tab:: 英文

        Although not recommended, you can use the ``--disable-warnings`` command-line option to suppress the
        warning summary entirely from the test run output.

完全禁用警告捕获
----------------------------------

**Disabling warning capture entirely**

.. tabs::

    .. tab:: 中文

        此插件默认启用，但可以通过在 ``pytest.ini`` 文件中禁用:

            .. code-block:: ini

                [pytest]
                addopts = -p no:warnings

        或者在命令行中传递 ``-p no:warnings``。如果您的测试套件使用外部系统处理警告，这可能会很有用。

    .. tab:: 英文

        This plugin is enabled by default but can be disabled entirely in your ``pytest.ini`` file with:

            .. code-block:: ini

                [pytest]
                addopts = -p no:warnings

        Or passing ``-p no:warnings`` in the command-line. This might be useful if your test suites handles warnings
        using an external system.


.. _`deprecation-warnings`:

DeprecationWarning 和 PendingDeprecationWarning
------------------------------------------------

**DeprecationWarning and PendingDeprecationWarning**

.. tabs::

    .. tab:: 中文

        默认情况下，pytest 将显示来自用户代码和第三方库的 ``DeprecationWarning`` 和 ``PendingDeprecationWarning`` 警告，这符合 :pep:`565` 的建议。这有助于用户保持代码的现代性，并在弃用警告被有效移除时避免出现故障。

        然而，在用户通过 :func:`pytest.warns`、:func:`pytest.deprecated_call` 或使用 :fixture:`recwarn` 固件捕获任何类型的警告的特定情况下，将不会显示任何警告。

        有时，隐藏一些发生在您无法控制的代码（例如第三方库）中的特定弃用警告是有用的，此时您可以使用警告过滤器选项（ini 或标记）来忽略这些警告。

        例如：

        .. code-block:: ini

            [pytest]
            filterwarnings =
                ignore:.*U.*mode is deprecated:DeprecationWarning

        这将忽略所有类型为 ``DeprecationWarning`` 的警告，其中消息的开头与正则表达式 ``".*U.*mode is deprecated"`` 匹配。

        有关更多示例，请参见 :ref:`@pytest.mark.filterwarnings <filterwarnings>` 和 :ref:`Controlling warnings <controlling-warnings>`。

        .. note::

            如果在解释器级别配置了警告，使用 :envvar:`python:PYTHONWARNINGS` 环境变量或 ``-W`` 命令行选项，pytest 默认将不配置任何过滤器。

            此外，pytest 不遵循 :pep:`506` 建议重置所有警告过滤器，因为这可能会破坏自行通过调用 :func:`warnings.simplefilter` 配置警告过滤器的测试套件（有关此的示例，请参见 :issue:`2430`）。

    .. tab:: 英文

        By default pytest will display ``DeprecationWarning`` and ``PendingDeprecationWarning`` warnings from
        user code and third-party libraries, as recommended by :pep:`565`.
        This helps users keep their code modern and avoid breakages when deprecated warnings are effectively removed.

        However, in the specific case where users capture any type of warnings in their test, either with
        :func:`pytest.warns`, :func:`pytest.deprecated_call` or using the :fixture:`recwarn` fixture,
        no warning will be displayed at all.

        Sometimes it is useful to hide some specific deprecation warnings that happen in code that you have no control over
        (such as third-party libraries), in which case you might use the warning filters options (ini or marks) to ignore
        those warnings.

        For example:

        .. code-block:: ini

            [pytest]
            filterwarnings =
                ignore:.*U.*mode is deprecated:DeprecationWarning


        This will ignore all warnings of type ``DeprecationWarning`` where the start of the message matches
        the regular expression ``".*U.*mode is deprecated"``.

        See :ref:`@pytest.mark.filterwarnings <filterwarnings>` and
        :ref:`Controlling warnings <controlling-warnings>` for more examples.

        .. note::

            If warnings are configured at the interpreter level, using
            the :envvar:`python:PYTHONWARNINGS` environment variable or the
            ``-W`` command-line option, pytest will not configure any filters by default.

            Also pytest doesn't follow :pep:`506` suggestion of resetting all warning filters because
            it might break test suites that configure warning filters themselves
            by calling :func:`warnings.simplefilter` (see :issue:`2430` for an example of that).


.. _`ensuring a function triggers a deprecation warning`:

.. _ensuring_function_triggers:

确保代码触发弃用警告
--------------------------------------------

**Ensuring code triggers a deprecation warning**

.. tabs::

    .. tab:: 中文

        您还可以使用 :func:`pytest.deprecated_call` 来检查某个函数调用是否触发 ``DeprecationWarning`` 或 ``PendingDeprecationWarning``：

        .. code-block:: python

            import pytest


            def test_myfunction_deprecated():
                with pytest.deprecated_call():
                    myfunction(17)

        如果在用 ``17`` 参数调用 ``myfunction`` 时不发出弃用警告，该测试将失败。

    .. tab:: 英文

        You can also use :func:`pytest.deprecated_call` for checking
        that a certain function call triggers a ``DeprecationWarning`` or
        ``PendingDeprecationWarning``:

        .. code-block:: python

            import pytest


            def test_myfunction_deprecated():
                with pytest.deprecated_call():
                    myfunction(17)

        This test will fail if ``myfunction`` does not issue a deprecation warning
        when called with a ``17`` argument.




.. _`asserting warnings`:

.. _assertwarnings:

.. _`asserting warnings with the warns function`:

.. _warns:

使用 warns 函数断言警告
------------------------------------------

**Asserting warnings with the warns function**

.. tabs::

    .. tab:: 中文

        你可以使用 :func:`pytest.warns` 检查代码是否引发特定的警告，这与 :ref:`raises <assertraises>` 的工作方式类似 ( 不同之处在于 :ref:`raises <assertraises>` 不捕获所有异常，仅捕获 ``expected_exception`` ):

        .. code-block:: python

            import warnings

            import pytest


            def test_warning():
                with pytest.warns(UserWarning):
                    warnings.warn("my warning", UserWarning)

        如果没有引发相关警告，测试将失败。使用关键字参数 ``match`` 来断言警告是否与文本或正则表达式匹配。要匹配可能包含正则表达式元字符（如 ``(`` 或 ``.``）的字面字符串，可以先使用 ``re.escape`` 对模式进行转义。

        一些示例：

        .. code-block:: pycon

            >>> with warns(UserWarning, match="must be 0 or None"):
            ...     warnings.warn("value must be 0 or None", UserWarning)
            ...

            >>> with warns(UserWarning, match=r"must be \d+$"):
            ...     warnings.warn("value must be 42", UserWarning)
            ...

            >>> with warns(UserWarning, match=r"must be \d+$"):
            ...     warnings.warn("this is not here", UserWarning)
            ...
            Traceback (most recent call last):
            ...
            Failed: DID NOT WARN. No warnings of type ...UserWarning... were emitted...

            >>> with warns(UserWarning, match=re.escape("issue with foo() func")):
            ...     warnings.warn("issue with foo() func")
            ...

        你还可以在函数或代码字符串上调用 :func:`pytest.warns`：

        .. code-block:: python

            pytest.warns(expected_warning, func, *args, **kwargs)
            pytest.warns(expected_warning, "func(*args, **kwargs)")

        该函数还返回一个所有引发的警告的列表（作为 ``warnings.WarningMessage`` 对象），你可以查询这些对象以获取更多信息：

        .. code-block:: python

            with pytest.warns(RuntimeWarning) as record:
                warnings.warn("another warning", RuntimeWarning)

            # 检查仅引发了一条警告
            assert len(record) == 1
            # 检查消息是否匹配
            assert record[0].message.args[0] == "another warning"

        另外，你可以使用 :fixture:`recwarn` 夹具详细检查引发的警告（见 :ref:`below <recwarn>`）。

        :fixture:`recwarn` 夹具会自动确保在测试结束时重置警告过滤器，因此不会泄露全局状态。

    .. tab:: 英文

        You can check that code raises a particular warning using :func:`pytest.warns`,
        which works in a similar manner to :ref:`raises <assertraises>` (except that
        :ref:`raises <assertraises>` does not capture all exceptions, only the
        ``expected_exception``):

        .. code-block:: python

            import warnings

            import pytest


            def test_warning():
                with pytest.warns(UserWarning):
                    warnings.warn("my warning", UserWarning)

        The test will fail if the warning in question is not raised. Use the keyword
        argument ``match`` to assert that the warning matches a text or regex.
        To match a literal string that may contain regular expression metacharacters like ``(`` or ``.``, the pattern can
        first be escaped with ``re.escape``.

        Some examples:

        .. code-block:: pycon


            >>> with warns(UserWarning, match="must be 0 or None"):
            ...     warnings.warn("value must be 0 or None", UserWarning)
            ...

            >>> with warns(UserWarning, match=r"must be \d+$"):
            ...     warnings.warn("value must be 42", UserWarning)
            ...

            >>> with warns(UserWarning, match=r"must be \d+$"):
            ...     warnings.warn("this is not here", UserWarning)
            ...
            Traceback (most recent call last):
            ...
            Failed: DID NOT WARN. No warnings of type ...UserWarning... were emitted...

            >>> with warns(UserWarning, match=re.escape("issue with foo() func")):
            ...     warnings.warn("issue with foo() func")
            ...

        You can also call :func:`pytest.warns` on a function or code string:

        .. code-block:: python

            pytest.warns(expected_warning, func, *args, **kwargs)
            pytest.warns(expected_warning, "func(*args, **kwargs)")

        The function also returns a list of all raised warnings (as
        ``warnings.WarningMessage`` objects), which you can query for
        additional information:

        .. code-block:: python

            with pytest.warns(RuntimeWarning) as record:
                warnings.warn("another warning", RuntimeWarning)

            # check that only one warning was raised
            assert len(record) == 1
            # check that the message matches
            assert record[0].message.args[0] == "another warning"

        Alternatively, you can examine raised warnings in detail using the
        :fixture:`recwarn` fixture (see :ref:`below <recwarn>`).


        The :fixture:`recwarn` fixture automatically ensures to reset the warnings
        filter at the end of the test, so no global state is leaked.

.. _`recording warnings`:

.. _recwarn:

记录警告
------------------

**Recording warnings**

.. tabs::

    .. tab:: 中文

        你可以使用 :func:`pytest.warns` 上下文管理器或 :fixture:`recwarn` 夹具来记录引发的警告。

        要使用 :func:`pytest.warns` 记录警告而不对警告进行任何断言，可以不传递任何期望的警告类型参数，默认将使用通用的警告：

        .. code-block:: python

            with pytest.warns() as record:
                warnings.warn("user", UserWarning)
                warnings.warn("runtime", RuntimeWarning)

            assert len(record) == 2
            assert str(record[0].message) == "user"
            assert str(record[1].message) == "runtime"

        :fixture:`recwarn` 夹具将记录整个函数的警告：

        .. code-block:: python

            import warnings


            def test_hello(recwarn):
                warnings.warn("hello", UserWarning)
                assert len(recwarn) == 1
                w = recwarn.pop(UserWarning)
                assert issubclass(w.category, UserWarning)
                assert str(w.message) == "hello"
                assert w.filename
                assert w.lineno

        :fixture:`recwarn` 夹具和 :func:`pytest.warns` 上下文管理器都返回相同的记录警告的接口：一个 :class:`~_pytest.recwarn.WarningsRecorder` 实例。要查看记录的警告，你可以遍历这个实例，调用 ``len`` 来获取记录的警告数量，或通过索引获取特定的记录警告。

    .. tab:: 英文

        You can record raised warnings either using the :func:`pytest.warns` context manager or with
        the :fixture:`recwarn` fixture.

        To record with :func:`pytest.warns` without asserting anything about the warnings,
        pass no arguments as the expected warning type and it will default to a generic Warning:

        .. code-block:: python

            with pytest.warns() as record:
                warnings.warn("user", UserWarning)
                warnings.warn("runtime", RuntimeWarning)

            assert len(record) == 2
            assert str(record[0].message) == "user"
            assert str(record[1].message) == "runtime"

        The :fixture:`recwarn` fixture will record warnings for the whole function:

        .. code-block:: python

            import warnings


            def test_hello(recwarn):
                warnings.warn("hello", UserWarning)
                assert len(recwarn) == 1
                w = recwarn.pop(UserWarning)
                assert issubclass(w.category, UserWarning)
                assert str(w.message) == "hello"
                assert w.filename
                assert w.lineno

        Both the :fixture:`recwarn` fixture and the :func:`pytest.warns` context manager return the same interface for recorded
        warnings: a :class:`~_pytest.recwarn.WarningsRecorder` instance. To view the recorded warnings, you can
        iterate over this instance, call ``len`` on it to get the number of recorded
        warnings, or index into it to get a particular recorded warning.


.. _`warns use cases`:

测试中警告的其他用例
-----------------------------------------

**Additional use cases of warnings in tests**

.. tabs::

    .. tab:: 中文

        以下是一些在测试中经常遇到的与警告相关的用例，以及处理它们的建议：

        - 要确保 **至少发出一个** 指定的警告，可以使用：

        .. code-block:: python

            def test_warning():
                with pytest.warns((RuntimeWarning, UserWarning)):
                    ...

        - 要确保 **仅** 发出某些警告，可以使用：

        .. code-block:: python

            def test_warning(recwarn):
                ...
                assert len(recwarn) == 1
                user_warning = recwarn.pop(UserWarning)
                assert issubclass(user_warning.category, UserWarning)

        - 要确保 **不** 发出任何警告，可以使用：

        .. code-block:: python

            def test_warning():
                with warnings.catch_warnings():
                    warnings.simplefilter("error")
                    ...

        - 要抑制警告，可以使用：

        .. code-block:: python

            with warnings.catch_warnings():
                warnings.simplefilter("ignore")
                ...

    .. tab:: 英文

        Here are some use cases involving warnings that often come up in tests, and suggestions on how to deal with them:

        - To ensure that **at least one** of the indicated warnings is issued, use:

        .. code-block:: python

            def test_warning():
                with pytest.warns((RuntimeWarning, UserWarning)):
                    ...

        - To ensure that **only** certain warnings are issued, use:

        .. code-block:: python

            def test_warning(recwarn):
                ...
                assert len(recwarn) == 1
                user_warning = recwarn.pop(UserWarning)
                assert issubclass(user_warning.category, UserWarning)

        -  To ensure that **no** warnings are emitted, use:

        .. code-block:: python

            def test_warning():
                with warnings.catch_warnings():
                    warnings.simplefilter("error")
                    ...

        - To suppress warnings, use:

        .. code-block:: python

            with warnings.catch_warnings():
                warnings.simplefilter("ignore")
                ...


.. _custom_failure_messages:

自定义失败消息
-----------------------

**Custom failure messages**

.. tabs::

    .. tab:: 中文

        记录警告提供了在未发出警告或满足其他条件时生成自定义测试失败消息的机会。

        .. code-block:: python

            def test():
                with pytest.warns(Warning) as record:
                    f()
                    if not record:
                        pytest.fail("Expected a warning!")

        如果在调用 ``f`` 时没有发出任何警告，则 ``not record`` 将评估为 ``True``。然后你可以使用自定义错误消息调用 :func:`pytest.fail`。

    .. tab:: 英文

        Recording warnings provides an opportunity to produce custom test
        failure messages for when no warnings are issued or other conditions
        are met.

        .. code-block:: python

            def test():
                with pytest.warns(Warning) as record:
                    f()
                    if not record:
                        pytest.fail("Expected a warning!")

        If no warnings are issued when calling ``f``, then ``not record`` will
        evaluate to ``True``.  You can then call :func:`pytest.fail` with a
        custom error message.

.. _internal-warnings:

内部 pytest 警告
------------------------

**Internal pytest warnings**

.. tabs::

    .. tab:: 中文

        在某些情况下，pytest 可能会生成自己的警告，例如不当使用或过时的特性。

        例如，如果 pytest 遇到一个与 :confval:`python_classes` 匹配但同时定义了 ``__init__`` 构造函数的类，它会发出警告，因为这会阻止该类的实例化：

        .. code-block:: python

            # content of test_pytest_warnings.py
            class Test:
                def __init__(self):
                    pass

                def test_foo(self):
                    assert 1 == 1

        .. code-block:: pytest

            $ pytest test_pytest_warnings.py -q

            ============================= warnings summary =============================
            test_pytest_warnings.py:1
            /home/sweet/project/test_pytest_warnings.py:1: PytestCollectionWarning: cannot collect test class 'Test' because it has a __init__ constructor (from: test_pytest_warnings.py)
                class Test:

            -- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
            1 warning in 0.12s

        这些警告可以使用与过滤其他类型警告相同的内置机制进行过滤。

        请阅读我们的 :ref:`backwards-compatibility` 以了解我们如何处理弃用和最终删除特性。

        完整的警告列表在 :ref:`the reference documentation <warnings ref>` 中列出。

    .. tab:: 英文

        pytest may generate its own warnings in some situations, such as improper usage or deprecated features.

        For example, pytest will emit a warning if it encounters a class that matches :confval:`python_classes` but also
        defines an ``__init__`` constructor, as this prevents the class from being instantiated:

        .. code-block:: python

            # content of test_pytest_warnings.py
            class Test:
                def __init__(self):
                    pass

                def test_foo(self):
                    assert 1 == 1

        .. code-block:: pytest

            $ pytest test_pytest_warnings.py -q

            ============================= warnings summary =============================
            test_pytest_warnings.py:1
            /home/sweet/project/test_pytest_warnings.py:1: PytestCollectionWarning: cannot collect test class 'Test' because it has a __init__ constructor (from: test_pytest_warnings.py)
                class Test:

            -- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
            1 warning in 0.12s

        These warnings might be filtered using the same builtin mechanisms used to filter other types of warnings.

        Please read our :ref:`backwards-compatibility` to learn how we proceed about deprecating and eventually removing
        features.

        The full list of warnings is listed in :ref:`the reference documentation <warnings ref>`.


.. _`resource-warnings`:

资源警告
-----------------

**Resource Warnings**

.. tabs::

    .. tab:: 中文

        当 :class:`ResourceWarning` 被 pytest 捕获时，如果启用了 :mod:`tracemalloc` 模块，可以获得源的额外信息。

        在运行测试时，启用 :mod:`tracemalloc` 的一种方便方法是将 :envvar:`PYTHONTRACEMALLOC` 设置为足够大的帧数（例如 ``20``，但该数字取决于应用程序）。

        有关更多信息，请参考 Python 文档中的 `Python Development Mode <https://docs.python.org/3/library/devmode.html>`__ 部分。

    .. tab:: 英文

        Additional information of the source of a :class:`ResourceWarning` can be obtained when captured by pytest if
        :mod:`tracemalloc` module is enabled.

        One convenient way to enable :mod:`tracemalloc` when running tests is to set the :envvar:`PYTHONTRACEMALLOC` to a large
        enough number of frames (say ``20``, but that number is application dependent).

        For more information, consult the `Python Development Mode <https://docs.python.org/3/library/devmode.html>`__ section in the Python documentation.
