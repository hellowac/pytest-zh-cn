
.. _`mark examples`:

使用自定义标记
=================================================

**Working with custom markers**

.. tabs::

    .. tab:: 中文

        以下是一些使用 :ref:`mark` 机制的示例。

    .. tab:: 英文

        Here are some examples using the :ref:`mark` mechanism.

.. _`mark run`:

标记测试函数并选择它们进行运行
----------------------------------------------------

**Marking test functions and selecting them for a run**

.. tabs::

    .. tab:: 中文

        您可以像这样为测试函数标记自定义元数据：

        .. code-block:: python

            # content of test_server.py

            import pytest


            @pytest.mark.webtest
            def test_send_http():
                pass  # 执行一些针对您的应用程序的 webtest 测试


            @pytest.mark.device(serial="123")
            def test_something_quick():
                pass


            @pytest.mark.device(serial="abc")
            def test_another():
                pass


            class TestClass:
                def test_method(self):
                    pass



        然后，您可以限制测试运行，仅运行带有 ``webtest`` 标记的测试：

        .. code-block:: pytest

            $ pytest -v -m webtest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 3 deselected / 1 selected

            test_server.py::test_send_http PASSED                                [100%]

            ===================== 1 passed, 3 deselected in 0.12s ======================

        或者反向运行，运行所有测试，除了 webtest 测试：

        .. code-block:: pytest

            $ pytest -v -m "not webtest"
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 1 deselected / 3 selected

            test_server.py::test_something_quick PASSED                          [ 33%]
            test_server.py::test_another PASSED                                  [ 66%]
            test_server.py::TestClass::test_method PASSED                        [100%]

            ===================== 3 passed, 1 deselected in 0.12s ======================

        .. _`marker_keyword_expression_example`:

        此外，您可以限制测试运行，仅运行匹配一个或多个标记关键字参数的测试，例如，仅运行标记为 ``device`` 且具体为 ``serial="123"`` 的测试：

        .. code-block:: pytest

            $ pytest -v -m "device(serial='123')"
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 3 deselected / 1 selected

            test_server.py::test_something_quick PASSED                          [100%]

            ===================== 1 passed, 3 deselected in 0.12s ======================

        .. note:: 仅支持关键字参数匹配的标记表达式。

        .. note:: 仅支持 :class:`int`、(未转义的) :class:`str`、:class:`bool` 和 :data:`None` 值在标记表达式中。

    .. tab:: 英文

        You can "mark" a test function with custom metadata like this:

        .. code-block:: python

            # content of test_server.py

            import pytest


            @pytest.mark.webtest
            def test_send_http():
                pass  # perform some webtest test for your app


            @pytest.mark.device(serial="123")
            def test_something_quick():
                pass


            @pytest.mark.device(serial="abc")
            def test_another():
                pass


            class TestClass:
                def test_method(self):
                    pass



        You can then restrict a test run to only run tests marked with ``webtest``:

        .. code-block:: pytest

            $ pytest -v -m webtest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 3 deselected / 1 selected

            test_server.py::test_send_http PASSED                                [100%]

            ===================== 1 passed, 3 deselected in 0.12s ======================

        Or the inverse, running all tests except the webtest ones:

        .. code-block:: pytest

            $ pytest -v -m "not webtest"
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 1 deselected / 3 selected

            test_server.py::test_something_quick PASSED                          [ 33%]
            test_server.py::test_another PASSED                                  [ 66%]
            test_server.py::TestClass::test_method PASSED                        [100%]

            ===================== 3 passed, 1 deselected in 0.12s ======================

        .. _`marker_keyword_expression_example`:

        Additionally, you can restrict a test run to only run tests matching one or multiple marker
        keyword arguments, e.g. to run only tests marked with ``device`` and the specific ``serial="123"``:

        .. code-block:: pytest

            $ pytest -v -m "device(serial='123')"
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 3 deselected / 1 selected

            test_server.py::test_something_quick PASSED                          [100%]

            ===================== 1 passed, 3 deselected in 0.12s ======================

        .. note:: Only keyword argument matching is supported in marker expressions.

        .. note:: Only :class:`int`, (unescaped) :class:`str`, :class:`bool` & :data:`None` values are supported in marker expressions.

根据节点 ID 选择测试
--------------------------------------

**Selecting tests based on their node ID**

.. tabs::

    .. tab:: 中文

        您可以提供一个或多个 :ref:`节点 ID <node-id>` 作为位置参数，仅选择指定的测试。这使得基于模块、类、方法或函数名称轻松选择测试：

        .. code-block:: pytest

            $ pytest -v test_server.py::TestClass::test_method
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 1 item

            test_server.py::TestClass::test_method PASSED                        [100%]

            ============================ 1 passed in 0.12s =============================

        您还可以根据类进行选择：

        .. code-block:: pytest

            $ pytest -v test_server.py::TestClass
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 1 item

            test_server.py::TestClass::test_method PASSED                        [100%]

            ============================ 1 passed in 0.12s =============================

        或者选择多个节点：

        .. code-block:: pytest

            $ pytest -v test_server.py::TestClass test_server.py::test_send_http
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 2 items

            test_server.py::TestClass::test_method PASSED                        [ 50%]
            test_server.py::test_send_http PASSED                                [100%]

            ============================ 2 passed in 0.12s =============================

        .. _node-id:

        .. note::

            节点 ID 的格式为 ``module.py::class::method`` 或
            ``module.py::function``。节点 ID 控制哪些测试被收集，因此 ``module.py::class`` 将选择该类上的所有测试方法。
            每个参数化 fixture 或测试的每个参数也会创建节点，因此选择参数化测试必须包括参数值，例如
            ``module.py::function[param]``。

            当使用 ``-rf`` 选项运行 pytest 时，失败测试的节点 ID 将显示在测试摘要信息中。您还可以从 ``pytest --collect-only`` 的输出构建节点 ID。

    .. tab:: 英文

        You can provide one or more :ref:`node IDs <node-id>` as positional
        arguments to select only specified tests. This makes it easy to select
        tests based on their module, class, method, or function name:

        .. code-block:: pytest

            $ pytest -v test_server.py::TestClass::test_method
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 1 item

            test_server.py::TestClass::test_method PASSED                        [100%]

            ============================ 1 passed in 0.12s =============================

        You can also select on the class:

        .. code-block:: pytest

            $ pytest -v test_server.py::TestClass
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 1 item

            test_server.py::TestClass::test_method PASSED                        [100%]

            ============================ 1 passed in 0.12s =============================

        Or select multiple nodes:

        .. code-block:: pytest

            $ pytest -v test_server.py::TestClass test_server.py::test_send_http
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 2 items

            test_server.py::TestClass::test_method PASSED                        [ 50%]
            test_server.py::test_send_http PASSED                                [100%]

            ============================ 2 passed in 0.12s =============================

        .. _node-id:

        .. note::

            Node IDs are of the form ``module.py::class::method`` or
            ``module.py::function``.  Node IDs control which tests are
            collected, so ``module.py::class`` will select all test methods
            on the class.  Nodes are also created for each parameter of a
            parametrized fixture or test, so selecting a parametrized test
            must include the parameter value, e.g.
            ``module.py::function[param]``.

            Node IDs for failing tests are displayed in the test summary info
            when running pytest with the ``-rf`` option.  You can also
            construct Node IDs from the output of ``pytest --collect-only``.

使用 ``-k expr`` 根据名称选择测试
-------------------------------------------------------

**Using ``-k expr`` to select tests based on their name**

.. tabs::

    .. tab:: 中文

        .. versionadded:: 2.0/2.3.4

        您可以使用 ``-k`` 命令行选项指定一个表达式，该表达式对测试名称执行子字符串匹配，而不是 ``-m`` 提供的确切匹配。这使得根据测试名称轻松选择测试：

        .. versionchanged:: 5.4

        表达式匹配现在是大小写不敏感的。

        .. code-block:: pytest

            $ pytest -v -k http  # 使用上述定义的示例模块运行
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 3 deselected / 1 selected

            test_server.py::test_send_http PASSED                                [100%]

            ===================== 1 passed, 3 deselected in 0.12s ======================

        您还可以运行所有测试，除了与关键字匹配的测试：

        .. code-block:: pytest

            $ pytest -k "not send_http" -v
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 1 deselected / 3 selected

            test_server.py::test_something_quick PASSED                          [ 33%]
            test_server.py::test_another PASSED                                  [ 66%]
            test_server.py::TestClass::test_method PASSED                        [100%]

            ===================== 3 passed, 1 deselected in 0.12s ======================

        或者选择 "http" 和 "quick" 测试：

        .. code-block:: pytest

            $ pytest -k "http or quick" -v
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 2 deselected / 2 selected

            test_server.py::test_send_http PASSED                                [ 50%]
            test_server.py::test_something_quick PASSED                          [100%]

            ===================== 2 passed, 2 deselected in 0.12s ======================

        您可以使用 ``and``、``or``、``not`` 和括号。

        除了测试名称，``-k`` 还会匹配测试父级的名称（通常是它所在文件和类的名称）、设置在测试函数上的属性、应用于它或其父级的标记，以及显式添加到它或其父级的任何 :attr:`extra keywords <_pytest.nodes.Node.extra_keyword_matches>`。

    .. tab:: 英文

        .. versionadded:: 2.0/2.3.4

        You can use the ``-k`` command line option to specify an expression
        which implements a substring match on the test names instead of the
        exact match on markers that ``-m`` provides.  This makes it easy to
        select tests based on their names:

        .. versionchanged:: 5.4

        The expression matching is now case-insensitive.

        .. code-block:: pytest

            $ pytest -v -k http  # running with the above defined example module
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 3 deselected / 1 selected

            test_server.py::test_send_http PASSED                                [100%]

            ===================== 1 passed, 3 deselected in 0.12s ======================

        And you can also run all tests except the ones that match the keyword:

        .. code-block:: pytest

            $ pytest -k "not send_http" -v
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 1 deselected / 3 selected

            test_server.py::test_something_quick PASSED                          [ 33%]
            test_server.py::test_another PASSED                                  [ 66%]
            test_server.py::TestClass::test_method PASSED                        [100%]

            ===================== 3 passed, 1 deselected in 0.12s ======================

        Or to select "http" and "quick" tests:

        .. code-block:: pytest

            $ pytest -k "http or quick" -v
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 4 items / 2 deselected / 2 selected

            test_server.py::test_send_http PASSED                                [ 50%]
            test_server.py::test_something_quick PASSED                          [100%]

            ===================== 2 passed, 2 deselected in 0.12s ======================

        You can use ``and``, ``or``, ``not`` and parentheses.


        In addition to the test's name, ``-k`` also matches the names of the test's parents (usually, the name of the file and class it's in),
        attributes set on the test function, markers applied to it or its parents and any :attr:`extra keywords <_pytest.nodes.Node.extra_keyword_matches>`
        explicitly added to it or its parents.


注册标记
-------------------------------------

**Registering markers**

.. ini-syntax for custom markers:

.. tabs::

    .. tab:: 中文

        注册测试套件的标记非常简单：

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            markers =
                webtest: 将测试标记为 webtest。
                slow: 将测试标记为慢速。

        可以通过在每一行中定义每个标记来注册多个自定义标记，如上例所示。

        您可以查看测试套件中有哪些标记 - 列表包括我们刚定义的 ``webtest`` 和 ``slow`` 标记：

        .. code-block:: pytest

            $ pytest --markers
            @pytest.mark.webtest: 将测试标记为 webtest。

            @pytest.mark.slow: 将测试标记为慢速。

            @pytest.mark.filterwarnings(warning): 为给定测试添加警告过滤器。请参见 https://docs.pytest.org/en/stable/how-to/capture-warnings.html#pytest-mark-filterwarnings

            @pytest.mark.skip(reason=None): 跳过给定的测试函数，带有可选原因。示例：skip(reason="no way of currently testing this") 将跳过该测试。

            @pytest.mark.skipif(condition, ..., *, reason=...): 如果任何条件评估为 True，则跳过给定的测试函数。示例：skipif(sys.platform == 'win32') 如果我们在 win32 平台上，则跳过该测试。请参见 https://docs.pytest.org/en/stable/reference/reference.html#pytest-mark-skipif

            @pytest.mark.xfail(condition, ..., *, reason=..., run=True, raises=None, strict=xfail_strict): 如果任何条件评估为 True，则将测试函数标记为预期失败。可选择指定原因以便更好地报告，并在不想执行测试函数时设置 run=False。如果仅期望特定异常，您可以将它们列在 raises 中，如果测试以其他方式失败，则将其报告为真实失败。请参见 https://docs.pytest.org/en/stable/reference/reference.html#pytest-mark-xfail

            @pytest.mark.parametrize(argnames, argvalues): 多次调用测试函数，并依次传递不同的参数。如果 argnames 仅指定一个名称，则 argvalues 通常需要是一个值的列表；如果 argnames 指定多个名称，则需要是一个值的元组列表。示例：@parametrize('arg1', [1,2]) 将导致对装饰的测试函数进行两次调用，一次 arg1=1，另一次 arg1=2。请参见 https://docs.pytest.org/en/stable/how-to/parametrize.html 获取更多信息和示例。

            @pytest.mark.usefixtures(fixturename1, fixturename2, ...): 将测试标记为需要所有指定的夹具。请参见 https://docs.pytest.org/en/stable/explanation/fixtures.html#usefixtures

            @pytest.mark.tryfirst: 将钩子实现函数标记为插件机制将尽早尝试调用它。已弃用，请改用 @pytest.hookimpl(tryfirst=True)。

            @pytest.mark.trylast: 将钩子实现函数标记为插件机制将尽晚尝试调用它。已弃用，请改用 @pytest.hookimpl(trylast=True)。

        有关如何从插件添加和使用标记的示例，请参见 :ref:`adding a custom marker from a plugin`。

        .. note::

            建议显式注册标记，以便：

            * 在您的测试套件中有一个定义标记的地方

            * 通过 ``pytest --markers`` 查询现有标记时输出良好

            * 如果您使用 ``--strict-markers`` 选项，则标记中的拼写错误会被视为错误。

    .. tab:: 英文

        Registering markers for your test suite is simple:

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            markers =
                webtest: mark a test as a webtest.
                slow: mark test as slow.

        Multiple custom markers can be registered, by defining each one in its own line, as shown in above example.

        You can ask which markers exist for your test suite - the list includes our just defined ``webtest`` and ``slow`` markers:

        .. code-block:: pytest

            $ pytest --markers
            @pytest.mark.webtest: mark a test as a webtest.

            @pytest.mark.slow: mark test as slow.

            @pytest.mark.filterwarnings(warning): add a warning filter to the given test. see https://docs.pytest.org/en/stable/how-to/capture-warnings.html#pytest-mark-filterwarnings

            @pytest.mark.skip(reason=None): skip the given test function with an optional reason. Example: skip(reason="no way of currently testing this") skips the test.

            @pytest.mark.skipif(condition, ..., *, reason=...): skip the given test function if any of the conditions evaluate to True. Example: skipif(sys.platform == 'win32') skips the test if we are on the win32 platform. See https://docs.pytest.org/en/stable/reference/reference.html#pytest-mark-skipif

            @pytest.mark.xfail(condition, ..., *, reason=..., run=True, raises=None, strict=xfail_strict): mark the test function as an expected failure if any of the conditions evaluate to True. Optionally specify a reason for better reporting and run=False if you don't even want to execute the test function. If only specific exception(s) are expected, you can list them in raises, and if the test fails in other ways, it will be reported as a true failure. See https://docs.pytest.org/en/stable/reference/reference.html#pytest-mark-xfail

            @pytest.mark.parametrize(argnames, argvalues): call a test function multiple times passing in different arguments in turn. argvalues generally needs to be a list of values if argnames specifies only one name or a list of tuples of values if argnames specifies multiple names. Example: @parametrize('arg1', [1,2]) would lead to two calls of the decorated test function, one with arg1=1 and another with arg1=2.see https://docs.pytest.org/en/stable/how-to/parametrize.html for more info and examples.

            @pytest.mark.usefixtures(fixturename1, fixturename2, ...): mark tests as needing all of the specified fixtures. see https://docs.pytest.org/en/stable/explanation/fixtures.html#usefixtures

            @pytest.mark.tryfirst: mark a hook implementation function such that the plugin machinery will try to call it first/as early as possible. DEPRECATED, use @pytest.hookimpl(tryfirst=True) instead.

            @pytest.mark.trylast: mark a hook implementation function such that the plugin machinery will try to call it last/as late as possible. DEPRECATED, use @pytest.hookimpl(trylast=True) instead.


        For an example on how to add and work with markers from a plugin, see
        :ref:`adding a custom marker from a plugin`.

        .. note::

            It is recommended to explicitly register markers so that:

            * There is one place in your test suite defining your markers

            * Asking for existing markers via ``pytest --markers`` gives good output

            * Typos in function markers are treated as an error if you use
            the ``--strict-markers`` option.

.. _`scoped-marking`:

标记整个类或模块
----------------------------------------------------

**Marking whole classes or modules**

.. tabs::

    .. tab:: 中文

        您可以使用 ``pytest.mark`` 装饰器与类结合，以将标记应用于其所有测试方法：

        .. code-block:: python

            # content of test_mark_classlevel.py
            import pytest


            @pytest.mark.webtest
            class TestClass:
                def test_startup(self):
                    pass

                def test_startup_and_more(self):
                    pass

        这相当于直接将装饰器应用于这两个测试函数。

        要在模块级别应用标记，请使用 :globalvar:`pytestmark` 全局变量：

        .. code-block:: python

            import pytest
            pytestmark = pytest.mark.webtest

        或者多个标记：

        .. code-block:: python

            pytestmark = [pytest.mark.webtest, pytest.mark.slowtest]


        由于历史原因，在引入类装饰器之前，可以像这样在测试类上设置 :globalvar:`pytestmark` 属性：

        .. code-block:: python

            import pytest


            class TestClass:
                pytestmark = pytest.mark.webtest

    .. tab:: 英文

        You may use ``pytest.mark`` decorators with classes to apply markers to all of
        its test methods:

        .. code-block:: python

            # content of test_mark_classlevel.py
            import pytest


            @pytest.mark.webtest
            class TestClass:
                def test_startup(self):
                    pass

                def test_startup_and_more(self):
                    pass

        This is equivalent to directly applying the decorator to the
        two test functions.

        To apply marks at the module level, use the :globalvar:`pytestmark` global variable::

            import pytest
            pytestmark = pytest.mark.webtest

        or multiple markers::

            pytestmark = [pytest.mark.webtest, pytest.mark.slowtest]


        Due to legacy reasons, before class decorators were introduced, it is possible to set the
        :globalvar:`pytestmark` attribute on a test class like this:

        .. code-block:: python

            import pytest


            class TestClass:
                pytestmark = pytest.mark.webtest

.. _`marking individual tests when using parametrize`:

使用参数化时标记单个测试
-----------------------------------------------

**Marking individual tests when using parametrize**

.. tabs::

    .. tab:: 中文

        当使用 `parametrize` 时，应用标记将使其作用于每个单独的测试。然而，也可以将标记应用于单个测试实例：

        .. code-block:: python

            import pytest


            @pytest.mark.foo
            @pytest.mark.parametrize(
                ("n", "expected"), [(1, 2), pytest.param(1, 3, marks=pytest.mark.bar), (2, 3)]
            )
            def test_increment(n, expected):
                assert n + 1 == expected

        在这个例子中，标记 "foo" 将应用于三个测试中的每一个，而 "bar" 标记仅应用于第二个测试。跳过（Skip）和预期失败（xfail）标记也可以以这种方式应用，参见 :ref:`skip/xfail with parametrize`。

    .. tab:: 英文

        When using parametrize, applying a mark will make it apply
        to each individual test. However it is also possible to
        apply a marker to an individual test instance:

        .. code-block:: python

            import pytest


            @pytest.mark.foo
            @pytest.mark.parametrize(
                ("n", "expected"), [(1, 2), pytest.param(1, 3, marks=pytest.mark.bar), (2, 3)]
            )
            def test_increment(n, expected):
                assert n + 1 == expected

        In this example the mark "foo" will apply to each of the three
        tests, whereas the "bar" mark is only applied to the second test.
        Skip and xfail marks can also be applied in this way, see :ref:`skip/xfail with parametrize`.

.. _`adding a custom marker from a plugin`:

自定义标记和命令行选项来控制测试运行
----------------------------------------------------------

**Custom marker and command line option to control test runs**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        插件可以提供自定义标记，并根据它实现特定的行为。这是一个自包含的示例，添加了一个命令行选项和一个参数化测试函数标记，以便根据命名环境运行指定的测试：

        .. code-block:: python

            # content of conftest.py

            import pytest


            def pytest_addoption(parser):
                parser.addoption(
                    "-E",
                    action="store",
                    metavar="NAME",
                    help="只运行与环境 NAME 匹配的测试。",
                )


            def pytest_configure(config):
                # 注册一个额外的标记
                config.addinivalue_line(
                    "markers", "env(name): 标记测试仅在命名环境中运行"
                )


            def pytest_runtest_setup(item):
                envnames = [mark.args[0] for mark in item.iter_markers(name="env")]
                if envnames:
                    if item.config.getoption("-E") not in envnames:
                        pytest.skip(f"测试需要在 {envnames!r} 中的环境")

        使用此本地插件的测试文件：

        .. code-block:: python

            # content of test_someenv.py

            import pytest


            @pytest.mark.env("stage1")
            def test_basic_db_operation():
                pass

        以及指定与测试需求不同环境的示例调用：

        .. code-block:: pytest

            $ pytest -E stage2
            =========================== 测试会话开始 ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_someenv.py s                                                    [100%]

            ============================ 1 个测试被跳过，耗时 0.12s ============================

        这是一个指定确切所需环境的示例：

        .. code-block:: pytest

            $ pytest -E stage1
            =========================== 测试会话开始 ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_someenv.py .                                                    [100%]

            ============================ 1 个测试通过，耗时 0.12s =============================

        ``--markers`` 选项始终会为你提供可用标记的列表：

        .. code-block:: pytest

            $ pytest --markers
            @pytest.mark.env(name): 标记测试仅在命名环境中运行

            @pytest.mark.filterwarnings(warning): 为给定测试添加警告过滤器。参见 https://docs.pytest.org/en/stable/how-to/capture-warnings.html#pytest-mark-filterwarnings

            @pytest.mark.skip(reason=None): 跳过给定的测试函数，附加可选理由。例如：skip(reason="目前无法测试此") 跳过该测试。

            @pytest.mark.skipif(condition, ..., *, reason=...): 如果任何条件评估为 True，则跳过给定的测试函数。例如：skipif(sys.platform == 'win32') 如果我们在 win32 平台上，则跳过测试。参见 https://docs.pytest.org/en/stable/reference/reference.html#pytest-mark-skipif

            @pytest.mark.xfail(condition, ..., *, reason=..., run=True, raises=None, strict=xfail_strict): 如果任何条件评估为 True，则标记测试函数为预期失败。可选地指定理由以便更好的报告，设置 run=False 如果你甚至不想执行测试函数。如果仅期望特定异常，可以在 raises 中列出它们，如果测试以其他方式失败，将被报告为真正的失败。参见 https://docs.pytest.org/en/stable/reference/reference.html#pytest-mark-xfail

            @pytest.mark.parametrize(argnames, argvalues): 多次调用测试函数，逐次传入不同参数。如果 argnames 仅指定一个名称，则 argvalues 通常需要是值的列表；如果 argnames 指定多个名称，则需要是值的元组列表。例如：@parametrize('arg1', [1,2]) 将导致装饰的测试函数被调用两次，一次 arg1=1，另一次 arg1=2。有关更多信息和示例，请参见 https://docs.pytest.org/en/stable/how-to/parametrize.html。

            @pytest.mark.usefixtures(fixturename1, fixturename2, ...): 将测试标记为需要所有指定的夹具。参见 https://docs.pytest.org/en/stable/explanation/fixtures.html#usefixtures

            @pytest.mark.tryfirst: 标记一个钩子实现函数，以便插件机制尽可能早地调用它。已弃用，请改用 @pytest.hookimpl(tryfirst=True)。

            @pytest.mark.trylast: 标记一个钩子实现函数，以便插件机制尽可能晚地调用它。已弃用，请改用 @pytest.hookimpl(trylast=True)。

    .. tab:: 英文

        .. regendoc:wipe

        Plugins can provide custom markers and implement specific behaviour
        based on it. This is a self-contained example which adds a command
        line option and a parametrized test function marker to run tests
        specified via named environments:

        .. code-block:: python

            # content of conftest.py

            import pytest


            def pytest_addoption(parser):
                parser.addoption(
                    "-E",
                    action="store",
                    metavar="NAME",
                    help="only run tests matching the environment NAME.",
                )


            def pytest_configure(config):
                # register an additional marker
                config.addinivalue_line(
                    "markers", "env(name): mark test to run only on named environment"
                )


            def pytest_runtest_setup(item):
                envnames = [mark.args[0] for mark in item.iter_markers(name="env")]
                if envnames:
                    if item.config.getoption("-E") not in envnames:
                        pytest.skip(f"test requires env in {envnames!r}")

        A test file using this local plugin:

        .. code-block:: python

            # content of test_someenv.py

            import pytest


            @pytest.mark.env("stage1")
            def test_basic_db_operation():
                pass

        and an example invocations specifying a different environment than what
        the test needs:

        .. code-block:: pytest

            $ pytest -E stage2
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_someenv.py s                                                    [100%]

            ============================ 1 skipped in 0.12s ============================

        and here is one that specifies exactly the environment needed:

        .. code-block:: pytest

            $ pytest -E stage1
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_someenv.py .                                                    [100%]

            ============================ 1 passed in 0.12s =============================

        The ``--markers`` option always gives you a list of available markers:

        .. code-block:: pytest

            $ pytest --markers
            @pytest.mark.env(name): mark test to run only on named environment

            @pytest.mark.filterwarnings(warning): add a warning filter to the given test. see https://docs.pytest.org/en/stable/how-to/capture-warnings.html#pytest-mark-filterwarnings

            @pytest.mark.skip(reason=None): skip the given test function with an optional reason. Example: skip(reason="no way of currently testing this") skips the test.

            @pytest.mark.skipif(condition, ..., *, reason=...): skip the given test function if any of the conditions evaluate to True. Example: skipif(sys.platform == 'win32') skips the test if we are on the win32 platform. See https://docs.pytest.org/en/stable/reference/reference.html#pytest-mark-skipif

            @pytest.mark.xfail(condition, ..., *, reason=..., run=True, raises=None, strict=xfail_strict): mark the test function as an expected failure if any of the conditions evaluate to True. Optionally specify a reason for better reporting and run=False if you don't even want to execute the test function. If only specific exception(s) are expected, you can list them in raises, and if the test fails in other ways, it will be reported as a true failure. See https://docs.pytest.org/en/stable/reference/reference.html#pytest-mark-xfail

            @pytest.mark.parametrize(argnames, argvalues): call a test function multiple times passing in different arguments in turn. argvalues generally needs to be a list of values if argnames specifies only one name or a list of tuples of values if argnames specifies multiple names. Example: @parametrize('arg1', [1,2]) would lead to two calls of the decorated test function, one with arg1=1 and another with arg1=2.see https://docs.pytest.org/en/stable/how-to/parametrize.html for more info and examples.

            @pytest.mark.usefixtures(fixturename1, fixturename2, ...): mark tests as needing all of the specified fixtures. see https://docs.pytest.org/en/stable/explanation/fixtures.html#usefixtures

            @pytest.mark.tryfirst: mark a hook implementation function such that the plugin machinery will try to call it first/as early as possible. DEPRECATED, use @pytest.hookimpl(tryfirst=True) instead.

            @pytest.mark.trylast: mark a hook implementation function such that the plugin machinery will try to call it last/as late as possible. DEPRECATED, use @pytest.hookimpl(trylast=True) instead.


.. _`passing callables to custom markers`:

将可调用函数传递给自定义标记
--------------------------------------------

**Passing a callable to custom markers**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        以下是将在下一个示例中使用的配置文件：

        .. code-block:: python

            # content of conftest.py
            import sys


            def pytest_runtest_setup(item):
                for marker in item.iter_markers(name="my_marker"):
                    print(marker)
                    sys.stdout.flush()

        自定义标记可以设置其参数，即 ``args`` 和 ``kwargs`` 属性，可以通过将其作为可调用对象调用或使用 ``pytest.mark.MARKER_NAME.with_args`` 来定义。这两种方法在大多数情况下实现相同的效果。

        然而，如果有一个可调用对象作为唯一的位置参数且没有关键字参数，使用 ``pytest.mark.MARKER_NAME(c)`` 将不会将 ``c`` 作为位置参数传递，而是用自定义标记装饰 ``c``（参见 :ref:`MarkDecorator <mark>`）。幸运的是，``pytest.mark.MARKER_NAME.with_args`` 可以解决这个问题：

        .. code-block:: python

            # content of test_custom_marker.py
            import pytest


            def hello_world(*args, **kwargs):
                return "Hello World"


            @pytest.mark.my_marker.with_args(hello_world)
            def test_with_args():
                pass

        输出如下：

        .. code-block:: pytest

            $ pytest -q -s
            Mark(name='my_marker', args=(<function hello_world at 0xdeadbeef0001>,), kwargs={})
            .
            1 passed in 0.12s

        我们可以看到，自定义标记的参数设置已扩展为函数 ``hello_world``。这是创建自定义标记作为可调用对象的关键区别，该过程在幕后调用 ``__call__``，而使用 ``with_args`` 则不会。

    .. tab:: 英文

        .. regendoc:wipe

        Below is the config file that will be used in the next examples:

        .. code-block:: python

            # content of conftest.py
            import sys


            def pytest_runtest_setup(item):
                for marker in item.iter_markers(name="my_marker"):
                    print(marker)
                    sys.stdout.flush()

        A custom marker can have its argument set, i.e. ``args`` and ``kwargs`` properties, defined by either invoking it as a callable or using ``pytest.mark.MARKER_NAME.with_args``. These two methods achieve the same effect most of the time.

        However, if there is a callable as the single positional argument with no keyword arguments, using the ``pytest.mark.MARKER_NAME(c)`` will not pass ``c`` as a positional argument but decorate ``c`` with the custom marker (see :ref:`MarkDecorator <mark>`). Fortunately, ``pytest.mark.MARKER_NAME.with_args`` comes to the rescue:

        .. code-block:: python

            # content of test_custom_marker.py
            import pytest


            def hello_world(*args, **kwargs):
                return "Hello World"


            @pytest.mark.my_marker.with_args(hello_world)
            def test_with_args():
                pass

        The output is as follows:

        .. code-block:: pytest

            $ pytest -q -s
            Mark(name='my_marker', args=(<function hello_world at 0xdeadbeef0001>,), kwargs={})
            .
            1 passed in 0.12s

        We can see that the custom marker has its argument set extended with the function ``hello_world``. This is the key difference between creating a custom marker as a callable, which invokes ``__call__`` behind the scenes, and using ``with_args``.


读取从多个位置设置的标记
----------------------------------------------------

**Reading markers which were set from multiple places**

.. tabs::

    .. tab:: 中文

        .. versionadded: 2.2.2

        .. regendoc:wipe

        如果你在测试套件中大量使用标记，你可能会遇到同一个测试函数上应用多个标记的情况。通过插件代码，你可以读取所有这样的设置。示例：

        .. code-block:: python

            # content of test_mark_three_times.py
            import pytest

            pytestmark = pytest.mark.glob("module", x=1)


            @pytest.mark.glob("class", x=2)
            class TestClass:
                @pytest.mark.glob("function", x=3)
                def test_something(self):
                    pass

        在这里，我们将标记 "glob" 应用三次于同一个测试函数。从 conftest 文件中，我们可以这样读取：

        .. code-block:: python

            # content of conftest.py
            import sys


            def pytest_runtest_setup(item):
                for mark in item.iter_markers(name="glob"):
                    print(f"glob args={mark.args} kwargs={mark.kwargs}")
                    sys.stdout.flush()

        让我们在不捕获输出的情况下运行这个，看看我们得到什么：

        .. code-block:: pytest

            $ pytest -q -s
            glob args=('function',) kwargs={'x': 3}
            glob args=('class',) kwargs={'x': 2}
            glob args=('module',) kwargs={'x': 1}
            .
            1 passed in 0.12s

    .. tab:: 英文

        .. versionadded: 2.2.2

        .. regendoc:wipe

        If you are heavily using markers in your test suite you may encounter the case where a marker is applied several times to a test function.  From plugin
        code you can read over all such settings.  Example:

        .. code-block:: python

            # content of test_mark_three_times.py
            import pytest

            pytestmark = pytest.mark.glob("module", x=1)


            @pytest.mark.glob("class", x=2)
            class TestClass:
                @pytest.mark.glob("function", x=3)
                def test_something(self):
                    pass

        Here we have the marker "glob" applied three times to the same
        test function.  From a conftest file we can read it like this:

        .. code-block:: python

            # content of conftest.py
            import sys


            def pytest_runtest_setup(item):
                for mark in item.iter_markers(name="glob"):
                    print(f"glob args={mark.args} kwargs={mark.kwargs}")
                    sys.stdout.flush()

        Let's run this without capturing output and see what we get:

        .. code-block:: pytest

            $ pytest -q -s
            glob args=('function',) kwargs={'x': 3}
            glob args=('class',) kwargs={'x': 2}
            glob args=('module',) kwargs={'x': 1}
            .
            1 passed in 0.12s

使用 pytest 标记特定于平台的测试
--------------------------------------------------------------

**Marking platform specific tests with pytest**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        考虑你有一个测试套件，用于标记特定平台的测试，例如 ``pytest.mark.darwin``、``pytest.mark.win32`` 等，并且还有一些在所有平台上运行且没有特定标记的测试。如果你现在想要仅运行特定平台的测试，可以使用以下插件：

        .. code-block:: python

            # content of conftest.py
            #
            import sys

            import pytest

            ALL = set("darwin linux win32".split())


            def pytest_runtest_setup(item):
                supported_platforms = ALL.intersection(mark.name for mark in item.iter_markers())
                plat = sys.platform
                if supported_platforms and plat not in supported_platforms:
                    pytest.skip(f"无法在平台 {plat} 上运行")

        这样，如果测试为其他平台指定，则会被跳过。让我们做一个小测试文件，展示这是什么样子：

        .. code-block:: python

            # content of test_plat.py

            import pytest


            @pytest.mark.darwin
            def test_if_apple_is_evil():
                pass


            @pytest.mark.linux
            def test_if_linux_works():
                pass


            @pytest.mark.win32
            def test_if_win32_crashes():
                pass


            def test_runs_everywhere():
                pass

        然后你会看到两个测试被跳过，两个测试按预期执行：

        .. code-block:: pytest

            $ pytest -rs # 此选项报告跳过的原因
            =========================== 测试会话开始 ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items

            test_plat.py s.s.                                                    [100%]

            ========================= 简短测试摘要信息 ==========================
            SKIPPED [2] conftest.py:13: 无法在平台 linux 上运行
            ======================= 2 个通过，2 个跳过，耗时 0.12s =======================

        请注意，如果你通过标记命令行选项指定平台，如下所示：

        .. code-block:: pytest

            $ pytest -m linux
            =========================== 测试会话开始 ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items / 3 被排除 / 1 被选择

            test_plat.py .                                                       [100%]

            ===================== 1 个通过，3 个被排除，耗时 0.12s ======================

        那么未标记的测试将不会运行。因此，这是一种将运行限制为特定测试的方法。

    .. tab:: 英文

        .. regendoc:wipe

        Consider you have a test suite which marks tests for particular platforms,
        namely ``pytest.mark.darwin``, ``pytest.mark.win32`` etc. and you
        also have tests that run on all platforms and have no specific
        marker.  If you now want to have a way to only run the tests
        for your particular platform, you could use the following plugin:

        .. code-block:: python

            # content of conftest.py
            #
            import sys

            import pytest

            ALL = set("darwin linux win32".split())


            def pytest_runtest_setup(item):
                supported_platforms = ALL.intersection(mark.name for mark in item.iter_markers())
                plat = sys.platform
                if supported_platforms and plat not in supported_platforms:
                    pytest.skip(f"cannot run on platform {plat}")

        then tests will be skipped if they were specified for a different platform.
        Let's do a little test file to show how this looks like:

        .. code-block:: python

            # content of test_plat.py

            import pytest


            @pytest.mark.darwin
            def test_if_apple_is_evil():
                pass


            @pytest.mark.linux
            def test_if_linux_works():
                pass


            @pytest.mark.win32
            def test_if_win32_crashes():
                pass


            def test_runs_everywhere():
                pass

        then you will see two tests skipped and two executed tests as expected:

        .. code-block:: pytest

            $ pytest -rs # this option reports skip reasons
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items

            test_plat.py s.s.                                                    [100%]

            ========================= short test summary info ==========================
            SKIPPED [2] conftest.py:13: cannot run on platform linux
            ======================= 2 passed, 2 skipped in 0.12s =======================

        Note that if you specify a platform via the marker-command line option like this:

        .. code-block:: pytest

            $ pytest -m linux
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items / 3 deselected / 1 selected

            test_plat.py .                                                       [100%]

            ===================== 1 passed, 3 deselected in 0.12s ======================

        then the unmarked-tests will not be run.  It is thus a way to restrict the run to the specific tests.

根据测试名称自动添加标记
--------------------------------------------------------

**Automatically adding markers based on test names**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        如果你有一个测试套件，其中测试函数名称指示某种类型的测试，你可以实现一个钩子，自动定义标记，以便你可以使用 ``-m`` 选项。让我们来看一下这个测试模块：

        .. code-block:: python

            # content of test_module.py


            def test_interface_simple():
                assert 0


            def test_interface_complex():
                assert 0


            def test_event_simple():
                assert 0


            def test_something_else():
                assert 0

        我们想要动态定义两个标记，可以在 ``conftest.py`` 插件中做到这一点：

        .. code-block:: python

            # content of conftest.py

            import pytest


            def pytest_collection_modifyitems(items):
                for item in items:
                    if "interface" in item.nodeid:
                        item.add_marker(pytest.mark.interface)
                    elif "event" in item.nodeid:
                        item.add_marker(pytest.mark.event)

        现在我们可以使用 ``-m option`` 选择一组测试：

        .. code-block:: pytest

            $ pytest -m interface --tb=short
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items / 2 deselected / 2 selected

            test_module.py FF                                                    [100%]

            ================================= FAILURES =================================
            __________________________ test_interface_simple ___________________________
            test_module.py:4: in test_interface_simple
                assert 0
            E   assert 0
            __________________________ test_interface_complex __________________________
            test_module.py:8: in test_interface_complex
                assert 0
            E   assert 0
            ========================= short test summary info ==========================
            FAILED test_module.py::test_interface_simple - assert 0
            FAILED test_module.py::test_interface_complex - assert 0
            ===================== 2 failed, 2 deselected in 0.12s ======================

        或者选择“event”和“interface”测试：

        .. code-block:: pytest

            $ pytest -m "interface or event" --tb=short
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items / 1 deselected / 3 selected

            test_module.py FFF                                                   [100%]

            ================================= FAILURES =================================
            __________________________ test_interface_simple ___________________________
            test_module.py:4: in test_interface_simple
                assert 0
            E   assert 0
            __________________________ test_interface_complex __________________________
            test_module.py:8: in test_interface_complex
                assert 0
            E   assert 0
            ____________________________ test_event_simple _____________________________
            test_module.py:12: in test_event_simple
                assert 0
            E   assert 0
            ========================= short test summary info ==========================
            FAILED test_module.py::test_interface_simple - assert 0
            FAILED test_module.py::test_interface_complex - assert 0
            FAILED test_module.py::test_event_simple - assert 0
            ===================== 3 failed, 1 deselected in 0.12s ======================

    .. tab:: 英文

        .. regendoc:wipe

        If you have a test suite where test function names indicate a certain
        type of test, you can implement a hook that automatically defines
        markers so that you can use the ``-m`` option with it. Let's look
        at this test module:

        .. code-block:: python

            # content of test_module.py


            def test_interface_simple():
                assert 0


            def test_interface_complex():
                assert 0


            def test_event_simple():
                assert 0


            def test_something_else():
                assert 0

        We want to dynamically define two markers and can do it in a
        ``conftest.py`` plugin:

        .. code-block:: python

            # content of conftest.py

            import pytest


            def pytest_collection_modifyitems(items):
                for item in items:
                    if "interface" in item.nodeid:
                        item.add_marker(pytest.mark.interface)
                    elif "event" in item.nodeid:
                        item.add_marker(pytest.mark.event)

        We can now use the ``-m option`` to select one set:

        .. code-block:: pytest

            $ pytest -m interface --tb=short
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items / 2 deselected / 2 selected

            test_module.py FF                                                    [100%]

            ================================= FAILURES =================================
            __________________________ test_interface_simple ___________________________
            test_module.py:4: in test_interface_simple
                assert 0
            E   assert 0
            __________________________ test_interface_complex __________________________
            test_module.py:8: in test_interface_complex
                assert 0
            E   assert 0
            ========================= short test summary info ==========================
            FAILED test_module.py::test_interface_simple - assert 0
            FAILED test_module.py::test_interface_complex - assert 0
            ===================== 2 failed, 2 deselected in 0.12s ======================

        or to select both "event" and "interface" tests:

        .. code-block:: pytest

            $ pytest -m "interface or event" --tb=short
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items / 1 deselected / 3 selected

            test_module.py FFF                                                   [100%]

            ================================= FAILURES =================================
            __________________________ test_interface_simple ___________________________
            test_module.py:4: in test_interface_simple
                assert 0
            E   assert 0
            __________________________ test_interface_complex __________________________
            test_module.py:8: in test_interface_complex
                assert 0
            E   assert 0
            ____________________________ test_event_simple _____________________________
            test_module.py:12: in test_event_simple
                assert 0
            E   assert 0
            ========================= short test summary info ==========================
            FAILED test_module.py::test_interface_simple - assert 0
            FAILED test_module.py::test_interface_complex - assert 0
            FAILED test_module.py::test_event_simple - assert 0
            ===================== 3 failed, 1 deselected in 0.12s ======================
