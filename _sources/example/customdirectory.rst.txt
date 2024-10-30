.. _`custom directory collectors`:

使用自定义目录收集器
====================================================

**Using a custom directory collector**

.. tabs::

    .. tab:: 中文

        默认情况下，pytest 使用 :class:`pytest.Package` 收集包含 ``__init__.py`` 文件的目录，使用 :class:`pytest.Dir` 收集其他目录。如果你想自定义如何收集一个目录，可以编写自己的 :class:`pytest.Directory` 收集器，并使用 :hook:`pytest_collect_directory` 将其挂接上。

    .. tab:: 英文

        By default, pytest collects directories using :class:`pytest.Package`, for directories with ``__init__.py`` files,
        and :class:`pytest.Dir` for other directories.
        If you want to customize how a directory is collected, you can write your own :class:`pytest.Directory` collector,
        and use :hook:`pytest_collect_directory` to hook it up.

.. _`directory manifest plugin`:

目录清单文件的基本示例
--------------------------------------------------------------

**A basic example for a directory manifest file**

.. tabs::

    .. tab:: 中文

        假设你想在每个目录的基础上自定义收集方式。这里是一个示例 ``conftest.py`` 插件，允许目录包含一个 ``manifest.json`` 文件，该文件定义了该目录的收集方式。在这个例子中，仅支持简单的文件列表，然而你可以想象添加其他键，例如排除项和通配符。

        .. include:: customdirectory/conftest.py
            :literal:

        你可以创建一个 ``manifest.json`` 文件和一些测试文件：

        .. include:: customdirectory/tests/manifest.json
            :literal:

        .. include:: customdirectory/tests/test_first.py
            :literal:

        .. include:: customdirectory/tests/test_second.py
            :literal:

        .. include:: customdirectory/tests/test_third.py
            :literal:

        现在你可以执行测试规范：

        .. code-block:: pytest

            customdirectory $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project/customdirectory
            configfile: pytest.ini
            collected 2 items

            tests/test_first.py .                                                [ 50%]
            tests/test_second.py .                                               [100%]

            ============================ 2 passed in 0.12s =============================

        .. regendoc:wipe

        注意 ``test_third.py`` 没有被执行，因为它没有在清单中列出。

        你可以验证你的自定义收集器出现在收集树中：

        .. code-block:: pytest

            customdirectory $ pytest --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project/customdirectory
            configfile: pytest.ini
            collected 2 items

            <Dir customdirectory>
            <ManifestDirectory tests>
                <Module test_first.py>
                <Function test_1>
                <Module test_second.py>
                <Function test_2>

            ======================== 2 tests collected in 0.12s ========================

    .. tab:: 英文

        Suppose you want to customize how collection is done on a per-directory basis.
        Here is an example ``conftest.py`` plugin that allows directories to contain a ``manifest.json`` file,
        which defines how the collection should be done for the directory.
        In this example, only a simple list of files is supported,
        however you can imagine adding other keys, such as exclusions and globs.

        .. include:: customdirectory/conftest.py
            :literal:

        You can create a ``manifest.json`` file and some test files:

        .. include:: customdirectory/tests/manifest.json
            :literal:

        .. include:: customdirectory/tests/test_first.py
            :literal:

        .. include:: customdirectory/tests/test_second.py
            :literal:

        .. include:: customdirectory/tests/test_third.py
            :literal:

        An you can now execute the test specification:

        .. code-block:: pytest

            customdirectory $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project/customdirectory
            configfile: pytest.ini
            collected 2 items

            tests/test_first.py .                                                [ 50%]
            tests/test_second.py .                                               [100%]

            ============================ 2 passed in 0.12s =============================

        .. regendoc:wipe

        Notice how ``test_three.py`` was not executed, because it is not listed in the manifest.

        You can verify that your custom collector appears in the collection tree:

        .. code-block:: pytest

            customdirectory $ pytest --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project/customdirectory
            configfile: pytest.ini
            collected 2 items

            <Dir customdirectory>
            <ManifestDirectory tests>
                <Module test_first.py>
                <Function test_1>
                <Module test_second.py>
                <Function test_2>

            ======================== 2 tests collected in 0.12s ========================
