.. _plugins:
.. _`writing-plugins`:

编写插件
===============

**Writing plugins**

.. tabs::

    .. tab:: 中文

        实现适用于您项目的 `本地 conftest 插件 <local conftest plugins>`_ 或者适用于多个项目的 `pip 可安装插件 <pip-installable plugins>`_ 非常简单，这些插件还可以用于第三方项目。如只需使用而不编写插件，请参阅 :ref:`using plugins`。

        一个插件包含一个或多个钩子函数。:ref:`Writing hooks <writinghooks>` 讲解了如何编写钩子函数的基本内容和详细信息。``pytest`` 通过调用以下插件的 :ref:`明确定义的钩子 <hook-reference>` 实现配置、收集、运行和报告的各个方面：

        * 内置插件：从 pytest 的内部 ``_pytest`` 目录加载。

        * :ref:`外部插件 <extplugins>`：通过其打包元数据中的 :ref:`入口点 <pip-installable plugins>` 发现已安装的第三方模块。

        * `conftest.py 插件 <conftest.py plugins>`_ ：在测试目录中自动发现的模块。

        原则上，每次钩子调用都是一次 ``1:N`` 的 Python 函数调用，其中 ``N`` 是给定规范注册的实现函数数量。所有规范和实现都遵循 ``pytest_`` 前缀命名约定，使它们易于区分和查找。

    .. tab:: 英文

        It is easy to implement `local conftest plugins`_ for your own project
        or `pip-installable plugins`_ that can be used throughout many projects,
        including third party projects.  Please refer to :ref:`using plugins` if you
        only want to use but not write plugins.

        A plugin contains one or multiple hook functions. :ref:`Writing hooks <writinghooks>`
        explains the basics and details of how you can write a hook function yourself.
        ``pytest`` implements all aspects of configuration, collection, running and
        reporting by calling :ref:`well specified hooks <hook-reference>` of the following plugins:

        * builtin plugins: loaded from pytest's internal ``_pytest`` directory.

        * :ref:`external plugins <extplugins>`: installed third-party modules discovered
        through :ref:`entry points <pip-installable plugins>` in their packaging metadata

        * `conftest.py plugins`_: modules auto-discovered in test directories

        In principle, each hook call is a ``1:N`` Python function call where ``N`` is the
        number of registered implementation functions for a given specification.
        All specifications and implementations follow the ``pytest_`` prefix
        naming convention, making them easy to distinguish and find.

.. _`pluginorder`:

工具启动时插件发现顺序
--------------------------------------

**Plugin discovery order at tool startup**

.. tabs::

    .. tab:: 中文

        ``pytest`` 在工具启动时通过以下方式加载插件模块：

        1. 扫描命令行中的 ``-p no:name`` 选项，并*阻止*该插件的加载（即使是内置插件也可以通过这种方式阻止加载）。此过程在正常的命令行解析之前执行。

        2. 加载所有内置插件。

        3. 扫描命令行中的 ``-p name`` 选项并加载指定插件。此过程在正常的命令行解析之前执行。

        4. 加载通过安装的第三方包中的 :ref:`入口点 <pip-installable plugins>` 注册的所有插件，除非设置了 :envvar:`PYTEST_DISABLE_PLUGIN_AUTOLOAD` 环境变量。

        5. 加载通过 :envvar:`PYTEST_PLUGINS` 环境变量指定的所有插件。

        6. 加载所有“初始” :file:`conftest.py` 文件：

        - 确定测试路径：在命令行中指定的路径，否则使用 :confval:`testpaths` （如果在 rootdir 中定义并运行），否则为当前目录。
        - 对于每个测试路径，加载相对于测试路径的目录部分的 ``conftest.py`` 和 ``test*/conftest.py`` （如果存在）。
          在加载某个 ``conftest.py`` 文件之前，先加载其所有父目录中的 ``conftest.py`` 文件。加载完某个 ``conftest.py`` 文件后，递归加载其中 :globalvar:`pytest_plugins` 变量中指定的所有插件（如果存在）。

    .. tab:: 英文

        ``pytest`` loads plugin modules at tool startup in the following way:

        1. by scanning the command line for the ``-p no:name`` option and *blocking* that plugin from being loaded (even builtin plugins can be blocked this way). This happens before normal command-line parsing.

        1. by loading all builtin plugins.

        2. by scanning the command line for the ``-p name`` option and loading the specified plugin. This happens before normal command-line parsing.

        1. by loading all plugins registered through installed third-party package :ref:`entry points <pip-installable plugins>`, unless the :envvar:`PYTEST_DISABLE_PLUGIN_AUTOLOAD` environment variable is set.

        1. by loading all plugins specified through the :envvar:`PYTEST_PLUGINS` environment variable.

        2. by loading all "initial ":file:`conftest.py` files:

        - determine the test paths: specified on the command line, otherwise in :confval:`testpaths` if defined and running from the rootdir, otherwise the current dir
        - for each test path, load ``conftest.py`` and ``test*/conftest.py`` relative to the directory part of the test path, if exist. Before a ``conftest.py`` file is loaded, load ``conftest.py`` files in all of its parent directories.
            After a ``conftest.py`` file is loaded, recursively load all plugins specified in its :globalvar:`pytest_plugins` variable if present.


.. _`conftest.py plugins`:
.. _`localplugin`:
.. _`local conftest plugins`:

conftest.py: 本地每个目录插件
----------------------------------------

**conftest.py: local per-directory plugins**

.. tabs::

    .. tab:: 中文

        本地 ``conftest.py`` 插件包含特定于目录的钩子实现。会话和测试运行活动将调用文件系统根目录附近的所有 ``conftest.py`` 文件中定义的钩子。以下示例展示了如何实现 ``pytest_runtest_setup`` 钩子，使其仅在 ``a`` 子目录中的测试运行时被调用，而不适用于其他目录::

            a/conftest.py:
                def pytest_runtest_setup(item):
                    # called for running each test in 'a' directory
                    print("setting up", item)

            a/test_sub.py:
                def test_sub():
                    pass

            test_flat.py:
                def test_flat():
                    pass

        可以这样运行它::

            pytest test_flat.py --capture=no  # 不会显示 "setting up"
            pytest a/test_sub.py --capture=no  # 会显示 "setting up"

        .. note::

            如果您的 ``conftest.py`` 文件不位于 Python 包目录中（即包含 ``__init__.py`` 的目录），那么 `import conftest` 可能会出现歧义，因为您的 ``PYTHONPATH`` 或 ``sys.path`` 中可能还存在其他 ``conftest.py`` 文件。因此，建议项目将 ``conftest.py`` 文件放在包作用域中，或者从不在 ``conftest.py`` 文件中导入任何内容。

            参见： :ref:`pythonpath`.

        .. note::

            由于 pytest 在启动时的插件发现机制，某些钩子无法在非 :ref:`initial <pluginorder>` 的 conftest.py 文件中实现。具体细节请参阅每个钩子的文档。

    .. tab:: 英文

        Local ``conftest.py`` plugins contain directory-specific hook
        implementations.  Hook Session and test running activities will
        invoke all hooks defined in ``conftest.py`` files closer to the
        root of the filesystem.  Example of implementing the
        ``pytest_runtest_setup`` hook so that is called for tests in the ``a``
        sub directory but not for other directories::

            a/conftest.py:
                def pytest_runtest_setup(item):
                    # called for running each test in 'a' directory
                    print("setting up", item)

            a/test_sub.py:
                def test_sub():
                    pass

            test_flat.py:
                def test_flat():
                    pass

        Here is how you might run it::

            pytest test_flat.py --capture=no  # will not show "setting up"
            pytest a/test_sub.py --capture=no  # will show "setting up"

        .. note::
            If you have ``conftest.py`` files which do not reside in a
            python package directory (i.e. one containing an ``__init__.py``) then
            "import conftest" can be ambiguous because there might be other
            ``conftest.py`` files as well on your ``PYTHONPATH`` or ``sys.path``.
            It is thus good practice for projects to either put ``conftest.py``
            under a package scope or to never import anything from a
            ``conftest.py`` file.

            See also: :ref:`pythonpath`.

        .. note::
            Some hooks cannot be implemented in conftest.py files which are not
            :ref:`initial <pluginorder>` due to how pytest discovers plugins during
            startup. See the documentation of each hook for details.

编写您自己的插件
-----------------------

**Writing your own plugin**

.. tabs::

    .. tab:: 中文

        如果您想编写一个插件，可以从以下多个实际示例中进行参考：

        * 自定义收集示例插件：:ref:`yaml plugin`
        * 提供 pytest 自身功能的内置插件
        * 许多提供额外功能的 :ref:`外部插件 <plugin-list>`

        以上插件均通过实现 :ref:`hooks <hook-reference>` 和/或 :ref:`fixtures <fixture>` 来扩展和增强功能。

        .. note::
            强烈建议查看出色的 `cookiecutter-pytest-plugin <https://github.com/pytest-dev/cookiecutter-pytest-plugin>`_ 项目，这是用于编写插件的 `cookiecutter 模板 <https://github.com/audreyr/cookiecutter>`_ 。

            此模板提供了一个出色的起点，包括一个工作插件、tox 运行的测试、详尽的 README 文件以及预配置的入口点。

        一旦您的插件除您自己外也拥有了一些满意的用户，请考虑将插件 :ref:`贡献给 pytest-dev<submitplugin>`。

    .. tab:: 英文

        If you want to write a plugin, there are many real-life examples
        you can copy from:

        * a custom collection example plugin: :ref:`yaml plugin`
        * builtin plugins which provide pytest's own functionality
        * many :ref:`external plugins <plugin-list>` providing additional features

        All of these plugins implement :ref:`hooks <hook-reference>` and/or :ref:`fixtures <fixture>`
        to extend and add functionality.

        .. note::
            Make sure to check out the excellent
            `cookiecutter-pytest-plugin <https://github.com/pytest-dev/cookiecutter-pytest-plugin>`_
            project, which is a `cookiecutter template <https://github.com/audreyr/cookiecutter>`_
            for authoring plugins.

            The template provides an excellent starting point with a working plugin,
            tests running with tox, a comprehensive README file as well as a
            pre-configured entry-point.

        Also consider :ref:`contributing your plugin to pytest-dev<submitplugin>`
        once it has some happy users other than yourself.


.. _`setuptools entry points`:
.. _`pip-installable plugins`:

使其他人可以安装您的插件
----------------------------------------

**Making your plugin installable by others**

.. tabs::

    .. tab:: 中文

        如果您想让您的插件对外可用，可以为您的分发定义一个所谓的入口点，这样 ``pytest`` 就可以找到您的插件模块。入口点是 :std:doc:`打包工具 <packaging:specifications/entry-points>` 提供的一个特性。

        pytest 会查找 ``pytest11`` 入口点以发现其插件，因此可以在 ``pyproject.toml`` 文件中定义您的插件来使其可用。

        .. sourcecode:: toml

            # 示例 ./pyproject.toml 文件
            [build-system]
            requires = ["hatchling"]
            build-backend = "hatchling.build"

            [project]
            name = "myproject"
            classifiers = [
                "Framework :: Pytest",
            ]

            [project.entry-points.pytest11]
            myproject = "myproject.pluginmodule"

        如果一个包以这种方式安装， ``pytest`` 会将 ``myproject.pluginmodule`` 作为插件加载，后者可以定义 :ref:`hooks <hook-reference>` 。使用 ``pytest --trace-config`` 确认注册。

        .. note::

            请确保在 `PyPI 分类 <https://pypi.org/classifiers/>`_ 列表中包括 ``Framework :: Pytest`` ，这样便于用户找到您的插件。

    .. tab:: 英文

        If you want to make your plugin externally available, you
        may define a so-called entry point for your distribution so
        that ``pytest`` finds your plugin module. Entry points are
        a feature that is provided by :std:doc:`packaging tools
        <packaging:specifications/entry-points>`.

        pytest looks up the ``pytest11`` entrypoint to discover its
        plugins, thus you can make your plugin available by defining
        it in your ``pyproject.toml`` file.

        .. sourcecode:: toml

            # sample ./pyproject.toml file
            [build-system]
            requires = ["hatchling"]
            build-backend = "hatchling.build"

            [project]
            name = "myproject"
            classifiers = [
                "Framework :: Pytest",
            ]

            [project.entry-points.pytest11]
            myproject = "myproject.pluginmodule"

        If a package is installed this way, ``pytest`` will load
        ``myproject.pluginmodule`` as a plugin which can define
        :ref:`hooks <hook-reference>`. Confirm registration with ``pytest --trace-config``

        .. note::

            Make sure to include ``Framework :: Pytest`` in your list of
            `PyPI classifiers <https://pypi.org/classifiers/>`_
            to make it easy for users to find your plugin.


.. _assertion-rewriting:

断言重写
-------------------

**Assertion Rewriting**

.. tabs::

    .. tab:: 中文

        ``pytest`` 的主要功能之一是使用简单的 assert 语句，并在断言失败时提供详细的表达式检查。这通过“断言重写”来实现，该功能在代码被编译为字节码之前修改解析的 AST。这是通过 :pep:`302` 导入钩子实现的，在 ``pytest`` 启动时会尽早安装该钩子，并在模块被导入时执行此重写。然而，为了确保测试和生产环境中运行的字节码一致，此钩子仅重写测试模块本身（由 :confval:`python_files` 配置选项定义）和任何插件中的模块。其他被导入的模块不会被重写，而会保持普通的断言行为。

        如果您在其他模块中有需要启用断言重写的辅助断言功能，您需要明确要求 ``pytest`` 在导入该模块之前对其进行重写。

        .. autofunction:: pytest.register_assert_rewrite
            :noindex:

        当您编写一个通过包创建的 pytest 插件时，这尤其重要。导入钩子只会将 ``conftest.py`` 文件和 ``pytest11`` 入口点中列出的模块视为插件。以下是一个示例包结构::

            pytest_foo/__init__.py
            pytest_foo/plugin.py
            pytest_foo/helper.py

        以下是 ``setup.py`` 的典型示例代码：

        .. code-block:: python

            setup(..., entry_points={"pytest11": ["foo = pytest_foo.plugin"]}, ...)

        在这种情况下，只有 ``pytest_foo/plugin.py`` 会被重写。如果辅助模块也包含需要重写的 assert 语句，则在导入之前需要将其标记为可重写。最简单的方法是在 ``__init__.py`` 模块中标记它，当导入包中的模块时，该模块总是会首先被导入。这样，``plugin.py`` 仍然可以正常导入 ``helper.py``。此时，``pytest_foo/__init__.py`` 的内容应如下所示：

        .. code-block:: python

            import pytest

            pytest.register_assert_rewrite("pytest_foo.helper")

    .. tab:: 英文

        One of the main features of ``pytest`` is the use of plain assert
        statements and the detailed introspection of expressions upon
        assertion failures.  This is provided by "assertion rewriting" which
        modifies the parsed AST before it gets compiled to bytecode.  This is
        done via a :pep:`302` import hook which gets installed early on when
        ``pytest`` starts up and will perform this rewriting when modules get
        imported.  However, since we do not want to test different bytecode
        from what you will run in production, this hook only rewrites test modules
        themselves (as defined by the :confval:`python_files` configuration option),
        and any modules which are part of plugins.
        Any other imported module will not be rewritten and normal assertion behaviour
        will happen.

        If you have assertion helpers in other modules where you would need
        assertion rewriting to be enabled you need to ask ``pytest``
        explicitly to rewrite this module before it gets imported.

        .. autofunction:: pytest.register_assert_rewrite
            :noindex:

        This is especially important when you write a pytest plugin which is
        created using a package.  The import hook only treats ``conftest.py``
        files and any modules which are listed in the ``pytest11`` entrypoint
        as plugins.  As an example consider the following package::

            pytest_foo/__init__.py
            pytest_foo/plugin.py
            pytest_foo/helper.py

        With the following typical ``setup.py`` extract:

        .. code-block:: python

            setup(..., entry_points={"pytest11": ["foo = pytest_foo.plugin"]}, ...)

        In this case only ``pytest_foo/plugin.py`` will be rewritten.  If the
        helper module also contains assert statements which need to be
        rewritten it needs to be marked as such, before it gets imported.
        This is easiest by marking it for rewriting inside the
        ``__init__.py`` module, which will always be imported first when a
        module inside a package is imported.  This way ``plugin.py`` can still
        import ``helper.py`` normally.  The contents of
        ``pytest_foo/__init__.py`` will then need to look like this:

        .. code-block:: python

            import pytest

            pytest.register_assert_rewrite("pytest_foo.helper")


在测试模块或 conftest 文件中要求/加载插件
-----------------------------------------------------------

**Requiring/Loading plugins in a test module or conftest file**

.. tabs::

    .. tab:: 中文

        您可以在测试模块或 ``conftest.py`` 文件中使用 :globalvar:`pytest_plugins` 来要求插件：

        .. code-block:: python

            pytest_plugins = ["name1", "name2"]

        当加载测试模块或 conftest 插件时，指定的插件也将被加载。任何模块都可以被标记为插件，包括内部应用程序模块：

        .. code-block:: python

            pytest_plugins = "myapp.testsupport.myplugin"

        :globalvar:`pytest_plugins` 会递归处理，因此请注意，在上面的示例中，如果 ``myapp.testsupport.myplugin`` 也声明了 :globalvar:`pytest_plugins`，则该变量的内容也将被作为插件加载，依此类推。

        .. _`requiring plugins in non-root conftests`:

        .. note::
            在非根 ``conftest.py`` 文件中使用 :globalvar:`pytest_plugins` 变量要求插件已被弃用。

            这很重要，因为 ``conftest.py`` 文件实现了每个目录的钩子实现，但一旦导入了插件，它将影响整个目录树。为了避免混淆，在不位于测试根目录的任何 ``conftest.py`` 文件中定义 :globalvar:`pytest_plugins` 已被弃用，并会发出警告。

        此机制使在应用程序或甚至外部应用程序中共享 fixtures 变得简单，而无需使用 :std:doc:`entry point packaging metadata <packaging:guides/creating-and-discovering-plugins>` 技术创建外部插件。

        通过 :globalvar:`pytest_plugins` 导入的插件也会自动标记为断言重写（见 :func:`pytest.register_assert_rewrite`）。但是，为了使其生效，模块必须尚未被导入；如果在处理 :globalvar:`pytest_plugins` 语句时该模块已经被导入，将会产生警告，插件中的断言将不会被重写。要解决此问题，您可以在导入模块之前自己调用 :func:`pytest.register_assert_rewrite`，或者可以安排代码以延迟导入，直到插件注册之后。

    .. tab:: 英文

        You can require plugins in a test module or a ``conftest.py`` file using :globalvar:`pytest_plugins`:

        .. code-block:: python

            pytest_plugins = ["name1", "name2"]

        When the test module or conftest plugin is loaded the specified plugins
        will be loaded as well. Any module can be blessed as a plugin, including internal
        application modules:

        .. code-block:: python

            pytest_plugins = "myapp.testsupport.myplugin"

        :globalvar:`pytest_plugins` are processed recursively, so note that in the example above
        if ``myapp.testsupport.myplugin`` also declares :globalvar:`pytest_plugins`, the contents
        of the variable will also be loaded as plugins, and so on.

        .. _`requiring plugins in non-root conftests`:

        .. note::
            Requiring plugins using :globalvar:`pytest_plugins` variable in non-root
            ``conftest.py`` files is deprecated.

            This is important because ``conftest.py`` files implement per-directory
            hook implementations, but once a plugin is imported, it will affect the
            entire directory tree. In order to avoid confusion, defining
            :globalvar:`pytest_plugins` in any ``conftest.py`` file which is not located in the
            tests root directory is deprecated, and will raise a warning.

        This mechanism makes it easy to share fixtures within applications or even
        external applications without the need to create external plugins using the
        :std:doc:`entry point packaging metadata
        <packaging:guides/creating-and-discovering-plugins>` technique.

        Plugins imported by :globalvar:`pytest_plugins` will also automatically be marked
        for assertion rewriting (see :func:`pytest.register_assert_rewrite`).
        However for this to have any effect the module must not be
        imported already; if it was already imported at the time the
        :globalvar:`pytest_plugins` statement is processed, a warning will result and
        assertions inside the plugin will not be rewritten.  To fix this you
        can either call :func:`pytest.register_assert_rewrite` yourself before
        the module is imported, or you can arrange the code to delay the
        importing until after the plugin is registered.


通过名称访问另一个插件
--------------------------------

**Accessing another plugin by name**

.. tabs::

    .. tab:: 中文

        如果一个插件想要与另一个插件的代码协作，它可以通过插件管理器获得引用，如下所示：

        .. sourcecode:: python

            plugin = config.pluginmanager.get_plugin("name_of_plugin")

        如果您想查看现有插件的名称，请使用 ``--trace-config`` 选项。

    .. tab:: 英文

        If a plugin wants to collaborate with code from
        another plugin it can obtain a reference through
        the plugin manager like this:

        .. sourcecode:: python

            plugin = config.pluginmanager.get_plugin("name_of_plugin")

        If you want to look at the names of existing plugins, use
        the ``--trace-config`` option.


.. _registering-markers:

注册自定义标记
--------------------------

**Registering custom markers**

.. tabs::

    .. tab:: 中文

        如果您的插件使用了任何标记，您应该注册它们，以便它们出现在 pytest 的帮助文本中，并且不会 :ref:`导致虚假警告 <unknown-marks>`。
        例如，以下插件将为所有用户注册 ``cool_marker`` 和 ``mark_with`` :

        .. code-block:: python

            def pytest_configure(config):
                config.addinivalue_line("markers", "cool_marker: this one is for cool tests.")
                config.addinivalue_line(
                    "markers", "mark_with(arg, arg2): this marker takes arguments."
                )

    .. tab:: 英文

        If your plugin uses any markers, you should register them so that they appear in
        pytest's help text and do not :ref:`cause spurious warnings <unknown-marks>`.
        For example, the following plugin would register ``cool_marker`` and
        ``mark_with`` for all users:

        .. code-block:: python

            def pytest_configure(config):
                config.addinivalue_line("markers", "cool_marker: this one is for cool tests.")
                config.addinivalue_line(
                    "markers", "mark_with(arg, arg2): this marker takes arguments."
                )


测试插件
---------------

**Testing plugins**

.. tabs::

    .. tab:: 中文

        pytest 附带一个名为 ``pytester`` 的插件，帮助您为插件代码编写测试。该插件默认情况下是禁用的，因此在使用之前需要先启用它。

        您可以通过在测试目录中的 ``conftest.py`` 文件中添加以下行来做到这一点：

        .. code-block:: python

            # content of conftest.py

            pytest_plugins = ["pytester"]

        或者，您可以使用 ``-p pytester`` 命令行选项调用 pytest。

        这将允许您使用 :py:class:`pytester <pytest.Pytester>` 夹具来测试您的插件代码。

        让我们用一个示例演示您可以使用该插件做什么。假设我们开发了一个提供 ``hello`` 夹具的插件，该夹具返回一个函数，我们可以用一个可选参数调用此函数。如果不提供值，它将返回字符串 ``Hello World!``；如果提供了字符串值，则返回 ``Hello {value}!``。

        .. code-block:: python

            import pytest


            def pytest_addoption(parser):
                group = parser.getgroup("helloworld")
                group.addoption(
                    "--name",
                    action="store",
                    dest="name",
                    default="World",
                    help='Default "name" for hello().',
                )


            @pytest.fixture
            def hello(request):
                name = request.config.getoption("name")

                def _hello(name=None):
                    if not name:
                        name = request.config.getoption("name")
                    return f"Hello {name}!"

                return _hello


        现在，``pytester`` 夹具提供了一个方便的 API，用于创建临时的 ``conftest.py`` 文件和测试文件。它还允许我们运行测试并返回结果对象，通过该对象我们可以断言测试的结果。

        .. code-block:: python

            def test_hello(pytester):
                """确保我们的插件正常工作。"""

                # 创建一个临时 conftest.py 文件
                pytester.makeconftest(
                    """
                    import pytest

                    @pytest.fixture(params=[
                        "Brianna",
                        "Andreas",
                        "Floris",
                    ])
                    def name(request):
                        return request.param
                """
                )

                # 创建一个临时 pytest 测试文件
                pytester.makepyfile(
                    """
                    def test_hello_default(hello):
                        assert hello() == "Hello World!"

                    def test_hello_name(hello, name):
                        assert hello(name) == "Hello {0}!".format(name)
                """
                )

                # 使用 pytest 运行所有测试
                result = pytester.runpytest()

                # 检查所有 4 个测试是否通过
                result.assert_outcomes(passed=4)


        此外，在运行 pytest 之前，还可以将示例复制到 ``pytester`` 的隔离环境中。这样，我们可以将测试逻辑抽象到单独的文件中，这对于较长的测试和/或较长的 ``conftest.py`` 文件尤其有用。

        请注意，要使 ``pytester.copy_example`` 工作，我们需要在 ``pytest.ini`` 中设置 `pytester_example_dir`，以告知 pytest 在哪里查找示例文件。

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            pytester_example_dir = .


        .. code-block:: python

            # content of test_example.py


            def test_plugin(pytester):
                pytester.copy_example("test_example.py")
                pytester.runpytest("-k", "test_example")


            def test_example():
                pass

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            configfile: pytest.ini
            collected 2 items

            test_example.py ..                                                   [100%]

            ============================ 2 passed in 0.12s =============================

        有关 ``runpytest()`` 返回的结果对象以及它提供的方法的更多信息，请查看 :py:class:`RunResult <_pytest.pytester.RunResult>` 文档。

    .. tab:: 英文

        pytest comes with a plugin named ``pytester`` that helps you write tests for
        your plugin code. The plugin is disabled by default, so you will have to enable
        it before you can use it.

        You can do so by adding the following line to a ``conftest.py`` file in your
        testing directory:

        .. code-block:: python

            # content of conftest.py

            pytest_plugins = ["pytester"]

        Alternatively you can invoke pytest with the ``-p pytester`` command line
        option.

        This will allow you to use the :py:class:`pytester <pytest.Pytester>`
        fixture for testing your plugin code.

        Let's demonstrate what you can do with the plugin with an example. Imagine we
        developed a plugin that provides a fixture ``hello`` which yields a function
        and we can invoke this function with one optional parameter. It will return a
        string value of ``Hello World!`` if we do not supply a value or ``Hello
        {value}!`` if we do supply a string value.

        .. code-block:: python

            import pytest


            def pytest_addoption(parser):
                group = parser.getgroup("helloworld")
                group.addoption(
                    "--name",
                    action="store",
                    dest="name",
                    default="World",
                    help='Default "name" for hello().',
                )


            @pytest.fixture
            def hello(request):
                name = request.config.getoption("name")

                def _hello(name=None):
                    if not name:
                        name = request.config.getoption("name")
                    return f"Hello {name}!"

                return _hello


        Now the ``pytester`` fixture provides a convenient API for creating temporary
        ``conftest.py`` files and test files. It also allows us to run the tests and
        return a result object, with which we can assert the tests' outcomes.

        .. code-block:: python

            def test_hello(pytester):
                """Make sure that our plugin works."""

                # create a temporary conftest.py file
                pytester.makeconftest(
                    """
                    import pytest

                    @pytest.fixture(params=[
                        "Brianna",
                        "Andreas",
                        "Floris",
                    ])
                    def name(request):
                        return request.param
                """
                )

                # create a temporary pytest test file
                pytester.makepyfile(
                    """
                    def test_hello_default(hello):
                        assert hello() == "Hello World!"

                    def test_hello_name(hello, name):
                        assert hello(name) == "Hello {0}!".format(name)
                """
                )

                # run all tests with pytest
                result = pytester.runpytest()

                # check that all 4 tests passed
                result.assert_outcomes(passed=4)


        Additionally it is possible to copy examples to the ``pytester``'s isolated environment
        before running pytest on it. This way we can abstract the tested logic to separate files,
        which is especially useful for longer tests and/or longer ``conftest.py`` files.

        Note that for ``pytester.copy_example`` to work we need to set `pytester_example_dir`
        in our ``pytest.ini`` to tell pytest where to look for example files.

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            pytester_example_dir = .


        .. code-block:: python

            # content of test_example.py


            def test_plugin(pytester):
                pytester.copy_example("test_example.py")
                pytester.runpytest("-k", "test_example")


            def test_example():
                pass

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            configfile: pytest.ini
            collected 2 items

            test_example.py ..                                                   [100%]

            ============================ 2 passed in 0.12s =============================

        For more information about the result object that ``runpytest()`` returns, and
        the methods that it provides please check out the :py:class:`RunResult
        <_pytest.pytester.RunResult>` documentation.
