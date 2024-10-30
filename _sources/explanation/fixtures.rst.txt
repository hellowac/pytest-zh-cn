.. _about-fixtures:

关于 fixtures
===============

**About fixtures**

.. seealso:: :ref:`how-to-fixtures`
.. seealso:: :ref:`Fixtures reference <reference-fixtures>`

.. tabs::

    .. tab:: 中文

        pytest fixtures 装置设计得明确、模块化且可扩展。

    .. tab:: 英文

        pytest fixtures are designed to be explicit, modular and scalable.

什么是 Fixture
-----------------

**What fixtures are**

.. tabs::

    .. tab:: 中文
            
        在测试中， `fixture <https://en.wikipedia.org/wiki/Test_fixture#Software>`_ 提供了一个定义良好、可靠且一致的测试上下文。这可以包括环境（例如配置了已知参数的数据库）或内容（例如数据集）。

        Fixtures 定义了构成测试 *arrange* 阶段的步骤和数据（参见 :ref:`test-anatomy`）。在 pytest 中，它们是您定义的函数，用于实现此目的。它们还可以用于定义测试的 *act* 阶段；这是一种设计更复杂测试的强大技术。

        由 fixtures 设置的服务、状态或其他操作环境通过参数被测试函数访问。对于每个测试函数使用的 fixture，通常在测试函数的定义中会有一个参数（以 fixture 的名称命名）。

        我们可以通过使用装饰器 :py:func:`@pytest.fixture <pytest.fixture>` 来告诉 pytest 某个特定函数是一个 fixture。以下是 pytest 中 fixture 的简单示例：

        .. code-block:: python

            import pytest


            class Fruit:
                def __init__(self, name):
                    self.name = name

                def __eq__(self, other):
                    return self.name == other.name


            @pytest.fixture
            def my_fruit():
                return Fruit("apple")


            @pytest.fixture
            def fruit_basket(my_fruit):
                return [Fruit("banana"), my_fruit]


            def test_my_fruit_in_basket(my_fruit, fruit_basket):
                assert my_fruit in fruit_basket

        测试不必仅限于单个 fixture。它们可以依赖于您想要的任意多个 fixtures，且 fixtures 也可以使用其他 fixtures。这正是 pytest 的 fixture 系统真正闪耀的地方。

    .. tab:: 英文

        In testing, a `fixture <https://en.wikipedia.org/wiki/Test_fixture#Software>`_
        provides a defined, reliable and consistent context for the tests. This could
        include environment (for example a database configured with known parameters)
        or content (such as a dataset).

        Fixtures define the steps and data that constitute the *arrange* phase of a
        test (see :ref:`test-anatomy`). In pytest, they are functions you define that
        serve this purpose. They can also be used to define a test's *act* phase; this
        is a powerful technique for designing more complex tests.

        The services, state, or other operating environments set up by fixtures are
        accessed by test functions through arguments. For each fixture used by a test
        function there is typically a parameter (named after the fixture) in the test
        function's definition.

        We can tell pytest that a particular function is a fixture by decorating it with
        :py:func:`@pytest.fixture <pytest.fixture>`. Here's a simple example of
        what a fixture in pytest might look like:

        .. code-block:: python

            import pytest


            class Fruit:
                def __init__(self, name):
                    self.name = name

                def __eq__(self, other):
                    return self.name == other.name


            @pytest.fixture
            def my_fruit():
                return Fruit("apple")


            @pytest.fixture
            def fruit_basket(my_fruit):
                return [Fruit("banana"), my_fruit]


            def test_my_fruit_in_basket(my_fruit, fruit_basket):
                assert my_fruit in fruit_basket

        Tests don't have to be limited to a single fixture, either. They can depend on
        as many fixtures as you want, and fixtures can use other fixtures, as well. This
        is where pytest's fixture system really shines.


对 xUnit 样式的安装/拆卸函数的改进
-----------------------------------------------------------

**Improvements over xUnit-style setup/teardown functions**

.. tabs::

    .. tab:: 中文

        pytest fixtures 相较于经典的 xUnit 风格的设置/拆卸函数提供了显著的改进：

        * fixtures 具有明确的名称，并通过在测试函数、模块、类或整个项目中声明其使用来激活。

        * fixtures 以模块化方式实现，因为每个 fixture 名称触发一个 *fixture function*，该函数本身可以使用其他 fixtures。

        * fixture 管理可以从简单的单元测试扩展到复杂的功能测试，允许根据配置和组件选项参数化 fixtures 和测试，或在函数、类、模块或整个测试会话范围内重用 fixtures。

        * 拆卸逻辑可以轻松、安全地管理，无论使用多少 fixtures，无需手动小心处理错误或微观管理清理步骤的添加顺序。

        此外，pytest 继续支持 :ref:`xunitsetup`。您可以根据自己的喜好混合这两种风格，逐步从经典风格迁移到新风格。您还可以从现有的 :ref:`unittest.TestCase style <unittest.TestCase>` 开始。

    .. tab:: 英文

        pytest fixtures offer dramatic improvements over the classic xUnit
        style of setup/teardown functions:

        * fixtures have explicit names and are activated by declaring their use from test functions, modules, classes or whole projects.

        * fixtures are implemented in a modular manner, as each fixture name triggers a *fixture function* which can itself use other fixtures.

        * fixture management scales from simple unit to complex functional testing, allowing to parametrize fixtures and tests according to configuration and component options, or to reuse fixtures across function, class, module or whole test session scopes.

        * teardown logic can be easily, and safely managed, no matter how many fixtures   are used, without the need to carefully handle errors by hand or micromanage   the order that cleanup steps are added.

        In addition, pytest continues to support :ref:`xunitsetup`.  You can mix both styles, moving incrementally from classic to new style, as you prefer.  You can also start out from existing :ref:`unittest.TestCase style <unittest.TestCase>`.



Fixture 错误
--------------

**Fixture errors**

.. tabs::

    .. tab:: 中文

        pytest 尽力将给定测试的所有 fixtures 按线性顺序排列，以便它可以看到哪个 fixture 是第一个、第二个、第三个，依此类推。然而，如果早期的 fixture 发生问题并引发异常，pytest 将停止执行该测试的 fixtures，并将测试标记为有错误。

        当测试被标记为有错误时，并不意味着测试失败。这只是意味着由于它依赖的某个东西出现了问题，测试无法被执行。

        这也是为什么尽可能减少给定测试的不必要依赖是个好主意。这样，某个无关的事物出现问题不会导致我们对可能存在的问题的完整性产生误解。

        以下是一个快速示例来帮助解释：

        .. code-block:: python

            import pytest


            @pytest.fixture
            def order():
                return []


            @pytest.fixture
            def append_first(order):
                order.append(1)


            @pytest.fixture
            def append_second(order, append_first):
                order.extend([2])


            @pytest.fixture(autouse=True)
            def append_third(order, append_second):
                order += [3]


            def test_order(order):
                assert order == [1, 2, 3]


        如果由于某种原因，``order.append(1)`` 有一个 bug 并引发异常，我们将无法知道 ``order.extend([2])`` 或 ``order += [3]`` 是否也会有问题。在 ``append_first`` 抛出异常后，pytest 将不再运行 ``test_order`` 的任何其他 fixtures，甚至不会尝试运行 ``test_order`` 本身。唯一会运行的只是 ``order`` 和 ``append_first``。

    .. tab:: 英文

        pytest does its best to put all the fixtures for a given test in a linear order
        so that it can see which fixture happens first, second, third, and so on. If an
        earlier fixture has a problem, though, and raises an exception, pytest will stop
        executing fixtures for that test and mark the test as having an error.

        When a test is marked as having an error, it doesn't mean the test failed,
        though. It just means the test couldn't even be attempted because one of the
        things it depends on had a problem.

        This is one reason why it's a good idea to cut out as many unnecessary
        dependencies as possible for a given test. That way a problem in something
        unrelated isn't causing us to have an incomplete picture of what may or may not
        have issues.

        Here's a quick example to help explain:

        .. code-block:: python

            import pytest


            @pytest.fixture
            def order():
                return []


            @pytest.fixture
            def append_first(order):
                order.append(1)


            @pytest.fixture
            def append_second(order, append_first):
                order.extend([2])


            @pytest.fixture(autouse=True)
            def append_third(order, append_second):
                order += [3]


            def test_order(order):
                assert order == [1, 2, 3]


        If, for whatever reason, ``order.append(1)`` had a bug and it raises an exception,
        we wouldn't be able to know if ``order.extend([2])`` or ``order += [3]`` would
        also have problems. After ``append_first`` throws an exception, pytest won't run
        any more fixtures for ``test_order``, and it won't even try to run
        ``test_order`` itself. The only things that would've run would be ``order`` and
        ``append_first``.


共享测试数据
-----------------

**Sharing test data**

.. tabs::

    .. tab:: 中文

        如果您想将来自文件的测试数据提供给您的测试，一个好的方法是通过在 fixture 中加载这些数据，以供您的测试使用。这利用了 pytest 的自动缓存机制。

        另一种好的方法是在 ``tests`` 文件夹中添加数据文件。还有一些社区插件可帮助管理测试的这一方面，例如 :pypi:`pytest-datadir` 和 :pypi:`pytest-datafiles`。

    .. tab:: 英文

        If you want to make test data from files available to your tests, a good way
        to do this is by loading these data in a fixture for use by your tests.
        This makes use of the automatic caching mechanisms of pytest.

        Another good approach is by adding the data files in the ``tests`` folder.
        There are also community plugins available to help to manage this aspect of
        testing, e.g. :pypi:`pytest-datadir` and :pypi:`pytest-datafiles`.

.. _fixtures-signal-cleanup:

关于 Fixture 清理的说明
----------------------------

**A note about fixture cleanup**

.. tabs::

    .. tab:: 中文

        pytest 对 :data:`SIGTERM <signal.SIGTERM>` 和 ``SIGQUIT`` 信号没有进行任何特殊处理（:data:`SIGINT <signal.SIGINT>` 由 Python 运行时通过 :class:`KeyboardInterrupt` 自然处理），因此管理外部资源的 fixtures 在 Python 进程被这些信号终止时，可能会导致资源泄漏。

        pytest 不处理这些信号以执行 fixture 清理的原因是，信号处理程序是全局的，改变它们可能会干扰正在执行的代码。

        如果您测试套件中的 fixtures 在这些情况下需要特别处理终止，请参阅问题跟踪器中的 :issue:`this comment <5243#issuecomment-491522595>` 以获取可能的解决方法。

    .. tab:: 英文

        pytest does not do any special processing for :data:`SIGTERM <signal.SIGTERM>` and
        ``SIGQUIT`` signals (:data:`SIGINT <signal.SIGINT>` is handled naturally
        by the Python runtime via :class:`KeyboardInterrupt`), so fixtures that manage external resources which are important
        to be cleared when the Python process is terminated (by those signals) might leak resources.

        The reason pytest does not handle those signals to perform fixture cleanup is that signal handlers are global,
        and changing them might interfere with the code under execution.

        If fixtures in your suite need special care regarding termination in those scenarios,
        see :issue:`this comment <5243#issuecomment-491522595>` in the issue
        tracker for a possible workaround.
