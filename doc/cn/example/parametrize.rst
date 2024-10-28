
.. _paramexamples:

参数化测试
=================================================

**Parametrizing tests**

.. tabs::

    .. tab:: 中文

        ``pytest`` 允许轻松参数化测试函数。有关基本文档，请参阅：ref:`parametrize-basics`。

        下面我们提供了一些使用内置机制的示例。

    .. tab:: 英文

        ``pytest`` allows to easily parametrize test functions. For basic docs, see :ref:`parametrize-basics`.

        In the following we provide some examples using the builtin mechanisms.

根据命令行生成参数组合
----------------------------------------------------------------------------

**Generating parameters combinations, depending on command line**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        假设我们想要使用不同的计算参数执行测试，并且参数范围应由命令行参数确定。首先，我们写一个简单的（无操作）计算测试：

        .. code-block:: python

            # content of test_compute.py


            def test_compute(param1):
                assert param1 < 4

        现在我们添加一个测试配置，如下所示：

        .. code-block:: python

            # content of conftest.py


            def pytest_addoption(parser):
                parser.addoption("--all", action="store_true", help="run all combinations")


            def pytest_generate_tests(metafunc):
                if "param1" in metafunc.fixturenames:
                    if metafunc.config.getoption("all"):
                        end = 5
                    else:
                        end = 2
                    metafunc.parametrize("param1", range(end))

        这意味着如果不传递 ``--all``，我们只运行 2 个测试：

        .. code-block:: pytest

            $ pytest -q test_compute.py
            ..                                                                   [100%]
            2 passed in 0.12s

        我们只运行了两个计算，所以看到两个点。现在让我们运行全部组合：

        .. code-block:: pytest

            $ pytest -q --all
            ....F                                                                [100%]
            ================================= FAILURES =================================
            _____________________________ test_compute[4] ______________________________

            param1 = 4

                def test_compute(param1):
            >       assert param1 < 4
            E       assert 4 < 4

            test_compute.py:4: AssertionError
            ========================= short test summary info ==========================
            FAILED test_compute.py::test_compute[4] - assert 4 < 4
            1 failed, 4 passed in 0.12s

        正如预期的那样，当运行 ``param1`` 值的全范围时，最后一个值会出错。

    .. tab:: 英文

        .. regendoc:wipe

        Let's say we want to execute a test with different computation
        parameters and the parameter range shall be determined by a command
        line argument.  Let's first write a simple (do-nothing) computation test:

        .. code-block:: python

            # content of test_compute.py


            def test_compute(param1):
                assert param1 < 4

        Now we add a test configuration like this:

        .. code-block:: python

            # content of conftest.py


            def pytest_addoption(parser):
                parser.addoption("--all", action="store_true", help="run all combinations")


            def pytest_generate_tests(metafunc):
                if "param1" in metafunc.fixturenames:
                    if metafunc.config.getoption("all"):
                        end = 5
                    else:
                        end = 2
                    metafunc.parametrize("param1", range(end))

        This means that we only run 2 tests if we do not pass ``--all``:

        .. code-block:: pytest

            $ pytest -q test_compute.py
            ..                                                                   [100%]
            2 passed in 0.12s

        We run only two computations, so we see two dots.
        let's run the full monty:

        .. code-block:: pytest

            $ pytest -q --all
            ....F                                                                [100%]
            ================================= FAILURES =================================
            _____________________________ test_compute[4] ______________________________

            param1 = 4

                def test_compute(param1):
            >       assert param1 < 4
            E       assert 4 < 4

            test_compute.py:4: AssertionError
            ========================= short test summary info ==========================
            FAILED test_compute.py::test_compute[4] - assert 4 < 4
            1 failed, 4 passed in 0.12s

        As expected when running the full range of ``param1`` values we'll get an error on the last one.


测试 ID 的不同选项
------------------------------------

**Different options for test IDs**

.. tabs::

    .. tab:: 中文

        pytest 将为参数化测试中的每组值构建一个字符串，作为测试 ID。这些 ID 可以与 ``-k`` 一起使用，以选择特定的用例进行运行，并且在某个用例失败时，它们也会标识出具体的用例。使用 ``--collect-only`` 运行 pytest 将显示生成的 ID。

        数字、字符串、布尔值和 None 将使用它们通常的字符串表示形式来构建测试 ID。对于其他对象，pytest 将基于参数名称生成字符串：

        .. code-block:: python

            # content of test_time.py

            from datetime import datetime, timedelta

            import pytest

            testdata = [
                (datetime(2001, 12, 12), datetime(2001, 12, 11), timedelta(1)),
                (datetime(2001, 12, 11), datetime(2001, 12, 12), timedelta(-1)),
            ]


            @pytest.mark.parametrize("a,b,expected", testdata)
            def test_timedistance_v0(a, b, expected):
                diff = a - b
                assert diff == expected


            @pytest.mark.parametrize("a,b,expected", testdata, ids=["forward", "backward"])
            def test_timedistance_v1(a, b, expected):
                diff = a - b
                assert diff == expected


            def idfn(val):
                if isinstance(val, (datetime,)):
                    # 注意这不会显示任何小时/分钟/秒
                    return val.strftime("%Y%m%d")


            @pytest.mark.parametrize("a,b,expected", testdata, ids=idfn)
            def test_timedistance_v2(a, b, expected):
                diff = a - b
                assert diff == expected


            @pytest.mark.parametrize(
                "a,b,expected",
                [
                    pytest.param(
                        datetime(2001, 12, 12), datetime(2001, 12, 11), timedelta(1), id="forward"
                    ),
                    pytest.param(
                        datetime(2001, 12, 11), datetime(2001, 12, 12), timedelta(-1), id="backward"
                    ),
                ],
            )
            def test_timedistance_v3(a, b, expected):
                diff = a - b
                assert diff == expected

        在 ``test_timedistance_v0`` 中，我们让 pytest 生成测试 ID。

        在 ``test_timedistance_v1`` 中，我们将 ``ids`` 指定为字符串列表，这些字符串被用作测试 ID。这些 ID 简洁，但维护起来可能会很麻烦。

        在 ``test_timedistance_v2`` 中，我们将 ``ids`` 指定为一个函数，该函数可以生成字符串表示形式，以构成测试 ID 的一部分。因此，我们的 ``datetime`` 值使用 ``idfn`` 生成的标签，但由于我们没有为 ``timedelta`` 对象生成标签，它们仍然使用默认的 pytest 表示：

        .. code-block:: pytest

            $ pytest test_time.py --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 8 items

            <Dir parametrize.rst-204>
            <Module test_time.py>
                <Function test_timedistance_v0[a0-b0-expected0]>
                <Function test_timedistance_v0[a1-b1-expected1]>
                <Function test_timedistance_v1[forward]>
                <Function test_timedistance_v1[backward]>
                <Function test_timedistance_v2[20011212-20011211-expected0]>
                <Function test_timedistance_v2[20011211-20011212-expected1]>
                <Function test_timedistance_v3[forward]>
                <Function test_timedistance_v3[backward]>

            ======================== 8 tests collected in 0.12s ========================

        在 ``test_timedistance_v3`` 中，我们使用 ``pytest.param`` 一起指定测试 ID 和实际数据，而不是将它们单独列出。

    .. tab:: 英文

        pytest will build a string that is the test ID for each set of values in a
        parametrized test. These IDs can be used with ``-k`` to select specific cases
        to run, and they will also identify the specific case when one is failing.
        Running pytest with ``--collect-only`` will show the generated IDs.

        Numbers, strings, booleans and None will have their usual string representation
        used in the test ID. For other objects, pytest will make a string based on
        the argument name:

        .. code-block:: python

            # content of test_time.py

            from datetime import datetime, timedelta

            import pytest

            testdata = [
                (datetime(2001, 12, 12), datetime(2001, 12, 11), timedelta(1)),
                (datetime(2001, 12, 11), datetime(2001, 12, 12), timedelta(-1)),
            ]


            @pytest.mark.parametrize("a,b,expected", testdata)
            def test_timedistance_v0(a, b, expected):
                diff = a - b
                assert diff == expected


            @pytest.mark.parametrize("a,b,expected", testdata, ids=["forward", "backward"])
            def test_timedistance_v1(a, b, expected):
                diff = a - b
                assert diff == expected


            def idfn(val):
                if isinstance(val, (datetime,)):
                    # note this wouldn't show any hours/minutes/seconds
                    return val.strftime("%Y%m%d")


            @pytest.mark.parametrize("a,b,expected", testdata, ids=idfn)
            def test_timedistance_v2(a, b, expected):
                diff = a - b
                assert diff == expected


            @pytest.mark.parametrize(
                "a,b,expected",
                [
                    pytest.param(
                        datetime(2001, 12, 12), datetime(2001, 12, 11), timedelta(1), id="forward"
                    ),
                    pytest.param(
                        datetime(2001, 12, 11), datetime(2001, 12, 12), timedelta(-1), id="backward"
                    ),
                ],
            )
            def test_timedistance_v3(a, b, expected):
                diff = a - b
                assert diff == expected

        In ``test_timedistance_v0``, we let pytest generate the test IDs.

        In ``test_timedistance_v1``, we specified ``ids`` as a list of strings which were
        used as the test IDs. These are succinct, but can be a pain to maintain.

        In ``test_timedistance_v2``, we specified ``ids`` as a function that can generate a
        string representation to make part of the test ID. So our ``datetime`` values use the
        label generated by ``idfn``, but because we didn't generate a label for ``timedelta``
        objects, they are still using the default pytest representation:

        .. code-block:: pytest

            $ pytest test_time.py --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 8 items

            <Dir parametrize.rst-204>
            <Module test_time.py>
                <Function test_timedistance_v0[a0-b0-expected0]>
                <Function test_timedistance_v0[a1-b1-expected1]>
                <Function test_timedistance_v1[forward]>
                <Function test_timedistance_v1[backward]>
                <Function test_timedistance_v2[20011212-20011211-expected0]>
                <Function test_timedistance_v2[20011211-20011212-expected1]>
                <Function test_timedistance_v3[forward]>
                <Function test_timedistance_v3[backward]>

            ======================== 8 tests collected in 0.12s ========================

        In ``test_timedistance_v3``, we used ``pytest.param`` to specify the test IDs
        together with the actual data, instead of listing them separately.

“测试场景”的快速移植
------------------------------------

**A quick port of "testscenarios"**

.. tabs::

    .. tab:: 中文

        这是一个快速移植，用于运行使用 :pypi:`testscenarios` 配置的测试，这是 Robert Collins 为标准 unittest 框架提供的一个附加组件。我们只需稍微处理一下，以构造适合 pytest 的 :py:func:`Metafunc.parametrize <pytest.Metafunc.parametrize>` 的正确参数：

        .. code-block:: python

            # content of test_scenarios.py


            def pytest_generate_tests(metafunc):
                idlist = []
                argvalues = []
                for scenario in metafunc.cls.scenarios:
                    idlist.append(scenario[0])
                    items = scenario[1].items()
                    argnames = [x[0] for x in items]
                    argvalues.append([x[1] for x in items])
                metafunc.parametrize(argnames, argvalues, ids=idlist, scope="class")


            scenario1 = ("basic", {"attribute": "value"})
            scenario2 = ("advanced", {"attribute": "value2"})


            class TestSampleWithScenarios:
                scenarios = [scenario1, scenario2]

                def test_demo1(self, attribute):
                    assert isinstance(attribute, str)

                def test_demo2(self, attribute):
                    assert isinstance(attribute, str)

        这是一个完全自包含的示例，您可以通过以下方式运行：

        .. code-block:: pytest

            $ pytest test_scenarios.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items

            test_scenarios.py ....                                               [100%]

            ============================ 4 passed in 0.12s =============================

        如果您只收集测试，您还会看到 'advanced' 和 'basic' 作为测试函数的变体：

        .. code-block:: pytest

            $ pytest --collect-only test_scenarios.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items

            <Dir parametrize.rst-204>
            <Module test_scenarios.py>
                <Class TestSampleWithScenarios>
                <Function test_demo1[basic]>
                <Function test_demo2[basic]>
                <Function test_demo1[advanced]>
                <Function test_demo2[advanced]>

            ======================== 4 tests collected in 0.12s ========================

        请注意，我们告诉 ``metafunc.parametrize()``，您的场景值应被视为类范围。使用 pytest-2.3，这将导致基于资源的排序。

    .. tab:: 英文

        Here is a quick port to run tests configured with :pypi:`testscenarios`,
        an add-on from Robert Collins for the standard unittest framework. We
        only have to work a bit to construct the correct arguments for pytest's
        :py:func:`Metafunc.parametrize <pytest.Metafunc.parametrize>`:

        .. code-block:: python

            # content of test_scenarios.py


            def pytest_generate_tests(metafunc):
                idlist = []
                argvalues = []
                for scenario in metafunc.cls.scenarios:
                    idlist.append(scenario[0])
                    items = scenario[1].items()
                    argnames = [x[0] for x in items]
                    argvalues.append([x[1] for x in items])
                metafunc.parametrize(argnames, argvalues, ids=idlist, scope="class")


            scenario1 = ("basic", {"attribute": "value"})
            scenario2 = ("advanced", {"attribute": "value2"})


            class TestSampleWithScenarios:
                scenarios = [scenario1, scenario2]

                def test_demo1(self, attribute):
                    assert isinstance(attribute, str)

                def test_demo2(self, attribute):
                    assert isinstance(attribute, str)

        this is a fully self-contained example which you can run with:

        .. code-block:: pytest

            $ pytest test_scenarios.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items

            test_scenarios.py ....                                               [100%]

            ============================ 4 passed in 0.12s =============================

        If you just collect tests you'll also nicely see 'advanced' and 'basic' as variants for the test function:

        .. code-block:: pytest

            $ pytest --collect-only test_scenarios.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 4 items

            <Dir parametrize.rst-204>
            <Module test_scenarios.py>
                <Class TestSampleWithScenarios>
                <Function test_demo1[basic]>
                <Function test_demo2[basic]>
                <Function test_demo1[advanced]>
                <Function test_demo2[advanced]>

            ======================== 4 tests collected in 0.12s ========================

        Note that we told ``metafunc.parametrize()`` that your scenario values
        should be considered class-scoped.  With pytest-2.3 this leads to a
        resource-based ordering.

推迟参数化资源的设置
---------------------------------------------------

**Deferring the setup of parametrized resources**

.. tabs::

    .. tab:: 中文

        测试函数的参数化发生在收集阶段。最好在实际测试运行时设置昂贵的资源，例如数据库连接或子进程。以下是一个简单的示例，展示了如何实现这一点。这个测试需要一个 ``db`` 对象夹具：

        .. code-block:: python

            # content of test_backends.py

            import pytest


            def test_db_initialized(db):
                # 一个虚拟测试
                if db.__class__.__name__ == "DB2":
                    pytest.fail("出于演示目的故意失败")

        现在我们可以添加一个测试配置，该配置生成两次对 ``test_db_initialized`` 函数的调用，并且实现一个工厂，创建用于实际测试调用的数据库对象：

        .. code-block:: python

            # content of conftest.py
            import pytest


            def pytest_generate_tests(metafunc):
                if "db" in metafunc.fixturenames:
                    metafunc.parametrize("db", ["d1", "d2"], indirect=True)


            class DB1:
                "一个数据库对象"


            class DB2:
                "替代数据库对象"


            @pytest.fixture
            def db(request):
                if request.param == "d1":
                    return DB1()
                elif request.param == "d2":
                    return DB2()
                else:
                    raise ValueError("无效的内部测试配置")

        让我们先看看收集阶段的情况：

        .. code-block:: pytest

            $ pytest test_backends.py --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            <Dir parametrize.rst-204>
            <Module test_backends.py>
                <Function test_db_initialized[d1]>
                <Function test_db_initialized[d2]>

            ======================== 2 tests collected in 0.12s ========================

        然后当我们运行测试时：

        .. code-block:: pytest

            $ pytest -q test_backends.py
            .F                                                                   [100%]
            ================================= FAILURES =================================
            _________________________ test_db_initialized[d2] __________________________

            db = <conftest.DB2 object at 0xdeadbeef0001>

                def test_db_initialized(db):
                    # 一个虚拟测试
                    if db.__class__.__name__ == "DB2":
            >           pytest.fail("出于演示目的故意失败")
            E           Failed: deliberately failing for demo purposes

            test_backends.py:8: Failed
            ========================= short test summary info ==========================
            FAILED test_backends.py::test_db_initialized[d2] - Failed: deliberately f...
            1 failed, 1 passed in 0.12s

        第一次调用 ``db == "DB1"`` 通过，而第二次调用 ``db == "DB2"`` 失败。我们的 ``db`` 夹具函数在设置阶段实例化了每个 DB 值，而 ``pytest_generate_tests`` 在收集阶段生成了两次对 ``test_db_initialized`` 的调用。

    .. tab:: 英文

        .. regendoc:wipe

        The parametrization of test functions happens at collection
        time.  It is a good idea to setup expensive resources like DB
        connections or subprocess only when the actual test is run.
        Here is a simple example how you can achieve that. This test
        requires a ``db`` object fixture:

        .. code-block:: python

            # content of test_backends.py

            import pytest


            def test_db_initialized(db):
                # a dummy test
                if db.__class__.__name__ == "DB2":
                    pytest.fail("deliberately failing for demo purposes")

        We can now add a test configuration that generates two invocations of
        the ``test_db_initialized`` function and also implements a factory that
        creates a database object for the actual test invocations:

        .. code-block:: python

            # content of conftest.py
            import pytest


            def pytest_generate_tests(metafunc):
                if "db" in metafunc.fixturenames:
                    metafunc.parametrize("db", ["d1", "d2"], indirect=True)


            class DB1:
                "one database object"


            class DB2:
                "alternative database object"


            @pytest.fixture
            def db(request):
                if request.param == "d1":
                    return DB1()
                elif request.param == "d2":
                    return DB2()
                else:
                    raise ValueError("invalid internal test config")

        Let's first see how it looks like at collection time:

        .. code-block:: pytest

            $ pytest test_backends.py --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            <Dir parametrize.rst-204>
            <Module test_backends.py>
                <Function test_db_initialized[d1]>
                <Function test_db_initialized[d2]>

            ======================== 2 tests collected in 0.12s ========================

        And then when we run the test:

        .. code-block:: pytest

            $ pytest -q test_backends.py
            .F                                                                   [100%]
            ================================= FAILURES =================================
            _________________________ test_db_initialized[d2] __________________________

            db = <conftest.DB2 object at 0xdeadbeef0001>

                def test_db_initialized(db):
                    # a dummy test
                    if db.__class__.__name__ == "DB2":
            >           pytest.fail("deliberately failing for demo purposes")
            E           Failed: deliberately failing for demo purposes

            test_backends.py:8: Failed
            ========================= short test summary info ==========================
            FAILED test_backends.py::test_db_initialized[d2] - Failed: deliberately f...
            1 failed, 1 passed in 0.12s

        The first invocation with ``db == "DB1"`` passed while the second with ``db == "DB2"`` failed.  Our ``db`` fixture function has instantiated each of the DB values during the setup phase while the ``pytest_generate_tests`` generated two according calls to the ``test_db_initialized`` during the collection phase.

间接参数化
---------------------------------------------------

**Indirect parametrization**

.. tabs::

    .. tab:: 中文

        使用 ``indirect=True`` 参数进行测试参数化时，可以在将值传递给测试之前，通过夹具参数化测试：

        .. code-block:: python

            import pytest


            @pytest.fixture
            def fixt(request):
                return request.param * 3


            @pytest.mark.parametrize("fixt", ["a", "b"], indirect=True)
            def test_indirect(fixt):
                assert len(fixt) == 3

        这可以用来在测试运行时在夹具中进行更昂贵的设置，而不是在收集阶段执行这些设置步骤。

    .. tab:: 英文

        Using the ``indirect=True`` parameter when parametrizing a test allows to
        parametrize a test with a fixture receiving the values before passing them to a
        test:

        .. code-block:: python

            import pytest


            @pytest.fixture
            def fixt(request):
                return request.param * 3


            @pytest.mark.parametrize("fixt", ["a", "b"], indirect=True)
            def test_indirect(fixt):
                assert len(fixt) == 3

        This can be used, for example, to do more expensive setup at test run time in
        the fixture, rather than having to run those setup steps at collection time.

.. regendoc:wipe

对特定参数应用间接参数化
---------------------------------------------------

**Apply indirect on particular arguments**

.. tabs::

    .. tab:: 中文

        参数化通常使用多个参数名。可以对特定参数应用 ``indirect`` 参数。通过将参数名的列表或元组传递给 ``indirect`` 来实现。在下面的示例中，有一个函数 ``test_indirect`` 使用了两个夹具：``x`` 和 ``y``。这里我们将夹具 ``x`` 的名称列表传递给 indirect 参数。这个参数只会应用于该参数，而值 ``a`` 将传递给相应的夹具函数：

        .. code-block:: python

            # content of test_indirect_list.py

            import pytest


            @pytest.fixture(scope="function")
            def x(request):
                return request.param * 3


            @pytest.fixture(scope="function")
            def y(request):
                return request.param * 2


            @pytest.mark.parametrize("x, y", [("a", "b")], indirect=["x"])
            def test_indirect(x, y):
                assert x == "aaa"
                assert y == "b"

        这个测试的结果将是成功的：

        .. code-block:: pytest

            $ pytest -v test_indirect_list.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 1 item

            test_indirect_list.py::test_indirect[a-b] PASSED                     [100%]

            ============================ 1 passed in 0.12s =============================

    .. tab:: 英文

        Very often parametrization uses more than one argument name. There is opportunity to apply ``indirect``
        parameter on particular arguments. It can be done by passing list or tuple of
        arguments' names to ``indirect``. In the example below there is a function ``test_indirect`` which uses
        two fixtures: ``x`` and ``y``. Here we give to indirect the list, which contains the name of the
        fixture ``x``. The indirect parameter will be applied to this argument only, and the value ``a``
        will be passed to respective fixture function:

        .. code-block:: python

            # content of test_indirect_list.py

            import pytest


            @pytest.fixture(scope="function")
            def x(request):
                return request.param * 3


            @pytest.fixture(scope="function")
            def y(request):
                return request.param * 2


            @pytest.mark.parametrize("x, y", [("a", "b")], indirect=["x"])
            def test_indirect(x, y):
                assert x == "aaa"
                assert y == "b"

        The result of this test will be successful:

        .. code-block:: pytest

            $ pytest -v test_indirect_list.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 1 item

            test_indirect_list.py::test_indirect[a-b] PASSED                     [100%]

            ============================ 1 passed in 0.12s =============================

.. regendoc:wipe

通过每个类配置参数化测试方法
--------------------------------------------------------------

**Parametrizing test methods through per-class configuration**

.. _`unittest parametrizer`: https://github.com/testing-cabal/unittest-ext/blob/master/params.py

.. tabs::

    .. tab:: 中文

        这是一个实现了参数化方案的 ``pytest_generate_tests`` 函数，类似于 Michael Foord 的 `unittest parametrizer`_，但代码量要少得多：

        .. code-block:: python

            # content of ./test_parametrize.py
            import pytest


            def pytest_generate_tests(metafunc):
                # 每个测试函数调用一次
                funcarglist = metafunc.cls.params[metafunc.function.__name__]
                argnames = sorted(funcarglist[0])
                metafunc.parametrize(
                    argnames, [[funcargs[name] for name in argnames] for funcargs in funcarglist]
                )


            class TestClass:
                # 显示每个测试方法的多个参数集的映射
                params = {
                    "test_equals": [dict(a=1, b=2), dict(a=3, b=3)],
                    "test_zerodivision": [dict(a=1, b=0)],
                }

                def test_equals(self, a, b):
                    assert a == b

                def test_zerodivision(self, a, b):
                    with pytest.raises(ZeroDivisionError):
                        a / b

        我们的测试生成器查找一个类级别的定义，指定每个测试函数使用的参数集。让我们运行它：

        .. code-block:: pytest

            $ pytest -q
            F..                                                                  [100%]
            ================================= FAILURES =================================
            ________________________ TestClass.test_equals[1-2] ________________________

            self = <test_parametrize.TestClass object at 0xdeadbeef0002>, a = 1, b = 2

                def test_equals(self, a, b):
            >       assert a == b
            E       assert 1 == 2

            test_parametrize.py:21: AssertionError
            ========================= short test summary info ==========================
            FAILED test_parametrize.py::TestClass::test_equals[1-2] - assert 1 == 2
            1 failed, 2 passed in 0.12s

    .. tab:: 英文

        Here is an example ``pytest_generate_tests`` function implementing a
        parametrization scheme similar to Michael Foord's `unittest
        parametrizer`_ but in a lot less code:

        .. code-block:: python

            # content of ./test_parametrize.py
            import pytest


            def pytest_generate_tests(metafunc):
                # called once per each test function
                funcarglist = metafunc.cls.params[metafunc.function.__name__]
                argnames = sorted(funcarglist[0])
                metafunc.parametrize(
                    argnames, [[funcargs[name] for name in argnames] for funcargs in funcarglist]
                )


            class TestClass:
                # a map specifying multiple argument sets for a test method
                params = {
                    "test_equals": [dict(a=1, b=2), dict(a=3, b=3)],
                    "test_zerodivision": [dict(a=1, b=0)],
                }

                def test_equals(self, a, b):
                    assert a == b

                def test_zerodivision(self, a, b):
                    with pytest.raises(ZeroDivisionError):
                        a / b

        Our test generator looks up a class-level definition which specifies which
        argument sets to use for each test function.  Let's run it:

        .. code-block:: pytest

            $ pytest -q
            F..                                                                  [100%]
            ================================= FAILURES =================================
            ________________________ TestClass.test_equals[1-2] ________________________

            self = <test_parametrize.TestClass object at 0xdeadbeef0002>, a = 1, b = 2

                def test_equals(self, a, b):
            >       assert a == b
            E       assert 1 == 2

            test_parametrize.py:21: AssertionError
            ========================= short test summary info ==========================
            FAILED test_parametrize.py::TestClass::test_equals[1-2] - assert 1 == 2
            1 failed, 2 passed in 0.12s

使用多个装置进行参数化
--------------------------------------

**Parametrization with multiple fixtures**

.. tabs::

    .. tab:: 中文

        这是一个简化的现实生活示例，使用参数化测试来测试对象在不同 Python 解释器之间的序列化。我们定义了一个 ``test_basic_objects`` 函数，针对其三个参数运行不同的参数集：

        * ``python1``: 第一个 Python 解释器，用于将对象 pickle-dump 到文件
        * ``python2``: 第二个解释器，用于从文件 pickle-load 对象
        * ``obj``: 要被转储/加载的对象

        .. literalinclude:: multipython.py

        运行该测试时，如果我们没有安装所有的 Python 解释器，会有一些跳过的测试，否则将运行所有组合（3 个解释器 × 3 个解释器 × 3 个要序列化/反序列化的对象）：

        .. code-block:: pytest

            . $ pytest -rs -q multipython.py
            ssssssssssss...ssssssssssss                                          [100%]
            ========================= short test summary info ==========================
            SKIPPED [12] multipython.py:67: 'python3.9' not found
            SKIPPED [12] multipython.py:67: 'python3.11' not found
            3 passed, 24 skipped in 0.12s

    .. tab:: 英文

        Here is a stripped down real-life example of using parametrized
        testing for testing serialization of objects between different python
        interpreters.  We define a ``test_basic_objects`` function which
        is to be run with different sets of arguments for its three arguments:

        * ``python1``: first python interpreter, run to pickle-dump an object to a file
        * ``python2``: second interpreter, run to pickle-load an object from a file
        * ``obj``: object to be dumped/loaded

        .. literalinclude:: multipython.py

        Running it results in some skips if we don't have all the python interpreters installed and otherwise runs all combinations (3 interpreters times 3 interpreters times 3 objects to serialize/deserialize):

        .. code-block:: pytest

            . $ pytest -rs -q multipython.py
            ssssssssssss...ssssssssssss                                          [100%]
            ========================= short test summary info ==========================
            SKIPPED [12] multipython.py:67: 'python3.9' not found
            SKIPPED [12] multipython.py:67: 'python3.11' not found
            3 passed, 24 skipped in 0.12s

可选实现/导入的参数化
---------------------------------------------------

**Parametrization of optional implementations/imports**

.. tabs::

    .. tab:: 中文

        如果你想比较多个实现同一 API 的结果，可以编写测试函数，这些函数接收已经导入的实现，并在实现不可导入/不可用时跳过测试。假设我们有一个“基础”实现，而其他（可能是优化过的实现）需要提供相似的结果：

        .. code-block:: python

            # content of conftest.py

            import pytest


            @pytest.fixture(scope="session")
            def basemod(request):
                return pytest.importorskip("base")


            @pytest.fixture(scope="session", params=["opt1", "opt2"])
            def optmod(request):
                return pytest.importorskip(request.param)

        然后是一个简单函数的基础实现：

        .. code-block:: python

            # content of base.py
            def func1():
                return 1

        以及一个优化版本：

        .. code-block:: python

            # content of opt1.py
            def func1():
                return 1.0001

        最后是一个小的测试模块：

        .. code-block:: python

            # content of test_module.py

            def test_func1(basemod, optmod):
                assert round(basemod.func1(), 3) == round(optmod.func1(), 3)

        如果你运行这个并启用跳过报告：

        .. code-block:: pytest

            $ pytest -rs test_module.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py .s                                                    [100%]

            ========================= short test summary info ==========================
            SKIPPED [1] test_module.py:3: could not import 'opt2': No module named 'opt2'
            ======================= 1 passed, 1 skipped in 0.12s =======================

        你会看到我们没有 ``opt2`` 模块，因此我们的 ``test_func1`` 的第二次测试运行被跳过。几点说明：

        - ``conftest.py`` 文件中的 fixture 函数是“会话级别”的，因为我们只需要导入一次。

        - 如果你有多个测试函数和一个被跳过的导入，你会看到报告中的 ``[1]`` 计数增加。

        - 你可以在测试函数上使用 :ref:`@pytest.mark.parametrize <@pytest.mark.parametrize>` 风格的参数化来对输入/输出值进行参数化。

    .. tab:: 英文

        If you want to compare the outcomes of several implementations of a given
        API, you can write test functions that receive the already imported implementations
        and get skipped in case the implementation is not importable/available.  Let's
        say we have a "base" implementation and the other (possibly optimized ones)
        need to provide similar results:

        .. code-block:: python

            # content of conftest.py

            import pytest


            @pytest.fixture(scope="session")
            def basemod(request):
                return pytest.importorskip("base")


            @pytest.fixture(scope="session", params=["opt1", "opt2"])
            def optmod(request):
                return pytest.importorskip(request.param)

        And then a base implementation of a simple function:

        .. code-block:: python

            # content of base.py
            def func1():
                return 1

        And an optimized version:

        .. code-block:: python

            # content of opt1.py
            def func1():
                return 1.0001

        And finally a little test module:

        .. code-block:: python

            # content of test_module.py


            def test_func1(basemod, optmod):
                assert round(basemod.func1(), 3) == round(optmod.func1(), 3)


        If you run this with reporting for skips enabled:

        .. code-block:: pytest

            $ pytest -rs test_module.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py .s                                                    [100%]

            ========================= short test summary info ==========================
            SKIPPED [1] test_module.py:3: could not import 'opt2': No module named 'opt2'
            ======================= 1 passed, 1 skipped in 0.12s =======================

        You'll see that we don't have an ``opt2`` module and thus the second test run of our ``test_func1`` was skipped.  A few notes:

        - the fixture functions in the ``conftest.py`` file are "session-scoped" because we don't need to import more than once

        - if you have multiple test functions and a skipped import, you will see the ``[1]`` count increasing in the report

        - you can put :ref:`@pytest.mark.parametrize <@pytest.mark.parametrize>` style parametrization on the test functions to parametrize input/output values as well.


为单个参数化测试设置标记或测试 ID
--------------------------------------------------------------------

**Set marks or test ID for individual parametrized test**

.. tabs::

    .. tab:: 中文

        使用 ``pytest.param`` 可以将标记或测试 ID 应用到单个参数化测试。例如：

        .. code-block:: python

            # content of test_pytest_param_example.py
            import pytest


            @pytest.mark.parametrize(
                "test_input,expected",
                [
                    ("3+5", 8),
                    pytest.param("1+7", 8, marks=pytest.mark.basic),
                    pytest.param("2+4", 6, marks=pytest.mark.basic, id="basic_2+4"),
                    pytest.param(
                        "6*9", 42, marks=[pytest.mark.basic, pytest.mark.xfail], id="basic_6*9"
                    ),
                ],
            )
            def test_eval(test_input, expected):
                assert eval(test_input) == expected

        在这个例子中，我们有 4 个参数化测试。除了第一个测试外，其余三个参数化测试都使用了自定义标记 ``basic``，而对于第四个测试，我们还使用了内置标记 ``xfail`` 来表示该测试预期会失败。为了清晰起见，我们为某些测试设置了测试 ID。

        然后以详细模式运行 ``pytest``，并只选择 ``basic`` 标记：

        .. code-block:: pytest

            $ pytest -v -m basic
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 24 items / 21 deselected / 3 selected

            test_pytest_param_example.py::test_eval[1+7-8] PASSED                [ 33%]
            test_pytest_param_example.py::test_eval[basic_2+4] PASSED            [ 66%]
            test_pytest_param_example.py::test_eval[basic_6*9] XFAIL             [100%]

            =============== 2 passed, 21 deselected, 1 xfailed in 0.12s ================

        结果如下：

        - 收集了四个测试
        - 一个测试被排除，因为它没有 ``basic`` 标记。
        - 选择了三个带有 ``basic`` 标记的测试。
        - 测试 ``test_eval[1+7-8]`` 通过，但名称是自动生成的，可能会让人困惑。
        - 测试 ``test_eval[basic_2+4]`` 通过。
        - 测试 ``test_eval[basic_6*9]`` 被预期为失败，并且确实失败了。

    .. tab:: 英文

        Use ``pytest.param`` to apply marks or set test ID to individual parametrized test.
        For example:

        .. code-block:: python

            # content of test_pytest_param_example.py
            import pytest


            @pytest.mark.parametrize(
                "test_input,expected",
                [
                    ("3+5", 8),
                    pytest.param("1+7", 8, marks=pytest.mark.basic),
                    pytest.param("2+4", 6, marks=pytest.mark.basic, id="basic_2+4"),
                    pytest.param(
                        "6*9", 42, marks=[pytest.mark.basic, pytest.mark.xfail], id="basic_6*9"
                    ),
                ],
            )
            def test_eval(test_input, expected):
                assert eval(test_input) == expected

        In this example, we have 4 parametrized tests. Except for the first test,
        we mark the rest three parametrized tests with the custom marker ``basic``,
        and for the fourth test we also use the built-in mark ``xfail`` to indicate this
        test is expected to fail. For explicitness, we set test ids for some tests.

        Then run ``pytest`` with verbose mode and with only the ``basic`` marker:

        .. code-block:: pytest

            $ pytest -v -m basic
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 24 items / 21 deselected / 3 selected

            test_pytest_param_example.py::test_eval[1+7-8] PASSED                [ 33%]
            test_pytest_param_example.py::test_eval[basic_2+4] PASSED            [ 66%]
            test_pytest_param_example.py::test_eval[basic_6*9] XFAIL             [100%]

            =============== 2 passed, 21 deselected, 1 xfailed in 0.12s ================

        As the result:

        - Four tests were collected
        - One test was deselected because it doesn't have the ``basic`` mark.
        - Three tests with the ``basic`` mark was selected.
        - The test ``test_eval[1+7-8]`` passed, but the name is autogenerated and confusing.
        - The test ``test_eval[basic_2+4]`` passed.
        - The test ``test_eval[basic_6*9]`` was expected to fail and did fail.

.. _`parametrizing_conditional_raising`:

参数化条件提升
--------------------------------------------------------------------

**Parametrizing conditional raising**

.. tabs::

    .. tab:: 中文

        使用 :func:`pytest.raises` 与 :ref:`pytest.mark.parametrize ref` 装饰器编写参数化测试，其中某些测试会引发异常，而其他测试则不会。

        ``contextlib.nullcontext`` 可用于测试那些不预期引发异常但应返回某个值的情况。该值通过 ``enter_result`` 参数提供，并将作为 ``with`` 语句的目标（在下面的示例中为 ``e``）。

        例如：

        .. code-block:: python

            from contextlib import nullcontext

            import pytest


            @pytest.mark.parametrize(
                "example_input,expectation",
                [
                    (3, nullcontext(2)),
                    (2, nullcontext(3)),
                    (1, nullcontext(6)),
                    (0, pytest.raises(ZeroDivisionError)),
                ],
            )
            def test_division(example_input, expectation):
                """测试我对除法的了解。"""
                with expectation as e:
                    assert (6 / example_input) == e

        在上述示例中，前三个测试用例应正常运行而不引发任何异常，而第四个测试用例应引发 ``ZeroDivisionError`` 异常，这符合 pytest 的预期。

    .. tab:: 英文

        Use :func:`pytest.raises` with the
        :ref:`pytest.mark.parametrize ref` decorator to write parametrized tests
        in which some tests raise exceptions and others do not.

        ``contextlib.nullcontext`` can be used to test cases that are not expected to
        raise exceptions but that should result in some value. The value is given as the
        ``enter_result`` parameter, which will be available as the ``with`` statement’s
        target (``e`` in the example below).

        For example:

        .. code-block:: python

            from contextlib import nullcontext

            import pytest


            @pytest.mark.parametrize(
                "example_input,expectation",
                [
                    (3, nullcontext(2)),
                    (2, nullcontext(3)),
                    (1, nullcontext(6)),
                    (0, pytest.raises(ZeroDivisionError)),
                ],
            )
            def test_division(example_input, expectation):
                """Test how much I know division."""
                with expectation as e:
                    assert (6 / example_input) == e

        In the example above, the first three test cases should run without any
        exceptions, while the fourth should raise a ``ZeroDivisionError`` exception,
        which is expected by pytest.
