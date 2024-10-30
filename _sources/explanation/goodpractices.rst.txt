.. highlight:: python
.. _`goodpractices`:

良好的集成实践
=================================================

**Good Integration Practices**

使用 pip 安装软件包
-------------------------------------------------

**Install package with pip**

.. tabs::

    .. tab:: 中文

        对于开发，我们建议您使用 :mod:`venv` 来创建虚拟环境，并使用 :doc:`pip:index` 来安装您的应用程序及其任何依赖项，以及 ``pytest`` 包本身。这确保了您的代码和依赖项与系统 Python 安装隔离。

        在您的代码库根目录下创建一个 ``pyproject.toml`` 文件，具体如 :doc:`packaging:tutorials/packaging-projects` 中所述。前几行应如下所示：

        .. code-block:: toml

            [build-system]
            requires = ["hatchling"]
            build-backend = "hatchling.build"

            [project]
            name = "PACKAGENAME"
            version = "PACKAGEVERSION"

        其中 ``PACKAGENAME`` 和 ``PACKAGEVERSION`` 分别是您包的名称和版本。

        然后，您可以通过从同一目录运行以下命令以 "editable" 模式安装您的包：

        .. code-block:: bash

            pip install -e .

        这让您可以随意更改源代码（包括测试和应用程序），并重新运行测试。

    .. tab:: 英文

        For development, we recommend you use :mod:`venv` for virtual environments and
        :doc:`pip:index` for installing your application and any dependencies,
        as well as the ``pytest`` package itself.
        This ensures your code and dependencies are isolated from your system Python installation.

        Create a ``pyproject.toml`` file in the root of your repository as described in
        :doc:`packaging:tutorials/packaging-projects`.
        The first few lines should look like this:

        .. code-block:: toml

            [build-system]
            requires = ["hatchling"]
            build-backend = "hatchling.build"

            [project]
            name = "PACKAGENAME"
            version = "PACKAGEVERSION"

        where ``PACKAGENAME`` and ``PACKAGEVERSION`` are the name and version of your package respectively.

        You can then install your package in "editable" mode by running from the same directory:

        .. code-block:: bash

            pip install -e .

        which lets you change your source code (both tests and application) and rerun tests at will.

.. _`test discovery`:
.. _`Python test discovery`:

Python 测试发现的约定
-------------------------------------------------

**Conventions for Python test discovery**

.. tabs::

    .. tab:: 中文

        ``pytest`` 实现了以下标准测试发现流程：

        * 如果未指定任何参数，则从 :confval:`testpaths` 开始收集（如果已配置），或者从当前目录开始。或者，可以使用任意组合的目录、文件名或节点 ID 作为命令行参数。
        * 递归进入目录，除非它们与 :confval:`norecursedirs` 匹配。
        * 在这些目录中，搜索 ``test_*.py`` 或 ``*_test.py`` 文件，按其 `test package name`_ 导入。
        * 从这些文件中收集测试项：

        * 以 ``test`` 为前缀的测试函数或类外的方法。
        * 以 ``test`` 为前缀的测试函数或 ``Test`` 前缀测试类中的方法（不带 ``__init__`` 方法）。使用 ``@staticmethod`` 和 ``@classmethod`` 装饰的方法也被考虑在内。

        有关如何自定义测试发现的示例，请参见 :doc:`/example/pythoncollection`。

        在 Python 模块中，``pytest`` 还使用标准的 :ref:`unittest.TestCase <unittest.TestCase>` 子类化技术发现测试。

    .. tab:: 英文

        ``pytest`` implements the following standard test discovery:

        * If no arguments are specified then collection starts from :confval:`testpaths`
        (if configured) or the current directory. Alternatively, command line arguments
        can be used in any combination of directories, file names or node ids.
        * Recurse into directories, unless they match :confval:`norecursedirs`.
        * In those directories, search for ``test_*.py`` or ``*_test.py`` files, imported by their `test package name`_.
        * From those files, collect test items:

        * ``test`` prefixed test functions or methods outside of class.
        * ``test`` prefixed test functions or methods inside ``Test`` prefixed test classes (without an ``__init__`` method). Methods decorated with ``@staticmethod`` and ``@classmethods`` are also considered.

        For examples of how to customize your test discovery :doc:`/example/pythoncollection`.

        Within Python modules, ``pytest`` also discovers tests using the standard
        :ref:`unittest.TestCase <unittest.TestCase>` subclassing technique.


.. _`test layout`:

选择测试布局
----------------------

**Choosing a test layout**

.. tabs::

    .. tab:: 中文

        ``pytest`` 支持两种常见的测试布局：

    .. tab:: 英文

        ``pytest`` supports two common test layouts:

在应用程序代码之外进行测试
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Tests outside application code**

.. _src-layout:

.. _blog post: https://blog.ionelmc.ro/2014/05/25/python-packaging/#the-structure>


.. tabs::

    .. tab:: 中文

        将测试放置在实际应用代码之外的额外目录中可能很有用，特别是当您有许多功能测试或出于其他原因希望将测试与实际应用代码分开时（这通常是个好主意）：

        .. code-block:: text

            pyproject.toml
            src/
                mypkg/
                    __init__.py
                    app.py
                    view.py
            tests/
                test_app.py
                test_view.py
                ...

        这有以下好处：

        * 在执行 ``pip install .`` 后，您的测试可以针对已安装的版本运行。
        * 在执行 ``pip install --editable .`` 后，您的测试可以针对本地副本进行可编辑安装。

        对于新项目，我们建议使用 ``importlib`` :ref:`import mode <import-modes>` （有关详细解释，请参见 which-import-mode_）。
        为此，将以下内容添加到您的 ``pyproject.toml`` 中：

        .. code-block:: toml

            [tool.pytest.ini_options]
            addopts = [
                "--import-mode=importlib",
            ]


        通常，特别是如果您使用默认的导入模式 ``prepend``，**强烈**建议使用 ``src`` 布局。
        在此布局中，您的应用程序根包位于根目录的子目录中，即 ``src/mypkg/`` 而不是 ``mypkg``。

        这种布局可以避免许多常见的陷阱，并且有许多好处，Ionel Cristian Mărieș 在这篇优秀的 `blog post`_ 中对此进行了更好的解释。

        

        .. note::

            如果您不使用可编辑安装并使用上述 ``src`` 布局，则需要扩展 Python 的模块文件搜索路径，以便直接对本地副本执行测试。您可以通过设置 ``PYTHONPATH`` 环境变量以临时方式实现：

            .. code-block:: bash

            PYTHONPATH=src pytest

            或通过使用 :confval:`pythonpath` 配置变量并将以下内容添加到您的 ``pyproject.toml`` 中，以永久方式实现：

            .. code-block:: toml

                [tool.pytest.ini_options]
                pythonpath = "src"

        .. note::

            如果您不使用可编辑安装且不使用 ``src`` 布局（``mypkg`` 直接位于根目录中），则可以依赖于 Python 默认将当前目录放入 ``sys.path`` 中来导入您的包，并运行 ``python -m pytest`` 直接对本地副本执行测试。

            有关调用 ``pytest`` 和 ``python -m pytest`` 之间差异的更多信息，请参见 :ref:`pytest vs python -m pytest`。

    .. tab:: 英文

        Putting tests into an extra directory outside your actual application code
        might be useful if you have many functional tests or for other reasons want
        to keep tests separate from actual application code (often a good idea):

        .. code-block:: text

            pyproject.toml
            src/
                mypkg/
                    __init__.py
                    app.py
                    view.py
            tests/
                test_app.py
                test_view.py
                ...

        This has the following benefits:

        * Your tests can run against an installed version after executing ``pip install .``.
        * Your tests can run against the local copy with an editable install after executing ``pip install --editable .``.

        For new projects, we recommend to use ``importlib`` :ref:`import mode <import-modes>`
        (see which-import-mode_ for a detailed explanation).
        To this end, add the following to your ``pyproject.toml``:

        .. code-block:: toml

            [tool.pytest.ini_options]
            addopts = [
                "--import-mode=importlib",
            ]

        Generally, but especially if you use the default import mode ``prepend``,
        it is **strongly** suggested to use a ``src`` layout.
        Here, your application root package resides in a sub-directory of your root,
        i.e. ``src/mypkg/`` instead of ``mypkg``.

        This layout prevents a lot of common pitfalls and has many benefits,
        which are better explained in this excellent `blog post`_ by Ionel Cristian Mărieș.

        .. note::

            If you do not use an editable install and use the ``src`` layout as above you need to extend the Python's
            search path for module files to execute the tests against the local copy directly. You can do it in an
            ad-hoc manner by setting the ``PYTHONPATH`` environment variable:

            .. code-block:: bash

            PYTHONPATH=src pytest

            or in a permanent manner by using the :confval:`pythonpath` configuration variable and adding the
            following to your ``pyproject.toml``:

            .. code-block:: toml

                [tool.pytest.ini_options]
                pythonpath = "src"

        .. note::

            If you do not use an editable install and not use the ``src`` layout (``mypkg`` directly in the root
            directory) you can rely on the fact that Python by default puts the current directory in ``sys.path`` to
            import your package and run ``python -m pytest`` to execute the tests against the local copy directly.

            See :ref:`pytest vs python -m pytest` for more information about the difference between calling ``pytest`` and
            ``python -m pytest``.

作为应用程序代码的一部分进行测试
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Tests as part of application code**


.. _`test package name`:

.. tabs::

    .. tab:: 中文

        将测试目录内联到您的应用程序包中是有用的，特别是当您有测试与应用程序模块之间的直接关系，并希望将它们与应用程序一起分发时：

        .. code-block:: text

            pyproject.toml
            [src/]mypkg/
                __init__.py
                app.py
                view.py
                tests/
                    __init__.py
                    test_app.py
                    test_view.py
                    ...

        在这种方案中，使用 ``--pyargs`` 选项运行测试非常简单：

        .. code-block:: bash

            pytest --pyargs mypkg

        ``pytest`` 将发现 ``mypkg`` 的安装位置并从那里收集测试。

        请注意，这种布局也可以与前一节提到的 ``src`` 布局结合使用。

        .. note::

            您可以为您的应用程序使用命名空间包（PEP420），但 pytest 仍将根据 ``__init__.py`` 文件的存在执行 `test package name`_ 发现。如果您使用上述两种推荐的文件系统布局之一，但将 ``__init__.py`` 文件从目录中省略，它应该可以正常工作。然而，对于“内联测试”，您需要使用绝对导入来访问应用程序代码。

        .. note::

            在 ``prepend`` 和 ``append`` 导入模式中，如果 pytest 在递归文件系统时找到 ``"a/b/test_module.py"`` 测试文件，它将按以下方式确定导入名称：

            * 确定 ``basedir``：这是第一个不包含 ``__init__.py`` 的“向上”目录（朝向根目录）。例如，如果 ``a`` 和 ``b`` 都包含 ``__init__.py`` 文件，则 ``a`` 的父目录将成为 ``basedir``。

            * 执行 ``sys.path.insert(0, basedir)`` 以使测试模块可以通过完全限定的导入名称导入。

            * ``import a.b.test_module``，路径通过将路径分隔符 ``/`` 转换为 "." 字符来确定。这意味着您必须遵循目录和文件名称直接映射到导入名称的约定。

            采用这种稍微复杂的导入技术的原因是，在较大的项目中，多个测试模块可能相互导入，因此推导出一个标准导入名称有助于避免意外情况，例如测试模块被导入两次。

            使用 ``--import-mode=importlib``，事情就简单多了，因为 pytest 不需要更改 ``sys.path`` 或 ``sys.modules``，从而使事情变得不那么令人惊讶。

    .. tab:: 英文

        Inlining test directories into your application package
        is useful if you have direct relation between tests and application modules and
        want to distribute them along with your application:

        .. code-block:: text

            pyproject.toml
            [src/]mypkg/
                __init__.py
                app.py
                view.py
                tests/
                    __init__.py
                    test_app.py
                    test_view.py
                    ...

        In this scheme, it is easy to run your tests using the ``--pyargs`` option:

        .. code-block:: bash

            pytest --pyargs mypkg

        ``pytest`` will discover where ``mypkg`` is installed and collect tests from there.

        Note that this layout also works in conjunction with the ``src`` layout mentioned in the previous section.


        .. note::

            You can use namespace packages (PEP420) for your application
            but pytest will still perform `test package name`_ discovery based on the
            presence of ``__init__.py`` files.  If you use one of the
            two recommended file system layouts above but leave away the ``__init__.py``
            files from your directories, it should just work.  From
            "inlined tests", however, you will need to use absolute imports for
            getting at your application code.


        .. note::

            In ``prepend`` and ``append`` import-modes, if pytest finds a ``"a/b/test_module.py"``
            test file while recursing into the filesystem it determines the import name
            as follows:

            * determine ``basedir``: this is the first "upward" (towards the root)
            directory not containing an ``__init__.py``.  If e.g. both ``a``
            and ``b`` contain an ``__init__.py`` file then the parent directory
            of ``a`` will become the ``basedir``.

            * perform ``sys.path.insert(0, basedir)`` to make the test module
            importable under the fully qualified import name.

            * ``import a.b.test_module`` where the path is determined
            by converting path separators ``/`` into "." characters.  This means
            you must follow the convention of having directory and file
            names map directly to the import names.

            The reason for this somewhat evolved importing technique is
            that in larger projects multiple test modules might import
            from each other and thus deriving a canonical import name helps
            to avoid surprises such as a test module getting imported twice.

            With ``--import-mode=importlib`` things are less convoluted because
            pytest doesn't need to change ``sys.path`` or ``sys.modules``, making things
            much less surprising.


.. _which-import-mode:

选择导入模式
^^^^^^^^^^^^^^^^^^^^^^^

**Choosing an import mode**

.. tabs::

    .. tab:: 中文

        出于历史原因，pytest 默认使用 ``prepend`` :ref:`import mode <import-modes>`，而不是我们为新项目推荐的 ``importlib`` 导入模式。原因在于 ``prepend`` 模式的工作方式：

        由于没有包可用来推导完整的包名，``pytest`` 将把您的测试文件作为 *顶级* 模块导入。在第一个示例中（:ref:`src layout <src-layout>`），测试文件将作为 ``test_app`` 和 ``test_view`` 顶级模块导入，通过将 ``tests/`` 添加到 ``sys.path``。

        这导致与 ``importlib`` 导入模式相比的一个缺点：您的测试文件必须具有 **唯一名称**。

        如果您需要具有相同名称的测试模块，作为变通方法，您可以在 ``tests`` 文件夹及其子文件夹中添加 ``__init__.py`` 文件，将它们更改为包：

        .. code-block:: text

            pyproject.toml
            mypkg/
                ...
            tests/
                __init__.py
                foo/
                    __init__.py
                    test_view.py
                bar/
                    __init__.py
                    test_view.py

        现在 pytest 将加载模块为 ``tests.foo.test_view`` 和 ``tests.bar.test_view``，允许您拥有相同名称的模块。但这会引入一个微妙的问题：为了从 ``tests`` 目录加载测试模块，pytest 会将仓库根目录添加到 ``sys.path``，这会带来一个副作用，即 ``mypkg`` 也变得可以导入。

        如果您使用像 tox_ 这样的工具在虚拟环境中测试您的包，这将是一个问题，因为您希望测试的是您包的 *已安装* 版本，而不是来自仓库的本地代码。

        ``importlib`` 导入模式没有上述缺点，因为在导入测试模块时不会更改 ``sys.path``。

    .. tab:: 英文

        For historical reasons, pytest defaults to the ``prepend`` :ref:`import mode <import-modes>`
        instead of the ``importlib`` import mode we recommend for new projects.
        The reason lies in the way the ``prepend`` mode works:

        Since there are no packages to derive a full package name from,
        ``pytest`` will import your test files as *top-level* modules.
        The test files in the first example (:ref:`src layout <src-layout>`) would be imported as
        ``test_app`` and ``test_view`` top-level modules by adding ``tests/`` to ``sys.path``.

        This results in a drawback compared to the import mode ``importlib``:
        your test files must have **unique names**.

        If you need to have test modules with the same name,
        as a workaround you might add ``__init__.py`` files to your ``tests`` folder and subfolders,
        changing them to packages:

        .. code-block:: text

            pyproject.toml
            mypkg/
                ...
            tests/
                __init__.py
                foo/
                    __init__.py
                    test_view.py
                bar/
                    __init__.py
                    test_view.py

        Now pytest will load the modules as ``tests.foo.test_view`` and ``tests.bar.test_view``,
        allowing you to have modules with the same name.
        But now this introduces a subtle problem:
        in order to load the test modules from the ``tests`` directory,
        pytest prepends the root of the repository to ``sys.path``,
        which adds the side-effect that now ``mypkg`` is also importable.

        This is problematic if you are using a tool like tox_ to test your package in a virtual environment,
        because you want to test the *installed* version of your package,
        not the local code from the repository.

        The ``importlib`` import mode does not have any of the drawbacks above,
        because ``sys.path`` is not changed when importing test modules.


.. _`buildout`: http://www.buildout.org/en/latest/

.. _`use tox`:

tox
---

.. tabs::

    .. tab:: 中文

        一旦您完成工作并想确保您的实际包通过所有测试，您可能想要了解 :doc:`tox <tox:index>`，这是一个虚拟环境测试自动化工具。 ``tox`` 帮助您设置带有预定义依赖项的虚拟环境，然后执行带有选项的预配置测试命令。它将针对已安装的包运行测试，而不是针对您的源代码检出，从而帮助检测打包中的问题。

    .. tab:: 英文

        Once you are done with your work and want to make sure that your actual
        package passes all tests you may want to look into :doc:`tox <tox:index>`, the
        virtualenv test automation tool.
        ``tox`` helps you to setup virtualenv environments with pre-defined
        dependencies and then executing a pre-configured test command with
        options.  It will run tests against the installed package and not
        against your source code checkout, helping to detect packaging
        glitches.

不要通过 setuptools 运行
-------------------------

**Do not run via setuptools**

.. tabs::

    .. tab:: 中文

        与 setuptools 的集成 **不推荐**，即您不应使用 ``python setup.py test`` 或 ``pytest-runner``，并且未来可能会停止工作。

        这是不推荐使用的，因为它依赖于 setuptools 的已弃用功能，并依赖于破坏 pip 安全机制的功能。例如，'setup_requires' 和 'tests_require' 绕过了 ``pip --require-hashes``。有关更多信息和迁移说明，请参见 `pytest-runner notice <https://github.com/pytest-dev/pytest-runner#deprecation-notice>`_。另请参见 `pypa/setuptools#1684 <https://github.com/pypa/setuptools/issues/1684>`_。

        setuptools 打算 `移除测试命令 <https://github.com/pypa/setuptools/issues/931>`_。

    .. tab:: 英文

        Integration with setuptools is **not recommended**,
        i.e. you should not be using ``python setup.py test`` or ``pytest-runner``,
        and may stop working in the future.

        This is deprecated since it depends on deprecated features of setuptools
        and relies on features that break security mechanisms in pip.
        For example 'setup_requires' and 'tests_require' bypass ``pip --require-hashes``.
        For more information and migration instructions,
        see the `pytest-runner notice <https://github.com/pytest-dev/pytest-runner#deprecation-notice>`_.
        See also `pypa/setuptools#1684 <https://github.com/pypa/setuptools/issues/1684>`_.

        setuptools intends to
        `remove the test command <https://github.com/pypa/setuptools/issues/931>`_.

使用 flake8-pytest-style 进行检查
---------------------------------

**Checking with flake8-pytest-style**

.. tabs::

    .. tab:: 中文

        为了确保在您的项目中正确使用 pytest，使用 `flake8-pytest-style <https://github.com/m-burst/flake8-pytest-style>`_ flake8 插件是非常有帮助的。

        flake8-pytest-style 检查 pytest 代码中的常见错误和编码风格违规，例如不正确使用 fixtures、测试函数名称和标记。通过使用此插件，您可以在开发过程中及早捕获这些错误，并确保您的 pytest 代码一致且易于维护。

        在其 `PyPI 页面 <https://pypi.org/project/flake8-pytest-style/>`_ 上可以找到 flake8-pytest-style 检测到的 lint 列表。

        .. note::

            flake8-pytest-style 不是官方的 pytest 项目。某些规则强制执行特定的风格选择，例如使用 `@pytest.fixture()` 而不是 `@pytest.fixture`，但您可以配置插件以适应您喜欢的风格。

    .. tab:: 英文

        In order to ensure that pytest is being used correctly in your project,
        it can be helpful to use the `flake8-pytest-style <https://github.com/m-burst/flake8-pytest-style>`_ flake8 plugin.

        flake8-pytest-style checks for common mistakes and coding style violations in pytest code,
        such as incorrect use of fixtures, test function names, and markers.
        By using this plugin, you can catch these errors early in the development process
        and ensure that your pytest code is consistent and easy to maintain.

        A list of the lints detected by flake8-pytest-style can be found on its `PyPI page <https://pypi.org/project/flake8-pytest-style/>`_.

        .. note::

            flake8-pytest-style is not an official pytest project. Some of the rules enforce certain style choices, such as using `@pytest.fixture()` over `@pytest.fixture`, but you can configure the plugin to fit your preferred style.
