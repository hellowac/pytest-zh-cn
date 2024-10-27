配置
=============

**Configuration**

命令行选项和配置文件设置
-----------------------------------------------------------------

**Command line options and configuration file settings**

.. tabs::

    .. tab:: 中文

        您可以通过使用通用帮助选项获取关于命令行选项和 INI 风格配置文件中的值的帮助：

        .. code-block:: bash

            pytest -h   # 打印选项 _和_ 配置文件设置

        这将显示已安装插件注册的命令行和配置文件设置。

    .. tab:: 英文

        You can get help on command line options and values in INI-style
        configurations files by using the general help option:

        .. code-block:: bash

            pytest -h   # prints options _and_ config file settings

        This will display command line and configuration file settings
        which were registered by installed plugins.

.. _`config file formats`:

配置文件格式
--------------------------

**Configuration file formats**

.. tabs::

    .. tab:: 中文

        许多 :ref:`pytest 设置 <ini options ref>` 可以在 *配置文件* 中设置，按照约定，这些配置文件位于您代码库的根目录。

        以下是 pytest 支持的配置文件的快速示例：

    .. tab:: 英文

        Many :ref:`pytest settings <ini options ref>` can be set in a *configuration file*, which
        by convention resides in the root directory of your repository.

        A quick example of the configuration files supported by pytest:

pytest.ini
~~~~~~~~~~

**pytest.ini**

.. tabs::

    .. tab:: 中文

        ``pytest.ini`` 文件优先于其他文件，即使是空的文件。

        另外，可以使用隐藏版本 ``.pytest.ini``。

        .. code-block:: ini

            # pytest.ini 或 .pytest.ini
            [pytest]
            minversion = 6.0
            addopts = -ra -q
            testpaths =
                tests
                integration

    .. tab:: 英文

        ``pytest.ini`` files take precedence over other files, even when empty.

        Alternatively, the hidden version ``.pytest.ini`` can be used.

        .. code-block:: ini

            # pytest.ini or .pytest.ini
            [pytest]
            minversion = 6.0
            addopts = -ra -q
            testpaths =
                tests
                integration


pyproject.toml
~~~~~~~~~~~~~~

**pyproject.toml**

.. tabs::

    .. tab:: 中文

        .. versionadded:: 6.0

        ``pyproject.toml`` 文件在包含 ``tool.pytest.ini_options`` 表时会被考虑用于配置。

        .. code-block:: toml

            # pyproject.toml
            [tool.pytest.ini_options]
            minversion = "6.0"
            addopts = "-ra -q"
            testpaths = [
                "tests",
                "integration",
            ]

        .. note::

            可能有人会疑问为何使用 ``[tool.pytest.ini_options]`` 而不是 ``[tool.pytest]``，这与其他工具的情况不同。

            原因是 pytest 团队打算在未来充分利用丰富的 TOML 数据格式进行配置，因此保留 ``[tool.pytest]`` 表用于此目的。目前，``ini_options`` 表被用作现有 ``.ini`` 配置系统与未来配置格式之间的桥梁。

    .. tab:: 英文

        .. versionadded:: 6.0

        ``pyproject.toml`` are considered for configuration when they contain a ``tool.pytest.ini_options`` table.

        .. code-block:: toml

            # pyproject.toml
            [tool.pytest.ini_options]
            minversion = "6.0"
            addopts = "-ra -q"
            testpaths = [
                "tests",
                "integration",
            ]

        .. note::

            One might wonder why ``[tool.pytest.ini_options]`` instead of ``[tool.pytest]`` as is the
            case with other tools.

            The reason is that the pytest team intends to fully utilize the rich TOML data format
            for configuration in the future, reserving the ``[tool.pytest]`` table for that.
            The ``ini_options`` table is being used, for now, as a bridge between the existing
            ``.ini`` configuration system and the future configuration format.

tox.ini
~~~~~~~

**tox.ini**

.. tabs::

    .. tab:: 中文

        ``tox.ini`` 文件是 `tox <https://tox.readthedocs.io>`__ 项目的配置文件，也可以用于保存 pytest 配置，只要它们包含 ``[pytest]`` 部分。

        .. code-block:: ini

            # tox.ini
            [pytest]
            minversion = 6.0
            addopts = -ra -q
            testpaths =
                tests
                integration

    .. tab:: 英文

        ``tox.ini`` files are the configuration files of the `tox <https://tox.readthedocs.io>`__ project,
        and can also be used to hold pytest configuration if they have a ``[pytest]`` section.

        .. code-block:: ini

            # tox.ini
            [pytest]
            minversion = 6.0
            addopts = -ra -q
            testpaths =
                tests
                integration


setup.cfg
~~~~~~~~~

**setup.cfg**

.. tabs::

    .. tab:: 中文

        ``setup.cfg`` 文件是通用配置文件，最初由 ``distutils``（现已弃用）和 :std:doc:`setuptools <setuptools:userguide/declarative_config>` 使用，也可以用于保存 pytest 配置，只要它们包含 ``[tool:pytest]`` 部分。

        .. code-block:: ini

            # setup.cfg
            [tool:pytest]
            minversion = 6.0
            addopts = -ra -q
            testpaths =
                tests
                integration

        .. warning::

            不建议使用 ``setup.cfg``，除非用于非常简单的用例。``.cfg``
            文件使用与 ``pytest.ini`` 和 ``tox.ini`` 不同的解析器，这可能导致难以追踪的问题。
            尽可能建议使用后者文件或 ``pyproject.toml`` 来保存您的
            pytest 配置。

    .. tab:: 英文

        ``setup.cfg`` files are general purpose configuration files, used originally by ``distutils`` (now deprecated) and :std:doc:`setuptools <setuptools:userguide/declarative_config>`, and can also be used to hold pytest configuration
        if they have a ``[tool:pytest]`` section.

        .. code-block:: ini

            # setup.cfg
            [tool:pytest]
            minversion = 6.0
            addopts = -ra -q
            testpaths =
                tests
                integration

        .. warning::

            Usage of ``setup.cfg`` is not recommended unless for very simple use cases. ``.cfg``
            files use a different parser than ``pytest.ini`` and ``tox.ini`` which might cause hard to track
            down problems.
            When possible, it is recommended to use the latter files, or ``pyproject.toml``, to hold your
            pytest configuration.


.. _rootdir:
.. _configfiles:

初始化：确定 rootdir 和 configfile
--------------------------------------------------

**Initialization: determining rootdir and configfile**

.. tabs::

    .. tab:: 中文

        pytest 为每次测试运行确定一个 ``rootdir``，这取决于命令行参数（指定的测试文件、路径）以及配置文件的存在。确定的 ``rootdir`` 和 ``configfile`` 会在启动时作为 pytest 头部的一部分打印出来。

        以下是 ``pytest`` 使用 ``rootdir`` 的总结：

        * 在收集期间构造 *nodeids*；每个测试会被分配一个唯一的 *nodeid*，该 *nodeid* 以 ``rootdir`` 为根，考虑完整路径、类名、函数名和参数化（如有）。

        * 被插件用作存储项目/测试运行特定信息的稳定位置；例如，内部的 :ref:`cache <cache>` 插件会在 ``rootdir`` 中创建一个 ``.pytest_cache`` 子目录以存储其跨测试运行状态。

        ``rootdir`` **不** 用于修改 ``sys.path``/``PYTHONPATH`` 或影响模块的导入方式。有关更多细节，请参见 :ref:`pythonpath`。

        ``--rootdir=path`` 命令行选项可用于强制指定一个特定目录。
        请注意，与其他命令行选项不同，``--rootdir`` 不能与 ``pytest.ini`` 中的 :confval:`addopts` 一起使用，因为 ``rootdir`` 已经用于 *查找* ``pytest.ini``。

    .. tab:: 英文

        pytest determines a ``rootdir`` for each test run which depends on
        the command line arguments (specified test files, paths) and on
        the existence of configuration files.  The determined ``rootdir`` and ``configfile`` are
        printed as part of the pytest header during startup.

        Here's a summary what ``pytest`` uses ``rootdir`` for:

        * Construct *nodeids* during collection; each test is assigned
        a unique *nodeid* which is rooted at the ``rootdir`` and takes into account
        the full path, class name, function name and parametrization (if any).

        * Is used by plugins as a stable location to store project/test run specific information;
        for example, the internal :ref:`cache <cache>` plugin creates a ``.pytest_cache`` subdirectory
        in ``rootdir`` to store its cross-test run state.

        ``rootdir`` is **NOT** used to modify ``sys.path``/``PYTHONPATH`` or
        influence how modules are imported. See :ref:`pythonpath` for more details.

        The ``--rootdir=path`` command-line option can be used to force a specific directory.
        Note that contrary to other command-line options, ``--rootdir`` cannot be used with
        :confval:`addopts` inside ``pytest.ini`` because the ``rootdir`` is used to *find* ``pytest.ini``
        already.

查找 ``rootdir``
~~~~~~~~~~~~~~~~~~~~~~~

**Finding the ``rootdir``**

.. tabs::

    .. tab:: 中文

        以下是从 ``args`` 中查找 ``rootdir`` 的算法：

        - 如果命令行中传入了 ``-c``，则将其用作配置文件，并将其目录作为 ``rootdir``。

        - 确定指定的 ``args`` 的共同祖先目录，这些路径被识别为文件系统中存在的路径。如果没有找到这样的路径，则共同祖先目录设置为当前工作目录。

        - 在祖先目录及其上层查找 ``pytest.ini``、``pyproject.toml``、``tox.ini`` 和 ``setup.cfg`` 文件。如果匹配到一个文件，它将成为 ``configfile``，其目录将成为 ``rootdir``。

        - 如果未找到配置文件，则从共同祖先目录向上查找 ``setup.py`` 来确定 ``rootdir``。

        - 如果未找到 ``setup.py``，则在每个指定的 ``args`` 及其上层查找 ``pytest.ini``、``pyproject.toml``、``tox.ini`` 和 ``setup.cfg``。如果匹配到一个文件，它将成为 ``configfile``，其目录将成为 ``rootdir``。

        - 如果未找到 ``configfile`` 且未传递配置参数，则使用已经确定的共同祖先作为根目录。这允许在不属于某个包且没有特定配置文件的结构中使用 pytest。

        如果没有给出 ``args``，pytest 将在当前工作目录下收集测试，并从那里开始确定 ``rootdir``。

        文件仅在满足以下条件时才会匹配为配置：

        * ``pytest.ini``：将始终匹配并优先，即使是空的。
        * ``pyproject.toml``：包含 ``[tool.pytest.ini_options]`` 表。
        * ``tox.ini``：包含 ``[pytest]`` 部分。
        * ``setup.cfg``：包含 ``[tool:pytest]`` 部分。

        最后，如果没有找到其他匹配项，则 ``pyproject.toml`` 文件将被视为 ``configfile``，即使它不包含 ``[tool.pytest.ini_options]`` 表（这一点是在 ``8.1`` 中添加的）。

        文件将按照上述顺序考虑。来自多个 ``configfile`` 候选的选项永远不会合并 - 第一个匹配的胜出。

        配置文件还确定 ``rootpath`` 的值。

        :class:`Config <pytest.Config>` 对象（可以通过钩子或 :fixture:`pytestconfig` 夹具访问）将随后携带这些属性：

        - :attr:`config.rootpath <pytest.Config.rootpath>`：确定的根目录，保证存在。它用作构建测试地址（“nodeids”）的参考目录，也可以被插件用于存储每次测试运行的信息。

        - :attr:`config.inipath <pytest.Config.inipath>`：确定的 ``configfile``，可能为 ``None``（出于历史原因命名为 ``inipath``）。

        .. versionadded:: 6.1
            ``config.rootpath`` 和 ``config.inipath`` 属性。它们是较旧的 ``config.rootdir`` 和 ``config.inifile`` 的 :class:`pathlib.Path` 版本，后者的类型为 ``py.path.local``，并仍然存在以保持向后兼容。

        示例：

        .. code-block:: bash

            pytest path/to/testdir path/other/

        将确定共同祖先为 ``path``，然后按以下方式检查配置文件：

        .. code-block:: text

            # 首先查找 pytest.ini 文件
            path/pytest.ini
            path/pyproject.toml  # 必须包含 [tool.pytest.ini_options] 表以匹配
            path/tox.ini         # 必须包含 [pytest] 部分以匹配
            path/setup.cfg       # 必须包含 [tool:pytest] 部分以匹配
            pytest.ini
            ... # 一直到根目录

            # 现在查找 setup.py
            path/setup.py
            setup.py
            ... # 一直到根目录

        .. warning::

            自定义 pytest 插件命令行参数可能包含路径，如
            ``pytest --log-output ../../test.log args``。在这种情况下，``args`` 是强制性的，
            否则 pytest 会使用 test.log 的文件夹来确定 rootdir
            （另请参见 :issue:`1435`）。
            也可以使用点 ``.`` 来引用当前工作目录。

    .. tab:: 英文

        Here is the algorithm which finds the rootdir from ``args``:

        - If ``-c`` is passed in the command-line, use that as configuration file, and its directory as ``rootdir``.

        - Determine the common ancestor directory for the specified ``args`` that are recognised as paths that exist in the file system. If no such paths are found, the common ancestor directory is set to the current working directory.

        - Look for ``pytest.ini``, ``pyproject.toml``, ``tox.ini``, and ``setup.cfg`` files in the ancestor directory and upwards.  If one is matched, it becomes the ``configfile`` and its directory becomes the ``rootdir``.

        - If no configuration file was found, look for ``setup.py`` upwards from the common ancestor directory to determine the ``rootdir``.

        - If no ``setup.py`` was found, look for ``pytest.ini``, ``pyproject.toml``, ``tox.ini``, and ``setup.cfg`` in each of the specified ``args`` and upwards. If one is matched, it becomes the ``configfile`` and its directory becomes the ``rootdir``.

        - If no ``configfile`` was found and no configuration argument is passed, use the already determined common ancestor as root directory. This allows the use of pytest in structures that are not part of a package and don't have any particular configuration file.

        If no ``args`` are given, pytest collects test below the current working
        directory and also starts determining the ``rootdir`` from there.

        Files will only be matched for configuration if:

        * ``pytest.ini``: will always match and take precedence, even if empty.
        * ``pyproject.toml``: contains a ``[tool.pytest.ini_options]`` table.
        * ``tox.ini``: contains a ``[pytest]`` section.
        * ``setup.cfg``: contains a ``[tool:pytest]`` section.

        Finally, a ``pyproject.toml`` file will be considered the ``configfile`` if no other match was found, in this case
        even if it does not contain a ``[tool.pytest.ini_options]`` table (this was added in ``8.1``).

        The files are considered in the order above. Options from multiple ``configfiles`` candidates
        are never merged - the first match wins.

        The configuration file also determines the value of the ``rootpath``.

        The :class:`Config <pytest.Config>` object (accessible via hooks or through the :fixture:`pytestconfig` fixture) will subsequently carry these attributes:

        - :attr:`config.rootpath <pytest.Config.rootpath>`: the determined root directory, guaranteed to exist. It is used as a reference directory for constructing test addresses ("nodeids") and can be used also by plugins for storing per-testrun information.

        - :attr:`config.inipath <pytest.Config.inipath>`: the determined ``configfile``, may be ``None`` (it is named ``inipath`` for historical reasons).

        .. versionadded:: 6.1
            The ``config.rootpath`` and ``config.inipath`` properties. They are :class:`pathlib.Path` versions of the older ``config.rootdir`` and ``config.inifile``, which have type ``py.path.local``, and still exist for backward compatibility.



        Example:

        .. code-block:: bash

            pytest path/to/testdir path/other/

        will determine the common ancestor as ``path`` and then
        check for configuration files as follows:

        .. code-block:: text

            # first look for pytest.ini files
            path/pytest.ini
            path/pyproject.toml  # must contain a [tool.pytest.ini_options] table to match
            path/tox.ini         # must contain [pytest] section to match
            path/setup.cfg       # must contain [tool:pytest] section to match
            pytest.ini
            ... # all the way up to the root

            # now look for setup.py
            path/setup.py
            setup.py
            ... # all the way up to the root


        .. warning::

            Custom pytest plugin commandline arguments may include a path, as in
            ``pytest --log-output ../../test.log args``. Then ``args`` is mandatory,
            otherwise pytest uses the folder of test.log for rootdir determination
            (see also :issue:`1435`).
            A dot ``.`` for referencing to the current working directory is also
            possible.


.. _`how to change command line options defaults`:
.. _`adding default options`:


内置配置文件选项
----------------------------------------------

**Builtin configuration file options**

.. tabs::

    .. tab:: 中文

        有关完整的选项列表，请参阅 :ref:`参考文档 <ini options ref>`。

    .. tab:: 英文

        For the full list of options consult the :ref:`reference documentation <ini options ref>`.

语法高亮主题自定义
---------------------------------------

**Syntax highlighting theme customization**

.. tabs::

    .. tab:: 中文

        pytest 使用的语法高亮主题可以通过两个环境变量进行自定义：

        - :envvar:`PYTEST_THEME` 设置要使用的 `pygment style <https://pygments.org/docs/styles/>`_ 。
        - :envvar:`PYTEST_THEME_MODE` 将此样式设置为 *light* 或 *dark*。

    .. tab:: 英文

        The syntax highlighting themes used by pytest can be customized using two environment variables:

        - :envvar:`PYTEST_THEME` sets a `pygment style <https://pygments.org/docs/styles/>`_ to use.
        - :envvar:`PYTEST_THEME_MODE` sets this style to *light* or *dark*.
