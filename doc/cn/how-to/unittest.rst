
.. _`unittest.TestCase`:
.. _`unittest`:

如何在 pytest 中使用基于 unittest 的测试
===============================================

**How to use** ``unittest`` **-based tests with pytest**

.. tabs::

    .. tab:: 中文

        ``pytest`` 支持开箱即用地运行基于 Python ``unittest`` 的测试。它旨在利用现有的基于 ``unittest`` 的测试套件，使用 pytest 作为测试运行器，并逐步适应测试套件，以充分利用 pytest 的特性。

        要使用 ``pytest`` 运行现有的 ``unittest`` 风格测试套件，请输入：

        .. code-block:: bash

            pytest tests

        pytest 将自动收集 ``unittest.TestCase`` 子类及其在 ``test_*.py`` 或 ``*_test.py`` 文件中的 ``test`` 方法。

        几乎所有的 ``unittest`` 特性都得到支持：

        * ``@unittest.skip`` 风格的装饰器；
        * ``setUp/tearDown``；
        * ``setUpClass/tearDownClass``；
        * ``setUpModule/tearDownModule``；

        此外，:ref:`subtests <python:subtests>` 通过 `pytest-subtests`_ 插件得到支持。

        到目前为止，pytest 不支持以下功能：

        * `load_tests protocol`_ ;

    .. tab:: 英文

        ``pytest`` supports running Python ``unittest``-based tests out of the box.
        It's meant for leveraging existing ``unittest``-based test suites
        to use pytest as a test runner and also allow to incrementally adapt
        the test suite to take full advantage of pytest's features.

        To run an existing ``unittest``-style test suite using ``pytest``, type:

        .. code-block:: bash

            pytest tests


        pytest will automatically collect ``unittest.TestCase`` subclasses and
        their ``test`` methods in ``test_*.py`` or ``*_test.py`` files.

        Almost all ``unittest`` features are supported:

        * ``@unittest.skip`` style decorators;
        * ``setUp/tearDown``;
        * ``setUpClass/tearDownClass``;
        * ``setUpModule/tearDownModule``;

        Additionally, :ref:`subtests <python:subtests>` are supported by the
        `pytest-subtests`_ plugin.

        Up to this point pytest does not have support for the following features:

        * `load_tests protocol`_ ;

.. _`pytest-subtests`: https://github.com/pytest-dev/pytest-subtests
.. _`load_tests protocol`: https://docs.python.org/3/library/unittest.html#load-tests-protocol

开箱即用的优势
-----------------------

**Benefits out of the box**

.. tabs::

    .. tab:: 中文

        通过使用 pytest 运行测试套件，您可以利用几个功能，在大多数情况下无需修改现有代码：

        * 获取 :ref:`更有信息量的回溯 <tbreportdemo>`；
        * 捕获 :ref:`stdout 和 stderr <captures>`；
        * 使用 ``-k`` 和 ``-m`` 标志的 :ref:`测试选择选项 <select-tests>`；
        * :ref:`maxfail`；
        * 用于调试测试失败的 :ref:`--pdb <pdb-option>` 命令行选项
        （见下文的 :ref:`note <pdb-unittest-note>`）；
        * 使用 :pypi:`pytest-xdist` 插件将测试分发到多个 CPU；
        * 使用 :ref:`普通断言语句 <assert>` 代替 ``self.assert*`` 函数
        （ :pypi:`unittest2pytest` 在这方面非常有帮助）；

    .. tab:: 英文

        By running your test suite with pytest you can make use of several features,
        in most cases without having to modify existing code:

        * Obtain :ref:`more informative tracebacks <tbreportdemo>`;
        * :ref:`stdout and stderr <captures>` capturing;
        * :ref:`Test selection options <select-tests>` using ``-k`` and ``-m`` flags;
        * :ref:`maxfail`;
        * :ref:`--pdb <pdb-option>` command-line option for debugging on test failures
        (see :ref:`note <pdb-unittest-note>` below);
        * Distribute tests to multiple CPUs using the :pypi:`pytest-xdist` plugin;
        * Use :ref:`plain assert-statements <assert>` instead of ``self.assert*`` functions
        (:pypi:`unittest2pytest` is immensely helpful in this);


``unittest.TestCase`` 子类中的 pytest 功能
---------------------------------------------------

**pytest features in** ``unittest.TestCase`` **subclasses**

.. tabs::

    .. tab:: 中文

        以下 pytest 功能在 ``unittest.TestCase`` 子类中有效：

        * :ref:`标记 <mark>` ： :ref:`跳过 <skip>`、 :ref:`条件跳过 <skipif>`、 :ref:`预期失败 <xfail>` ；
        * :ref:`自动使用的 fixtures <mixing-fixtures>` ；

        以下 pytest 功能 **无效**，并且由于设计哲学的不同，可能永远不会有效：

        * :ref:`fixtures <fixture>` （除了 ``autouse`` fixtures，见 :ref:`下面 <mixing-fixtures>` ）；
        * :ref:`参数化 <parametrize>` ；
        * :ref:`自定义钩子 <writing-plugins>` ；

        第三方插件的兼容性可能因插件和测试套件而异。

    .. tab:: 英文

        The following pytest features work in ``unittest.TestCase`` subclasses:

        * :ref:`Marks <mark>`: :ref:`skip <skip>` , :ref:`skipif <skipif>` , :ref:`xfail <xfail>` ;
        * :ref:`Auto-use fixtures <mixing-fixtures>`;

        The following pytest features **do not** work, and probably
        never will due to different design philosophies:

        * :ref:`Fixtures <fixture>` (except for ``autouse`` fixtures, see :ref:`below <mixing-fixtures>`);
        * :ref:`Parametrization <parametrize>`;
        * :ref:`Custom hooks <writing-plugins>`;


        Third party plugins may or may not work well, depending on the plugin and the test suite.

.. _mixing-fixtures:

使用标记将 pytest 装置混合到 ``unittest.TestCase`` 子类中
------------------------------------------------------------------------

**Mixing pytest fixtures into** ``unittest.TestCase`` **subclasses using marks**

.. tabs::

    .. tab:: 中文

        使用 ``pytest`` 运行你的 unittest 允许你在 ``unittest.TestCase`` 风格的测试中使用其 :ref:`fixture 机制 <fixture>`。假设你至少浏览过 pytest fixture 的特性，让我们直接进入一个示例，该示例集成了 pytest 的 ``db_class`` fixture，设置一个类缓存的数据库对象，然后从 unittest 风格的测试中引用它：

        .. code-block:: python

            # conftest.py 的内容

            # 我们在下面定义一个 fixture 函数，它将通过
            # 在测试中引用其名称来“使用”

            import pytest


            @pytest.fixture(scope="class")
            def db_class(request):
                class DummyDB:
                    pass

                # 在调用测试上下文上设置一个类属性
                request.cls.db = DummyDB()

        这定义了一个 fixture 函数 ``db_class``，如果被使用，将在每个测试类中调用一次，并将类级别的 ``db`` 属性设置为 ``DummyDB`` 实例。fixture 函数通过接收一个特殊的 ``request`` 对象来实现这一点，该对象提供对 :ref:`请求测试上下文 <request-context>` 的访问，例如 ``cls`` 属性，表示使用该 fixture 的类。这种架构将 fixture 编写与实际测试代码解耦，并通过最小的引用（即 fixture 名称）允许重用该 fixture。因此，让我们编写一个实际的 ``unittest.TestCase`` 类，使用我们的 fixture 定义：

        .. code-block:: python

            # test_unittest_db.py 的内容

            import unittest

            import pytest


            @pytest.mark.usefixtures("db_class")
            class MyTest(unittest.TestCase):
                def test_method1(self):
                    assert hasattr(self, "db")
                    assert 0, self.db  # 为演示目的而失败

                def test_method2(self):
                    assert 0, self.db  # 为演示目的而失败

        ``@pytest.mark.usefixtures("db_class")`` 类装饰器确保 pytest fixture 函数 ``db_class`` 每个类调用一次。由于故意失败的断言语句，我们可以查看 traceback 中的 ``self.db`` 值：

        .. code-block:: pytest

            $ pytest test_unittest_db.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_unittest_db.py FF                                               [100%]

            ================================= FAILURES =================================
            ___________________________ MyTest.test_method1 ____________________________

            self = <test_unittest_db.MyTest testMethod=test_method1>

                def test_method1(self):
                    assert hasattr(self, "db")
            >       assert 0, self.db  # fail for demo purposes
            E       AssertionError: <conftest.db_class.<locals>.DummyDB object at 0xdeadbeef0001>
            E       assert 0

            test_unittest_db.py:11: AssertionError
            ___________________________ MyTest.test_method2 ____________________________

            self = <test_unittest_db.MyTest testMethod=test_method2>

                def test_method2(self):
            >       assert 0, self.db  # fail for demo purposes
            E       AssertionError: <conftest.db_class.<locals>.DummyDB object at 0xdeadbeef0001>
            E       assert 0

            test_unittest_db.py:14: AssertionError
            ========================= short test summary info ==========================
            FAILED test_unittest_db.py::MyTest::test_method1 - AssertionError: <conft...
            FAILED test_unittest_db.py::MyTest::test_method2 - AssertionError: <conft...
            ============================ 2 failed in 0.12s =============================

        这个默认的 pytest traceback 显示两个测试方法共享同一个 ``self.db`` 实例，这是我们在编写上述类作用域 fixture 函数时的意图。

    .. tab:: 英文

        Running your unittest with ``pytest`` allows you to use its
        :ref:`fixture mechanism <fixture>` with ``unittest.TestCase`` style
        tests.  Assuming you have at least skimmed the pytest fixture features,
        let's jump-start into an example that integrates a pytest ``db_class``
        fixture, setting up a class-cached database object, and then reference
        it from a unittest-style test:

        .. code-block:: python

            # content of conftest.py

            # we define a fixture function below and it will be "used" by
            # referencing its name from tests

            import pytest


            @pytest.fixture(scope="class")
            def db_class(request):
                class DummyDB:
                    pass

                # set a class attribute on the invoking test context
                request.cls.db = DummyDB()

        This defines a fixture function ``db_class`` which - if used - is
        called once for each test class and which sets the class-level
        ``db`` attribute to a ``DummyDB`` instance.  The fixture function
        achieves this by receiving a special ``request`` object which gives
        access to :ref:`the requesting test context <request-context>` such
        as the ``cls`` attribute, denoting the class from which the fixture
        is used.  This architecture de-couples fixture writing from actual test
        code and allows reuse of the fixture by a minimal reference, the fixture
        name.  So let's write an actual ``unittest.TestCase`` class using our
        fixture definition:

        .. code-block:: python

            # content of test_unittest_db.py

            import unittest

            import pytest


            @pytest.mark.usefixtures("db_class")
            class MyTest(unittest.TestCase):
                def test_method1(self):
                    assert hasattr(self, "db")
                    assert 0, self.db  # fail for demo purposes

                def test_method2(self):
                    assert 0, self.db  # fail for demo purposes

        The ``@pytest.mark.usefixtures("db_class")`` class-decorator makes sure that
        the pytest fixture function ``db_class`` is called once per class.
        Due to the deliberately failing assert statements, we can take a look at
        the ``self.db`` values in the traceback:

        .. code-block:: pytest

            $ pytest test_unittest_db.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_unittest_db.py FF                                               [100%]

            ================================= FAILURES =================================
            ___________________________ MyTest.test_method1 ____________________________

            self = <test_unittest_db.MyTest testMethod=test_method1>

                def test_method1(self):
                    assert hasattr(self, "db")
            >       assert 0, self.db  # fail for demo purposes
            E       AssertionError: <conftest.db_class.<locals>.DummyDB object at 0xdeadbeef0001>
            E       assert 0

            test_unittest_db.py:11: AssertionError
            ___________________________ MyTest.test_method2 ____________________________

            self = <test_unittest_db.MyTest testMethod=test_method2>

                def test_method2(self):
            >       assert 0, self.db  # fail for demo purposes
            E       AssertionError: <conftest.db_class.<locals>.DummyDB object at 0xdeadbeef0001>
            E       assert 0

            test_unittest_db.py:14: AssertionError
            ========================= short test summary info ==========================
            FAILED test_unittest_db.py::MyTest::test_method1 - AssertionError: <conft...
            FAILED test_unittest_db.py::MyTest::test_method2 - AssertionError: <conft...
            ============================ 2 failed in 0.12s =============================

        This default pytest traceback shows that the two test methods
        share the same ``self.db`` instance which was our intention
        when writing the class-scoped fixture function above.


使用自动使用装置并访问其他装置
---------------------------------------------------

**Using autouse fixtures and accessing other fixtures**

.. tabs::

    .. tab:: 中文

        虽然通常明确声明给定测试所需的 fixtures 是更好的做法，但有时你可能希望在特定上下文中自动使用 fixtures。毕竟，传统的 unittest-setup 风格要求使用这种隐式 fixture 编写，你可能已经习惯了这种方式或喜欢它。

        你可以使用 ``@pytest.fixture(autouse=True)`` 标记 fixture 函数，并在希望使用它的上下文中定义该 fixture 函数。让我们来看一个 ``initdir`` fixture，它使得 ``TestCase`` 类的所有测试方法在一个临时目录中执行，并预初始化 ``samplefile.ini`` 。我们的 ``initdir`` fixture 本身使用 pytest 内置的 :fixture:`tmp_path` fixture 来委托创建每个测试的临时目录：

        .. code-block:: python

            # test_unittest_cleandir.py 的内容
            import unittest

            import pytest


            class MyTest(unittest.TestCase):
                @pytest.fixture(autouse=True)
                def initdir(self, tmp_path, monkeypatch):
                    monkeypatch.chdir(tmp_path)  # 切换到 pytest 提供的临时目录
                    tmp_path.joinpath("samplefile.ini").write_text("# testdata", encoding="utf-8")

                def test_method(self):
                    with open("samplefile.ini", encoding="utf-8") as f:
                        s = f.read()
                    assert "testdata" in s

        由于 ``autouse`` 标志，``initdir`` fixture 函数将用于定义它的类的所有方法。这是使用 ``@pytest.mark.usefixtures("initdir")`` 标记在类上进行标记的简便方法，就像在前面的示例中那样。

        运行这个测试模块 ...:

        .. code-block:: pytest

            $ pytest -q test_unittest_cleandir.py
            .                                                                    [100%]
            1 passed in 0.12s

        ... 给我们一个通过的测试，因为 ``initdir`` fixture 函数在 ``test_method`` 之前被执行。

        .. note::

            ``unittest.TestCase`` 方法不能直接接收 fixture 参数，因为实现这一点可能会影响运行一般 unittest.TestCase 测试套件的能力。

            上述的 ``usefixtures`` 和 ``autouse`` 示例应该有助于将 pytest fixtures 混入 unittest 套件中。

            你还可以逐步从继承 ``unittest.TestCase`` 转向 *plain asserts*，然后开始逐步受益于完整的 pytest 特性集。

        .. _pdb-unittest-note:

        .. note::

            由于两个框架之间的架构差异，基于 ``unittest`` 的测试的设置和拆卸是在测试的 ``call`` 阶段进行，而不是在 ``pytest`` 的标准 ``setup`` 和 ``teardown`` 阶段。这在某些情况下很重要，特别是在推理错误时。例如，如果基于 ``unittest`` 的套件在设置过程中出现错误，``pytest`` 将在其 ``setup`` 阶段报告没有错误，而是在 ``call`` 阶段引发错误。

    .. tab:: 英文

        Although it's usually better to explicitly declare use of fixtures you need
        for a given test, you may sometimes want to have fixtures that are
        automatically used in a given context.  After all, the traditional
        style of unittest-setup mandates the use of this implicit fixture writing
        and chances are, you are used to it or like it.

        You can flag fixture functions with ``@pytest.fixture(autouse=True)``
        and define the fixture function in the context where you want it used.
        Let's look at an ``initdir`` fixture which makes all test methods of a
        ``TestCase`` class execute in a temporary directory with a
        pre-initialized ``samplefile.ini``.  Our ``initdir`` fixture itself uses
        the pytest builtin :fixture:`tmp_path` fixture to delegate the
        creation of a per-test temporary directory:

        .. code-block:: python

            # content of test_unittest_cleandir.py
            import unittest

            import pytest


            class MyTest(unittest.TestCase):
                @pytest.fixture(autouse=True)
                def initdir(self, tmp_path, monkeypatch):
                    monkeypatch.chdir(tmp_path)  # change to pytest-provided temporary directory
                    tmp_path.joinpath("samplefile.ini").write_text("# testdata", encoding="utf-8")

                def test_method(self):
                    with open("samplefile.ini", encoding="utf-8") as f:
                        s = f.read()
                    assert "testdata" in s

        Due to the ``autouse`` flag the ``initdir`` fixture function will be
        used for all methods of the class where it is defined.  This is a
        shortcut for using a ``@pytest.mark.usefixtures("initdir")`` marker
        on the class like in the previous example.

        Running this test module ...:

        .. code-block:: pytest

            $ pytest -q test_unittest_cleandir.py
            .                                                                    [100%]
            1 passed in 0.12s

        ... gives us one passed test because the ``initdir`` fixture function
        was executed ahead of the ``test_method``.

        .. note::

            ``unittest.TestCase`` methods cannot directly receive fixture
            arguments as implementing that is likely to inflict
            on the ability to run general unittest.TestCase test suites.

            The above ``usefixtures`` and ``autouse`` examples should help to mix in
            pytest fixtures into unittest suites.

            You can also gradually move away from subclassing from ``unittest.TestCase`` to *plain asserts*
            and then start to benefit from the full pytest feature set step by step.

        .. note::

            Due to architectural differences between the two frameworks, setup and
            teardown for ``unittest``-based tests is performed during the ``call`` phase
            of testing instead of in ``pytest``'s standard ``setup`` and ``teardown``
            stages. This can be important to understand in some situations, particularly
            when reasoning about errors. For example, if a ``unittest``-based suite
            exhibits errors during setup, ``pytest`` will report no errors during its
            ``setup`` phase and will instead raise the error during ``call``.
