.. _pythonpath:

pytest 导入机制和 ``sys.path`` / ``PYTHONPATH``
========================================================

**pytest import mechanisms and** ``sys.path`` / ``PYTHONPATH``

.. _`import-modes`:

导入模式
------------

**Import modes**

.. tabs::

    .. tab:: 中文

        pytest 作为一个测试框架，需要导入测试模块和 ``conftest.py`` 文件以进行执行。

        在 Python 中导入文件是一个非平凡的过程，因此可以通过 ``--import-mode`` 命令行标志控制导入过程的某些方面，该标志可以取以下值：

        .. _`import-mode-prepend`:

        * ``prepend`` （默认值）：如果模块所在的目录路径尚未在 :py:data:`sys.path` 中，则将其插入到 *开始* 位置，然后使用 :func:`importlib.import_module <importlib.import_module>` 函数进行导入。

          强烈建议通过向包含测试的目录添加 ``__init__.py`` 文件，将测试模块安排为包。这将使测试成为一个合适的 Python 包的一部分，从而允许 pytest 解析它们的完整名称（例如，``tests.core.test_core`` 对应于 ``tests/core/test_core.py``）。

          如果测试目录树没有按包的方式组织，那么每个测试文件需要与其他测试文件具有唯一名称，否则 pytest 会在发现两个具有相同名称的测试时引发错误。

          这是经典机制，追溯到 Python 2 仍在支持的时代。

        .. _`import-mode-append`:

        * ``append`` ：如果模块所在的目录尚未在 :py:data:`sys.path` 中，则将其附加到 :py:data:`sys.path` 的末尾，并使用 :func:`importlib.import_module <importlib.import_module>` 进行导入。

          这更好地允许用户在包的安装版本上运行测试模块，即使被测试的包具有相同的导入根。例如：

          ::

              testing/__init__.py
              testing/test_pkg_under_test.py
              pkg_under_test/

          当使用 ``--import-mode=append`` 时，测试将针对 ``pkg_under_test`` 的已安装版本运行，而在使用 ``prepend`` 时，它们会选择本地版本。这种混淆是我们倡导使用 :ref:`src-layouts <src-layout>` 的原因。

          与 ``prepend`` 一样，当测试目录树未以包的方式组织时，需要测试模块名称唯一，因为导入后模块将被放入 :py:data:`sys.modules`。

        .. _`import-mode-importlib`:

        * ``importlib`` ：此模式使用 :mod:`importlib` 提供的更细粒度的控制机制导入测试模块，而不改变 :py:data:`sys.path`。

          此模式的优点：

          * pytest 不会更改 :py:data:`sys.path`。
          * 测试模块名称不需要唯一——pytest 将根据 ``rootdir`` 自动生成唯一名称。

          缺点：

          * 测试模块不能相互导入。
          * 测试目录中的测试工具模块（例如，包含与测试相关的函数/类的 ``tests.helpers`` 模块）不可导入。在这种情况下，建议将测试工具模块与应用程序/库代码放在一起，例如 ``app.testing.helpers``。

          重要提示：我们所指的 "测试工具模块" 是指被其他测试直接导入的函数/类；这不包括 fixtures，fixtures 应放在 ``conftest.py`` 文件中，并与测试模块一起自动被 pytest 发现。

          工作原理如下：

          1. 给定某个模块路径，例如 ``tests/core/test_models.py``，推导出规范名称如 ``tests.core.test_models`` 并尝试导入它。

             对于非测试模块，如果它们可以通过 :py:data:`sys.path` 访问，则此步骤将成功。因此，例如，``.env/lib/site-packages/app/core.py`` 将作为 ``app.core`` 可导入。这在插件导入非测试模块（例如，文档测试）时发生。

             如果此步骤成功，模块将被返回。

             对于测试模块，除非它们可以从 :py:data:`sys.path` 访问，否则此步骤将失败。

          2. 如果上一步失败，我们使用 ``importlib`` 的功能直接导入模块，这使我们能够在不改变 :py:data:`sys.path` 的情况下导入它。

             由于 Python 要求模块也在 :py:data:`sys.modules` 中可用，pytest 根据其相对于 ``rootdir`` 的位置为其推导出一个唯一名称，并将该模块添加到 :py:data:`sys.modules`。

             例如，``tests/core/test_models.py`` 将作为模块 ``tests.core.test_models`` 被导入。

        .. versionadded:: 6.0

        .. note::

            起初，我们打算在未来版本中将 ``importlib`` 设为默认值，然而现在显然它有自己的一系列缺点，因此默认值将继续保持为 ``prepend``，至少在可预见的未来。

        .. note::

            默认情况下，pytest 不会自动尝试解析命名空间包，但可以通过 :confval:`consider_namespace_packages` 配置变量进行更改。

        .. seealso::

            :confval:`pythonpath` 配置变量。

            :confval:`consider_namespace_packages` 配置变量。

            :ref:`测试布局 <test layout>`。

    .. tab:: 英文

        pytest as a testing framework that needs to import test modules and ``conftest.py`` files for execution.

        Importing files in Python is a non-trivial process, so aspects of the
        import process can be controlled through the ``--import-mode`` command-line flag, which can assume
        these values:

        .. _`import-mode-prepend`:

        * ``prepend`` (default): The directory path containing each module will be inserted into the *beginning*
          of :py:data:`sys.path` if not already there, and then imported with
          the :func:`importlib.import_module <importlib.import_module>` function.

          It is highly recommended to arrange your test modules as packages by adding ``__init__.py`` files to your directories
          containing tests. This will make the tests part of a proper Python package, allowing pytest to resolve their full
          name (for example ``tests.core.test_core`` for ``test_core.py`` inside the ``tests.core`` package).

          If the test directory tree is not arranged as packages, then each test file needs to have a unique name
          compared to the other test files, otherwise pytest will raise an error if it finds two tests with the same name.

          This is the classic mechanism, dating back from the time Python 2 was still supported.

        .. _`import-mode-append`:

        * ``append``: the directory containing each module is appended to the end of :py:data:`sys.path` if not already
          there, and imported with :func:`importlib.import_module <importlib.import_module>`.

          This better allows users to run test modules against installed versions of a package even if the
          package under test has the same import root. For example:

          ::

            testing/__init__.py
            testing/test_pkg_under_test.py
            pkg_under_test/

          the tests will run against the installed version
          of ``pkg_under_test`` when ``--import-mode=append`` is used whereas
          with ``prepend``, they would pick up the local version. This kind of confusion is why
          we advocate for using :ref:`src-layouts <src-layout>`.

          Same as ``prepend``, requires test module names to be unique when the test directory tree is
          not arranged in packages, because the modules will put in :py:data:`sys.modules` after importing.

        .. _`import-mode-importlib`:

        * ``importlib``: this mode uses more fine control mechanisms provided by :mod:`importlib` to import test modules, without changing :py:data:`sys.path`.

          Advantages of this mode:

          * pytest will not change :py:data:`sys.path` at all.
          * Test module names do not need to be unique -- pytest will generate a unique name automatically based on the ``rootdir``.

          Disadvantages:

          * Test modules can't import each other.
          * Testing utility modules in the tests directories (for example a ``tests.helpers`` module containing test-related functions/classes)
          are not importable. The recommendation in this case it to place testing utility modules together with the application/library
          code, for example ``app.testing.helpers``.

          Important: by "test utility modules", we mean functions/classes which are imported by
          other tests directly; this does not include fixtures, which should be placed in ``conftest.py`` files, along
          with the test modules, and are discovered automatically by pytest.

          It works like this:

          1. Given a certain module path, for example ``tests/core/test_models.py``, derives a canonical name
             like ``tests.core.test_models`` and tries to import it.

             For non-test modules, this will work if they are accessible via :py:data:`sys.path`. So
             for example, ``.env/lib/site-packages/app/core.py`` will be importable as ``app.core``.
             This is happens when plugins import non-test modules (for example doctesting).

             If this step succeeds, the module is returned.

             For test modules, unless they are reachable from :py:data:`sys.path`, this step will fail.

          2. If the previous step fails, we import the module directly using ``importlib`` facilities, which lets us import it without
             changing :py:data:`sys.path`.

             Because Python requires the module to also be available in :py:data:`sys.modules`, pytest derives a unique name for it based
             on its relative location from the ``rootdir``, and adds the module to :py:data:`sys.modules`.

             For example, ``tests/core/test_models.py`` will end up being imported as the module ``tests.core.test_models``.

        .. versionadded:: 6.0

        .. note::

            Initially we intended to make ``importlib`` the default in future releases, however it is clear now that
            it has its own set of drawbacks so the default will remain ``prepend`` for the foreseeable future.

        .. note::

            By default, pytest will not attempt to resolve namespace packages automatically, but that can
            be changed via the :confval:`consider_namespace_packages` configuration variable.

        .. seealso::

            The :confval:`pythonpath` configuration variable.

            The :confval:`consider_namespace_packages` configuration variable.

            :ref:`test layout <test layout>`.


``prepend`` 和 ``append`` 导入模式场景
-------------------------------------------------

``prepend`` **and** ``append`` **import modes scenarios**

.. tabs::

    .. tab:: 中文

        以下是使用 ``prepend`` 或 ``append`` 导入模式时，pytest 需要更改 :py:data:`sys.path` 以导入测试模块或 ``conftest.py`` 文件的场景列表，以及用户可能因此遇到的问题。

    .. tab:: 英文

        Here's a list of scenarios when using ``prepend`` or ``append`` import modes where pytest needs to
        change :py:data:`sys.path` in order to import test modules or ``conftest.py`` files, and the issues users
        might encounter because of that.

包内的测试模块/``conftest.py`` 文件
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Test modules / ``conftest.py`` files inside packages**

.. tabs::

    .. tab:: 中文

        考虑以下文件和目录布局::

            root/
            |- foo/
            |- __init__.py
            |- conftest.py
            |- bar/
                |- __init__.py
                |- tests/
                    |- __init__.py
                    |- test_foo.py


        当执行以下命令时：

        .. code-block:: bash

            pytest root/

        pytest 会找到 ``foo/bar/tests/test_foo.py`` 并意识到它是一个包的一部分，因为同一文件夹中有一个 ``__init__.py`` 文件。然后，它会向上搜索，直到找到最后一个仍包含 ``__init__.py`` 文件的文件夹，以找到包的 *根*（在此情况下为 ``foo/``）。为了加载该模块，它会将 ``root/`` 插入到 :py:data:`sys.path` 的前面（如果还没有在那里），以便将 ``test_foo.py`` 加载为 *模块* ``foo.bar.tests.test_foo``。

        相同的逻辑适用于 ``conftest.py`` 文件：它将被导入为 ``foo.conftest`` 模块。

        在测试位于包中时，保持完整的包名是重要的，以避免问题并允许测试模块具有重复的名称。这在 :ref:`测试发现` 中也有详细讨论。

    .. tab:: 英文

        Consider this file and directory layout::

            root/
            |- foo/
            |- __init__.py
            |- conftest.py
            |- bar/
                |- __init__.py
                |- tests/
                    |- __init__.py
                    |- test_foo.py


        When executing:

        .. code-block:: bash

            pytest root/

        pytest will find ``foo/bar/tests/test_foo.py`` and realize it is part of a package given that
        there's an ``__init__.py`` file in the same folder. It will then search upwards until it can find the
        last folder which still contains an ``__init__.py`` file in order to find the package *root* (in
        this case ``foo/``). To load the module, it will insert ``root/``  to the front of
        :py:data:`sys.path` (if not there already) in order to load
        ``test_foo.py`` as the *module* ``foo.bar.tests.test_foo``.

        The same logic applies to the ``conftest.py`` file: it will be imported as ``foo.conftest`` module.

        Preserving the full package name is important when tests live in a package to avoid problems
        and allow test modules to have duplicated names. This is also discussed in details in
        :ref:`test discovery`.

独立测试模块/ ``conftest.py`` 文件
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Standalone test modules /** ``conftest.py`` **files**

.. tabs::

    .. tab:: 中文

        考虑以下文件和目录布局::

            root/
            |- foo/
            |- conftest.py
            |- bar/
                |- tests/
                    |- test_foo.py


        当执行以下命令时：

        .. code-block:: bash

            pytest root/

        pytest 会找到 ``foo/bar/tests/test_foo.py`` 并意识到它 **不** 是包的一部分，因为同一文件夹中没有 ``__init__.py`` 文件。然后，它会将 ``root/foo/bar/tests`` 添加到 :py:data:`sys.path` 中，以便将 ``test_foo.py`` 导入为 *模块* ``test_foo``。对 ``conftest.py`` 文件也会执行相同的操作，将 ``root/foo`` 添加到 :py:data:`sys.path` 以将其导入为 ``conftest``。

        因此，这种布局不能有相同名称的测试模块，因为它们都会在全局导入命名空间中被导入。

        这在 :ref:`测试发现` 中也有详细讨论。

    .. tab:: 英文

        Consider this file and directory layout::

            root/
            |- foo/
            |- conftest.py
            |- bar/
                |- tests/
                    |- test_foo.py


        When executing:

        .. code-block:: bash

            pytest root/

        pytest will find ``foo/bar/tests/test_foo.py`` and realize it is NOT part of a package given that
        there's no ``__init__.py`` file in the same folder. It will then add ``root/foo/bar/tests`` to
        :py:data:`sys.path` in order to import ``test_foo.py`` as the *module* ``test_foo``. The same is done
        with the ``conftest.py`` file by adding ``root/foo`` to :py:data:`sys.path` to import it as ``conftest``.

        For this reason this layout cannot have test modules with the same name, as they all will be
        imported in the global import namespace.

        This is also discussed in details in :ref:`test discovery`.

.. _`pytest vs python -m pytest`:

调用 ``pytest`` 与 ``python -m pytest``
-----------------------------------------------

**Invoking** ``pytest`` **versus** ``python -m pytest``

.. tabs::

    .. tab:: 中文

        使用 ``pytest [...]`` 运行 pytest 而不是 ``python -m pytest [...]`` 会产生几乎等效的行为，唯一的区别是后者会将当前目录添加到 :py:data:`sys.path` 中，这符合标准的 ``python`` 行为。

        另见 :ref:`invoke-python`。

    .. tab:: 英文

        Running pytest with ``pytest [...]`` instead of ``python -m pytest [...]`` yields nearly
        equivalent behaviour, except that the latter will add the current directory to :py:data:`sys.path`, which
        is standard ``python`` behavior.

        See also :ref:`invoke-python`.
