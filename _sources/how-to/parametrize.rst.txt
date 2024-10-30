
.. _`test generators`:
.. _`parametrizing-tests`:
.. _`parametrized test functions`:
.. _`parametrize`:

.. _`parametrize-basics`:

如何参数化fixtures和测试函数
==========================================================================

**How to parametrize fixtures and test functions**

.. tabs::

    .. tab:: 中文

        pytest 允许在多个级别进行测试参数化：

        - :py:func:`pytest.fixture` 允许对 :ref:`fixture 函数进行参数化 <fixture-parametrize>`。

        * `@pytest.mark.parametrize`_ 允许在测试函数或类中定义多个参数和 fixtures 的集合。

        * `pytest_generate_tests`_ 允许定义自定义参数化方案或扩展。

    .. tab:: 英文

        pytest enables test parametrization at several levels:

        - :py:func:`pytest.fixture` allows one to :ref:`parametrize fixture
        functions <fixture-parametrize>`.

        * `@pytest.mark.parametrize`_ allows one to define multiple sets of
        arguments and fixtures at the test function or class.

        * `pytest_generate_tests`_ allows one to define custom parametrization
        schemes or extensions.

.. _parametrizemark:
.. _`@pytest.mark.parametrize`:


``@pytest.mark.parametrize``: 参数化测试函数
---------------------------------------------------------------------

``@pytest.mark.parametrize``: **parametrizing test functions**

.. tabs::

    .. tab:: 中文

        .. regendoc: wipe

            几个改进。

        内置的 :ref:`pytest.mark.parametrize ref` 装饰器支持测试函数的参数化。  
        以下是一个典型的测试函数示例，用于检查某个输入是否产生预期的输出：

        .. code-block:: python

            # content of test_expectation.py
            import pytest


            @pytest.mark.parametrize("test_input,expected", [("3+5", 8), ("2+4", 6), ("6*9", 42)])
            def test_eval(test_input, expected):
                assert eval(test_input) == expected

        在这里，``@parametrize`` 装饰器定义了三个不同的 ``(test_input, expected)`` 元组，以便 ``test_eval`` 函数将使用它们依次运行三次：

        .. code-block:: pytest

            $ pytest
            =========================== 测试会话开始 ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 3 items

            test_expectation.py ..F                                              [100%]

            ================================= 失败 =================================
            ____________________________ test_eval[6*9-42] _____________________________

            test_input = '6*9', expected = 42

                @pytest.mark.parametrize("test_input,expected", [("3+5", 8), ("2+4", 6), ("6*9", 42)])
                def test_eval(test_input, expected):
            >       assert eval(test_input) == expected
            E       AssertionError: assert 54 == 42
            E        +  where 54 = eval('6*9')

            test_expectation.py:6: AssertionError
            ========================= 简短测试摘要信息 ==========================
            FAILED test_expectation.py::test_eval[6*9-42] - AssertionError: assert 54...
            ======================= 1 失败, 2 通过 in 0.12s ========================

        .. note::

            参数值会原样传递给测试（没有任何复制）。

            例如，如果您将列表或字典作为参数值传递，并且测试案例代码对其进行了修改，这些修改将在后续的测试案例调用中反映出来。

        .. note::

            默认情况下，pytest 会转义用于参数化的任何非 ASCII 字符，因为这样做有几个缺点。  
            如果您希望在参数化中使用 Unicode 字符串，并在终端中看到原始值（未转义），请在您的 ``pytest.ini`` 中使用此选项：

            .. code-block:: ini

                [pytest]
                disable_test_id_escaping_and_forfeit_all_rights_to_community_support = True

            但请注意，这可能会导致不必要的副作用，甚至在不同操作系统和当前安装的插件下出现错误，因此请自行承担风险。

        如本示例所示，只有一对输入/输出值未通过简单测试函数。  
        与测试函数参数一样，您可以在回溯中看到 ``input`` 和 ``output`` 值。

        请注意，您还可以在类或模块上使用参数化标记（见 :ref:`mark`），这将使用参数集调用多个函数，例如：

        .. code-block:: python

            import pytest


            @pytest.mark.parametrize("n,expected", [(1, 2), (3, 4)])
            class TestClass:
                def test_simple_case(self, n, expected):
                    assert n + 1 == expected

                def test_weird_simple_case(self, n, expected):
                    assert (n * 1) + 1 == expected

        要对模块中的所有测试进行参数化，您可以将 :globalvar:`pytestmark` 全局变量赋值：

        .. code-block:: python

            import pytest

            pytestmark = pytest.mark.parametrize("n,expected", [(1, 2), (3, 4)])


            class TestClass:
                def test_simple_case(self, n, expected):
                    assert n + 1 == expected

                def test_weird_simple_case(self, n, expected):
                    assert (n * 1) + 1 == expected

        在参数化中也可以标记单个测试实例，例如使用内置的 ``mark.xfail``：

        .. code-block:: python

            # content of test_expectation.py
            import pytest


            @pytest.mark.parametrize(
                "test_input,expected",
                [("3+5", 8), ("2+4", 6), pytest.param("6*9", 42, marks=pytest.mark.xfail)],
            )
            def test_eval(test_input, expected):
                assert eval(test_input) == expected

        让我们运行这个：

        .. code-block:: pytest

            $ pytest
            =========================== 测试会话开始 ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 3 items

            test_expectation.py ..x                                              [100%]

            ======================= 2 通过, 1 xfailed in 0.12s =======================

        之前导致失败的一个参数集现在显示为“xfailed”（预期失败）测试。

        如果提供给 ``parametrize`` 的值结果为空列表——例如，如果它们由某个函数动态生成——pytest 的行为由 :confval:`empty_parameter_set_mark` 选项定义。

        要获取多个参数化参数的所有组合，您可以堆叠 ``parametrize`` 装饰器：

        .. code-block:: python

            import pytest


            @pytest.mark.parametrize("x", [0, 1])
            @pytest.mark.parametrize("y", [2, 3])
            def test_foo(x, y):
                pass

        这将使用参数 ``x=0/y=2``、 ``x=1/y=2``、 ``x=0/y=3`` 和 ``x=1/y=3`` 依次运行测试，按装饰器的顺序消耗参数。

    .. tab:: 英文

        .. regendoc: wipe

            Several improvements.

        The builtin :ref:`pytest.mark.parametrize ref` decorator enables
        parametrization of arguments for a test function.  Here is a typical example
        of a test function that implements checking that a certain input leads
        to an expected output:

        .. code-block:: python

            # content of test_expectation.py
            import pytest


            @pytest.mark.parametrize("test_input,expected", [("3+5", 8), ("2+4", 6), ("6*9", 42)])
            def test_eval(test_input, expected):
                assert eval(test_input) == expected

        Here, the ``@parametrize`` decorator defines three different ``(test_input,expected)``
        tuples so that the ``test_eval`` function will run three times using
        them in turn:

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 3 items

            test_expectation.py ..F                                              [100%]

            ================================= FAILURES =================================
            ____________________________ test_eval[6*9-42] _____________________________

            test_input = '6*9', expected = 42

                @pytest.mark.parametrize("test_input,expected", [("3+5", 8), ("2+4", 6), ("6*9", 42)])
                def test_eval(test_input, expected):
            >       assert eval(test_input) == expected
            E       AssertionError: assert 54 == 42
            E        +  where 54 = eval('6*9')

            test_expectation.py:6: AssertionError
            ========================= short test summary info ==========================
            FAILED test_expectation.py::test_eval[6*9-42] - AssertionError: assert 54...
            ======================= 1 failed, 2 passed in 0.12s ========================

        .. note::

            Parameter values are passed as-is to tests (no copy whatsoever).

            For example, if you pass a list or a dict as a parameter value, and
            the test case code mutates it, the mutations will be reflected in subsequent
            test case calls.

        .. note::

            pytest by default escapes any non-ascii characters used in unicode strings
            for the parametrization because it has several downsides.
            If however you would like to use unicode strings in parametrization
            and see them in the terminal as is (non-escaped), use this option
            in your ``pytest.ini``:

            .. code-block:: ini

                [pytest]
                disable_test_id_escaping_and_forfeit_all_rights_to_community_support = True

            Keep in mind however that this might cause unwanted side effects and
            even bugs depending on the OS used and plugins currently installed,
            so use it at your own risk.


        As designed in this example, only one pair of input/output values fails
        the simple test function.  And as usual with test function arguments,
        you can see the ``input`` and ``output`` values in the traceback.

        Note that you could also use the parametrize marker on a class or a module
        (see :ref:`mark`) which would invoke several functions with the argument sets,
        for instance:


        .. code-block:: python

            import pytest


            @pytest.mark.parametrize("n,expected", [(1, 2), (3, 4)])
            class TestClass:
                def test_simple_case(self, n, expected):
                    assert n + 1 == expected

                def test_weird_simple_case(self, n, expected):
                    assert (n * 1) + 1 == expected


        To parametrize all tests in a module, you can assign to the :globalvar:`pytestmark` global variable:


        .. code-block:: python

            import pytest

            pytestmark = pytest.mark.parametrize("n,expected", [(1, 2), (3, 4)])


            class TestClass:
                def test_simple_case(self, n, expected):
                    assert n + 1 == expected

                def test_weird_simple_case(self, n, expected):
                    assert (n * 1) + 1 == expected


        It is also possible to mark individual test instances within parametrize,
        for example with the builtin ``mark.xfail``:

        .. code-block:: python

            # content of test_expectation.py
            import pytest


            @pytest.mark.parametrize(
                "test_input,expected",
                [("3+5", 8), ("2+4", 6), pytest.param("6*9", 42, marks=pytest.mark.xfail)],
            )
            def test_eval(test_input, expected):
                assert eval(test_input) == expected

        Let's run this:

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 3 items

            test_expectation.py ..x                                              [100%]

            ======================= 2 passed, 1 xfailed in 0.12s =======================

        The one parameter set which caused a failure previously now
        shows up as an "xfailed" (expected to fail) test.

        In case the values provided to ``parametrize`` result in an empty list - for
        example, if they're dynamically generated by some function - the behaviour of
        pytest is defined by the :confval:`empty_parameter_set_mark` option.

        To get all combinations of multiple parametrized arguments you can stack
        ``parametrize`` decorators:

        .. code-block:: python

            import pytest


            @pytest.mark.parametrize("x", [0, 1])
            @pytest.mark.parametrize("y", [2, 3])
            def test_foo(x, y):
                pass

        This will run the test with the arguments set to ``x=0/y=2``, ``x=1/y=2``,
        ``x=0/y=3``, and ``x=1/y=3`` exhausting parameters in the order of the decorators.

.. _`pytest_generate_tests`:

``pytest_generate_tests`` 基本示例
---------------------------------------------

**Basic** ``pytest_generate_tests`` **example**

.. tabs::

    .. tab:: 中文

        有时您可能希望实现自己的参数化方案，或为确定参数或夹具的范围引入一些动态性。  
        为此，您可以使用 ``pytest_generate_tests`` 钩子，它在收集测试函数时被调用。通过传入的 ``metafunc`` 对象，您可以检查请求的测试上下文，最重要的是，您可以调用 ``metafunc.parametrize()`` 以实现参数化。

        例如，假设我们想运行一个接受字符串输入的测试，这些输入通过一个新的 ``pytest`` 命令行选项设置。首先，让我们编写一个简单的测试，接受一个 ``stringinput`` 夹具函数参数：

        .. code-block:: python

            # content of test_strings.py


            def test_valid_string(stringinput):
                assert stringinput.isalpha()

        现在我们添加一个 ``conftest.py`` 文件，包含命令行选项的添加和测试函数的参数化：

        .. code-block:: python

            # content of conftest.py


            def pytest_addoption(parser):
                parser.addoption(
                    "--stringinput",
                    action="append",
                    default=[],
                    help="list of stringinputs to pass to test functions",
                )


            def pytest_generate_tests(metafunc):
                if "stringinput" in metafunc.fixturenames:
                    metafunc.parametrize("stringinput", metafunc.config.getoption("stringinput"))

        如果我们现在传递两个 stringinput 值，我们的测试将运行两次：

        .. code-block:: pytest

            $ pytest -q --stringinput="hello" --stringinput="world" test_strings.py
            ..                                                                   [100%]
            2 passed in 0.12s

        让我们还用一个会导致测试失败的 stringinput 运行：

        .. code-block:: pytest

            $ pytest -q --stringinput="!" test_strings.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            ___________________________ test_valid_string[!] ___________________________

            stringinput = '!'

                def test_valid_string(stringinput):
            >       assert stringinput.isalpha()
            E       AssertionError: assert False
            E        +  where False = <built-in method isalpha of str object at 0xdeadbeef0001>()
            E        +    where <built-in method isalpha of str object at 0xdeadbeef0001> = '!'.isalpha

            test_strings.py:4: AssertionError
            ========================= short test summary info ==========================
            FAILED test_strings.py::test_valid_string[!] - AssertionError: assert False
            1 failed in 0.12s

        如预期，我们的测试函数失败。

        如果您不指定 stringinput，它将被跳过，因为 ``metafunc.parametrize()`` 将以空参数列表调用：

        .. code-block:: pytest

            $ pytest -q -rs test_strings.py
            s                                                                    [100%]
            ========================= short test summary info ==========================
            SKIPPED [1] test_strings.py: got empty parameter set ['stringinput'], function test_valid_string at /home/sweet/project/test_strings.py:2
            1 skipped in 0.12s

        请注意，当使用不同的参数集多次调用 ``metafunc.parametrize`` 时，所有参数名称在这些集合中不能重复，否则会引发错误。

    .. tab:: 英文

        Sometimes you may want to implement your own parametrization scheme
        or implement some dynamism for determining the parameters or scope
        of a fixture.   For this, you can use the ``pytest_generate_tests`` hook
        which is called when collecting a test function.  Through the passed in
        ``metafunc`` object you can inspect the requesting test context and, most
        importantly, you can call ``metafunc.parametrize()`` to cause
        parametrization.

        For example, let's say we want to run a test taking string inputs which
        we want to set via a new ``pytest`` command line option.  Let's first write
        a simple test accepting a ``stringinput`` fixture function argument:

        .. code-block:: python

            # content of test_strings.py


            def test_valid_string(stringinput):
                assert stringinput.isalpha()

        Now we add a ``conftest.py`` file containing the addition of a
        command line option and the parametrization of our test function:

        .. code-block:: python

            # content of conftest.py


            def pytest_addoption(parser):
                parser.addoption(
                    "--stringinput",
                    action="append",
                    default=[],
                    help="list of stringinputs to pass to test functions",
                )


            def pytest_generate_tests(metafunc):
                if "stringinput" in metafunc.fixturenames:
                    metafunc.parametrize("stringinput", metafunc.config.getoption("stringinput"))

        If we now pass two stringinput values, our test will run twice:

        .. code-block:: pytest

            $ pytest -q --stringinput="hello" --stringinput="world" test_strings.py
            ..                                                                   [100%]
            2 passed in 0.12s

        Let's also run with a stringinput that will lead to a failing test:

        .. code-block:: pytest

            $ pytest -q --stringinput="!" test_strings.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            ___________________________ test_valid_string[!] ___________________________

            stringinput = '!'

                def test_valid_string(stringinput):
            >       assert stringinput.isalpha()
            E       AssertionError: assert False
            E        +  where False = <built-in method isalpha of str object at 0xdeadbeef0001>()
            E        +    where <built-in method isalpha of str object at 0xdeadbeef0001> = '!'.isalpha

            test_strings.py:4: AssertionError
            ========================= short test summary info ==========================
            FAILED test_strings.py::test_valid_string[!] - AssertionError: assert False
            1 failed in 0.12s

        As expected our test function fails.

        If you don't specify a stringinput it will be skipped because
        ``metafunc.parametrize()`` will be called with an empty parameter
        list:

        .. code-block:: pytest

            $ pytest -q -rs test_strings.py
            s                                                                    [100%]
            ========================= short test summary info ==========================
            SKIPPED [1] test_strings.py: got empty parameter set ['stringinput'], function test_valid_string at /home/sweet/project/test_strings.py:2
            1 skipped in 0.12s

        Note that when calling ``metafunc.parametrize`` multiple times with different parameter sets, all parameter names across
        those sets cannot be duplicated, otherwise an error will be raised.

更多示例
-------------

**More examples**

.. tabs::

    .. tab:: 中文

        要查看更多示例，您可能需要看看 :ref:“更多参数化示例 <paramexamples>”。

    .. tab:: 英文

        For further examples, you might want to look at :ref:`more
        parametrization examples <paramexamples>`.
