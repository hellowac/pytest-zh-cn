.. _reference-fixtures:
.. _fixture:
.. _fixtures:
.. _`@pytest.fixture`:
.. _`pytest.fixture`:


Fixtures 参考
========================================================

**Fixtures reference**

.. seealso:: :ref:`about-fixtures`
.. seealso:: :ref:`how-to-fixtures`

.. _`Dependency injection`: https://en.wikipedia.org/wiki/Dependency_injection


内置 fixtures
-----------------

**Built-in fixtures**

.. tabs::

    .. tab:: 中文

        :ref:`Fixtures <fixtures-api>` 使用 :ref:`@pytest.fixture <pytest.fixture-api>` 装饰器定义。Pytest 有几个有用的内置 fixtures ：

        :fixture:`capfd`
                捕获文件描述符 ``1`` 和 ``2`` 的文本输出。

        :fixture:`capfdbinary`
                捕获文件描述符 ``1`` 和 ``2`` 的字节输出。

        :fixture:`caplog`
                控制日志记录并访问日志条目。

        :fixture:`capsys`
                捕获 ``sys.stdout`` 和 ``sys.stderr`` 的文本输出。

        :fixture:`capsysbinary`
                捕获 ``sys.stdout`` 和 ``sys.stderr`` 的字节输出。

        :fixture:`cache`
                在 pytest 运行之间存储和检索值。

        :fixture:`doctest_namespace`
                提供注入到 doctests 命名空间的字典。

        :fixture:`monkeypatch`
            临时修改类、函数、字典、
            ``os.environ`` 和其他对象。

        :fixture:`pytestconfig`
                访问配置值、插件管理器和插件钩子。

        :fixture:`record_property`
            向测试添加额外属性。

        :fixture:`record_testsuite_property`
            向测试套件添加额外属性。

        :fixture:`recwarn`
                记录测试函数发出的警告。

        :fixture:`request`
            提供正在执行的测试函数的信息。

        :fixture:`testdir`
                提供一个临时测试目录，以帮助运行和
                测试 pytest 插件。

        :fixture:`tmp_path`
            提供一个 :class:`pathlib.Path` 对象，指向一个临时目录
            该目录对每个测试函数都是唯一的。

        :fixture:`tmp_path_factory`
                创建会话范围的临时目录并返回
                :class:`pathlib.Path` 对象。

        :fixture:`tmpdir`
                提供一个 `py.path.local <https://py.readthedocs.io/en/latest/path.html>`_ 对象，指向一个临时
                目录，该目录对每个测试函数都是唯一的；
                被 :fixture:`tmp_path` 替代。

        :fixture:`tmpdir_factory`
                创建会话范围的临时目录并返回
                ``py.path.local`` 对象；
                被 :fixture:`tmp_path_factory` 替代。

    .. tab:: 英文

        :ref:`Fixtures <fixtures-api>` are defined using the :ref:`@pytest.fixture
        <pytest.fixture-api>` decorator. Pytest has several useful built-in fixtures:

            :fixture:`capfd`
                    Capture, as text, output to file descriptors ``1`` and ``2``.

            :fixture:`capfdbinary`
                    Capture, as bytes, output to file descriptors ``1`` and ``2``.

            :fixture:`caplog`
                    Control logging and access log entries.

            :fixture:`capsys`
                    Capture, as text, output to ``sys.stdout`` and ``sys.stderr``.

            :fixture:`capsysbinary`
                    Capture, as bytes, output to ``sys.stdout`` and ``sys.stderr``.

            :fixture:`cache`
                    Store and retrieve values across pytest runs.

            :fixture:`doctest_namespace`
                    Provide a dict injected into the doctests namespace.

            :fixture:`monkeypatch`
                Temporarily modify classes, functions, dictionaries,
                ``os.environ``, and other objects.

            :fixture:`pytestconfig`
                    Access to configuration values, pluginmanager and plugin hooks.

            :fixture:`record_property`
                Add extra properties to the test.

            :fixture:`record_testsuite_property`
                Add extra properties to the test suite.

            :fixture:`recwarn`
                    Record warnings emitted by test functions.

            :fixture:`request`
                Provide information on the executing test function.

            :fixture:`testdir`
                    Provide a temporary test directory to aid in running, and
                    testing, pytest plugins.

            :fixture:`tmp_path`
                Provide a :class:`pathlib.Path` object to a temporary directory
                which is unique to each test function.

            :fixture:`tmp_path_factory`
                    Make session-scoped temporary directories and return
                    :class:`pathlib.Path` objects.

            :fixture:`tmpdir`
                    Provide a `py.path.local <https://py.readthedocs.io/en/latest/path.html>`_ object to a temporary
                    directory which is unique to each test function;
                    replaced by :fixture:`tmp_path`.

            :fixture:`tmpdir_factory`
                    Make session-scoped temporary directories and return
                    ``py.path.local`` objects;
                    replaced by :fixture:`tmp_path_factory`.


.. _`conftest.py`:
.. _`conftest`:

Fixture 可用性
---------------------

**Fixture availability**

.. tabs::

    .. tab:: 中文

         fixtures 的可用性是从测试的角度来决定的。只有在定义该 fixtures 的作用域内，测试才能请求一个 fixtures 。如果一个 fixtures 是在一个类内部定义的，它只能被该类中的测试请求。但如果一个 fixtures 是在模块的全局作用域内定义的，那么该模块中的每个测试，即使它是在一个类内部定义的，也可以请求这个 fixtures 。

        类似地，只有当测试在同一个作用域内时，它才能受到autouse fixtures 的影响（见 :ref:`autouse order` ）。

        一个 fixtures 也可以请求任何其他 fixtures ，无论它在哪里定义，只要请求它们的测试可以看到所有涉及的 fixtures 。

        例如，这里有一个测试文件，其中一个 fixtures （ ``outer`` ）请求了一个在不同作用域内定义的 fixtures （ ``inner`` ）：

        .. literalinclude:: /example/fixtures/test_fixtures_request_different_scope.py

        从测试的角度来看，它们没有问题看到每个它们依赖的 fixtures ：

            .. image:: /example/fixtures/test_fixtures_request_different_scope.*
                :align: center

        因此，当它们运行时，``outer`` 将毫无问题地找到 ``inner``，因为 pytest 是从测试的角度进行搜索的。

        .. note::
             fixtures 的定义作用域对其实例化顺序没有影响：顺序由 :ref:`here <fixture order>` 中描述的逻辑决定。

    .. tab:: 英文

        Fixture availability is determined from the perspective of the test. A fixture
        is only available for tests to request if they are in the scope that fixture is
        defined in. If a fixture is defined inside a class, it can only be requested by
        tests inside that class. But if a fixture is defined inside the global scope of
        the module, then every test in that module, even if it's defined inside a class,
        can request it.

        Similarly, a test can also only be affected by an autouse fixture if that test
        is in the same scope that autouse fixture is defined in (see
        :ref:`autouse order`).

        A fixture can also request any other fixture, no matter where it's defined, so
        long as the test requesting them can see all fixtures involved.

        For example, here's a test file with a fixture (``outer``) that requests a
        fixture (``inner``) from a scope it wasn't defined in:

        .. literalinclude:: /example/fixtures/test_fixtures_request_different_scope.py

        From the tests' perspectives, they have no problem seeing each of the fixtures
        they're dependent on:

            .. image:: /example/fixtures/test_fixtures_request_different_scope.*
                :align: center

        So when they run, ``outer`` will have no problem finding ``inner``, because
        pytest searched from the tests' perspectives.

        .. note::
            The scope a fixture is defined in has no bearing on the order it will be
            instantiated in: the order is mandated by the logic described
            :ref:`here <fixture order>`.

``conftest.py``: 跨多个文件分享 fixtures
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``conftest.py``: **sharing fixtures across multiple files**

.. tabs::

    .. tab:: 中文

        ``conftest.py`` 文件用于为整个目录提供 fixtures 。在 ``conftest.py`` 中定义的 fixtures 可以被该包中的任何测试使用，而无需显式导入（pytest 会自动发现它们）。

        你可以拥有多个嵌套的目录/包来包含测试，每个目录可以有自己的 ``conftest.py`` 及其 fixtures ，这些 fixtures 将附加到父目录中的 ``conftest.py`` 文件提供的 fixtures 上。

        例如，给定以下测试文件结构：

        ::

            tests/
                __init__.py

                conftest.py
                    # tests/conftest.py 的内容
                    import pytest

                    @pytest.fixture
                    def order():
                        return []

                    @pytest.fixture
                    def top(order, innermost):
                        order.append("top")

                test_top.py
                    # tests/test_top.py 的内容
                    import pytest

                    @pytest.fixture
                    def innermost(order):
                        order.append("innermost top")

                    def test_order(order, top):
                        assert order == ["innermost top", "top"]

                subpackage/
                    __init__.py

                    conftest.py
                        # tests/subpackage/conftest.py 的内容
                        import pytest

                        @pytest.fixture
                        def mid(order):
                            order.append("mid subpackage")

                    test_subpackage.py
                        # tests/subpackage/test_subpackage.py 的内容
                        import pytest

                        @pytest.fixture
                        def innermost(order, mid):
                            order.append("innermost subpackage")

                        def test_order(order, top):
                            assert order == ["mid subpackage", "innermost subpackage", "top"]

        作用域的边界可以如下可视化：

        .. image:: /example/fixtures/fixture_availability.*
            :align: center

        目录成为它们自己的一种作用域，其中在该目录的 ``conftest.py`` 文件中定义的 fixtures 对整个作用域可用。

        测试可以向上搜索（跨出一个圆圈）以找到 fixtures ，但不能向下搜索（进入一个圆圈）以继续查找。因此，``tests/subpackage/test_subpackage.py::test_order`` 能够找到在 ``tests/subpackage/test_subpackage.py`` 中定义的 ``innermost``  fixtures ，但在 ``tests/test_top.py`` 中定义的 fixtures 对它不可用，因为它必须下探一个层级（进入一个圆圈）才能找到它。

        测试找到的第一个 fixtures 将被使用，因此 :ref:` fixtures 可以被重写 <override fixtures>` 如果你需要更改或扩展特定作用域的 fixtures 功能。

        你还可以使用 ``conftest.py`` 文件实现 :ref:`每目录本地插件 <conftest.py plugins>`。

    .. tab:: 英文

        The ``conftest.py`` file serves as a means of providing fixtures for an entire
        directory. Fixtures defined in a ``conftest.py`` can be used by any test
        in that package without needing to import them (pytest will automatically
        discover them).

        You can have multiple nested directories/packages containing your tests, and
        each directory can have its own ``conftest.py`` with its own fixtures, adding on
        to the ones provided by the ``conftest.py`` files in parent directories.

        For example, given a test file structure like this:

        ::

            tests/
                __init__.py

                conftest.py
                    # content of tests/conftest.py
                    import pytest

                    @pytest.fixture
                    def order():
                        return []

                    @pytest.fixture
                    def top(order, innermost):
                        order.append("top")

                test_top.py
                    # content of tests/test_top.py
                    import pytest

                    @pytest.fixture
                    def innermost(order):
                        order.append("innermost top")

                    def test_order(order, top):
                        assert order == ["innermost top", "top"]

                subpackage/
                    __init__.py

                    conftest.py
                        # content of tests/subpackage/conftest.py
                        import pytest

                        @pytest.fixture
                        def mid(order):
                            order.append("mid subpackage")

                    test_subpackage.py
                        # content of tests/subpackage/test_subpackage.py
                        import pytest

                        @pytest.fixture
                        def innermost(order, mid):
                            order.append("innermost subpackage")

                        def test_order(order, top):
                            assert order == ["mid subpackage", "innermost subpackage", "top"]

        The boundaries of the scopes can be visualized like this:

        .. image:: /example/fixtures/fixture_availability.*
            :align: center

        The directories become their own sort of scope where fixtures that are defined
        in a ``conftest.py`` file in that directory become available for that whole
        scope.

        Tests are allowed to search upward (stepping outside a circle) for fixtures, but
        can never go down (stepping inside a circle) to continue their search. So
        ``tests/subpackage/test_subpackage.py::test_order`` would be able to find the
        ``innermost`` fixture defined in ``tests/subpackage/test_subpackage.py``, but
        the one defined in ``tests/test_top.py`` would be unavailable to it because it
        would have to step down a level (step inside a circle) to find it.

        The first fixture the test finds is the one that will be used, so
        :ref:`fixtures can be overridden <override fixtures>` if you need to change or
        extend what one does for a particular scope.

        You can also use the ``conftest.py`` file to implement
        :ref:`local per-directory plugins <conftest.py plugins>`.

来自第三方插件的 fixtures
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Fixtures from third-party plugins**

.. tabs::

    .. tab:: 中文

         fixtures 不一定需要在这个结构中定义才能对测试可用。它们也可以由安装的第三方插件提供，这就是许多 pytest 插件的工作方式。只要这些插件已安装，它们提供的 fixtures 就可以在测试套件的任何地方请求。

        由于它们是从测试套件结构之外提供的，第三方插件并不像 ``conftest.py`` 文件和测试套件中的目录那样提供作用域。因此，pytest 会按照之前解释的方式在作用域之间搜索 fixtures ，只有在最后才会找到在插件中定义的 fixtures 。

        例如，给定以下文件结构：

        ::

            tests/
                __init__.py

                conftest.py
                    # tests/conftest.py 的内容
                    import pytest

                    @pytest.fixture
                    def order():
                        return []

                subpackage/
                    __init__.py

                    conftest.py
                        # tests/subpackage/conftest.py 的内容
                        import pytest

                        @pytest.fixture(autouse=True)
                        def mid(order, b_fix):
                            order.append("mid subpackage")

                    test_subpackage.py
                        # tests/subpackage/test_subpackage.py 的内容
                        import pytest

                        @pytest.fixture
                        def inner(order, mid, a_fix):
                            order.append("inner subpackage")

                        def test_order(order, inner):
                            assert order == ["b_fix", "mid subpackage", "a_fix", "inner subpackage"]

        如果 ``plugin_a`` 已安装并提供 fixtures  ``a_fix``，而 ``plugin_b`` 已安装并提供 fixtures  ``b_fix``，那么测试查找 fixtures 的过程如下：

        .. image:: /example/fixtures/fixture_availability_plugins.svg
            :align: center

        pytest 只会在插件中搜索 ``a_fix`` 和 ``b_fix``，在此之前会先在 ``tests/`` 内的作用域中查找。

        .. note:

            pytest 可以告诉你给定测试可用的 fixtures ，如果你调用 ``pytest`` 以及测试的名称（或其所在的作用域），并提供 ``--fixtures`` 标志，例如 ``pytest --fixtures test_something.py`` 
            （以 ``_`` 开头的 fixtures 名称只有在你同时提供 ``-v`` 标志时才会显示）。

    .. tab:: 英文

        Fixtures don't have to be defined in this structure to be available for tests,
        though. They can also be provided by third-party plugins that are installed, and
        this is how many pytest plugins operate. As long as those plugins are installed,
        the fixtures they provide can be requested from anywhere in your test suite.

        Because they're provided from outside the structure of your test suite,
        third-party plugins don't really provide a scope like `conftest.py` files and
        the directories in your test suite do. As a result, pytest will search for
        fixtures stepping out through scopes as explained previously, only reaching
        fixtures defined in plugins *last*.

        For example, given the following file structure:

        ::

            tests/
                __init__.py

                conftest.py
                    # content of tests/conftest.py
                    import pytest

                    @pytest.fixture
                    def order():
                        return []

                subpackage/
                    __init__.py

                    conftest.py
                        # content of tests/subpackage/conftest.py
                        import pytest

                        @pytest.fixture(autouse=True)
                        def mid(order, b_fix):
                            order.append("mid subpackage")

                    test_subpackage.py
                        # content of tests/subpackage/test_subpackage.py
                        import pytest

                        @pytest.fixture
                        def inner(order, mid, a_fix):
                            order.append("inner subpackage")

                        def test_order(order, inner):
                            assert order == ["b_fix", "mid subpackage", "a_fix", "inner subpackage"]

        If ``plugin_a`` is installed and provides the fixture ``a_fix``, and
        ``plugin_b`` is installed and provides the fixture ``b_fix``, then this is what
        the test's search for fixtures would look like:

        .. image:: /example/fixtures/fixture_availability_plugins.svg
            :align: center

        pytest will only search for ``a_fix`` and ``b_fix`` in the plugins after
        searching for them first in the scopes inside ``tests/``.

        .. note:

            pytest can tell you what fixtures are available for a given test if you call
            ``pytests`` along with the test's name (or the scope it's in), and provide
            the ``--fixtures`` flag, e.g. ``pytest --fixtures test_something.py``
            (fixtures with names that start with ``_`` will only be shown if you also
            provide the ``-v`` flag).


.. _`fixture order`:

Fixture 实例顺序
---------------------------

**Fixture instantiation order**

.. tabs::

    .. tab:: 中文

        当 pytest 想要执行一个测试时，一旦它知道将要执行哪些 fixtures ，就必须确定它们的执行顺序。为此，它考虑了三个因素：

        1. 作用域
        2. 依赖关系
        3. 自动使用（autouse）

         fixtures 或测试的名称、它们的定义位置、定义顺序以及请求 fixtures 的顺序在执行顺序上没有其他影响，仅仅是巧合。虽然 pytest 会尽量确保这些巧合在每次运行中保持一致，但这并不是应该依赖的因素。如果你想控制执行顺序，最安全的做法是依赖这三个因素，并确保依赖关系明确。

    .. tab:: 英文

        When pytest wants to execute a test, once it knows what fixtures will be
        executed, it has to figure out the order they'll be executed in. To do this, it
        considers 3 factors:

        1. scope
        2. dependencies
        3. autouse

        Names of fixtures or tests, where they're defined, the order they're defined in,
        and the order fixtures are requested in have no bearing on execution order
        beyond coincidence. While pytest will try to make sure coincidences like these
        stay consistent from run to run, it's not something that should be depended on.
        If you want to control the order, it's safest to rely on these 3 things and make
        sure dependencies are clearly established.

更高范围的 fixtures 将首先执行
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Higher-scoped fixtures are executed first**

.. tabs::

    .. tab:: 中文

        在对 fixtures 的函数请求中，较高作用域的 fixtures （例如 ``session``）会在较低作用域的 fixtures （例如 ``function`` 或 ``class``）之前执行。

        以下是一个示例：

        .. literalinclude:: /example/fixtures/test_fixtures_order_scope.py

        测试将会通过，因为较大作用域的 fixtures 优先执行。

        执行顺序如下：

        .. image:: /example/fixtures/test_fixtures_order_scope.*
            :align: center

    .. tab:: 英文

        Within a function request for fixtures, those of higher-scopes (such as
        ``session``) are executed before lower-scoped fixtures (such as ``function`` or
        ``class``).

        Here's an example:

        .. literalinclude:: /example/fixtures/test_fixtures_order_scope.py

        The test will pass because the larger scoped fixtures are executing first.

        The order breaks down to this:

        .. image:: /example/fixtures/test_fixtures_order_scope.*
            :align: center

相同顺序的 Fixture 根据依赖关系执行
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Fixtures of the same order execute based on dependencies**

.. tabs::

    .. tab:: 中文

        当一个 fixtures 请求另一个 fixtures 时，另一个 fixtures 会首先执行。因此，如果 fixtures  ``a`` 请求 fixtures  ``b``， fixtures  ``b`` 将首先执行，因为 ``a`` 依赖于 ``b``，而无法在没有它的情况下运行。即使 ``a`` 不需要 ``b`` 的结果，如果它需要确保在 ``b`` 之后执行，仍然可以请求 ``b``。

        例如：

        .. literalinclude:: /example/fixtures/test_fixtures_order_dependencies.py

        如果我们绘制出依赖关系，结果看起来如下：

        .. image:: /example/fixtures/test_fixtures_order_dependencies.*
            :align: center

        每个 fixtures 所提供的规则（即每个 fixtures 必须在什么 fixtures 之后执行）是足够全面的，可以简化为：

        .. image:: /example/fixtures/test_fixtures_order_dependencies_flat.*
            :align: center

        必须通过这些请求提供足够的信息，以便 pytest 能够确定一个清晰的线性依赖链，从而为给定测试确定操作顺序。如果存在任何模糊性，且操作顺序可以有多种解释，则应假定 pytest 可以在任何时刻选择这些解释中的任何一种。

        例如，如果 ``d`` 没有请求 ``c``，即图形将如下所示：

        .. image:: /example/fixtures/test_fixtures_order_dependencies_unclear.*
            :align: center

        因为除了 ``g`` 之外，没有任何东西请求 ``c``，而 ``g`` 也请求 ``f``，因此现在不清楚 ``c`` 应该在 ``f``、``e`` 还是 ``d`` 之前/之后执行。对 ``c`` 的唯一规则是它必须在 ``b`` 之后和 ``g`` 之前执行。

        在这种情况下，pytest 不知道 ``c`` 应该放在什么地方，因此应假设它可以在 ``g`` 和 ``b`` 之间的任何位置。

        这并不一定是坏事，但值得注意。如果它们的执行顺序可能影响测试所针对的行为，或可能以其他方式影响测试结果，则应以允许 pytest 线性化/"扁平化" 该顺序的方式明确定义顺序。

    .. tab:: 英文

        When a fixture requests another fixture, the other fixture is executed first.
        So if fixture ``a`` requests fixture ``b``, fixture ``b`` will execute first,
        because ``a`` depends on ``b`` and can't operate without it. Even if ``a``
        doesn't need the result of ``b``, it can still request ``b`` if it needs to make
        sure it is executed after ``b``.

        For example:

        .. literalinclude:: /example/fixtures/test_fixtures_order_dependencies.py

        If we map out what depends on what, we get something that looks like this:

        .. image:: /example/fixtures/test_fixtures_order_dependencies.*
            :align: center

        The rules provided by each fixture (as to what fixture(s) each one has to come
        after) are comprehensive enough that it can be flattened to this:

        .. image:: /example/fixtures/test_fixtures_order_dependencies_flat.*
            :align: center

        Enough information has to be provided through these requests in order for pytest
        to be able to figure out a clear, linear chain of dependencies, and as a result,
        an order of operations for a given test. If there's any ambiguity, and the order
        of operations can be interpreted more than one way, you should assume pytest
        could go with any one of those interpretations at any point.

        For example, if ``d`` didn't request ``c``, i.e.the graph would look like this:

        .. image:: /example/fixtures/test_fixtures_order_dependencies_unclear.*
            :align: center

        Because nothing requested ``c`` other than ``g``, and ``g`` also requests ``f``,
        it's now unclear if ``c`` should go before/after ``f``, ``e``, or ``d``. The
        only rules that were set for ``c`` is that it must execute after ``b`` and
        before ``g``.

        pytest doesn't know where ``c`` should go in the case, so it should be assumed
        that it could go anywhere between ``g`` and ``b``.

        This isn't necessarily bad, but it's something to keep in mind. If the order
        they execute in could affect the behavior a test is targeting, or could
        otherwise influence the result of a test, then the order should be defined
        explicitly in a way that allows pytest to linearize/"flatten" that order.

.. _`autouse order`:

Autouse Fixture 在其范围内首先执行
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Autouse fixtures are executed first within their scope**

.. tabs::

    .. tab:: 中文

        autouse fixtures 假定适用于可以引用它们的每个测试，因此它们会在该范围内的其他 fixtures 之前执行。被 autouse fixtures 请求的 fixtures 在实际上也会成为适用于真实 autouse fixtures 所应用测试的 autouse fixtures 。

        所以，如果 fixtures  ``a`` 是自动使用的，而 fixtures  ``b`` 不是，但 fixtures  ``a`` 请求了 fixtures  ``b``，那么 fixtures  ``b`` 在实际上也将成为 autouse fixtures ，但仅适用于 ``a`` 适用的测试。

        在最后一个例子中，如果 ``d`` 没有请求 ``c``，图形会变得不清晰。但如果 ``c`` 是自动使用的，那么 ``b`` 和 ``a`` 实际上也将是自动使用的，因为 ``c`` 依赖于它们。因此，它们都将被提升到该范围内的非 autouse fixtures 之上。

        所以如果测试文件看起来像这样：

        .. literalinclude:: /example/fixtures/test_fixtures_order_autouse.py

        图形将如下所示：

        .. image:: /example/fixtures/test_fixtures_order_autouse.*
            :align: center

        因为 ``c`` 现在可以放在 ``d`` 之上，pytest 可以再次将图形线性化为：

        .. image:: /example/fixtures/test_fixtures_order_autouse_flat.*
            :align: center

        在这个例子中，``c`` 使得 ``b`` 和 ``a`` 也有效地成为 autouse fixtures 。

        不过，请小心使用 autouse fixtures ，因为 autouse fixtures 将自动为每个能够到达它的测试执行，即使这些测试没有请求它。例如，考虑这个文件：

        .. literalinclude:: /example/fixtures/test_fixtures_order_autouse_multiple_scopes.py

        即使 ``TestClassWithoutC1Request`` 中没有请求 ``c1``，它仍然会在其中的测试中执行：

        .. image:: /example/fixtures/test_fixtures_order_autouse_multiple_scopes.*
            :align: center

        但仅仅因为一个 autouse fixtures 请求了一个非 autouse fixtures ，并不意味着这个非 autouse fixtures 会在所有适用的上下文中变为 autouse fixtures 。它仅在真实的 autouse fixtures （请求非 autouse fixtures 的那个）所能应用的上下文中有效地变为 autouse fixtures 。

        例如，看看这个测试文件：

        .. literalinclude:: /example/fixtures/test_fixtures_order_autouse_temp_effects.py

        它将分解为如下内容：

        .. image:: /example/fixtures/test_fixtures_order_autouse_temp_effects.*
            :align: center

        对于 ``TestClassWithAutouse`` 中的 ``test_req`` 和 ``test_no_req``，``c3`` 有效地使 ``c2`` 成为 autouse fixtures ，这就是为什么 ``c2`` 和 ``c3`` 会在两个测试中执行，尽管没有被请求，以及为什么 ``c2`` 和 ``c3`` 会在 ``test_req`` 的 ``c1`` 之前执行。

        如果这使得 ``c2`` 成为一个 *实际* 的 autouse fixtures ，那么 ``c2`` 也会为 ``TestClassWithoutAutouse`` 中的测试执行，因为它们如果想的话可以引用 ``c2``。但它不会，因为从 ``TestClassWithoutAutouse`` 测试的角度看，``c2`` 不是一个 autouse fixtures ，因为它们无法看到 ``c3``。

        .. note:

            pytest 可以告诉你在给定测试中 fixtures 的执行顺序，如果你调用 ``pytest`` 并提供测试的名称（或它所在的范围），并提供 ``--setup-plan`` 标志，例如 ``pytest --setup-plan test_something.py``（以 ``_`` 开头的 fixtures 名称只有在你同时提供 ``-v`` 标志时才会显示）。

    .. tab:: 英文

        Autouse fixtures are assumed to apply to every test that could reference them,
        so they are executed before other fixtures in that scope. Fixtures that are
        requested by autouse fixtures effectively become autouse fixtures themselves for
        the tests that the real autouse fixture applies to.

        So if fixture ``a`` is autouse and fixture ``b`` is not, but fixture ``a``
        requests fixture ``b``, then fixture ``b`` will effectively be an autouse
        fixture as well, but only for the tests that ``a`` applies to.

        In the last example, the graph became unclear if ``d`` didn't request ``c``. But
        if ``c`` was autouse, then ``b`` and ``a`` would effectively also be autouse
        because ``c`` depends on them. As a result, they would all be shifted above
        non-autouse fixtures within that scope.

        So if the test file looked like this:

        .. literalinclude:: /example/fixtures/test_fixtures_order_autouse.py

        the graph would look like this:

        .. image:: /example/fixtures/test_fixtures_order_autouse.*
            :align: center

        Because ``c`` can now be put above ``d`` in the graph, pytest can once again
        linearize the graph to this:

        .. image:: /example/fixtures/test_fixtures_order_autouse_flat.*
            :align: center

        In this example, ``c`` makes ``b`` and ``a`` effectively autouse fixtures as
        well.

        Be careful with autouse, though, as an autouse fixture will automatically
        execute for every test that can reach it, even if they don't request it. For
        example, consider this file:

        .. literalinclude:: /example/fixtures/test_fixtures_order_autouse_multiple_scopes.py

        Even though nothing in ``TestClassWithoutC1Request`` is requesting ``c1``, it still
        is executed for the tests inside it anyway:

        .. image:: /example/fixtures/test_fixtures_order_autouse_multiple_scopes.*
            :align: center

        But just because one autouse fixture requested a non-autouse fixture, that
        doesn't mean the non-autouse fixture becomes an autouse fixture for all contexts
        that it can apply to. It only effectively becomes an autouse fixture for the
        contexts the real autouse fixture (the one that requested the non-autouse
        fixture) can apply to.

        For example, take a look at this test file:

        .. literalinclude:: /example/fixtures/test_fixtures_order_autouse_temp_effects.py

        It would break down to something like this:

        .. image:: /example/fixtures/test_fixtures_order_autouse_temp_effects.*
            :align: center

        For ``test_req`` and ``test_no_req`` inside ``TestClassWithAutouse``, ``c3``
        effectively makes ``c2`` an autouse fixture, which is why ``c2`` and ``c3`` are
        executed for both tests, despite not being requested, and why ``c2`` and ``c3``
        are executed before ``c1`` for ``test_req``.

        If this made ``c2`` an *actual* autouse fixture, then ``c2`` would also execute
        for the tests inside ``TestClassWithoutAutouse``, since they can reference
        ``c2`` if they wanted to. But it doesn't, because from the perspective of the
        ``TestClassWithoutAutouse`` tests, ``c2`` isn't an autouse fixture, since they
        can't see ``c3``.


        .. note:

            pytest can tell you what order the fixtures will execute in for a given test
            if you call ``pytests`` along with the test's name (or the scope it's in),
            and provide the ``--setup-plan`` flag, e.g.
            ``pytest --setup-plan test_something.py`` (fixtures with names that start
            with ``_`` will only be shown if you also provide the ``-v`` flag).
