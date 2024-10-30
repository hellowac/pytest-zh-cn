:tocdepth: 3

.. _`api-reference`:

API 参考
=============

**API Reference**

.. tabs::

    .. tab:: 中文

        本页包含 pytest API 的完整参考。

    .. tab:: 英文

        This page contains the full reference to pytest's API.


常量
---------

**Constants**

pytest.__version__
~~~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        当前 pytest 版本，以字符串形式::

            >>> import pytest
            >>> pytest.__version__
            '7.0.0'

    .. tab:: 英文

        The current pytest version, as a string::

            >>> import pytest
            >>> pytest.__version__
            '7.0.0'


.. _`version-tuple`:

pytest.version_tuple
~~~~~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        .. versionadded:: 7.0

        当前的 pytest 版本，以元组形式表示::

            >>> import pytest
            >>> pytest.version_tuple
            (7, 0, 0)

        对于预发行版本，最后一个组件将是一个字符串，表示预发行版本::

            >>> import pytest
            >>> pytest.version_tuple
            (7, 0, '0rc1')

    .. tab:: 英文

        .. versionadded:: 7.0

        The current pytest version, as a tuple::

            >>> import pytest
            >>> pytest.version_tuple
            (7, 0, 0)

        For pre-releases, the last component will be a string with the prerelease version::

            >>> import pytest
            >>> pytest.version_tuple
            (7, 0, '0rc1')


函数
---------

**Functions**

pytest.approx
~~~~~~~~~~~~~

.. autofunction:: pytest.approx

pytest.fail
~~~~~~~~~~~

**Tutorial**: :ref:`skipping`

.. autofunction:: pytest.fail(reason, [pytrace=True])

.. class:: pytest.fail.Exception

    The exception raised by :func:`pytest.fail`.

pytest.skip
~~~~~~~~~~~

.. autofunction:: pytest.skip(reason, [allow_module_level=False])

.. class:: pytest.skip.Exception

    The exception raised by :func:`pytest.skip`.

.. _`pytest.importorskip ref`:

pytest.importorskip
~~~~~~~~~~~~~~~~~~~

.. autofunction:: pytest.importorskip

pytest.xfail
~~~~~~~~~~~~

.. autofunction:: pytest.xfail

.. class:: pytest.xfail.Exception

    The exception raised by :func:`pytest.xfail`.

pytest.exit
~~~~~~~~~~~

.. autofunction:: pytest.exit(reason, [returncode=None])

.. class:: pytest.exit.Exception

    The exception raised by :func:`pytest.exit`.

pytest.main
~~~~~~~~~~~

**Tutorial**: :ref:`pytest.main-usage`

.. autofunction:: pytest.main

pytest.param
~~~~~~~~~~~~

.. autofunction:: pytest.param(*values, [id], [marks])

pytest.raises
~~~~~~~~~~~~~

**Tutorial**: :ref:`assertraises`

.. autofunction:: pytest.raises(expected_exception: Exception [, *, match])
    :with: excinfo

pytest.deprecated_call
~~~~~~~~~~~~~~~~~~~~~~

**Tutorial**: :ref:`ensuring_function_triggers`

.. autofunction:: pytest.deprecated_call([match])
    :with:

pytest.register_assert_rewrite
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Tutorial**: :ref:`assertion-rewriting`

.. autofunction:: pytest.register_assert_rewrite

pytest.warns
~~~~~~~~~~~~

**Tutorial**: :ref:`assertwarnings`

.. autofunction:: pytest.warns(expected_warning: Exception, [match])
    :with:

pytest.freeze_includes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Tutorial**: :ref:`freezing-pytest`

.. autofunction:: pytest.freeze_includes

.. _`marks ref`:

Marks
-----

.. tabs::

    .. tab:: 中文

        标记可以用于为 *测试函数*（但不能用于 fixtures）应用元数据，这些元数据可以被 fixtures 或插件访问。

    .. tab:: 英文

        Marks can be used to apply metadata to *test functions* (but not fixtures), which can then be accessed by fixtures or plugins.




.. _`pytest.mark.filterwarnings ref`:

pytest.mark.filterwarnings
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        **教程**: :ref:`filterwarnings`

        为标记的测试项添加警告过滤器。

        .. py:function:: pytest.mark.filterwarnings(filter)

            :keyword str filter:
                一个 *警告规格字符串*，由元组 ``(action, message, category, module, lineno)`` 的内容组成，
                这些内容在 Python 文档的 :ref:`python:warning-filter` 部分中有说明，用 ``":"`` 分隔。可选字段可以省略。
                传递用于过滤的模块名称不会进行正则表达式转义。

                例如:

                .. code-block:: python

                    @pytest.mark.filterwarnings("ignore:.*usage will be deprecated.*:DeprecationWarning")
                    def test_foo(): ...

    .. tab:: 英文

        **Tutorial**: :ref:`filterwarnings`

        Add warning filters to marked test items.

        .. py:function:: pytest.mark.filterwarnings(filter)

            :keyword str filter:
                A *warning specification string*, which is composed of contents of the tuple ``(action, message, category, module, lineno)``
                as specified in :ref:`python:warning-filter` section of
                the Python documentation, separated by ``":"``. Optional fields can be omitted.
                Module names passed for filtering are not regex-escaped.

                For example:

                .. code-block:: python

                    @pytest.mark.filterwarnings("ignore:.*usage will be deprecated.*:DeprecationWarning")
                    def test_foo(): ...


.. _`pytest.mark.parametrize ref`:

pytest.mark.parametrize
~~~~~~~~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        **教程**: :ref:`parametrize`

        此标记与 :py:meth:`pytest.Metafunc.parametrize` 的签名相同；请参见该部分。

    .. tab:: 英文

        **Tutorial**: :ref:`parametrize`

        This mark has the same signature as :py:meth:`pytest.Metafunc.parametrize`; see there.


.. _`pytest.mark.skip ref`:

pytest.mark.skip
~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        **教程**: :ref:`skip`

        无条件跳过测试函数。

        .. py:function:: pytest.mark.skip(reason=None)

            :keyword str reason: 跳过测试函数的原因。

    .. tab:: 英文

        **Tutorial**: :ref:`skip`

        Unconditionally skip a test function.

        .. py:function:: pytest.mark.skip(reason=None)

            :keyword str reason: Reason why the test function is being skipped.


.. _`pytest.mark.skipif ref`:

pytest.mark.skipif
~~~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        **教程**: :ref:`skipif`

        如果条件为 ``True``，则跳过测试函数。

        .. py:function:: pytest.mark.skipif(condition, *, reason=None)

            :type condition: bool or str
            :param condition: 如果条件应该被跳过则为 ``True/False``，或者是一个 :ref:`条件字符串 <string conditions>`。
            :keyword str reason: 跳过测试函数的原因。

    .. tab:: 英文

        **Tutorial**: :ref:`skipif`

        Skip a test function if a condition is ``True``.

        .. py:function:: pytest.mark.skipif(condition, *, reason=None)

            :type condition: bool or str
            :param condition: ``True/False`` if the condition should be skipped or a :ref:`condition string <string conditions>`.
            :keyword str reason: Reason why the test function is being skipped.


.. _`pytest.mark.usefixtures ref`:

pytest.mark.usefixtures
~~~~~~~~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        **教程**: :ref:`usefixtures`

        将测试函数标记为使用给定的 fixture 名称。

        .. py:function:: pytest.mark.usefixtures(*names)

            :param args: 要使用的 fixture 名称，以字符串形式表示。

        .. note::

            在钩子中使用 `usefixtures` 时，仅在测试设置之前应用于测试函数时可以加载 fixtures
            （例如在 `pytest_collection_modifyitems` 钩子中）。

            还要注意，此标记对 **fixtures** 的应用没有效果。

    .. tab:: 英文

        **Tutorial**: :ref:`usefixtures`

        Mark a test function as using the given fixture names.

        .. py:function:: pytest.mark.usefixtures(*names)

            :param args: The names of the fixture to use, as strings.

        .. note::

            When using `usefixtures` in hooks, it can only load fixtures when applied to a test function before test setup
            (for example in the `pytest_collection_modifyitems` hook).

            Also note that this mark has no effect when applied to **fixtures**.



.. _`pytest.mark.xfail ref`:

pytest.mark.xfail
~~~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        **教程**: :ref:`xfail`

        将测试函数标记为 *预期失败*。

        .. py:function:: pytest.mark.xfail(condition=False, *, reason=None, raises=None, run=True, strict=xfail_strict)

            :keyword Union[bool, str] condition:
                标记测试函数为 xfail 的条件（``True/False`` 或一个
                :ref:`条件字符串 <string conditions>`）。如果是 ``bool``，还必须指定 ``reason`` （见 :ref:`条件字符串 <string conditions>`）。
            :keyword str reason:
                测试函数被标记为 xfail 的原因。
            :keyword raises:
                预期由测试函数引发的异常类（或类元组）；其他异常将导致测试失败。
                注意，传入类的子类也会匹配（类似于 ``except`` 语句的工作方式）。
            :type raises: Type[:py:exc:`Exception`]

            :keyword bool run:
                测试函数是否应该实际执行。如果为 ``False``，函数将始终 xfail，并且不会执行（如果函数发生段错误，这很有用）。
            :keyword bool strict:
                * 如果为 ``False``，则如果函数失败，将在终端输出中显示为 ``xfailed``，如果函数通过，则显示为 ``xpass``。在这两种情况下，这不会导致测试套件整体失败。这对于标记 *不稳定* 测试（随机失败的测试）特别有用，以便稍后处理。
                * 如果为 ``True``，则如果函数失败，将在终端输出中显示为 ``xfailed``，但如果意外通过，则将 **导致** 测试套件失败。这对于标记总是失败的函数特别有用，如果它们意外开始通过，应该有明确的指示（例如，库的新版本修复了已知的 bug）。

                默认为 :confval:`xfail_strict`，默认值为 ``False``。

    .. tab:: 英文

        **Tutorial**: :ref:`xfail`

        Marks a test function as *expected to fail*.

        .. py:function:: pytest.mark.xfail(condition=False, *, reason=None, raises=None, run=True, strict=xfail_strict)

            :keyword Union[bool, str] condition:
                Condition for marking the test function as xfail (``True/False`` or a
                :ref:`condition string <string conditions>`). If a ``bool``, you also have
                to specify ``reason`` (see :ref:`condition string <string conditions>`).
            :keyword str reason:
                Reason why the test function is marked as xfail.
            :keyword raises:
                Exception class (or tuple of classes) expected to be raised by the test function; other exceptions will fail the test.
                Note that subclasses of the classes passed will also result in a match (similar to how the ``except`` statement works).
            :type raises: Type[:py:exc:`Exception`]

            :keyword bool run:
                Whether the test function should actually be executed. If ``False``, the function will always xfail and will
                not be executed (useful if a function is segfaulting).
            :keyword bool strict:
                * If ``False`` the function will be shown in the terminal output as ``xfailed`` if it fails
                  and as ``xpass`` if it passes. In both cases this will not cause the test suite to fail as a whole. This
                  is particularly useful to mark *flaky* tests (tests that fail at random) to be tackled later.
                * If ``True``, the function will be shown in the terminal output as ``xfailed`` if it fails, but if it
                  unexpectedly passes then it will **fail** the test suite. This is particularly useful to mark functions
                  that are always failing and there should be a clear indication if they unexpectedly start to pass (for example
                  a new release of a library fixes a known bug).

                Defaults to :confval:`xfail_strict`, which is ``False`` by default.


自定义marks
~~~~~~~~~~~~

**Custom marks**

.. tabs::

    .. tab:: 中文

        标记是使用工厂对象 ``pytest.mark`` 动态创建的，并作为装饰器应用。

        例如:

        .. code-block:: python

            @pytest.mark.timeout(10, "slow", method="thread")
            def test_function(): ...

        将创建并附加一个 :class:`Mark <pytest.Mark>` 对象到收集的
        :class:`Item <pytest.Item>`，然后可以通过 fixtures 或 hooks 使用
        :meth:`Node.iter_markers <_pytest.nodes.Node.iter_markers>` 访问该对象。 ``mark`` 对象将具有以下属性：

        .. code-block:: python

            mark.args == (10, "slow")
            mark.kwargs == {"method": "thread"}

        使用多个自定义标记的示例：

        .. code-block:: python

            @pytest.mark.timeout(10, "slow", method="thread")
            @pytest.mark.slow
            def test_function(): ...

        当使用 :meth:`Node.iter_markers <_pytest.nodes.Node.iter_markers>` 或
        :meth:`Node.iter_markers_with_node <_pytest.nodes.Node.iter_markers_with_node>` 处理多个标记时，离函数最近的标记将优先被迭代。上述示例将导致 ``@pytest.mark.slow`` 紧接着 ``@pytest.mark.timeout(...)``。

    .. tab:: 英文

        Marks are created dynamically using the factory object ``pytest.mark`` and applied as a decorator.

        For example:

        .. code-block:: python

            @pytest.mark.timeout(10, "slow", method="thread")
            def test_function(): ...

        Will create and attach a :class:`Mark <pytest.Mark>` object to the collected
        :class:`Item <pytest.Item>`, which can then be accessed by fixtures or hooks with
        :meth:`Node.iter_markers <_pytest.nodes.Node.iter_markers>`. The ``mark`` object will have the following attributes:

        .. code-block:: python

            mark.args == (10, "slow")
            mark.kwargs == {"method": "thread"}

        Example for using multiple custom markers:

        .. code-block:: python

            @pytest.mark.timeout(10, "slow", method="thread")
            @pytest.mark.slow
            def test_function(): ...

        When :meth:`Node.iter_markers <_pytest.nodes.Node.iter_markers>` or :meth:`Node.iter_markers_with_node <_pytest.nodes.Node.iter_markers_with_node>` is used with multiple markers, the marker closest to the function will be iterated over first. The above example will result in ``@pytest.mark.slow`` followed by ``@pytest.mark.timeout(...)``.

.. _`fixtures-api`:

Fixtures
--------

.. tabs::

    .. tab:: 中文

        **教程**: :ref:`fixture`

        Fixtures 通过将其声明为参数名，由测试函数或其他 fixtures 请求。

        需要 fixture 的测试示例：

        .. code-block:: python

            def test_output(capsys):
                print("hello")
                out, err = capsys.readouterr()
                assert out == "hello\n"

        需要另一个 fixture 的 fixture 示例：

        .. code-block:: python

            @pytest.fixture
            def db_session(tmp_path):
                fn = tmp_path / "db.file"
                return connect(fn)

        有关更多详细信息，请参阅完整的 :ref:`fixtures 文档 <fixture>`。

    .. tab:: 英文

        **Tutorial**: :ref:`fixture`

        Fixtures are requested by test functions or other fixtures by declaring them as argument names.


        Example of a test requiring a fixture:

        .. code-block:: python

            def test_output(capsys):
                print("hello")
                out, err = capsys.readouterr()
                assert out == "hello\n"


        Example of a fixture requiring another fixture:

        .. code-block:: python

            @pytest.fixture
            def db_session(tmp_path):
                fn = tmp_path / "db.file"
                return connect(fn)

        For more details, consult the full :ref:`fixtures docs <fixture>`.


.. _`pytest.fixture-api`:

@pytest.fixture
~~~~~~~~~~~~~~~

.. autofunction:: pytest.fixture
    :decorator:


.. fixture:: capfd

capfd
~~~~~~

**Tutorial**: :ref:`captures`

.. autofunction:: _pytest.capture.capfd()
    :no-auto-options:


.. fixture:: capfdbinary

capfdbinary
~~~~~~~~~~~~

**Tutorial**: :ref:`captures`

.. autofunction:: _pytest.capture.capfdbinary()
    :no-auto-options:


.. fixture:: caplog

caplog
~~~~~~

**Tutorial**: :ref:`logging`

.. autofunction:: _pytest.logging.caplog()
    :no-auto-options:

    Returns a :class:`pytest.LogCaptureFixture` instance.

.. autoclass:: pytest.LogCaptureFixture()
    :members:


.. fixture:: capsys

capsys
~~~~~~

**Tutorial**: :ref:`captures`

.. autofunction:: _pytest.capture.capsys()
    :no-auto-options:

.. autoclass:: pytest.CaptureFixture()
    :members:

.. fixture:: capsysbinary

capsysbinary
~~~~~~~~~~~~

**Tutorial**: :ref:`captures`

.. autofunction:: _pytest.capture.capsysbinary()
    :no-auto-options:


.. fixture:: cache

config.cache
~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        **教程**: :ref:`cache`

        ``config.cache`` 对象允许其他插件和 fixtures 在测试运行之间存储和检索值。要从 fixtures 中访问它，请将 ``pytestconfig`` 请求到您的 fixture 中，并通过 ``pytestconfig.cache`` 获取。

        在底层, cache 插件使用 :py:mod:`json` 标准库模块的简单 ``dumps``/``loads`` API。

        ``config.cache`` 是 :class:`pytest.Cache` 的一个实例：

        .. autoclass:: pytest.Cache()
        :members:

        .. fixture:: doctest_namespace

    .. tab:: 英文

        **Tutorial**: :ref:`cache`

        The ``config.cache`` object allows other plugins and fixtures
        to store and retrieve values across test runs. To access it from fixtures
        request ``pytestconfig`` into your fixture and get it with ``pytestconfig.cache``.

        Under the hood, the cache plugin uses the simple
        ``dumps``/``loads`` API of the :py:mod:`json` stdlib module.

        ``config.cache`` is an instance of :class:`pytest.Cache`:

        .. autoclass:: pytest.Cache()
        :members:


        .. fixture:: doctest_namespace

doctest_namespace
~~~~~~~~~~~~~~~~~

**Tutorial**: :ref:`doctest`

.. autofunction:: _pytest.doctest.doctest_namespace()


.. fixture:: monkeypatch

monkeypatch
~~~~~~~~~~~

**Tutorial**: :ref:`monkeypatching`

.. autofunction:: _pytest.monkeypatch.monkeypatch()
    :no-auto-options:

    Returns a :class:`~pytest.MonkeyPatch` instance.

.. autoclass:: pytest.MonkeyPatch
    :members:


.. fixture:: pytestconfig

pytestconfig
~~~~~~~~~~~~

.. autofunction:: _pytest.fixtures.pytestconfig()


.. fixture:: pytester

pytester
~~~~~~~~

.. tabs::

    .. tab:: 中文

        .. versionadded:: 6.2

        提供一个 :class:`~pytest.Pytester` 实例，可以用来运行和测试 pytest 本身。

        它提供了一个空目录，pytest 可以在隔离环境中执行，并包含编写测试、配置文件以及与预期输出匹配的工具。

        要使用它，请在您的最上层 ``conftest.py`` 文件中包含：

        .. code-block:: python

            pytest_plugins = "pytester"



        .. autoclass:: pytest.Pytester()
            :members:

        .. autoclass:: pytest.RunResult()
            :members:

        .. autoclass:: pytest.LineMatcher()
            :members:
            :special-members: __str__

        .. autoclass:: pytest.HookRecorder()
            :members:

        .. autoclass:: pytest.RecordedHookCall()
            :members:


        .. fixture:: record_property

    .. tab:: 英文

        .. versionadded:: 6.2

        Provides a :class:`~pytest.Pytester` instance that can be used to run and test pytest itself.

        It provides an empty directory where pytest can be executed in isolation, and contains facilities
        to write tests, configuration files, and match against expected output.

        To use it, include in your topmost ``conftest.py`` file:

        .. code-block:: python

            pytest_plugins = "pytester"



        .. autoclass:: pytest.Pytester()
            :members:

        .. autoclass:: pytest.RunResult()
            :members:

        .. autoclass:: pytest.LineMatcher()
            :members:
            :special-members: __str__

        .. autoclass:: pytest.HookRecorder()
            :members:

        .. autoclass:: pytest.RecordedHookCall()
            :members:


        .. fixture:: record_property

record_property
~~~~~~~~~~~~~~~~~~~

**Tutorial**: :ref:`record_property example`

.. autofunction:: _pytest.junitxml.record_property()


.. fixture:: record_testsuite_property

record_testsuite_property
~~~~~~~~~~~~~~~~~~~~~~~~~

**Tutorial**: :ref:`record_testsuite_property example`

.. autofunction:: _pytest.junitxml.record_testsuite_property()


.. fixture:: recwarn

recwarn
~~~~~~~

**Tutorial**: :ref:`recwarn`

.. autofunction:: _pytest.recwarn.recwarn()
    :no-auto-options:

.. autoclass:: pytest.WarningsRecorder()
    :members:
    :special-members: __getitem__, __iter__, __len__


.. fixture:: request

request
~~~~~~~

.. tabs::

    .. tab:: 中文

        **示例**: :ref:`request example`

        ``request`` 固件是一个特殊的固件，提供请求测试函数的信息。

        .. autoclass:: pytest.FixtureRequest()
            :members:


        .. fixture:: testdir

    .. tab:: 英文

        **Example**: :ref:`request example`

        The ``request`` fixture is a special fixture providing information of the requesting test function.

        .. autoclass:: pytest.FixtureRequest()
            :members:


        .. fixture:: testdir

testdir
~~~~~~~

.. tabs::

    .. tab:: 中文

        与 :fixture:`pytester` 相同，但在适用时提供返回传统的 ``py.path.local`` 对象的实例方法。

        新代码应避免使用 :fixture:`testdir`，而应使用 :fixture:`pytester`。

        .. autoclass:: pytest.Testdir()
            :members:
            :noindex: TimeoutExpired


        .. fixture:: tmp_path

    .. tab:: 英文

        Identical to :fixture:`pytester`, but provides an instance whose methods return
        legacy ``py.path.local`` objects instead when applicable.

        New code should avoid using :fixture:`testdir` in favor of :fixture:`pytester`.

        .. autoclass:: pytest.Testdir()
            :members:
            :noindex: TimeoutExpired


        .. fixture:: tmp_path

tmp_path
~~~~~~~~

**Tutorial**: :ref:`tmp_path`

.. autofunction:: _pytest.tmpdir.tmp_path()
    :no-auto-options:


.. fixture:: tmp_path_factory

tmp_path_factory
~~~~~~~~~~~~~~~~

**Tutorial**: :ref:`tmp_path_factory example`

.. _`tmp_path_factory factory api`:

``tmp_path_factory`` is an instance of :class:`~pytest.TempPathFactory`:

.. autoclass:: pytest.TempPathFactory()
    :members:


.. fixture:: tmpdir

tmpdir
~~~~~~

**Tutorial**: :ref:`tmpdir and tmpdir_factory`

.. autofunction:: _pytest.legacypath.LegacyTmpdirPlugin.tmpdir()
    :no-auto-options:


.. fixture:: tmpdir_factory

tmpdir_factory
~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        **教程**: :ref:`tmpdir and tmpdir_factory`

        ``tmpdir_factory`` 是 :class:`~pytest.TempdirFactory` 的一个实例：

        .. autoclass:: pytest.TempdirFactory()
            :members:

    .. tab:: 英文

        **Tutorial**: :ref:`tmpdir and tmpdir_factory`

        ``tmpdir_factory`` is an instance of :class:`~pytest.TempdirFactory`:

        .. autoclass:: pytest.TempdirFactory()
            :members:


.. _`hook-reference`:

Hooks
-----

.. tabs::

    .. tab:: 中文

        **教程**: :ref:`writing-plugins`

        参考所有可以通过 :ref:`conftest.py 文件 <localplugin>` 和 :ref:`插件 <plugins>` 实现的钩子。

    .. tab:: 英文

        **Tutorial**: :ref:`writing-plugins`

        Reference to all hooks which can be implemented by :ref:`conftest.py files <localplugin>` and :ref:`plugins <plugins>`.

@pytest.hookimpl
~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        .. function:: pytest.hookimpl
            :decorator:

            pytest 的装饰器用于将函数标记为钩子实现。

            见 :ref:`writinghooks` 和 :func:`pluggy.HookimplMarker`.



    .. tab:: 英文

        .. function:: pytest.hookimpl
            :decorator:

            pytest's decorator for marking functions as hook implementations.

            See :ref:`writinghooks` and :func:`pluggy.HookimplMarker`.

@pytest.hookspec
~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        .. function:: pytest.hookspec
            :decorator:

            pytest 的装饰器用于将函数标记为钩子实现。

            见 :ref:`declaringhooks` and :func:`pluggy.HookspecMarker`.

        .. currentmodule:: _pytest.hookspec

    .. tab:: 英文

        .. function:: pytest.hookspec
            :decorator:

            pytest's decorator for marking functions as hook specifications.

            See :ref:`declaringhooks` and :func:`pluggy.HookspecMarker`.

        .. currentmodule:: _pytest.hookspec

引导钩子
~~~~~~~~~~~~~~~~~~~

**Bootstrapping hooks**

.. tabs::

    .. tab:: 中文

        引导钩子用于早期注册的插件（内部和第三方插件）。

        .. hook:: pytest_load_initial_conftests  
        .. autofunction:: pytest_load_initial_conftests  
        .. hook:: pytest_cmdline_parse  
        .. autofunction:: pytest_cmdline_parse  
        .. hook:: pytest_cmdline_main  
        .. autofunction:: pytest_cmdline_main  

    .. tab:: 英文

        Bootstrapping hooks called for plugins registered early enough (internal and third-party plugins).

        .. hook:: pytest_load_initial_conftests
        .. autofunction:: pytest_load_initial_conftests
        .. hook:: pytest_cmdline_parse
        .. autofunction:: pytest_cmdline_parse
        .. hook:: pytest_cmdline_main
        .. autofunction:: pytest_cmdline_main

.. _`initialization-hooks`:

初始化钩子
~~~~~~~~~~~~~~~~~~~~

**Initialization hooks**

.. tabs::

    .. tab:: 中文

        初始化挂钩调用插件和 ``conftest.py`` 文件。

        .. hook:: pytest_addoption
        .. autofunction:: pytest_addoption
        .. hook:: pytest_addhooks
        .. autofunction:: pytest_addhooks
        .. hook:: pytest_configure
        .. autofunction:: pytest_configure
        .. hook:: pytest_unconfigure
        .. autofunction:: pytest_unconfigure
        .. hook:: pytest_sessionstart
        .. autofunction:: pytest_sessionstart
        .. hook:: pytest_sessionfinish
        .. autofunction:: pytest_sessionfinish

        .. hook:: pytest_plugin_registered
        .. autofunction:: pytest_plugin_registered

    .. tab:: 英文

        Initialization hooks called for plugins and ``conftest.py`` files.

        .. hook:: pytest_addoption
        .. autofunction:: pytest_addoption
        .. hook:: pytest_addhooks
        .. autofunction:: pytest_addhooks
        .. hook:: pytest_configure
        .. autofunction:: pytest_configure
        .. hook:: pytest_unconfigure
        .. autofunction:: pytest_unconfigure
        .. hook:: pytest_sessionstart
        .. autofunction:: pytest_sessionstart
        .. hook:: pytest_sessionfinish
        .. autofunction:: pytest_sessionfinish

        .. hook:: pytest_plugin_registered
        .. autofunction:: pytest_plugin_registered

收集钩子
~~~~~~~~~~~~~~~~

**Collection hooks**

.. tabs::

    .. tab:: 中文

        ``pytest`` 调用以下钩子来收集文件和目录：

        .. hook:: pytest_collection  
        .. autofunction:: pytest_collection  
        .. hook:: pytest_ignore_collect  
        .. autofunction:: pytest_ignore_collect  
        .. hook:: pytest_collect_directory  
        .. autofunction:: pytest_collect_directory  
        .. hook:: pytest_collect_file  
        .. autofunction:: pytest_collect_file  
        .. hook:: pytest_pycollect_makemodule  
        .. autofunction:: pytest_pycollect_makemodule  

        要影响 Python 模块中对象的收集，可以使用以下钩子：

        .. hook:: pytest_pycollect_makeitem  
        .. autofunction:: pytest_pycollect_makeitem  
        .. hook:: pytest_generate_tests  
        .. autofunction:: pytest_generate_tests  
        .. hook:: pytest_make_parametrize_id  
        .. autofunction:: pytest_make_parametrize_id  

        影响测试跳过的钩子：

        .. hook:: pytest_markeval_namespace  
        .. autofunction:: pytest_markeval_namespace  

        收集完成后，您可以修改项目的顺序，删除或以其他方式修改测试项目：

        .. hook:: pytest_collection_modifyitems  
        .. autofunction:: pytest_collection_modifyitems  

        .. note::

            如果此钩子在 ``conftest.py`` 文件中实现，它始终接收所有收集的项目，而不仅仅是
            在实现它的 ``conftest.py`` 下的项目。

        .. autofunction:: pytest_collection_finish  

    .. tab:: 英文

        ``pytest`` calls the following hooks for collecting files and directories:

        .. hook:: pytest_collection
        .. autofunction:: pytest_collection
        .. hook:: pytest_ignore_collect
        .. autofunction:: pytest_ignore_collect
        .. hook:: pytest_collect_directory
        .. autofunction:: pytest_collect_directory
        .. hook:: pytest_collect_file
        .. autofunction:: pytest_collect_file
        .. hook:: pytest_pycollect_makemodule
        .. autofunction:: pytest_pycollect_makemodule

        For influencing the collection of objects in Python modules
        you can use the following hook:

        .. hook:: pytest_pycollect_makeitem
        .. autofunction:: pytest_pycollect_makeitem
        .. hook:: pytest_generate_tests
        .. autofunction:: pytest_generate_tests
        .. hook:: pytest_make_parametrize_id
        .. autofunction:: pytest_make_parametrize_id

        Hooks for influencing test skipping:

        .. hook:: pytest_markeval_namespace
        .. autofunction:: pytest_markeval_namespace

        After collection is complete, you can modify the order of
        items, delete or otherwise amend the test items:

        .. hook:: pytest_collection_modifyitems
        .. autofunction:: pytest_collection_modifyitems

        .. note::
            If this hook is implemented in ``conftest.py`` files, it always receives all collected items, not only those
            under the ``conftest.py`` where it is implemented.

        .. autofunction:: pytest_collection_finish

测试运行钩子
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Test running (runtest) hooks**

.. tabs::

    .. tab:: 中文

        所有与运行测试相关的钩子接收一个 :py:class:`pytest.Item <pytest.Item>` 对象。

        .. hook:: pytest_runtestloop  
        .. autofunction:: pytest_runtestloop  
        .. hook:: pytest_runtest_protocol  
        .. autofunction:: pytest_runtest_protocol  
        .. hook:: pytest_runtest_logstart  
        .. autofunction:: pytest_runtest_logstart  
        .. hook:: pytest_runtest_logfinish  
        .. autofunction:: pytest_runtest_logfinish  
        .. hook:: pytest_runtest_setup  
        .. autofunction:: pytest_runtest_setup  
        .. hook:: pytest_runtest_call  
        .. autofunction:: pytest_runtest_call  
        .. hook:: pytest_runtest_teardown  
        .. autofunction:: pytest_runtest_teardown  
        .. hook:: pytest_runtest_makereport  
        .. autofunction:: pytest_runtest_makereport  

        要深入理解，您可以查看 ``_pytest.runner`` 中这些钩子的默认实现，也可以查看 ``_pytest.pdb``，它与 ``_pytest.capture`` 和其输入/输出捕获交互，以便在测试失败时立即进入交互式调试。

    .. tab:: 英文

        All runtest related hooks receive a :py:class:`pytest.Item <pytest.Item>` object.

        .. hook:: pytest_runtestloop
        .. autofunction:: pytest_runtestloop
        .. hook:: pytest_runtest_protocol
        .. autofunction:: pytest_runtest_protocol
        .. hook:: pytest_runtest_logstart
        .. autofunction:: pytest_runtest_logstart
        .. hook:: pytest_runtest_logfinish
        .. autofunction:: pytest_runtest_logfinish
        .. hook:: pytest_runtest_setup
        .. autofunction:: pytest_runtest_setup
        .. hook:: pytest_runtest_call
        .. autofunction:: pytest_runtest_call
        .. hook:: pytest_runtest_teardown
        .. autofunction:: pytest_runtest_teardown
        .. hook:: pytest_runtest_makereport
        .. autofunction:: pytest_runtest_makereport

        For deeper understanding you may look at the default implementation of
        these hooks in ``_pytest.runner`` and maybe also
        in ``_pytest.pdb`` which interacts with ``_pytest.capture``
        and its input/output capturing in order to immediately drop
        into interactive debugging when a test failure occurs.

.. hook:: pytest_pyfunc_call
.. autofunction:: pytest_pyfunc_call

报告钩子
~~~~~~~~~~~~~~~

**Reporting hooks**

.. tabs::

    .. tab:: 中文

        与会话相关的报告钩子：

        .. hook:: pytest_collectstart  
        .. autofunction:: pytest_collectstart  
        .. hook:: pytest_make_collect_report  
        .. autofunction:: pytest_make_collect_report  
        .. hook:: pytest_itemcollected  
        .. autofunction:: pytest_itemcollected  
        .. hook:: pytest_collectreport  
        .. autofunction:: pytest_collectreport  
        .. hook:: pytest_deselected  
        .. autofunction:: pytest_deselected  
        .. hook:: pytest_report_header  
        .. autofunction:: pytest_report_header  
        .. hook:: pytest_report_collectionfinish  
        .. autofunction:: pytest_report_collectionfinish  
        .. hook:: pytest_report_teststatus  
        .. autofunction:: pytest_report_teststatus  
        .. hook:: pytest_report_to_serializable  
        .. autofunction:: pytest_report_to_serializable  
        .. hook:: pytest_report_from_serializable  
        .. autofunction:: pytest_report_from_serializable  
        .. hook:: pytest_terminal_summary  
        .. autofunction:: pytest_terminal_summary  
        .. hook:: pytest_fixture_setup  
        .. autofunction:: pytest_fixture_setup  
        .. hook:: pytest_fixture_post_finalizer  
        .. autofunction:: pytest_fixture_post_finalizer  
        .. hook:: pytest_warning_recorded  
        .. autofunction:: pytest_warning_recorded  

        用于报告测试执行的中心钩子：

        .. hook:: pytest_runtest_logreport  
        .. autofunction:: pytest_runtest_logreport  

        与断言相关的钩子：

        .. hook:: pytest_assertrepr_compare  
        .. autofunction:: pytest_assertrepr_compare  
        .. hook:: pytest_assertion_pass  
        .. autofunction:: pytest_assertion_pass  

    .. tab:: 英文

        Session related reporting hooks:

        .. hook:: pytest_collectstart
        .. autofunction:: pytest_collectstart
        .. hook:: pytest_make_collect_report
        .. autofunction:: pytest_make_collect_report
        .. hook:: pytest_itemcollected
        .. autofunction:: pytest_itemcollected
        .. hook:: pytest_collectreport
        .. autofunction:: pytest_collectreport
        .. hook:: pytest_deselected
        .. autofunction:: pytest_deselected
        .. hook:: pytest_report_header
        .. autofunction:: pytest_report_header
        .. hook:: pytest_report_collectionfinish
        .. autofunction:: pytest_report_collectionfinish
        .. hook:: pytest_report_teststatus
        .. autofunction:: pytest_report_teststatus
        .. hook:: pytest_report_to_serializable
        .. autofunction:: pytest_report_to_serializable
        .. hook:: pytest_report_from_serializable
        .. autofunction:: pytest_report_from_serializable
        .. hook:: pytest_terminal_summary
        .. autofunction:: pytest_terminal_summary
        .. hook:: pytest_fixture_setup
        .. autofunction:: pytest_fixture_setup
        .. hook:: pytest_fixture_post_finalizer
        .. autofunction:: pytest_fixture_post_finalizer
        .. hook:: pytest_warning_recorded
        .. autofunction:: pytest_warning_recorded

        Central hook for reporting about test execution:

        .. hook:: pytest_runtest_logreport
        .. autofunction:: pytest_runtest_logreport

        Assertion related hooks:

        .. hook:: pytest_assertrepr_compare
        .. autofunction:: pytest_assertrepr_compare
        .. hook:: pytest_assertion_pass
        .. autofunction:: pytest_assertion_pass


调试/交互钩子
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Debugging/Interaction hooks

.. tabs::

    .. tab:: 中文

        有一些钩子可以用于特殊报告或异常交互：

    .. tab:: 英文

        There are few hooks which can be used for special
        reporting or interaction with exceptions:

.. hook:: pytest_internalerror
.. autofunction:: pytest_internalerror
.. hook:: pytest_keyboard_interrupt
.. autofunction:: pytest_keyboard_interrupt
.. hook:: pytest_exception_interact
.. autofunction:: pytest_exception_interact
.. hook:: pytest_enter_pdb
.. autofunction:: pytest_enter_pdb
.. hook:: pytest_leave_pdb
.. autofunction:: pytest_leave_pdb


收集树对象
-----------------------

**Collection tree objects**

.. tabs::

    .. tab:: 中文

        这些是构成收集树的收集器和项目类（统称为“节点”）。

    .. tab:: 英文

        These are the collector and item classes (collectively called "nodes") which make up the collection tree.

Node
~~~~

.. autoclass:: _pytest.nodes.Node()
    :members:
    :show-inheritance:

Collector
~~~~~~~~~

.. autoclass:: pytest.Collector()
    :members:
    :show-inheritance:

Item
~~~~

.. autoclass:: pytest.Item()
    :members:
    :show-inheritance:

File
~~~~

.. autoclass:: pytest.File()
    :members:
    :show-inheritance:

FSCollector
~~~~~~~~~~~

.. autoclass:: _pytest.nodes.FSCollector()
    :members:
    :show-inheritance:

Session
~~~~~~~

.. autoclass:: pytest.Session()
    :members:
    :show-inheritance:

Package
~~~~~~~

.. autoclass:: pytest.Package()
    :members:
    :show-inheritance:

Module
~~~~~~

.. autoclass:: pytest.Module()
    :members:
    :show-inheritance:

Class
~~~~~

.. autoclass:: pytest.Class()
    :members:
    :show-inheritance:

Function
~~~~~~~~

.. autoclass:: pytest.Function()
    :members:
    :show-inheritance:

FunctionDefinition
~~~~~~~~~~~~~~~~~~

.. autoclass:: _pytest.python.FunctionDefinition()
    :members:
    :show-inheritance:


对象
-------

**Objects**

.. tabs::

    .. tab:: 中文

        可从 :ref:`fixtures <fixture>` 或 :ref:`hooks <hook-reference>` 访问或从 ``pytest`` 导入的对象。

    .. tab:: 英文

        Objects accessible from :ref:`fixtures <fixture>` or :ref:`hooks <hook-reference>` or importable from ``pytest``.


CallInfo
~~~~~~~~

.. autoclass:: pytest.CallInfo()
    :members:

CollectReport
~~~~~~~~~~~~~

.. autoclass:: pytest.CollectReport()
    :members:
    :show-inheritance:
    :inherited-members:

Config
~~~~~~

.. autoclass:: pytest.Config()
    :members:

Dir
~~~

.. autoclass:: pytest.Dir()
    :members:

Directory
~~~~~~~~~

.. autoclass:: pytest.Directory()
    :members:

ExceptionInfo
~~~~~~~~~~~~~

.. autoclass:: pytest.ExceptionInfo()
    :members:


ExitCode
~~~~~~~~

.. autoclass:: pytest.ExitCode
    :members:


FixtureDef
~~~~~~~~~~

.. autoclass:: pytest.FixtureDef()
    :members:
    :show-inheritance:

MarkDecorator
~~~~~~~~~~~~~

.. autoclass:: pytest.MarkDecorator()
    :members:


MarkGenerator
~~~~~~~~~~~~~

.. autoclass:: pytest.MarkGenerator()
    :members:


Mark
~~~~

.. autoclass:: pytest.Mark()
    :members:


Metafunc
~~~~~~~~

.. autoclass:: pytest.Metafunc()
    :members:

Parser
~~~~~~

.. autoclass:: pytest.Parser()
    :members:

OptionGroup
~~~~~~~~~~~

.. autoclass:: pytest.OptionGroup()
    :members:

PytestPluginManager
~~~~~~~~~~~~~~~~~~~

.. autoclass:: pytest.PytestPluginManager()
    :members:
    :undoc-members:
    :inherited-members:
    :show-inheritance:

TerminalReporter
~~~~~~~~~~~~~~~~

.. autoclass:: pytest.TerminalReporter
    :members:
    :inherited-members:

TestReport
~~~~~~~~~~

.. autoclass:: pytest.TestReport()
    :members:
    :show-inheritance:
    :inherited-members:

TestShortLogReport
~~~~~~~~~~~~~~~~~~

.. autoclass:: pytest.TestShortLogReport()
    :members:

Result
~~~~~~~

.. tabs::

    .. tab:: 中文

        在 :ref:`hook 包装器 <hookwrapper>` 中使用的结果对象，请参阅 :py:class:`pluggy 文档中的 Result <pluggy.Result>` 以了解更多信息。

    .. tab:: 英文

        Result object used within :ref:`hook wrappers <hookwrapper>`, see :py:class:`Result in the pluggy documentation <pluggy.Result>` for more information.

Stash
~~~~~

.. autoclass:: pytest.Stash
    :special-members: __setitem__, __getitem__, __delitem__, __contains__, __len__
    :members:

.. autoclass:: pytest.StashKey
    :show-inheritance:
    :members:


全局变量
----------------

**Global Variables**

.. tabs::

    .. tab:: 中文

        pytest 对在测试模块或 ``conftest.py`` 文件中定义的一些全局变量采取特殊处理。

        .. globalvar:: collect_ignore

        **教程**: :ref:`customizing-test-collection`

        可以在 *conftest.py 文件* 中声明，以排除测试目录或模块。
        需要是一个路径的列表（``str``, :class:`pathlib.Path` 或任何 :class:`os.PathLike`）。

        .. code-block:: python

        collect_ignore = ["setup.py"]


        .. globalvar:: collect_ignore_glob

        **教程**: :ref:`customizing-test-collection`

        可以在 *conftest.py 文件* 中声明，以使用 Unix shell 风格的通配符排除测试目录或模块。
        需要是 ``list[str]``，其中 ``str`` 可以包含通配模式。

        .. code-block:: python

        collect_ignore_glob = ["*_ignore.py"]


        .. globalvar:: pytest_plugins

        **教程**: :ref:`available installable plugins`

        可以在 *测试模块* 和 *conftest.py 文件* 的 **全局** 级别声明，以注册附加插件。
        可以是一个 ``str`` 或 ``Sequence[str]``。

        .. code-block:: python

            pytest_plugins = "myapp.testsupport.myplugin"

        .. code-block:: python

            pytest_plugins = ("myapp.testsupport.tools", "myapp.testsupport.regression")


        .. globalvar:: pytestmark

        **教程**: :ref:`scoped-marking`

        可以在 *测试模块* 的 **全局** 级别声明，以将一个或多个 :ref:`marks <marks ref>` 应用到所有测试函数和方法。
        可以是单个标记或标记列表（按从左到右的顺序应用）。

        .. code-block:: python

            import pytest

            pytestmark = pytest.mark.webtest


        .. code-block:: python

            import pytest

            pytestmark = [pytest.mark.integration, pytest.mark.slow]

    .. tab:: 英文

        pytest treats some global variables in a special manner when defined in a test module or ``conftest.py`` files.


        .. globalvar:: collect_ignore

        **Tutorial**: :ref:`customizing-test-collection`

        Can be declared in *conftest.py files* to exclude test directories or modules.
        Needs to be a list of paths (``str``, :class:`pathlib.Path` or any :class:`os.PathLike`).

        .. code-block:: python

        collect_ignore = ["setup.py"]


        .. globalvar:: collect_ignore_glob

        **Tutorial**: :ref:`customizing-test-collection`

        Can be declared in *conftest.py files* to exclude test directories or modules
        with Unix shell-style wildcards. Needs to be ``list[str]`` where ``str`` can
        contain glob patterns.

        .. code-block:: python

        collect_ignore_glob = ["*_ignore.py"]


        .. globalvar:: pytest_plugins

        **Tutorial**: :ref:`available installable plugins`

        Can be declared at the **global** level in *test modules* and *conftest.py files* to register additional plugins.
        Can be either a ``str`` or ``Sequence[str]``.

        .. code-block:: python

            pytest_plugins = "myapp.testsupport.myplugin"

        .. code-block:: python

            pytest_plugins = ("myapp.testsupport.tools", "myapp.testsupport.regression")


        .. globalvar:: pytestmark

        **Tutorial**: :ref:`scoped-marking`

        Can be declared at the **global** level in *test modules* to apply one or more :ref:`marks <marks ref>` to all
        test functions and methods. Can be either a single mark or a list of marks (applied in left-to-right order).

        .. code-block:: python

            import pytest

            pytestmark = pytest.mark.webtest


        .. code-block:: python

            import pytest

            pytestmark = [pytest.mark.integration, pytest.mark.slow]


环境变量
---------------------

**Environment Variables**

.. tabs::

    .. tab:: 中文

        可以用来改变 pytest 行为的环境变量。

        .. envvar:: CI

        当设置时（无论值如何），pytest 认可它正在 CI 过程中运行。是 ``BUILD_NUMBER`` 变量的替代。另见 :ref:`ci-pipelines`。

        .. envvar:: BUILD_NUMBER

        当设置时（无论值如何），pytest 认可它正在 CI 过程中运行。是 CI 变量的替代。另见 :ref:`ci-pipelines`。

        .. envvar:: PYTEST_ADDOPTS

        这包含一个命令行（由 py:mod:`shlex` 模块解析），将被 **前置** 到用户提供的命令行，更多信息见 :ref:`adding default options`。

        .. envvar:: PYTEST_VERSION

        该环境变量在 pytest 会话开始时定义，之后未定义。
        它包含 ``pytest.__version__`` 的值，除此之外可以用来轻松检查代码是否在 pytest 运行中执行。

        .. envvar:: PYTEST_CURRENT_TEST

        该变量不应该由用户设置，而是由 pytest 内部设置为当前测试的名称，以便其他进程可以检查它，更多信息见 :ref:`pytest current test env`。

        .. envvar:: PYTEST_DEBUG

        当设置时，pytest 将打印跟踪和调试信息。

        .. envvar:: PYTEST_DISABLE_PLUGIN_AUTOLOAD

        当设置时，通过 :std:doc:`entry point packaging
        metadata <packaging:guides/creating-and-discovering-plugins>` 禁用插件自动加载。仅加载明确指定的插件。

        .. envvar:: PYTEST_PLUGINS

        包含应作为插件加载的模块的以逗号分隔的列表：

        .. code-block:: bash

            export PYTEST_PLUGINS=mymodule.plugin,xdist

        .. envvar:: PYTEST_THEME

        设置要用于代码输出的 `pygment style <https://pygments.org/docs/styles/>`_。

        .. envvar:: PYTEST_THEME_MODE

        将 :envvar:`PYTEST_THEME` 设置为 *dark* 或 *light*。

        .. envvar:: PY_COLORS

        当设置为 ``1`` 时，pytest 将在终端输出中使用颜色。
        当设置为 ``0`` 时，pytest 将不使用颜色。
        ``PY_COLORS`` 优先于 ``NO_COLOR`` 和 ``FORCE_COLOR``。

        .. envvar:: NO_COLOR

        当设置为非空字符串（无论值如何）时，pytest 将不在终端输出中使用颜色。
        ``PY_COLORS`` 优先于 ``NO_COLOR``，而 ``NO_COLOR`` 优先于 ``FORCE_COLOR``。
        有关其他支持此社区标准的库，请参见 `no-color.org <https://no-color.org/>`__。

        .. envvar:: FORCE_COLOR

        当设置为非空字符串（无论值如何）时，pytest 将在终端输出中使用颜色。
        ``PY_COLORS`` 和 ``NO_COLOR`` 优先于 ``FORCE_COLOR``。

    .. tab:: 英文

        Environment variables that can be used to change pytest's behavior.

        .. envvar:: CI

        When set (regardless of value), pytest acknowledges that is running in a CI process. Alternative to ``BUILD_NUMBER`` variable. See also :ref:`ci-pipelines`.

        .. envvar:: BUILD_NUMBER

        When set (regardless of value), pytest acknowledges that is running in a CI process. Alternative to CI variable. See also :ref:`ci-pipelines`.

        .. envvar:: PYTEST_ADDOPTS

        This contains a command-line (parsed by the py:mod:`shlex` module) that will be **prepended** to the command line given
        by the user, see :ref:`adding default options` for more information.

        .. envvar:: PYTEST_VERSION

        This environment variable is defined at the start of the pytest session and is undefined afterwards.
        It contains the value of ``pytest.__version__``, and among other things can be used to easily check if a code is running from within a pytest run.

        .. envvar:: PYTEST_CURRENT_TEST

        This is not meant to be set by users, but is set by pytest internally with the name of the current test so other
        processes can inspect it, see :ref:`pytest current test env` for more information.

        .. envvar:: PYTEST_DEBUG

        When set, pytest will print tracing and debug information.

        .. envvar:: PYTEST_DISABLE_PLUGIN_AUTOLOAD

        When set, disables plugin auto-loading through :std:doc:`entry point packaging
        metadata <packaging:guides/creating-and-discovering-plugins>`. Only explicitly
        specified plugins will be loaded.

        .. envvar:: PYTEST_PLUGINS

        Contains comma-separated list of modules that should be loaded as plugins:

        .. code-block:: bash

            export PYTEST_PLUGINS=mymodule.plugin,xdist

        .. envvar:: PYTEST_THEME

        Sets a `pygment style <https://pygments.org/docs/styles/>`_ to use for the code output.

        .. envvar:: PYTEST_THEME_MODE

        Sets the :envvar:`PYTEST_THEME` to be either *dark* or *light*.

        .. envvar:: PY_COLORS

        When set to ``1``, pytest will use color in terminal output.
        When set to ``0``, pytest will not use color.
        ``PY_COLORS`` takes precedence over ``NO_COLOR`` and ``FORCE_COLOR``.

        .. envvar:: NO_COLOR

        When set to a non-empty string (regardless of value), pytest will not use color in terminal output.
        ``PY_COLORS`` takes precedence over ``NO_COLOR``, which takes precedence over ``FORCE_COLOR``.
        See `no-color.org <https://no-color.org/>`__ for other libraries supporting this community standard.

        .. envvar:: FORCE_COLOR

        When set to a non-empty string (regardless of value), pytest will use color in terminal output.
        ``PY_COLORS`` and ``NO_COLOR`` take precedence over ``FORCE_COLOR``.

异常
----------

**Exceptions**

.. autoexception:: pytest.UsageError()
    :show-inheritance:

.. autoexception:: pytest.FixtureLookupError()
    :show-inheritance:

.. _`warnings ref`:

告警
--------

**Warnings**

.. tabs::

    .. tab:: 中文

        在某些情况下（例如不当使用或弃用的功能）会生成自定义警告。

    .. tab:: 英文

        Custom warnings generated in some situations such as improper usage or deprecated features.

.. autoclass:: pytest.PytestWarning
   :show-inheritance:

.. autoclass:: pytest.PytestAssertRewriteWarning
   :show-inheritance:

.. autoclass:: pytest.PytestCacheWarning
   :show-inheritance:

.. autoclass:: pytest.PytestCollectionWarning
   :show-inheritance:

.. autoclass:: pytest.PytestConfigWarning
   :show-inheritance:

.. autoclass:: pytest.PytestDeprecationWarning
   :show-inheritance:

.. autoclass:: pytest.PytestExperimentalApiWarning
   :show-inheritance:

.. autoclass:: pytest.PytestRemovedIn9Warning
  :show-inheritance:

.. autoclass:: pytest.PytestUnknownMarkWarning
   :show-inheritance:

.. autoclass:: pytest.PytestUnraisableExceptionWarning
   :show-inheritance:

.. autoclass:: pytest.PytestUnhandledThreadExceptionWarning
   :show-inheritance:


.. tabs::

    .. tab:: 中文

        有关更多信息，请参阅文档中的 :ref:`internal-warnings` 部分。

    .. tab:: 英文

        Consult the :ref:`internal-warnings` section in the documentation for more information.


.. _`ini options ref`:

配置选项
---------------------

**Configuration Options**

.. tabs::

    .. tab:: 中文

        这是一个内置配置选项的列表，可以写入 ``pytest.ini`` （或 ``.pytest.ini``）、``pyproject.toml``、``tox.ini`` 或 ``setup.cfg`` 文件，通常位于您仓库的根目录。

        要详细了解每种文件格式，请参见 :ref:`config file formats`。

        .. warning::
            除了非常简单的用例外，不推荐使用 ``setup.cfg``。``.cfg`` 文件使用与 ``pytest.ini`` 和 ``tox.ini`` 不同的解析器，这可能会导致难以追踪的问题。
            如果可能，建议使用后者文件或 ``pyproject.toml`` 来保存您的 pytest 配置。

        可以通过使用 ``-o/--override-ini`` 在命令行中覆盖配置选项，也可以多次传递。预期格式为 ``name=value``。例如::

            pytest -o console_output_style=classic -o cache_dir=/tmp/mycache


        .. confval:: addopts

            将指定的 ``OPTS`` 添加到命令行参数集，就好像它们是由用户指定的一样。例如：如果您的 ini 文件内容是：

            .. code-block:: ini

                    # content of pytest.ini
                    [pytest]
                    addopts = --maxfail=2 -rf  # 在 2 次失败后退出，报告失败信息

            发出 ``pytest test_hello.py`` 实际上意味着：

            .. code-block:: bash

                    pytest --maxfail=2 -rf test_hello.py

            默认是不添加任何选项。


        .. confval:: cache_dir

            设置缓存插件内容存储的目录。默认目录为
            ``.pytest_cache``，它在 :ref:`rootdir <rootdir>` 中创建。目录可以是
            相对或绝对路径。如果设置相对路径，则目录相对于 :ref:`rootdir <rootdir>` 创建。此外，路径可以包含环境变量，这些变量将被扩展。有关缓存插件的更多信息，请参阅 :ref:`cache_provider`。

        .. confval:: consider_namespace_packages

            控制 pytest 是否应尝试识别收集 Python 模块时的 `namespace packages <https://packaging.python.org/en/latest/guides/packaging-namespace-packages>`__。
            默认值为 ``False``。

            如果您正在测试的包是命名空间包的一部分，请设置为 ``True``。

            仅支持 `native namespace packages <https://packaging.python.org/en/latest/guides/packaging-namespace-packages/#native-namespace-packages>`__，不打算支持 `legacy namespace packages <https://packaging.python.org/en/latest/guides/packaging-namespace-packages/#legacy-namespace-packages>`__。

            .. versionadded:: 8.1

        .. confval:: console_output_style

            设置运行测试时的控制台输出样式：

            * ``classic``: 经典的 pytest 输出。
            * ``progress``: 类似经典 pytest 输出，但带有进度指示器。
            * ``progress-even-when-capture-no``: 即使 ``capture=no`` 也允许使用进度指示器。
            * ``count``: 类似进度，但显示已完成测试的数量而不是百分比。

            默认值为 ``progress``，但如果您更喜欢经典模式或新模式导致意外问题，可以回退到 ``classic``：

            .. code-block:: ini

                    # content of pytest.ini
                    [pytest]
                    console_output_style = classic


        .. confval:: disable_test_id_escaping_and_forfeit_all_rights_to_community_support

            .. versionadded:: 4.4

            pytest 默认会转义在参数化中使用的任何非 ASCII 字符，因为这样做有几个缺点。
            如果您希望在参数化中使用 Unicode 字符串，并在终端中看到未转义的字符串，请在 ``pytest.ini`` 中使用此选项：

            .. code-block:: ini

                [pytest]
                disable_test_id_escaping_and_forfeit_all_rights_to_community_support = True

            但请注意，这可能会导致不希望的副作用，甚至取决于所使用的操作系统和当前安装的插件而导致错误，因此请自行承担风险。

            默认值：``False``。

            参见 :ref:`parametrizemark`。

        .. confval:: doctest_encoding

            用于解码带有文档字符串的文本文件的默认编码。
            :ref:`参见 pytest 如何处理 doctests <doctest>`。


        .. confval:: doctest_optionflags

            一个或多个来自标准 ``doctest`` 模块的 doctest 标志名称。
            :ref:`参见 pytest 如何处理 doctests <doctest>`。


        .. confval:: empty_parameter_set_mark

            允许选择在参数化中为空参数集的操作。

            * ``skip`` 跳过带有空参数集的测试（默认）
            * ``xfail`` 将带有空参数集的测试标记为 xfail(run=False)
            * ``fail_at_collect`` 如果参数化收集了一个空参数集，则引发异常

            .. code-block:: ini

                # content of pytest.ini
                [pytest]
                empty_parameter_set_mark = xfail

            .. note::

                此选项的默认值计划在未来的版本中更改为 ``xfail``，因为这被认为更不易出错，详细信息请参见 :issue:`3155`。

        .. confval:: faulthandler_timeout

            如果测试运行时间超过 ``X`` 秒（包括 fixture 设置和拆卸），则转储所有线程的回溯。使用 :func:`faulthandler.dump_traceback_later` 函数实现，因此所有相关警告都适用。

            .. code-block:: ini

                # content of pytest.ini
                [pytest]
                faulthandler_timeout=5

            有关更多信息，请参见 :ref:`faulthandler`。

        .. confval:: filterwarnings

            设置应该针对匹配警告采取的过滤器和操作列表。默认情况下，测试会话期间发出的所有警告将在测试会话结束时的总结中显示。

            .. code-block:: ini

                # content of pytest.ini
                [pytest]
                filterwarnings =
                    error
                    ignore::DeprecationWarning

            这告诉 pytest 忽略弃用警告并将所有其他警告转换为错误。有关更多信息，请参见 :ref:`warnings`。

        .. confval:: junit_duration_report

            .. versionadded:: 4.1

            配置持续时间如何记录到 JUnit XML 报告中：

            * ``total`` （默认值）：报告的持续时间包括设置、调用和拆卸时间。
            * ``call``：报告的持续时间仅包括调用时间，不包括设置和拆卸。

            .. code-block:: ini

                [pytest]
                junit_duration_report = call


        .. confval:: junit_family

            .. versionadded:: 4.2
            .. versionchanged:: 6.1
                默认值更改为 ``xunit2``。

            配置生成的 JUnit XML 文件的格式。可能的选项有：

            * ``xunit1`` （或 ``legacy``）：生成旧样式输出，与 xunit 1.0 格式兼容。
            * ``xunit2``：生成 `xunit 2.0 样式输出 <https://github.com/jenkinsci/xunit-plugin/blob/xunit-2.3.2/src/main/resources/org/jenkinsci/plugins/xunit/types/model/xsd/junit-10.xsd>`__，应与最新的 Jenkins 版本更兼容。 **这是默认值**。

            .. code-block:: ini

                [pytest]
                junit_family = xunit2


        .. confval:: junit_logging

            .. versionadded:: 3.5
            .. versionchanged:: 5.4
                添加了 ``log``、 ``all``、 ``out-err`` 选项。

            配置捕获的输出是否应写入 JUnit XML 文件。有效值为：

            * ``log``：仅写入 ``logging`` 捕获的输出。
            * ``system-out``：写入捕获的 ``stdout`` 内容。
            * ``system-err``：写入捕获的 ``stderr`` 内容。
            * ``out-err``：同时写入捕获的 ``stdout`` 和 ``stderr`` 内容。
            * ``all``：写入捕获的 ``logging``、``stdout`` 和 ``stderr`` 内容。
            * ``no`` （默认值）：不写入捕获的输出。

            .. code-block:: ini

                [pytest]
                junit_logging = system-out


        .. confval:: junit_log_passing_tests

            .. versionadded:: 4.6

            如果 ``junit_logging != "no"``，则配置捕获的输出是否应为 **通过** 的测试写入 JUnit XML 文件。默认值为 ``True``。

            .. code-block:: ini

                [pytest]
                junit_log_passing_tests = False


        .. confval:: junit_suite_name

            要设置根测试套件 XML 项目的名称，可以在配置文件中配置 ``junit_suite_name`` 选项：

            .. code-block:: ini

                [pytest]
                junit_suite_name = my_suite


        .. confval:: log_auto_indent

            允许对多行日志消息进行选择性自动缩进。

            支持命令行选项 ``--log-auto-indent [value]`` 和配置选项 ``log_auto_indent = [value]`` 来设置
            所有日志记录的自动缩进行为。

            ``[value]`` 可以是：
                * True 或 "On" - 动态自动缩进多行日志消息
                * False 或 "Off" 或 0 - 不自动缩进多行日志消息（默认行为）
                * [正整数] - 按 [value] 空格自动缩进多行日志消息

            .. code-block:: ini

                [pytest]
                log_auto_indent = False

            支持将关键字参数 ``extra={"auto_indent": [value]}`` 传递给
            ``logging.log()`` 的调用，以指定日志中特定条目的自动缩进行为。``extra`` 关键字参数会覆盖命令行或配置中指定的值。

        .. confval:: log_cli

            在测试运行期间启用日志显示（也称为 :ref:`"实时日志记录" <live_logs>`）。默认值为 ``False``。

            .. code-block:: ini

                [pytest]
                log_cli = True

        .. confval:: log_cli_date_format

            设置一个与 :py:func:`time.strftime` 兼容的字符串，用于格式化实时日志记录中的日期。

            .. code-block:: ini

                [pytest]
                log_cli_date_format = %Y-%m-%d %H:%M:%S

            有关更多信息，请参见 :ref:`live_logs`。

        .. confval:: log_cli_format

            设置一个与 :py:mod:`logging` 兼容的字符串，用于格式化实时日志记录消息。

            .. code-block:: ini

                [pytest]
                log_cli_format = %(asctime)s %(levelname)s %(message)s

            有关更多信息，请参见 :ref:`live_logs`。

        .. confval:: log_cli_level

            设置应为实时日志记录捕获的最低日志消息级别。可以使用整数值或级别名称。

            .. code-block:: ini

                [pytest]
                log_cli_level = INFO

            有关更多信息，请参见 :ref:`live_logs`。

        .. confval:: log_date_format

            设置一个与 :py:func:`time.strftime` 兼容的字符串，用于格式化日志捕获中的日期。

            .. code-block:: ini

                [pytest]
                log_date_format = %Y-%m-%d %H:%M:%S

            有关更多信息，请参见 :ref:`logging`。

        .. confval:: log_file

            设置一个相对于当前工作目录的文件名，用于写入日志消息，此外还可以使用其他活动的日志记录设施。

            .. code-block:: ini

                [pytest]
                log_file = logs/pytest-logs.txt

            有关更多信息，请参见 :ref:`logging`。

        .. confval:: log_file_date_format

            设置一个与 :py:func:`time.strftime` 兼容的字符串，用于格式化日志文件中的日期。

            .. code-block:: ini

                [pytest]
                log_file_date_format = %Y-%m-%d %H:%M:%S

            有关更多信息，请参见 :ref:`logging`。

        .. confval:: log_file_format

            设置一个与 :py:mod:`logging` 兼容的字符串，用于格式化重定向到日志文件的日志消息。

            .. code-block:: ini

                [pytest]
                log_file_format = %(asctime)s %(levelname)s %(message)s

            有关更多信息，请参见 :ref:`logging`。

        .. confval:: log_file_level

            设置应为日志文件捕获的最低日志消息级别。可以使用整数值或级别名称。

            .. code-block:: ini

                [pytest]
                log_file_level = INFO

            有关更多信息，请参见 :ref:`logging`。

        .. confval:: log_format

            设置一个与 :py:mod:`logging` 兼容的字符串，用于格式化捕获的日志消息。

            .. code-block:: ini

                [pytest]
                log_format = %(asctime)s %(levelname)s %(message)s

            有关更多信息，请参见 :ref:`logging`。

        .. confval:: log_level

            设置应为日志捕获的最低日志消息级别。可以使用整数值或级别名称。

            .. code-block:: ini

                [pytest]
                log_level = INFO

            有关更多信息，请参见 :ref:`logging`。

        .. confval:: markers

            当使用 ``--strict-markers`` 或 ``--strict`` 命令行参数时，
            仅允许已知标记——由核心 pytest 或某个插件在代码中定义的标记。

            您可以在此设置中列出其他标记，以将其添加到白名单中，
            在这种情况下，您可能想将 ``--strict-markers`` 添加到 ``addopts``
            中，以避免未来的回归：

            .. code-block:: ini

                [pytest]
                addopts = --strict-markers
                markers =
                    slow
                    serial

            .. note::
                强烈建议使用 ``--strict-markers``。``--strict`` 仅保留用于
                向后兼容，可能会使其他人感到困惑，因为它仅适用于
                标记，而不适用于其他选项。

        .. confval:: minversion

            指定运行测试所需的最低 pytest 版本。

            .. code-block:: ini

                    # content of pytest.ini
                    [pytest]
                    minversion = 3.0  # 如果使用 pytest-2.8 运行，将失败

        .. confval:: norecursedirs

            设置在递归查找测试时要避免的目录基名模式。单个（fnmatch 风格）模式
            应用于目录的基名，以决定是否递归进入它。
            模式匹配字符::

                    *       匹配所有内容
                    ?       匹配任意单个字符
                    [seq]   匹配 seq 中的任意字符
                    [!seq]  匹配不在 seq 中的任意字符

            默认模式为 ``'*.egg'``、``'.*'``、``'_darcs'``、``'build'``、
            ``'CVS'``、``'dist'``、``'node_modules'``、``'venv'``、``'{arch}'``。
            设置 ``norecursedirs`` 会替换默认值。以下是避免某些目录的示例：

            .. code-block:: ini

                    [pytest]
                    norecursedirs = .svn _build tmp*

            这将告诉 ``pytest`` 不要查看典型的 Subversion 或
            sphinx-build 目录或任何以 ``tmp`` 为前缀的目录。

            此外，``pytest`` 将尝试智能识别并忽略
            虚拟环境。任何被认为是虚拟环境根目录的目录
            在测试收集期间将不会被考虑，除非
            给定 ``--collect-in-virtualenv``。还请注意，``norecursedirs``
            优先于 ``--collect-in-virtualenv``；例如，如果您打算
            在与 ``'.*'`` 匹配的基础目录中的虚拟环境中运行测试，
            您 *必须* 在使用
            ``--collect-in-virtualenv`` 标志的同时覆盖 ``norecursedirs``。

        .. confval:: python_classes

            一个或多个名称前缀或通配符样式的模式，用于确定哪些类
            被视为测试收集的对象。通过在模式之间添加空格来搜索多个通配符模式。
            默认情况下，pytest 会将任何以 ``Test`` 开头的类视为测试收集。
            下面是一个示例，演示如何从以 ``Suite`` 结尾的类中收集测试：

            .. code-block:: ini

                [pytest]
                python_classes = *Suite

            请注意，``unittest.TestCase`` 派生的类始终会被收集
            ，无论此选项如何，因为使用 ``unittest`` 自身的收集框架来收集这些测试。


        .. confval:: python_files

            一个或多个通配符样式的文件模式，用于确定哪些 Python 文件
            被视为测试模块。通过在模式之间添加空格来搜索多个通配符模式：

            .. code-block:: ini

                [pytest]
                python_files = test_*.py check_*.py example_*.py

            或每行一个：

            .. code-block:: ini

                [pytest]
                python_files =
                    test_*.py
                    check_*.py
                    example_*.py

            默认情况下，匹配 ``test_*.py`` 和 ``*_test.py`` 的文件将被视为
            测试模块。


        .. confval:: python_functions

            一个或多个名称前缀或通配符模式，用于确定哪些测试函数
            和方法被视为测试。通过在模式之间添加空格来搜索多个通配符模式。
            默认情况下，pytest 会将任何以 ``test`` 开头的函数视为测试。
            下面是一个示例，演示如何收集以 ``_test`` 结尾的测试函数和方法：

            .. code-block:: ini

                [pytest]
                python_functions = *_test

            请注意，这对存在于 ``unittest.TestCase`` 派生类上的方法没有影响，
            因为使用 ``unittest`` 自身的收集框架来收集这些测试。

            请参见 :ref:`change naming conventions` 以获取更详细的示例。


        .. confval:: pythonpath

            设置应添加到 Python 搜索路径的目录列表。
            目录将被添加到 :data:`sys.path` 的开头。
            类似于 :envvar:`PYTHONPATH` 环境变量，这些目录将
            包含在 Python 查找导入模块的路径中。
            路径相对于 :ref:`rootdir <rootdir>` 目录。
            目录在测试会话期间将保留在路径中。

            .. code-block:: ini

                [pytest]
                pythonpath = src1 src2


        .. confval:: required_plugins

            一个以空格分隔的插件列表，pytest 运行时必须存在。
            插件可以列出，也可以不带版本说明，直接跟在名称后面。
            不允许不同版本说明之间有空白。
            如果找不到其中任何一个插件，则会发出错误。

            .. code-block:: ini

                [pytest]
                required_plugins = pytest-django>=3.0.0,<4.0.0 pytest-html pytest-xdist>=1.0.0


        .. confval:: testpaths

            设置应在从 :ref:`rootdir <rootdir>` 目录执行 pytest 时
            搜索测试的目录列表，当命令行中未指定特定目录、文件或测试 ID 时。
            文件系统路径可以使用 shell 风格的通配符，包括递归
            ``**`` 模式。

            当所有项目测试都位于已知位置时，这很有用，以加速
            测试收集并避免意外拾取不需要的测试。

            .. code-block:: ini

                [pytest]
                testpaths = testing doc

            该配置意味着执行：

            .. code-block:: console

                pytest

            与执行：

            .. code-block:: console

                pytest testing doc

            有相同的实际效果。

        .. confval:: tmp_path_retention_count

            应根据 `tmp_path_retention_policy` 保留多少个 `tmp_path` 目录会话。

            .. code-block:: ini

                [pytest]
                tmp_path_retention_count = 3

            默认值: ``3``


        .. confval:: tmp_path_retention_policy

            控制由 `tmp_path` 固件创建的哪些目录被保留，
            基于测试结果。

                * `all`: 保留所有测试的目录，无论结果如何。
                * `failed`: 仅保留结果为 `error` 或 `failed` 的测试目录。
                * `none`: 目录在每个测试结束后始终被删除，无论结果如何。

            .. code-block:: ini

                [pytest]
                tmp_path_retention_policy = all

            默认值: ``all``


        .. confval:: truncation_limit_chars

            控制用于截断断言消息内容的最大字符数。

            将值设置为 ``0`` 将禁用截断的字符限制。

            .. code-block:: ini

                [pytest]
                truncation_limit_chars = 640

            pytest 默认将断言消息截断到一定限制，以防止与大量数据比较导致控制台输出过载。

            默认值: ``640``

            .. note::

                如果 pytest 检测到正在 :ref:`运行于 CI <ci-pipelines>`，则会自动禁用截断。


        .. confval:: truncation_limit_lines

            控制用于截断断言消息内容的最大行数。

            将值设置为 ``0`` 将禁用截断的行限制。

            .. code-block:: ini

                [pytest]
                truncation_limit_lines = 8

            pytest 默认将断言消息截断到一定限制，以防止与大量数据比较导致控制台输出过载。

            默认值: ``8``

            .. note::

                如果 pytest 检测到正在 :ref:`运行于 CI <ci-pipelines>`，则会自动禁用截断。


        .. confval:: usefixtures

            将应用于所有测试函数的固件列表；在语义上，这与将 ``@pytest.mark.usefixtures`` 标记应用于所有测试函数是相同的。

            .. code-block:: ini

                [pytest]
                usefixtures =
                    clean_db


        .. confval:: verbosity_assertions

            设置专门用于断言相关输出的详细级别，覆盖应用程序的整体级别。

            .. code-block:: ini

                [pytest]
                verbosity_assertions = 2

            默认为应用程序的整体详细级别（通过 ``-v`` 命令行选项）。可以使用特殊值
            "auto" 来显式使用全局详细级别。


        .. confval:: verbosity_test_cases

            设置专门用于测试用例执行相关输出的详细级别，覆盖应用程序的整体级别。

            .. code-block:: ini

                [pytest]
                verbosity_test_cases = 2

            默认为应用程序的整体详细级别（通过 ``-v`` 命令行选项）。可以使用特殊值
            "auto" 来显式使用全局详细级别。


        .. confval:: xfail_strict

            如果设置为 ``True``，则标记为 ``@pytest.mark.xfail`` 的测试如果实际成功，默认将导致
            测试套件失败。
            更多信息，请参见 :ref:`xfail strict tutorial`。

            .. code-block:: ini

                [pytest]
                xfail_strict = True

    .. tab:: 英文

        Here is a list of builtin configuration options that may be written in a ``pytest.ini`` (or ``.pytest.ini``), ``pyproject.toml``, ``tox.ini``, or ``setup.cfg`` file, usually located at the root of your repository.

        To see each file format in details, see :ref:`config file formats`.

        .. warning::
            Usage of ``setup.cfg`` is not recommended except for very simple use cases. ``.cfg``
            files use a different parser than ``pytest.ini`` and ``tox.ini`` which might cause hard to track
            down problems.
            When possible, it is recommended to use the latter files, or ``pyproject.toml``, to hold your pytest configuration.

        Configuration options may be overwritten in the command-line by using ``-o/--override-ini``, which can also be passed multiple times. The expected format is ``name=value``. For example::

            pytest -o console_output_style=classic -o cache_dir=/tmp/mycache


        .. confval:: addopts

            Add the specified ``OPTS`` to the set of command line arguments as if they
            had been specified by the user. Example: if you have this ini file content:

            .. code-block:: ini

                    # content of pytest.ini
                    [pytest]
                    addopts = --maxfail=2 -rf  # exit after 2 failures, report fail info

            issuing ``pytest test_hello.py`` actually means:

            .. code-block:: bash

                    pytest --maxfail=2 -rf test_hello.py

            Default is to add no options.


        .. confval:: cache_dir

            Sets the directory where the cache plugin's content is stored. Default directory is
            ``.pytest_cache`` which is created in :ref:`rootdir <rootdir>`. Directory may be
            relative or absolute path. If setting relative path, then directory is created
            relative to :ref:`rootdir <rootdir>`. Additionally, a path may contain environment
            variables, that will be expanded. For more information about cache plugin
            please refer to :ref:`cache_provider`.

        .. confval:: consider_namespace_packages

            Controls if pytest should attempt to identify `namespace packages <https://packaging.python.org/en/latest/guides/packaging-namespace-packages>`__
            when collecting Python modules. Default is ``False``.

            Set to ``True`` if the package you are testing is part of a namespace package.

            Only `native namespace packages <https://packaging.python.org/en/latest/guides/packaging-namespace-packages/#native-namespace-packages>`__
            are supported, with no plans to support `legacy namespace packages <https://packaging.python.org/en/latest/guides/packaging-namespace-packages/#legacy-namespace-packages>`__.

            .. versionadded:: 8.1

        .. confval:: console_output_style

            Sets the console output style while running tests:

            * ``classic``: classic pytest output.
            * ``progress``: like classic pytest output, but with a progress indicator.
            * ``progress-even-when-capture-no``: allows the use of the progress indicator even when ``capture=no``.
            * ``count``: like progress, but shows progress as the number of tests completed instead of a percent.

            The default is ``progress``, but you can fallback to ``classic`` if you prefer or
            the new mode is causing unexpected problems:

            .. code-block:: ini

                    # content of pytest.ini
                    [pytest]
                    console_output_style = classic


        .. confval:: disable_test_id_escaping_and_forfeit_all_rights_to_community_support

            .. versionadded:: 4.4

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

            Default: ``False``.

            See :ref:`parametrizemark`.

        .. confval:: doctest_encoding



            Default encoding to use to decode text files with docstrings.
            :ref:`See how pytest handles doctests <doctest>`.


        .. confval:: doctest_optionflags

            One or more doctest flag names from the standard ``doctest`` module.
            :ref:`See how pytest handles doctests <doctest>`.


        .. confval:: empty_parameter_set_mark

            Allows to pick the action for empty parametersets in parameterization

            * ``skip`` skips tests with an empty parameterset (default)
            * ``xfail`` marks tests with an empty parameterset as xfail(run=False)
            * ``fail_at_collect`` raises an exception if parametrize collects an empty parameter set

            .. code-block:: ini

            # content of pytest.ini
            [pytest]
            empty_parameter_set_mark = xfail

            .. note::

            The default value of this option is planned to change to ``xfail`` in future releases
            as this is considered less error prone, see :issue:`3155` for more details.


        .. confval:: faulthandler_timeout

            Dumps the tracebacks of all threads if a test takes longer than ``X`` seconds to run (including
            fixture setup and teardown). Implemented using the :func:`faulthandler.dump_traceback_later` function,
            so all caveats there apply.

            .. code-block:: ini

                    # content of pytest.ini
                    [pytest]
                    faulthandler_timeout=5

            For more information please refer to :ref:`faulthandler`.

        .. confval:: filterwarnings

            Sets a list of filters and actions that should be taken for matched
            warnings. By default all warnings emitted during the test session
            will be displayed in a summary at the end of the test session.

            .. code-block:: ini

                    # content of pytest.ini
                    [pytest]
                    filterwarnings =
                        error
                        ignore::DeprecationWarning

            This tells pytest to ignore deprecation warnings and turn all other warnings
            into errors. For more information please refer to :ref:`warnings`.


        .. confval:: junit_duration_report

            .. versionadded:: 4.1

            Configures how durations are recorded into the JUnit XML report:

            * ``total`` (the default): duration times reported include setup, call, and teardown times.
            * ``call``: duration times reported include only call times, excluding setup and teardown.

            .. code-block:: ini

                [pytest]
                junit_duration_report = call


        .. confval:: junit_family

            .. versionadded:: 4.2
            .. versionchanged:: 6.1
                Default changed to ``xunit2``.

            Configures the format of the generated JUnit XML file. The possible options are:

            * ``xunit1`` (or ``legacy``): produces old style output, compatible with the xunit 1.0 format.
            * ``xunit2``: produces `xunit 2.0 style output <https://github.com/jenkinsci/xunit-plugin/blob/xunit-2.3.2/src/main/resources/org/jenkinsci/plugins/xunit/types/model/xsd/junit-10.xsd>`__, which should be more compatible with latest Jenkins versions.  **This is the default**.

            .. code-block:: ini

                [pytest]
                junit_family = xunit2


        .. confval:: junit_logging

            .. versionadded:: 3.5
            .. versionchanged:: 5.4
                ``log``, ``all``, ``out-err`` options added.

            Configures if captured output should be written to the JUnit XML file. Valid values are:

            * ``log``: write only ``logging`` captured output.
            * ``system-out``: write captured ``stdout`` contents.
            * ``system-err``: write captured ``stderr`` contents.
            * ``out-err``: write both captured ``stdout`` and ``stderr`` contents.
            * ``all``: write captured ``logging``, ``stdout`` and ``stderr`` contents.
            * ``no`` (the default): no captured output is written.

            .. code-block:: ini

                [pytest]
                junit_logging = system-out


        .. confval:: junit_log_passing_tests

            .. versionadded:: 4.6

            If ``junit_logging != "no"``, configures if the captured output should be written
            to the JUnit XML file for **passing** tests. Default is ``True``.

            .. code-block:: ini

                [pytest]
                junit_log_passing_tests = False


        .. confval:: junit_suite_name

            To set the name of the root test suite xml item, you can configure the ``junit_suite_name`` option in your config file:

            .. code-block:: ini

                [pytest]
                junit_suite_name = my_suite

        .. confval:: log_auto_indent

            Allow selective auto-indentation of multiline log messages.

            Supports command line option ``--log-auto-indent [value]``
            and config option ``log_auto_indent = [value]`` to set the
            auto-indentation behavior for all logging.

            ``[value]`` can be:
                * True or "On" - Dynamically auto-indent multiline log messages
                * False or "Off" or 0 - Do not auto-indent multiline log messages (the default behavior)
                * [positive integer] - auto-indent multiline log messages by [value] spaces

            .. code-block:: ini

                [pytest]
                log_auto_indent = False

            Supports passing kwarg ``extra={"auto_indent": [value]}`` to
            calls to ``logging.log()`` to specify auto-indentation behavior for
            a specific entry in the log. ``extra`` kwarg overrides the value specified
            on the command line or in the config.

        .. confval:: log_cli

            Enable log display during test run (also known as :ref:`"live logging" <live_logs>`).
            The default is ``False``.

            .. code-block:: ini

                [pytest]
                log_cli = True

        .. confval:: log_cli_date_format



            Sets a :py:func:`time.strftime`-compatible string that will be used when formatting dates for live logging.

            .. code-block:: ini

                [pytest]
                log_cli_date_format = %Y-%m-%d %H:%M:%S

            For more information, see :ref:`live_logs`.

        .. confval:: log_cli_format



            Sets a :py:mod:`logging`-compatible string used to format live logging messages.

            .. code-block:: ini

                [pytest]
                log_cli_format = %(asctime)s %(levelname)s %(message)s

            For more information, see :ref:`live_logs`.


        .. confval:: log_cli_level



            Sets the minimum log message level that should be captured for live logging. The integer value or
            the names of the levels can be used.

            .. code-block:: ini

                [pytest]
                log_cli_level = INFO

            For more information, see :ref:`live_logs`.


        .. confval:: log_date_format



            Sets a :py:func:`time.strftime`-compatible string that will be used when formatting dates for logging capture.

            .. code-block:: ini

                [pytest]
                log_date_format = %Y-%m-%d %H:%M:%S

            For more information, see :ref:`logging`.


        .. confval:: log_file



            Sets a file name relative to the current working directory where log messages should be written to, in addition
            to the other logging facilities that are active.

            .. code-block:: ini

                [pytest]
                log_file = logs/pytest-logs.txt

            For more information, see :ref:`logging`.


        .. confval:: log_file_date_format



            Sets a :py:func:`time.strftime`-compatible string that will be used when formatting dates for the logging file.

            .. code-block:: ini

                [pytest]
                log_file_date_format = %Y-%m-%d %H:%M:%S

            For more information, see :ref:`logging`.

        .. confval:: log_file_format



            Sets a :py:mod:`logging`-compatible string used to format logging messages redirected to the logging file.

            .. code-block:: ini

                [pytest]
                log_file_format = %(asctime)s %(levelname)s %(message)s

            For more information, see :ref:`logging`.

        .. confval:: log_file_level

            Sets the minimum log message level that should be captured for the logging file. The integer value or
            the names of the levels can be used.

            .. code-block:: ini

                [pytest]
                log_file_level = INFO

            For more information, see :ref:`logging`.


        .. confval:: log_format

            Sets a :py:mod:`logging`-compatible string used to format captured logging messages.

            .. code-block:: ini

                [pytest]
                log_format = %(asctime)s %(levelname)s %(message)s

            For more information, see :ref:`logging`.


        .. confval:: log_level

            Sets the minimum log message level that should be captured for logging capture. The integer value or
            the names of the levels can be used.

            .. code-block:: ini

                [pytest]
                log_level = INFO

            For more information, see :ref:`logging`.


        .. confval:: markers

            When the ``--strict-markers`` or ``--strict`` command-line arguments are used,
            only known markers - defined in code by core pytest or some plugin - are allowed.

            You can list additional markers in this setting to add them to the whitelist,
            in which case you probably want to add ``--strict-markers`` to ``addopts``
            to avoid future regressions:

            .. code-block:: ini

                [pytest]
                addopts = --strict-markers
                markers =
                    slow
                    serial

            .. note::
                The use of ``--strict-markers`` is highly preferred. ``--strict`` was kept for
                backward compatibility only and may be confusing for others as it only applies to
                markers and not to other options.

        .. confval:: minversion

            Specifies a minimal pytest version required for running tests.

            .. code-block:: ini

                    # content of pytest.ini
                    [pytest]
                    minversion = 3.0  # will fail if we run with pytest-2.8


        .. confval:: norecursedirs

            Set the directory basename patterns to avoid when recursing
            for test discovery.  The individual (fnmatch-style) patterns are
            applied to the basename of a directory to decide if to recurse into it.
            Pattern matching characters::

                    *       matches everything
                    ?       matches any single character
                    [seq]   matches any character in seq
                    [!seq]  matches any char not in seq

            Default patterns are ``'*.egg'``, ``'.*'``, ``'_darcs'``, ``'build'``,
            ``'CVS'``, ``'dist'``, ``'node_modules'``, ``'venv'``, ``'{arch}'``.
            Setting a ``norecursedirs`` replaces the default.  Here is an example of
            how to avoid certain directories:

            .. code-block:: ini

                    [pytest]
                    norecursedirs = .svn _build tmp*

            This would tell ``pytest`` to not look into typical subversion or
            sphinx-build directories or into any ``tmp`` prefixed directory.

            Additionally, ``pytest`` will attempt to intelligently identify and ignore
            a virtualenv.  Any directory deemed to be the root of a virtual environment
            will not be considered during test collection unless
            ``--collect-in-virtualenv`` is given.  Note also that ``norecursedirs``
            takes precedence over ``--collect-in-virtualenv``; e.g. if you intend to
            run tests in a virtualenv with a base directory that matches ``'.*'`` you
            *must* override ``norecursedirs`` in addition to using the
            ``--collect-in-virtualenv`` flag.

        .. confval:: python_classes

            One or more name prefixes or glob-style patterns determining which classes
            are considered for test collection. Search for multiple glob patterns by
            adding a space between patterns. By default, pytest will consider any
            class prefixed with ``Test`` as a test collection.  Here is an example of how
            to collect tests from classes that end in ``Suite``:

            .. code-block:: ini

                [pytest]
                python_classes = *Suite

            Note that ``unittest.TestCase`` derived classes are always collected
            regardless of this option, as ``unittest``'s own collection framework is used
            to collect those tests.


        .. confval:: python_files

            One or more Glob-style file patterns determining which python files
            are considered as test modules. Search for multiple glob patterns by
            adding a space between patterns:

            .. code-block:: ini

                    [pytest]
                    python_files = test_*.py check_*.py example_*.py

            Or one per line:

            .. code-block:: ini

                    [pytest]
                    python_files =
                        test_*.py
                        check_*.py
                        example_*.py

            By default, files matching ``test_*.py`` and ``*_test.py`` will be considered
            test modules.


        .. confval:: python_functions

            One or more name prefixes or glob-patterns determining which test functions
            and methods are considered tests. Search for multiple glob patterns by
            adding a space between patterns. By default, pytest will consider any
            function prefixed with ``test`` as a test.  Here is an example of how
            to collect test functions and methods that end in ``_test``:

            .. code-block:: ini

                    [pytest]
                    python_functions = *_test

            Note that this has no effect on methods that live on a ``unittest.TestCase``
            derived class, as ``unittest``'s own collection framework is used
            to collect those tests.

            See :ref:`change naming conventions` for more detailed examples.


        .. confval:: pythonpath

            Sets list of directories that should be added to the python search path.
            Directories will be added to the head of :data:`sys.path`.
            Similar to the :envvar:`PYTHONPATH` environment variable, the directories will be
            included in where Python will look for imported modules.
            Paths are relative to the :ref:`rootdir <rootdir>` directory.
            Directories remain in path for the duration of the test session.

            .. code-block:: ini

                    [pytest]
                    pythonpath = src1 src2


        .. confval:: required_plugins

            A space separated list of plugins that must be present for pytest to run.
            Plugins can be listed with or without version specifiers directly following
            their name. Whitespace between different version specifiers is not allowed.
            If any one of the plugins is not found, emit an error.

            .. code-block:: ini

                [pytest]
                required_plugins = pytest-django>=3.0.0,<4.0.0 pytest-html pytest-xdist>=1.0.0


        .. confval:: testpaths

            Sets list of directories that should be searched for tests when
            no specific directories, files or test ids are given in the command line when
            executing pytest from the :ref:`rootdir <rootdir>` directory.
            File system paths may use shell-style wildcards, including the recursive
            ``**`` pattern.

            Useful when all project tests are in a known location to speed up
            test collection and to avoid picking up undesired tests by accident.

            .. code-block:: ini

                    [pytest]
                    testpaths = testing doc

            This configuration means that executing:

            .. code-block:: console

                pytest

            has the same practical effects as executing:

            .. code-block:: console

                pytest testing doc

        .. confval:: tmp_path_retention_count

            How many sessions should we keep the `tmp_path` directories,
            according to `tmp_path_retention_policy`.

            .. code-block:: ini

                [pytest]
                tmp_path_retention_count = 3

            Default: ``3``


        .. confval:: tmp_path_retention_policy



            Controls which directories created by the `tmp_path` fixture are kept around,
            based on test outcome.

                * `all`: retains directories for all tests, regardless of the outcome.
                * `failed`: retains directories only for tests with outcome `error` or `failed`.
                * `none`: directories are always removed after each test ends, regardless of the outcome.

            .. code-block:: ini

                [pytest]
                tmp_path_retention_policy = all

            Default: ``all``


        .. confval:: truncation_limit_chars

            Controls maximum number of characters to truncate assertion message contents.

            Setting value to ``0`` disables the character limit for truncation.

            .. code-block:: ini

                [pytest]
                truncation_limit_chars = 640

            pytest truncates the assert messages to a certain limit by default to prevent comparison with large data to overload the console output.

            Default: ``640``

            .. note::

                If pytest detects it is :ref:`running on CI <ci-pipelines>`, truncation is disabled automatically.


        .. confval:: truncation_limit_lines

            Controls maximum number of linesto truncate assertion message contents.

            Setting value to ``0`` disables the lines limit for truncation.

            .. code-block:: ini

                [pytest]
                truncation_limit_lines = 8

            pytest truncates the assert messages to a certain limit by default to prevent comparison with large data to overload the console output.

            Default: ``8``

            .. note::

                If pytest detects it is :ref:`running on CI <ci-pipelines>`, truncation is disabled automatically.


        .. confval:: usefixtures

            List of fixtures that will be applied to all test functions; this is semantically the same to apply
            the ``@pytest.mark.usefixtures`` marker to all test functions.


            .. code-block:: ini

                [pytest]
                usefixtures =
                    clean_db


        .. confval:: verbosity_assertions

            Set a verbosity level specifically for assertion related output, overriding the application wide level.

            .. code-block:: ini

                [pytest]
                verbosity_assertions = 2

            Defaults to application wide verbosity level (via the ``-v`` command-line option). A special value of
            "auto" can be used to explicitly use the global verbosity level.


        .. confval:: verbosity_test_cases

            Set a verbosity level specifically for test case execution related output, overriding the application wide level.

            .. code-block:: ini

                [pytest]
                verbosity_test_cases = 2

            Defaults to application wide verbosity level (via the ``-v`` command-line option). A special value of
            "auto" can be used to explicitly use the global verbosity level.


        .. confval:: xfail_strict

            If set to ``True``, tests marked with ``@pytest.mark.xfail`` that actually succeed will by default fail the
            test suite.
            For more information, see :ref:`xfail strict tutorial`.


            .. code-block:: ini

                [pytest]
                xfail_strict = True


.. _`command-line-flags`:

命令行标志
------------------

**Command-line Flags**

.. tabs::

    .. tab:: 中文

        所有命令行标志可以通过运行 ``pytest --help`` 获取::

            $ pytest --help
            使用: pytest [选项] [文件或目录] [文件或目录] [...]

            位置参数:
                文件或目录

            一般:
                -k 表达式             仅运行匹配给定子字符串
                                    表达式的测试。表达式是一个可在 Python 中评估
                                    的表达式，其中所有名称都与测试名称及其父类
                                    进行子字符串匹配。
                                    示例: -k 'test_method or test_other' 匹配所有
                                    名称包含 'test_method' 或 'test_other' 的测试函数和类，
                                    而 -k 'not test_method' 匹配那些名称中不包含
                                    'test_method' 的测试。 -k 'not test_method
                                    and not test_other' 将消除匹配。
                                    此外，关键字也会与包含额外名称的类和
                                    函数进行匹配，这些额外名称在它们的
                                    'extra_keyword_matches' 集中，以及直接
                                    分配给它们的函数名称。匹配不区分大小写。
                -m MARKEXPR           仅运行与给定标记表达式匹配的测试。例如:
                                    -m 'mark1 and not mark2'。
                --markers             显示标记（内置、插件和每个项目的标记）。
                -x, --exitfirst       在第一个错误或失败测试时立即退出
                --fixtures, --funcargs
                                    显示可用的固件，按插件出现顺序排序
                                    （以 '_' 开头的固件仅在 '-v' 时显示）
                --fixtures-per-test   显示每个测试的固件
                --pdb                 在错误或键盘中断时启动交互式 Python 调试器
                --pdbcls=模块名:类名
                                    指定用于 --pdb 的自定义交互式 Python 调试器。
                                    例如:
                                    --pdbcls=IPython.terminal.debugger:TerminalPdb
                --trace               在运行每个测试时立即中断
                --capture=方法        每个测试的捕获方法：fd|sys|no|tee-sys 之一
                -s                    --capture=no 的快捷方式
                --runxfail            将 xfail 测试的结果报告为未标记
                --lf, --last-failed   仅重新运行上次运行中失败的测试（如果没有失败则全部）
                --ff, --failed-first  运行所有测试，但优先运行上次失败的测试。这
                                    可能会重新排序测试，从而导致重复的固件
                                    设置/拆卸。
                --nf, --new-first     优先运行新文件中的测试，然后运行按文件修改时间
                                    排序的其余测试
                --cache-show=[CACHESHOW]
                                    显示缓存内容，不执行收集或
                                    测试。可选参数：通配符（默认：'*'）。
                --cache-clear         在测试运行开始时移除所有缓存内容
                --lfnf={all,none}, --last-failed-no-failures={all,none}
                                    与 ``--lf`` 一起使用时，决定是否在没有
                                    之前（已知）失败或未找到任何缓存的
                                    ``lastfailed`` 数据时执行测试。
                                    ``all`` （默认）再次运行完整的测试套件。
                                    ``none`` 仅发出关于没有已知
                                    失败的消息并成功退出。
                --sw, --stepwise      在测试失败时退出，并在下次从上次失败的
                                    测试继续
                --sw-skip, --stepwise-skip
                                    忽略第一个失败的测试，但在下一个
                                    失败的测试上停止。隐式启用 --stepwise。

            报告:
                --durations=N         显示 N 个最慢的设置/测试持续时间（N=0 显示全部）
                --durations-min=N     包含在最慢列表中的最小持续时间（秒）。默认: 0.005。
                -v, --verbose         增加详细程度
                --no-header           禁用标题
                --no-summary          禁用摘要
                --no-fold-skipped     不在短摘要中折叠被跳过的测试。
                -q, --quiet           减少详细程度
                --verbosity=VERBOSE   设置详细程度。默认: 0。
                -r 字符              显示额外的测试摘要信息，字符包括：
                                    (f)ailed, (E)rror, (s)kipped, (x)failed, (X)passed,
                                    (p)assed, (P)assed with output, (a)ll except passed
                                    (p/P), 或 (A)ll. (w)arnings 默认启用
                                    （见 --disable-warnings），'N' 可用于重置
                                    列表。（默认: 'fE'）。
                --disable-warnings, --disable-pytest-warnings
                                    禁用警告摘要
                -l, --showlocals      在回溯中显示局部变量（默认禁用）
                --no-showlocals       隐藏回溯中的局部变量（否定通过 addopts
                                    传递的 --showlocals）
                --tb=样式            回溯打印模式
                                    (auto/long/short/line/native/no)
                --xfail-tb            显示 xfail 的回溯（只要 --tb != no）
                --show-capture={no,stdout,stderr,log,all}
                                    控制在失败测试中显示捕获的 stdout/stderr/log 的方式。默认：all。
                --full-trace          不截断任何回溯（默认是截断）
                --color=颜色         彩色终端输出（yes/no/auto）
                --code-highlight={yes,no}
                                    是否突出显示代码（仅在 --color
                                    也启用时）。默认：yes。
                --pastebin=模式       将失败|全部信息发送到 bpaste.net pastebin 服务
                --junit-xml=路径      在给定路径创建 junit-xml 风格的报告文件
                --junit-prefix=字符串 在 junit-xml 输出中为类名添加前缀

            pytest-warnings:
                -W PYTHONWARNINGS, --pythonwarnings=PYTHONWARNINGS
                                    设置要报告的警告，参见 Python 自身的 -W 选项
                --maxfail=num         在第一个 num 次失败或错误后退出
                --strict-config       在解析配置文件的 `pytest` 部分时遇到的任何警告都会引发错误
                --strict-markers      在配置文件的 `markers` 部分未注册的标记将引发错误
                --strict              （已弃用）--strict-markers 的别名
                -c FILE, --config-file=FILE
                                    从 `FILE` 加载配置，而不是尝试定位隐式配置文件之一。
                --continue-on-collection-errors
                                    即使发生收集错误也强制执行测试
                --rootdir=ROOTDIR     定义测试的根目录。可以是相对路径：
                                    'root_dir'，'./root_dir'，
                                    'root_dir/another_dir/'；绝对路径：
                                    '/home/user/root_dir'；带变量的路径：
                                    '$HOME/root_dir'。

            收集:
                --collect-only, --co  仅收集测试，不执行它们
                --pyargs              尝试将所有参数解释为 Python 包
                --ignore=path         在收集期间忽略路径（允许多次）
                --ignore-glob=path    在收集期间忽略路径模式（允许多次）
                --deselect=nodeid_prefix
                                    在收集期间取消选择项目（通过节点 ID 前缀）
                                    （允许多次）
                --confcutdir=dir      仅加载与指定目录相对的 conftest.py
                --noconftest          不加载任何 conftest.py 文件
                --keep-duplicates     保留重复的测试
                --collect-in-virtualenv
                                    不忽略本地虚拟环境目录中的测试
                --import-mode={prepend,append,importlib}
                                    导入测试模块和 conftest 文件时添加到 sys.path。
                                    默认：prepend。
                --doctest-modules     运行所有 .py 模块中的文档测试
                --doctest-report={none,cdiff,ndiff,udiff,only_first_failure}
                                    为文档测试失败选择另一种输出格式
                --doctest-glob=pat    文档测试文件匹配模式，默认：test*.txt
                --doctest-ignore-import-errors
                                    忽略文档测试收集错误
                --doctest-continue-on-failure
                                    对于给定的文档测试，在第一次失败后继续运行

            测试会话调试和配置:
                --basetemp=dir        此次测试运行的基础临时目录。
                                    （警告：如果该目录存在将被删除。）
                -V, --version         显示 pytest 版本和插件信息。重复给出时，还会显示插件信息。
                -h, --help            显示帮助信息和配置信息
                -p name               早期加载给定插件模块名称或入口点
                                    （允许多次）。要避免加载插件，使用
                                    `no:` 前缀，例如 `no:doctest`。
                --trace-config        跟踪 conftest.py 文件的考虑
                --debug=[DEBUG_FILE_NAME]
                                    将内部跟踪调试信息存储在此日志文件中。该文件以 'w' 打开并被截断，
                                    请小心使用。默认：pytestdebug.log。
                -o OVERRIDE_INI, --override-ini=OVERRIDE_INI
                                    使用 "option=value" 风格覆盖 ini 选项，例如：
                                    `-o xfail_strict=True -o cache_dir=cache`。
                --assert=模式         控制断言调试工具。
                                    'plain' 不执行任何断言调试。
                                    'rewrite'（默认）在导入测试模块时重写断言语句以提供断言
                                    表达式信息。
                --setup-only          仅设置固件，不执行测试
                --setup-show          在执行测试时显示固件的设置
                --setup-plan          显示将要执行的固件和测试，但不执行任何内容

            logging:
                --log-level=LEVEL     捕获/显示的消息级别。默认情况下未设置，因此取决于根/父日志处理程序的有效级别，默认为 "WARNING"。
                --log-format=LOG_FORMAT
                                    日志模块使用的日志格式
                --log-date-format=LOG_DATE_FORMAT
                                    日志模块使用的日期格式
                --log-cli-level=LOG_CLI_LEVEL
                                    CLI 日志级别
                --log-cli-format=LOG_CLI_FORMAT
                                    日志模块使用的日志格式
                --log-cli-date-format=LOG_CLI_DATE_FORMAT
                                    日志模块使用的日期格式
                --log-file=LOG_FILE   日志将写入的文件路径
                --log-file-mode={w,a}
                                    日志文件打开模式
                --log-file-level=LOG_FILE_LEVEL
                                    日志文件的日志级别
                --log-file-format=LOG_FILE_FORMAT
                                    日志模块使用的日志格式
                --log-file-date-format=LOG_FILE_DATE_FORMAT
                                    日志模块使用的日期格式
                --log-auto-indent=LOG_AUTO_INDENT
                                    自动缩进传递给日志模块的多行消息。接受 true|on、false|off 或整数。
                --log-disable=LOGGER_DISABLE
                                    通过名称禁用日志记录器。可以多次传递。

            [pytest] ini 选项在找到的第一个 pytest.ini|tox.ini|setup.cfg|pyproject.toml 文件中：

                markers (linelist):   为测试函数注册新标记
                empty_parameter_set_mark (string):
                                    空参数集的默认标记
                norecursedirs (args): 避免递归的目录模式
                testpaths (args):     在命令行未给出文件或目录时搜索测试的目录
                filterwarnings (linelist):
                                    每行指定一个模式用于
                                    warnings.filterwarnings。在 -W/--pythonwarnings 之后处理。
                consider_namespace_packages (bool):
                                    在导入期间解析模块名称时考虑命名空间包
                usefixtures (args):   要与此项目一起使用的默认固件列表
                python_files (args):  Python 测试模块发现的 glob 样式文件模式
                python_classes (args):
                                    Python 测试类发现的前缀或 glob 名称
                python_functions (args):
                                    Python 测试函数和方法发现的前缀或 glob 名称
                disable_test_id_escaping_and_forfeit_all_rights_to_community_support (bool):
                                    禁用字符串转义非 ASCII 字符，可能导致不必要的副作用（自担风险使用）
                console_output_style (string):
                                    控制台输出：“经典”或带有附加进度信息（“进度” (百分比) | “计数” | “即使捕获=no 也进度” (强制即使捕获=no 也显示进度)
                verbosity_test_cases (string):
                                    为测试用例执行指定一个详细程度，覆盖主级别。更高的级别将提供有关每个执行测试用例的更多详细信息。
                xfail_strict (bool):  当未明确给出时，xfail 标记的严格参数的默认值（默认：False）
                tmp_path_retention_count (string):
                                    根据 `tmp_path_retention_policy`，应保留多少个 `tmp_path` 目录。
                tmp_path_retention_policy (string):
                                    控制基于测试结果保留哪些由 `tmp_path` 固件创建的目录。
                                    （all/failed/none）
                enable_assertion_pass_hook (bool):
                                    启用 pytest_assertion_pass 钩子。确保删除任何先前生成的 pyc 缓存文件。
                verbosity_assertions (string):
                                    为断言指定一个详细程度，覆盖主级别。更高的级别将在断言失败时提供更详细的解释。
                junit_suite_name (string):
                                    JUnit 报告的测试套件名称
                junit_logging (string):
                                    将捕获的日志消息写入 JUnit 报告：可以是 no|log|system-out|system-err|out-err|all
                junit_log_passing_tests (bool):
                                    捕获通过测试的日志信息以便写入 JUnit 报告：
                junit_duration_report (string):
                                    报告的持续时间：可以是 total|call
                junit_family (string):
                                    发出 XML 的模式：可以是 legacy|xunit1|xunit2
                doctest_optionflags (args):
                                    文档测试的选项标志
                doctest_encoding (string):
                                    文档测试文件使用的编码
                cache_dir (string):   缓存目录路径
                log_level (string):   --log-level 的默认值
                log_format (string):  --log-format 的默认值
                log_date_format (string):
                                    --log-date-format 的默认值
                log_cli (bool):       在测试运行期间启用日志显示（也称为“实时日志”）
                log_cli_level (string):
                                    --log-cli-level 的默认值
                log_cli_format (string):
                                    --log-cli-format 的默认值
                log_cli_date_format (string):
                                    --log-cli-date-format 的默认值
                log_file (string):    --log-file 的默认值
                log_file_mode (string):
                                    --log-file-mode 的默认值
                log_file_level (string):
                                    --log-file-level 的默认值
                log_file_format (string):
                                    --log-file-format 的默认值
                log_file_date_format (string):
                                    --log-file-date-format 的默认值
                log_auto_indent (string):
                                    --log-auto-indent 的默认值
                pythonpath (paths):   添加路径到 sys.path
                faulthandler_timeout (string):
                                    如果测试运行超过 TIMEOUT 秒，转储所有线程的回溯信息
                addopts (args):       附加命令行选项
                minversion (string):  最小要求的 pytest 版本
                required_plugins (args):
                                    pytest 运行时必须存在的插件

            环境变量:
                CI                       设置时（无论值如何），pytest 知道它正在 CI 进程中运行，并且不会截断摘要信息
                BUILD_NUMBER             等同于 CI
                PYTEST_ADDOPTS           附加命令行选项
                PYTEST_PLUGINS           启动时加载的逗号分隔插件
                PYTEST_DISABLE_PLUGIN_AUTOLOAD 设置为禁用插件自动加载
                PYTEST_DEBUG             设置以启用 pytest 内部的调试跟踪

            要查看可用的标记，请输入：pytest --markers
            要查看可用的固件，请输入：pytest --fixtures
            （根据指定的 file_or_dir 或当前目录显示，如果未指定；以'_'开头的固件仅在使用 '-v' 选项时显示）

    .. tab:: 英文

        All the command-line flags can be obtained by running ``pytest --help``::

            $ pytest --help
            usage: pytest [options] [file_or_dir] [file_or_dir] [...]

            positional arguments:
                file_or_dir

            general:
                -k EXPRESSION         Only run tests which match the given substring
                                    expression. An expression is a Python evaluable
                                    expression where all names are substring-matched
                                    against test names and their parent classes.
                                    Example: -k 'test_method or test_other' matches all
                                    test functions and classes whose name contains
                                    'test_method' or 'test_other', while -k 'not
                                    test_method' matches those that don't contain
                                    'test_method' in their names. -k 'not test_method
                                    and not test_other' will eliminate the matches.
                                    Additionally keywords are matched to classes and
                                    functions containing extra names in their
                                    'extra_keyword_matches' set, as well as functions
                                    which have names assigned directly to them. The
                                    matching is case-insensitive.
                -m MARKEXPR           Only run tests matching given mark expression. For
                                    example: -m 'mark1 and not mark2'.
                --markers             show markers (builtin, plugin and per-project ones).
                -x, --exitfirst       Exit instantly on first error or failed test
                --fixtures, --funcargs
                                    Show available fixtures, sorted by plugin appearance
                                    (fixtures with leading '_' are only shown with '-v')
                --fixtures-per-test   Show fixtures per test
                --pdb                 Start the interactive Python debugger on errors or
                                    KeyboardInterrupt
                --pdbcls=modulename:classname
                                    Specify a custom interactive Python debugger for use
                                    with --pdb.For example:
                                    --pdbcls=IPython.terminal.debugger:TerminalPdb
                --trace               Immediately break when running each test
                --capture=method      Per-test capturing method: one of fd|sys|no|tee-sys
                -s                    Shortcut for --capture=no
                --runxfail            Report the results of xfail tests as if they were
                                    not marked
                --lf, --last-failed   Rerun only the tests that failed at the last run (or
                                    all if none failed)
                --ff, --failed-first  Run all tests, but run the last failures first. This
                                    may re-order tests and thus lead to repeated fixture
                                    setup/teardown.
                --nf, --new-first     Run tests from new files first, then the rest of the
                                    tests sorted by file mtime
                --cache-show=[CACHESHOW]
                                    Show cache contents, don't perform collection or
                                    tests. Optional argument: glob (default: '*').
                --cache-clear         Remove all cache contents at start of test run
                --lfnf={all,none}, --last-failed-no-failures={all,none}
                                    With ``--lf``, determines whether to execute tests
                                    when there are no previously (known) failures or
                                    when no cached ``lastfailed`` data was found.
                                    ``all`` (the default) runs the full test suite
                                    again. ``none`` just emits a message about no known
                                    failures and exits successfully.
                --sw, --stepwise      Exit on test failure and continue from last failing
                                    test next time
                --sw-skip, --stepwise-skip
                                    Ignore the first failing test but stop on the next
                                    failing test. Implicitly enables --stepwise.

            Reporting:
                --durations=N         Show N slowest setup/test durations (N=0 for all)
                --durations-min=N     Minimal duration in seconds for inclusion in slowest
                                    list. Default: 0.005.
                -v, --verbose         Increase verbosity
                --no-header           Disable header
                --no-summary          Disable summary
                --no-fold-skipped     Do not fold skipped tests in short summary.
                -q, --quiet           Decrease verbosity
                --verbosity=VERBOSE   Set verbosity. Default: 0.
                -r chars              Show extra test summary info as specified by chars:
                                    (f)ailed, (E)rror, (s)kipped, (x)failed, (X)passed,
                                    (p)assed, (P)assed with output, (a)ll except passed
                                    (p/P), or (A)ll. (w)arnings are enabled by default
                                    (see --disable-warnings), 'N' can be used to reset
                                    the list. (default: 'fE').
                --disable-warnings, --disable-pytest-warnings
                                    Disable warnings summary
                -l, --showlocals      Show locals in tracebacks (disabled by default)
                --no-showlocals       Hide locals in tracebacks (negate --showlocals
                                    passed through addopts)
                --tb=style            Traceback print mode
                                    (auto/long/short/line/native/no)
                --xfail-tb            Show tracebacks for xfail (as long as --tb != no)
                --show-capture={no,stdout,stderr,log,all}
                                    Controls how captured stdout/stderr/log is shown on
                                    failed tests. Default: all.
                --full-trace          Don't cut any tracebacks (default is to cut)
                --color=color         Color terminal output (yes/no/auto)
                --code-highlight={yes,no}
                                    Whether code should be highlighted (only if --color
                                    is also enabled). Default: yes.
                --pastebin=mode       Send failed|all info to bpaste.net pastebin service
                --junit-xml=path      Create junit-xml style report file at given path
                --junit-prefix=str    Prepend prefix to classnames in junit-xml output

            pytest-warnings:
                -W PYTHONWARNINGS, --pythonwarnings=PYTHONWARNINGS
                                    Set which warnings to report, see -W option of
                                    Python itself
                --maxfail=num         Exit after first num failures or errors
                --strict-config       Any warnings encountered while parsing the `pytest`
                                    section of the configuration file raise errors
                --strict-markers      Markers not registered in the `markers` section of
                                    the configuration file raise errors
                --strict              (Deprecated) alias to --strict-markers
                -c FILE, --config-file=FILE
                                    Load configuration from `FILE` instead of trying to
                                    locate one of the implicit configuration files.
                --continue-on-collection-errors
                                    Force test execution even if collection errors occur
                --rootdir=ROOTDIR     Define root directory for tests. Can be relative
                                    path: 'root_dir', './root_dir',
                                    'root_dir/another_dir/'; absolute path:
                                    '/home/user/root_dir'; path with variables:
                                    '$HOME/root_dir'.

            collection:
                --collect-only, --co  Only collect tests, don't execute them
                --pyargs              Try to interpret all arguments as Python packages
                --ignore=path         Ignore path during collection (multi-allowed)
                --ignore-glob=path    Ignore path pattern during collection (multi-
                                    allowed)
                --deselect=nodeid_prefix
                                    Deselect item (via node id prefix) during collection
                                    (multi-allowed)
                --confcutdir=dir      Only load conftest.py's relative to specified dir
                --noconftest          Don't load any conftest.py files
                --keep-duplicates     Keep duplicate tests
                --collect-in-virtualenv
                                    Don't ignore tests in a local virtualenv directory
                --import-mode={prepend,append,importlib}
                                    Prepend/append to sys.path when importing test
                                    modules and conftest files. Default: prepend.
                --doctest-modules     Run doctests in all .py modules
                --doctest-report={none,cdiff,ndiff,udiff,only_first_failure}
                                    Choose another output format for diffs on doctest
                                    failure
                --doctest-glob=pat    Doctests file matching pattern, default: test*.txt
                --doctest-ignore-import-errors
                                    Ignore doctest collection errors
                --doctest-continue-on-failure
                                    For a given doctest, continue to run after the first
                                    failure

            test session debugging and configuration:
                --basetemp=dir        Base temporary directory for this test run.
                                    (Warning: this directory is removed if it exists.)
                -V, --version         Display pytest version and information about
                                    plugins. When given twice, also display information
                                    about plugins.
                -h, --help            Show help message and configuration info
                -p name               Early-load given plugin module name or entry point
                                    (multi-allowed). To avoid loading of plugins, use
                                    the `no:` prefix, e.g. `no:doctest`.
                --trace-config        Trace considerations of conftest.py files
                --debug=[DEBUG_FILE_NAME]
                                    Store internal tracing debug information in this log
                                    file. This file is opened with 'w' and truncated as
                                    a result, care advised. Default: pytestdebug.log.
                -o OVERRIDE_INI, --override-ini=OVERRIDE_INI
                                    Override ini option with "option=value" style, e.g.
                                    `-o xfail_strict=True -o cache_dir=cache`.
                --assert=MODE         Control assertion debugging tools.
                                    'plain' performs no assertion debugging.
                                    'rewrite' (the default) rewrites assert statements
                                    in test modules on import to provide assert
                                    expression information.
                --setup-only          Only setup fixtures, do not execute tests
                --setup-show          Show setup of fixtures while executing tests
                --setup-plan          Show what fixtures and tests would be executed but
                                    don't execute anything

            logging:
                --log-level=LEVEL     Level of messages to catch/display. Not set by
                                    default, so it depends on the root/parent log
                                    handler's effective level, where it is "WARNING" by
                                    default.
                --log-format=LOG_FORMAT
                                    Log format used by the logging module
                --log-date-format=LOG_DATE_FORMAT
                                    Log date format used by the logging module
                --log-cli-level=LOG_CLI_LEVEL
                                    CLI logging level
                --log-cli-format=LOG_CLI_FORMAT
                                    Log format used by the logging module
                --log-cli-date-format=LOG_CLI_DATE_FORMAT
                                    Log date format used by the logging module
                --log-file=LOG_FILE   Path to a file when logging will be written to
                --log-file-mode={w,a}
                                    Log file open mode
                --log-file-level=LOG_FILE_LEVEL
                                    Log file logging level
                --log-file-format=LOG_FILE_FORMAT
                                    Log format used by the logging module
                --log-file-date-format=LOG_FILE_DATE_FORMAT
                                    Log date format used by the logging module
                --log-auto-indent=LOG_AUTO_INDENT
                                    Auto-indent multiline messages passed to the logging
                                    module. Accepts true|on, false|off or an integer.
                --log-disable=LOGGER_DISABLE
                                    Disable a logger by name. Can be passed multiple
                                    times.

            [pytest] ini-options in the first pytest.ini|tox.ini|setup.cfg|pyproject.toml file found:

                markers (linelist):   Register new markers for test functions
                empty_parameter_set_mark (string):
                                    Default marker for empty parametersets
                norecursedirs (args): Directory patterns to avoid for recursion
                testpaths (args):     Directories to search for tests when no files or
                                    directories are given on the command line
                filterwarnings (linelist):
                                    Each line specifies a pattern for
                                    warnings.filterwarnings. Processed after
                                    -W/--pythonwarnings.
                consider_namespace_packages (bool):
                                    Consider namespace packages when resolving module
                                    names during import
                usefixtures (args):   List of default fixtures to be used with this
                                    project
                python_files (args):  Glob-style file patterns for Python test module
                                    discovery
                python_classes (args):
                                    Prefixes or glob names for Python test class
                                    discovery
                python_functions (args):
                                    Prefixes or glob names for Python test function and
                                    method discovery
                disable_test_id_escaping_and_forfeit_all_rights_to_community_support (bool):
                                    Disable string escape non-ASCII characters, might
                                    cause unwanted side effects(use at your own risk)
                console_output_style (string):
                                    Console output: "classic", or with additional
                                    progress information ("progress" (percentage) |
                                    "count" | "progress-even-when-capture-no" (forces
                                    progress even when capture=no)
                verbosity_test_cases (string):
                                    Specify a verbosity level for test case execution,
                                    overriding the main level. Higher levels will
                                    provide more detailed information about each test
                                    case executed.
                xfail_strict (bool):  Default for the strict parameter of xfail markers
                                    when not given explicitly (default: False)
                tmp_path_retention_count (string):
                                    How many sessions should we keep the `tmp_path`
                                    directories, according to
                                    `tmp_path_retention_policy`.
                tmp_path_retention_policy (string):
                                    Controls which directories created by the `tmp_path`
                                    fixture are kept around, based on test outcome.
                                    (all/failed/none)
                enable_assertion_pass_hook (bool):
                                    Enables the pytest_assertion_pass hook. Make sure to
                                    delete any previously generated pyc cache files.
                verbosity_assertions (string):
                                    Specify a verbosity level for assertions, overriding
                                    the main level. Higher levels will provide more
                                    detailed explanation when an assertion fails.
                junit_suite_name (string):
                                    Test suite name for JUnit report
                junit_logging (string):
                                    Write captured log messages to JUnit report: one of
                                    no|log|system-out|system-err|out-err|all
                junit_log_passing_tests (bool):
                                    Capture log information for passing tests to JUnit
                                    report:
                junit_duration_report (string):
                                    Duration time to report: one of total|call
                junit_family (string):
                                    Emit XML for schema: one of legacy|xunit1|xunit2
                doctest_optionflags (args):
                                    Option flags for doctests
                doctest_encoding (string):
                                    Encoding used for doctest files
                cache_dir (string):   Cache directory path
                log_level (string):   Default value for --log-level
                log_format (string):  Default value for --log-format
                log_date_format (string):
                                    Default value for --log-date-format
                log_cli (bool):       Enable log display during test run (also known as
                                    "live logging")
                log_cli_level (string):
                                    Default value for --log-cli-level
                log_cli_format (string):
                                    Default value for --log-cli-format
                log_cli_date_format (string):
                                    Default value for --log-cli-date-format
                log_file (string):    Default value for --log-file
                log_file_mode (string):
                                    Default value for --log-file-mode
                log_file_level (string):
                                    Default value for --log-file-level
                log_file_format (string):
                                    Default value for --log-file-format
                log_file_date_format (string):
                                    Default value for --log-file-date-format
                log_auto_indent (string):
                                    Default value for --log-auto-indent
                pythonpath (paths):   Add paths to sys.path
                faulthandler_timeout (string):
                                    Dump the traceback of all threads if a test takes
                                    more than TIMEOUT seconds to finish
                addopts (args):       Extra command line options
                minversion (string):  Minimally required pytest version
                required_plugins (args):
                                    Plugins that must be present for pytest to run

            Environment variables:
                CI                       When set (regardless of value), pytest knows it is running in a CI process and does not truncate summary info
                BUILD_NUMBER             Equivalent to CI
                PYTEST_ADDOPTS           Extra command line options
                PYTEST_PLUGINS           Comma-separated plugins to load during startup
                PYTEST_DISABLE_PLUGIN_AUTOLOAD Set to disable plugin auto-loading
                PYTEST_DEBUG             Set to enable debug tracing of pytest's internals


            to see available markers type: pytest --markers
            to see available fixtures type: pytest --fixtures
            (shown according to specified file_or_dir or current dir if not specified; fixtures with leading '_' are only shown with the '-v' option
