.. _`assert`:

如何在测试中编写和报告断言
==================================================

**How to write and report assertions in tests**

.. _`assert with the assert statement`:

使用 ``assert`` 语句进行断言
---------------------------------------------------------

**Asserting with the** ``assert`` **statement**

.. tabs::

    .. tab:: 中文

        ``pytest`` 允许您使用标准 Python ``assert`` 来验证 Python 测试中的期望和数值。例如，您可以编写如下代码：

        .. code-block:: python

            # test_assert1.py 的内容
            def f():
                return 3


            def test_function():
                assert f() == 4

        以断言您的函数返回某个特定值。如果这个断言失败，您将看到函数调用的返回值：

        .. code-block:: pytest

            $ pytest test_assert1.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            收集到 1 项

            test_assert1.py F                                                    [100%]

            ================================= FAILURES =================================
            ______________________________ test_function _______________________________

                def test_function():
            >       assert f() == 4
            E       assert 3 == 4
            E        +  where 3 = f()

            test_assert1.py:6: AssertionError
            ========================= 测试摘要信息 ==========================
            FAILED test_assert1.py::test_function - assert 3 == 4
            ============================ 1 failed in 0.12s =============================

        ``pytest`` 支持显示最常见的子表达式的值，包括调用、属性、比较以及二元和一元运算符。（参见 :ref:`tbreportdemo`）。这使您可以使用习惯的 Python 结构，而无需样板代码，同时又不失去自省(introspection)信息。

        如果使用类似如下的方式为断言指定了消息：

        .. code-block:: python

            assert a % 2 == 0, "值为奇数，应该为偶数"

        则该消息会与断言自省(introspection)信息一起打印在回溯中。

        有关断言自省(introspection)的更多信息，请参见 :ref:`assert-details`。

    .. tab:: 英文

        ``pytest`` allows you to use the standard Python ``assert`` for verifying
        expectations and values in Python tests.  For example, you can write the
        following:

        .. code-block:: python

            # content of test_assert1.py
            def f():
                return 3


            def test_function():
                assert f() == 4

        to assert that your function returns a certain value. If this assertion fails
        you will see the return value of the function call:

        .. code-block:: pytest

            $ pytest test_assert1.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_assert1.py F                                                    [100%]

            ================================= FAILURES =================================
            ______________________________ test_function _______________________________

                def test_function():
            >       assert f() == 4
            E       assert 3 == 4
            E        +  where 3 = f()

            test_assert1.py:6: AssertionError
            ========================= short test summary info ==========================
            FAILED test_assert1.py::test_function - assert 3 == 4
            ============================ 1 failed in 0.12s =============================

        ``pytest`` has support for showing the values of the most common subexpressions
        including calls, attributes, comparisons, and binary and unary
        operators. (See :ref:`tbreportdemo`).  This allows you to use the
        idiomatic python constructs without boilerplate code while not losing
        introspection information.

        If a message is specified with the assertion like this:

        .. code-block:: python

            assert a % 2 == 0, "value was odd, should be even"

        it is printed alongside the assertion introspection in the traceback.

        See :ref:`assert-details` for more information on assertion introspection.

.. _`assertraises`:

关于预期异常的断言
------------------------------------------

**Assertions about expected exceptions**

.. tabs::

    .. tab:: 中文

        为了对引发的异常进行断言，您可以像这样使用 :func:`pytest.raises` 作为上下文管理器：

        .. code-block:: python

            import pytest


            def test_zero_division():
                with pytest.raises(ZeroDivisionError):
                    1 / 0

        如果您需要访问实际的异常信息，可以使用：

        .. code-block:: python

            def test_recursion_depth():
                with pytest.raises(RuntimeError) as excinfo:

                    def f():
                        f()

                    f()
                assert "maximum recursion" in str(excinfo.value)

        ``excinfo`` 是一个 :class:`~pytest.ExceptionInfo` 实例，它是实际引发的异常的包装器。主要感兴趣的属性有 ``.type``、``.value`` 和 ``.traceback``。

        请注意，``pytest.raises`` 将匹配异常类型或任何子类（就像标准的 ``except`` 语句一样）。如果您想检查某个代码块是否引发了确切的异常类型，您需要显式地进行检查：

        .. code-block:: python

            def test_foo_not_implemented():
                def foo():
                    raise NotImplementedError

                with pytest.raises(RuntimeError) as excinfo:
                    foo()
                assert excinfo.type is RuntimeError

        尽管该函数引发了 :class:`NotImplementedError`，但 :func:`pytest.raises` 调用仍会成功，因为 :class:`NotImplementedError` 是 :class:`RuntimeError` 的子类；然而，以下的 `assert` 语句将捕捉到这个问题。

    .. tab:: 英文

        In order to write assertions about raised exceptions, you can use
        :func:`pytest.raises` as a context manager like this:

        .. code-block:: python

            import pytest


            def test_zero_division():
                with pytest.raises(ZeroDivisionError):
                    1 / 0

        and if you need to have access to the actual exception info you may use:

        .. code-block:: python

            def test_recursion_depth():
                with pytest.raises(RuntimeError) as excinfo:

                    def f():
                        f()

                    f()
                assert "maximum recursion" in str(excinfo.value)

        ``excinfo`` is an :class:`~pytest.ExceptionInfo` instance, which is a wrapper around
        the actual exception raised.  The main attributes of interest are
        ``.type``, ``.value`` and ``.traceback``.

        Note that ``pytest.raises`` will match the exception type or any subclasses (like the standard ``except`` statement).
        If you want to check if a block of code is raising an exact exception type, you need to check that explicitly:


        .. code-block:: python

            def test_foo_not_implemented():
                def foo():
                    raise NotImplementedError

                with pytest.raises(RuntimeError) as excinfo:
                    foo()
                assert excinfo.type is RuntimeError

        The :func:`pytest.raises` call will succeed, even though the function raises :class:`NotImplementedError`, because
        :class:`NotImplementedError` is a subclass of :class:`RuntimeError`; however the following `assert` statement will
        catch the problem.

匹配异常消息
~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Matching exception messages**

.. tabs::

    .. tab:: 中文

        您可以将 ``match`` 关键字参数传递给上下文管理器，以测试正则表达式是否与异常的字符串表示匹配（类似于 ``unittest`` 中的 ``TestCase.assertRaisesRegex`` 方法）：

        .. code-block:: python

            import pytest


            def myfunc():
                raise ValueError("Exception 123 raised")


            def test_match():
                with pytest.raises(ValueError, match=r".* 123 .*"):
                    myfunc()

        注意事项：

        * ``match`` 参数是与 :func:`re.search` 函数匹配的，因此在上述示例中，``match='123'`` 也可以正常工作。
        * ``match`` 参数还会与 `PEP-678 <https://peps.python.org/pep-0678/>`__ 中的 ``__notes__`` 匹配。

    .. tab:: 英文

        You can pass a ``match`` keyword parameter to the context-manager to test
        that a regular expression matches on the string representation of an exception
        (similar to the ``TestCase.assertRaisesRegex`` method from ``unittest``):

        .. code-block:: python

            import pytest


            def myfunc():
                raise ValueError("Exception 123 raised")


            def test_match():
                with pytest.raises(ValueError, match=r".* 123 .*"):
                    myfunc()

        Notes:

        * The ``match`` parameter is matched with the :func:`re.search`
        function, so in the above example ``match='123'`` would have worked as well.
        * The ``match`` parameter also matches against `PEP-678 <https://peps.python.org/pep-0678/>`__ ``__notes__``.


.. _`assert-matching-exception-groups`:

匹配异常组
~~~~~~~~~~~~~~~~~~~~~~~~~

**Matching exception groups**

.. tabs::

    .. tab:: 中文

        您还可以使用 :func:`excinfo.group_contains() <pytest.ExceptionInfo.group_contains>` 方法来测试作为 :class:`ExceptionGroup` 一部分返回的异常：

        .. code-block:: python

            def test_exception_in_group():
                with pytest.raises(ExceptionGroup) as excinfo:
                    raise ExceptionGroup(
                        "Group message",
                        [
                            RuntimeError("Exception 123 raised"),
                        ],
                    )
                assert excinfo.group_contains(RuntimeError, match=r".* 123 .*")
                assert not excinfo.group_contains(TypeError)

        可选的 ``match`` 关键字参数的工作方式与 :func:`pytest.raises` 相同。

        默认情况下， ``group_contains()`` 会递归地搜索任何层级的嵌套 ``ExceptionGroup`` 实例中的匹配异常。如果您只想在特定层级匹配异常，可以指定 ``depth`` 关键字参数；直接包含在顶级 ``ExceptionGroup`` 中的异常会匹配 ``depth=1``。

        .. code-block:: python

            def test_exception_in_group_at_given_depth():
                with pytest.raises(ExceptionGroup) as excinfo:
                    raise ExceptionGroup(
                        "Group message",
                        [
                            RuntimeError(),
                            ExceptionGroup(
                                "Nested group",
                                [
                                    TypeError(),
                                ],
                            ),
                        ],
                    )
                assert excinfo.group_contains(RuntimeError, depth=1)
                assert excinfo.group_contains(TypeError, depth=2)
                assert not excinfo.group_contains(RuntimeError, depth=2)
                assert not excinfo.group_contains(TypeError, depth=1)

    .. tab:: 英文

        You can also use the :func:`excinfo.group_contains() <pytest.ExceptionInfo.group_contains>`
        method to test for exceptions returned as part of an :class:`ExceptionGroup`:

        .. code-block:: python

            def test_exception_in_group():
                with pytest.raises(ExceptionGroup) as excinfo:
                    raise ExceptionGroup(
                        "Group message",
                        [
                            RuntimeError("Exception 123 raised"),
                        ],
                    )
                assert excinfo.group_contains(RuntimeError, match=r".* 123 .*")
                assert not excinfo.group_contains(TypeError)

        The optional ``match`` keyword parameter works the same way as for
        :func:`pytest.raises`.

        By default ``group_contains()`` will recursively search for a matching
        exception at any level of nested ``ExceptionGroup`` instances. You can
        specify a ``depth`` keyword parameter if you only want to match an
        exception at a specific level; exceptions contained directly in the top
        ``ExceptionGroup`` would match ``depth=1``.

        .. code-block:: python

            def test_exception_in_group_at_given_depth():
                with pytest.raises(ExceptionGroup) as excinfo:
                    raise ExceptionGroup(
                        "Group message",
                        [
                            RuntimeError(),
                            ExceptionGroup(
                                "Nested group",
                                [
                                    TypeError(),
                                ],
                            ),
                        ],
                    )
                assert excinfo.group_contains(RuntimeError, depth=1)
                assert excinfo.group_contains(TypeError, depth=2)
                assert not excinfo.group_contains(RuntimeError, depth=2)
                assert not excinfo.group_contains(TypeError, depth=1)

替代形式（遗留）
~~~~~~~~~~~~~~~~~~~~~~~

**Alternate form (legacy)**

.. tabs::

    .. tab:: 中文

        还有一种替代形式，您可以传递一个将要执行的函数，以及 ``*args`` 和 ``**kwargs``，然后 :func:`pytest.raises` 会用这些参数执行该函数，并断言给定的异常被引发：

        .. code-block:: python

            def func(x):
                if x <= 0:
                    raise ValueError("x needs to be larger than zero")


            pytest.raises(ValueError, func, x=-1)

        在发生失败时，报告器将提供有用的输出，例如 *没有异常* 或 *异常类型错误*。

        这种形式是最初的 :func:`pytest.raises` API，开发时是在 Python 语言中添加 ``with`` 语句之前。如今，这种形式很少使用，使用上下文管理器形式（通过 ``with``）被认为更具可读性。尽管如此，这种形式仍然完全支持，并且并未被弃用。

    .. tab:: 英文

        There is an alternate form where you pass
        a function that will be executed, along ``*args`` and ``**kwargs``, and :func:`pytest.raises`
        will execute the function with the arguments and assert that the given exception is raised:

        .. code-block:: python

            def func(x):
                if x <= 0:
                    raise ValueError("x needs to be larger than zero")


            pytest.raises(ValueError, func, x=-1)

        The reporter will provide you with helpful output in case of failures such as *no
        exception* or *wrong exception*.

        This form was the original :func:`pytest.raises` API, developed before the ``with`` statement was
        added to the Python language. Nowadays, this form is rarely used, with the context-manager form (using ``with``)
        being considered more readable.
        Nonetheless, this form is fully supported and not deprecated in any way.

xfail 标记和 pytest.raises
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**xfail mark and pytest.raises**

.. tabs::

    .. tab:: 中文

        您还可以为 :ref:`pytest.mark.xfail <pytest.mark.xfail ref>` 指定一个 ``raises`` 参数，这样可以检查测试失败的方式更为具体，而不仅仅是引发了任何异常：

        .. code-block:: python

            def f():
                raise IndexError()


            @pytest.mark.xfail(raises=IndexError)
            def test_f():
                f()


        只有在测试通过引发 ``IndexError`` 或其子类而失败时，这种方式才会被标记为 "xfail"。

        * 将 :ref:`pytest.mark.xfail <pytest.mark.xfail ref>` 与 ``raises`` 参数结合使用，可能更适合用于记录未修复的错误（即测试描述 "应该" 发生的情况）或依赖项中的错误。

        * 使用 :func:`pytest.raises` 更适合于测试您自己代码故意引发的异常，这也是大多数情况。

    .. tab:: 英文

        It is also possible to specify a ``raises`` argument to
        :ref:`pytest.mark.xfail <pytest.mark.xfail ref>`, which checks that the test is failing in a more
        specific way than just having any exception raised:

        .. code-block:: python

            def f():
                raise IndexError()


            @pytest.mark.xfail(raises=IndexError)
            def test_f():
                f()


        This will only "xfail" if the test fails by raising ``IndexError`` or subclasses.

        * Using :ref:`pytest.mark.xfail <pytest.mark.xfail ref>` with the ``raises`` parameter is probably better for something
        like documenting unfixed bugs (where the test describes what "should" happen) or bugs in dependencies.

        * Using :func:`pytest.raises` is likely to be better for cases where you are
        testing exceptions your own code is deliberately raising, which is the majority of cases.


.. _`assertwarns`:

关于预期警告的断言
-----------------------------------------

**Assertions about expected warnings**

.. tabs::

    .. tab:: 中文

        您可以使用 :ref:`pytest.warns <warns>` 检查代码是否引发特定警告。
    
    .. tab:: 英文

        You can check that code raises a particular warning using :ref:`pytest.warns <warns>`.


.. _newreport:

利用上下文相关的比较
-------------------------------------------------

**Making use of context-sensitive comparisons**

.. tabs::

    .. tab:: 中文

        ``pytest`` 在遇到比较时提供丰富的上下文敏感信息。例如：

        .. code-block:: python

            # content of test_assert2.py
            def test_set_comparison():
                set1 = set("1308")
                set2 = set("8035")
                assert set1 == set2

        如果您运行该模块：

        .. code-block:: pytest

            $ pytest test_assert2.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_assert2.py F                                                    [100%]

            ================================= FAILURES =================================
            ___________________________ test_set_comparison ____________________________

                def test_set_comparison():
                    set1 = set("1308")
                    set2 = set("8035")
            >       assert set1 == set2
            E       AssertionError: assert {'0', '1', '3', '8'} == {'0', '3', '5', '8'}
            E
            E         左侧集合中的额外项目:
            E         '1'
            E         右侧集合中的额外项目:
            E         '5'
            E         使用 -v 获取更多差异信息

            test_assert2.py:4: AssertionError
            ========================= short test summary info ==========================
            FAILED test_assert2.py::test_set_comparison - AssertionError: assert {'0'...
            ============================ 1 failed in 0.12s =============================

        对于许多情况，会进行特殊比较：

        * 比较长字符串时：显示上下文差异
        * 比较长序列时：显示第一个失败的索引
        * 比较字典时：显示不同的条目

        请参见 :ref:`reporting demo <tbreportdemo>` 获取更多示例。

    .. tab:: 英文

        ``pytest`` has rich support for providing context-sensitive information when it encounters comparisons.  For example:

        .. code-block:: python

            # content of test_assert2.py
            def test_set_comparison():
                set1 = set("1308")
                set2 = set("8035")
                assert set1 == set2

        if you run this module:

        .. code-block:: pytest

            $ pytest test_assert2.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_assert2.py F                                                    [100%]

            ================================= FAILURES =================================
            ___________________________ test_set_comparison ____________________________

                def test_set_comparison():
                    set1 = set("1308")
                    set2 = set("8035")
            >       assert set1 == set2
            E       AssertionError: assert {'0', '1', '3', '8'} == {'0', '3', '5', '8'}
            E
            E         Extra items in the left set:
            E         '1'
            E         Extra items in the right set:
            E         '5'
            E         Use -v to get more diff

            test_assert2.py:4: AssertionError
            ========================= short test summary info ==========================
            FAILED test_assert2.py::test_set_comparison - AssertionError: assert {'0'...
            ============================ 1 failed in 0.12s =============================

        Special comparisons are done for a number of cases:

        * comparing long strings: a context diff is shown
        * comparing long sequences: first failing indices
        * comparing dicts: different entries

        See the :ref:`reporting demo <tbreportdemo>` for many more examples.

对失败断言定义自己的解释
---------------------------------------------------

**Defining your own explanation for failed assertions**

.. tabs::

    .. tab:: 中文

        可以通过实现 ``pytest_assertrepr_compare`` 钩子来添加自定义的详细解释。

        .. autofunction:: _pytest.hookspec.pytest_assertrepr_compare
            :noindex:

        作为示例，请考虑在 :ref:`conftest.py <conftest.py>` 文件中添加以下钩子，以为 ``Foo`` 对象提供替代解释：

        .. code-block:: python

            # content of conftest.py
            from test_foocompare import Foo


            def pytest_assertrepr_compare(op, left, right):
                if isinstance(left, Foo) and isinstance(right, Foo) and op == "==":
                    return [
                        "比较 Foo 实例:",
                        f"   值: {left.val} != {right.val}",
                    ]

        现在，给定这个测试模块：

        .. code-block:: python

            # content of test_foocompare.py
            class Foo:
                def __init__(self, val):
                    self.val = val

                def __eq__(self, other):
                    return self.val == other.val


            def test_compare():
                f1 = Foo(1)
                f2 = Foo(2)
                assert f1 == f2

        您可以运行测试模块，并获得在 conftest 文件中定义的自定义输出：

        .. code-block:: pytest

            $ pytest -q test_foocompare.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            _______________________________ test_compare _______________________________

                def test_compare():
                    f1 = Foo(1)
                    f2 = Foo(2)
            >       assert f1 == f2
            E       assert Comparing Foo instances:
            E            值: 1 != 2

            test_foocompare.py:12: AssertionError
            ========================= short test summary info ==========================
            FAILED test_foocompare.py::test_compare - assert Comparing Foo instances:
            1 failed in 0.12s

    .. tab:: 英文

        It is possible to add your own detailed explanations by implementing the ``pytest_assertrepr_compare`` hook.

        .. autofunction:: _pytest.hookspec.pytest_assertrepr_compare
            :noindex:

        As an example consider adding the following hook in a :ref:`conftest.py <conftest.py>` file which provides an alternative explanation for ``Foo`` objects:

        .. code-block:: python

            # content of conftest.py
            from test_foocompare import Foo


            def pytest_assertrepr_compare(op, left, right):
                if isinstance(left, Foo) and isinstance(right, Foo) and op == "==":
                    return [
                        "Comparing Foo instances:",
                        f"   vals: {left.val} != {right.val}",
                    ]

        now, given this test module:

        .. code-block:: python

            # content of test_foocompare.py
            class Foo:
                def __init__(self, val):
                    self.val = val

                def __eq__(self, other):
                    return self.val == other.val


            def test_compare():
                f1 = Foo(1)
                f2 = Foo(2)
                assert f1 == f2

        you can run the test module and get the custom output defined in the conftest file:

        .. code-block:: pytest

            $ pytest -q test_foocompare.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            _______________________________ test_compare _______________________________

                def test_compare():
                    f1 = Foo(1)
                    f2 = Foo(2)
            >       assert f1 == f2
            E       assert Comparing Foo instances:
            E            vals: 1 != 2

            test_foocompare.py:12: AssertionError
            ========================= short test summary info ==========================
            FAILED test_foocompare.py::test_compare - assert Comparing Foo instances:
            1 failed in 0.12s

.. _assert-details:
.. _`assert introspection`:

断言自省细节
-------------------------------

**Assertion introspection details**

.. tabs::

    .. tab:: 中文

        报告失败断言的详细信息是通过在执行之前重写断言语句来实现的。重写的断言语句将 introspection 信息添加到断言失败消息中。``pytest`` 仅重写通过其测试收集过程直接发现的测试模块，因此 **不直接作为测试模块的支持模块中的断言不会被重写**。

        您可以通过在导入之前调用 :ref:`register_assert_rewrite <assertion-rewriting>` 来手动启用导入模块的断言重写（在您的根 ``conftest.py`` 中进行此操作是一个好地方）。

        有关更多信息，Benjamin Peterson 写了 `pytest 新断言重写的幕后花絮 <http://pybites.blogspot.com/2011/07/behind-scenes-of-pytests-new-assertion.html>`_ 。

    .. tab:: 英文

        Reporting details about a failing assertion is achieved by rewriting assert
        statements before they are run.  Rewritten assert statements put introspection
        information into the assertion failure message.  ``pytest`` only rewrites test
        modules directly discovered by its test collection process, so **asserts in
        supporting modules which are not themselves test modules will not be rewritten**.

        You can manually enable assertion rewriting for an imported module by calling
        :ref:`register_assert_rewrite <assertion-rewriting>`
        before you import it (a good place to do that is in your root ``conftest.py``).

        For further information, Benjamin Peterson wrote up `Behind the scenes of pytest's new assertion rewriting <http://pybites.blogspot.com/2011/07/behind-scenes-of-pytests-new-assertion.html>`_.

断言重写缓存磁盘上的文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Assertion rewriting caches files on disk**

.. tabs::

    .. tab:: 中文

        ``pytest`` 会将重写的模块写回磁盘以进行缓存。您可以通过在 ``conftest.py`` 文件的顶部添加以下内容来禁用此行为（例如，避免在频繁移动文件的项目中留下过时的 ``.pyc`` 文件）：

        .. code-block:: python

            import sys

            sys.dont_write_bytecode = True

        请注意，您仍然可以享受断言 introspection 的好处，唯一的变化是 ``.pyc`` 文件不会被缓存到磁盘上。

        此外，如果无法写入新的 ``.pyc`` 文件（例如在只读文件系统或 zip 文件中），重写将会静默跳过缓存。

    .. tab:: 英文

        ``pytest`` will write back the rewritten modules to disk for caching. You can disable
        this behavior (for example to avoid leaving stale ``.pyc`` files around in projects that
        move files around a lot) by adding this to the top of your ``conftest.py`` file:

        .. code-block:: python

            import sys

            sys.dont_write_bytecode = True

        Note that you still get the benefits of assertion introspection, the only change is that
        the ``.pyc`` files won't be cached on disk.

        Additionally, rewriting will silently skip caching if it cannot write new ``.pyc`` files,
        i.e. in a read-only filesystem or a zipfile.


禁用断言重写
~~~~~~~~~~~~~~~~~~~~~~~~~~

**Disabling assert rewriting**

.. tabs::

    .. tab:: 中文

        ``pytest`` 在导入时通过使用导入钩子重写测试模块以生成新的 ``pyc`` 文件。大多数情况下，这个过程是透明的。然而，如果您自己使用导入机制，导入钩子可能会干扰。

        如果出现这种情况，您有两个选择：

        * 通过在模块的文档字符串中添加字符串 ``PYTEST_DONT_REWRITE`` 来禁用特定模块的重写。

        * 通过使用 ``--assert=plain`` 来禁用所有模块的重写。

    .. tab:: 英文

        ``pytest`` rewrites test modules on import by using an import
        hook to write new ``pyc`` files. Most of the time this works transparently.
        However, if you are working with the import machinery yourself, the import hook may
        interfere.

        If this is the case you have two options:

        * Disable rewriting for a specific module by adding the string
        ``PYTEST_DONT_REWRITE`` to its docstring.

        * Disable rewriting for all modules by using ``--assert=plain``.
