
.. _`tmp_path handling`:
.. _tmp_path:

如何在测试中使用临时目录和文件
===================================================

**How to use temporary directories and files in tests**

``tmp_path`` fixture
------------------------

**The** ``tmp_path`` **fixture**

.. tabs::

    .. tab:: 中文

        您可以使用 ``tmp_path`` 夹具，它将为每个测试函数提供一个唯一的临时目录。

        ``tmp_path`` 是一个 :class:`pathlib.Path` 对象。以下是一个示例测试用法：

        .. code-block:: python

            # content of test_tmp_path.py
            CONTENT = "content"


            def test_create_file(tmp_path):
                d = tmp_path / "sub"
                d.mkdir()
                p = d / "hello.txt"
                p.write_text(CONTENT, encoding="utf-8")
                assert p.read_text(encoding="utf-8") == CONTENT
                assert len(list(tmp_path.iterdir())) == 1
                assert 0

        运行此测试将会通过，除了最后的 ``assert 0`` 行，我们使用它来查看值：

        .. code-block:: pytest

            $ pytest test_tmp_path.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_tmp_path.py F                                                   [100%]

            ================================= FAILURES =================================
            _____________________________ test_create_file _____________________________

            tmp_path = PosixPath('PYTEST_TMPDIR/test_create_file0')

                def test_create_file(tmp_path):
                    d = tmp_path / "sub"
                    d.mkdir()
                    p = d / "hello.txt"
                    p.write_text(CONTENT, encoding="utf-8")
                    assert p.read_text(encoding="utf-8") == CONTENT
                    assert len(list(tmp_path.iterdir())) == 1
            >       assert 0
            E       assert 0

            test_tmp_path.py:11: AssertionError
            ========================= short test summary info ==========================
            FAILED test_tmp_path.py::test_create_file - assert 0
            ============================ 1 failed in 0.12s =============================

        默认情况下， ``pytest`` 会保留最近 3 次 ``pytest`` 调用的临时目录。通过将基础临时目录配置为每次并发运行唯一，支持对同一测试函数的并发调用。有关详细信息，请参见 `temporary directory location and retention`_ 。

    .. tab:: 英文

        You can use the ``tmp_path`` fixture which will provide a temporary directory
        unique to each test function.

        ``tmp_path`` is a :class:`pathlib.Path` object. Here is an example test usage:

        .. code-block:: python

            # content of test_tmp_path.py
            CONTENT = "content"


            def test_create_file(tmp_path):
                d = tmp_path / "sub"
                d.mkdir()
                p = d / "hello.txt"
                p.write_text(CONTENT, encoding="utf-8")
                assert p.read_text(encoding="utf-8") == CONTENT
                assert len(list(tmp_path.iterdir())) == 1
                assert 0

        Running this would result in a passed test except for the last
        ``assert 0`` line which we use to look at values:

        .. code-block:: pytest

            $ pytest test_tmp_path.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_tmp_path.py F                                                   [100%]

            ================================= FAILURES =================================
            _____________________________ test_create_file _____________________________

            tmp_path = PosixPath('PYTEST_TMPDIR/test_create_file0')

                def test_create_file(tmp_path):
                    d = tmp_path / "sub"
                    d.mkdir()
                    p = d / "hello.txt"
                    p.write_text(CONTENT, encoding="utf-8")
                    assert p.read_text(encoding="utf-8") == CONTENT
                    assert len(list(tmp_path.iterdir())) == 1
            >       assert 0
            E       assert 0

            test_tmp_path.py:11: AssertionError
            ========================= short test summary info ==========================
            FAILED test_tmp_path.py::test_create_file - assert 0
            ============================ 1 failed in 0.12s =============================

        By default, ``pytest`` retains the temporary directory for the last 3 ``pytest``
        invocations. Concurrent invocations of the same test function are supported by
        configuring the base temporary directory to be unique for each concurrent
        run. See `temporary directory location and retention`_ for details.

.. _`tmp_path_factory example`:

``tmp_path_factory`` fixture
--------------------------------

.. tabs::

    .. tab:: 中文

        ``tmp_path_factory`` 是一个会话范围的夹具，可以用来从任何其他夹具或测试中创建任意的临时目录。

        例如，假设您的测试套件需要一个大的图像文件，该图像是程序生成的。为了节省时间，您可以每个会话生成一次图像，而不是为每个使用它的测试计算相同的图像到自己的 ``tmp_path`` 中：

        .. code-block:: python

            # contents of conftest.py
            import pytest


            @pytest.fixture(scope="session")
            def image_file(tmp_path_factory):
                img = compute_expensive_image()
                fn = tmp_path_factory.mktemp("data") / "img.png"
                img.save(fn)
                return fn


            # contents of test_image.py
            def test_histogram(image_file):
                img = load_image(image_file)
                # compute and test histogram

        有关详细信息，请参见 :ref:`tmp_path_factory API <tmp_path_factory factory api>`。

    .. tab:: 英文

        The ``tmp_path_factory`` is a session-scoped fixture which can be used
        to create arbitrary temporary directories from any other fixture or test.

        For example, suppose your test suite needs a large image on disk, which is
        generated procedurally. Instead of computing the same image for each test
        that uses it into its own ``tmp_path``, you can generate it once per-session
        to save time:

        .. code-block:: python

            # contents of conftest.py
            import pytest


            @pytest.fixture(scope="session")
            def image_file(tmp_path_factory):
                img = compute_expensive_image()
                fn = tmp_path_factory.mktemp("data") / "img.png"
                img.save(fn)
                return fn


            # contents of test_image.py
            def test_histogram(image_file):
                img = load_image(image_file)
                # compute and test histogram

        See :ref:`tmp_path_factory API <tmp_path_factory factory api>` for details.

.. _`tmpdir and tmpdir_factory`:
.. _tmpdir:

``tmpdir`` 和 ``tmpdir_factory`` fixtures
----------------------------------------------

The ``tmpdir`` and ``tmpdir_factory`` fixtures

.. tabs::

    .. tab:: 中文

        ``tmpdir`` 和 ``tmpdir_factory`` 夹具类似于 ``tmp_path`` 和 ``tmp_path_factory``，但使用/返回传统的 `py.path.local`_ 对象，而不是标准的 :class:`pathlib.Path` 对象。

        .. note::

            如今，建议使用 ``tmp_path`` 和 ``tmp_path_factory``。

            为了帮助现代化旧代码库，可以在禁用 legacypath 插件的情况下运行 pytest：

            .. code-block:: bash

                pytest -p no:legacypath

            这将对使用传统路径的测试触发错误。
            它也可以作为配置文件中 :confval:`addopts` 参数的一部分永久设置。

        有关详细信息，请参见 :fixture:`tmpdir <tmpdir>` 和 :fixture:`tmpdir_factory <tmpdir_factory>` API。

    .. tab:: 英文

        The ``tmpdir`` and ``tmpdir_factory`` fixtures are similar to ``tmp_path``
        and ``tmp_path_factory``, but use/return legacy `py.path.local`_ objects
        rather than standard :class:`pathlib.Path` objects.

        .. note::
            These days, it is preferred to use ``tmp_path`` and ``tmp_path_factory``.

            In order to help modernize old code bases, one can run pytest with the legacypath
            plugin disabled:

            .. code-block:: bash

                pytest -p no:legacypath

            This will trigger errors on tests using the legacy paths.
            It can also be permanently set as part of the :confval:`addopts` parameter in the
            config file.

        See :fixture:`tmpdir <tmpdir>` :fixture:`tmpdir_factory <tmpdir_factory>`
        API for details.


.. _`temporary directory location and retention`:

临时目录位置和保留
------------------------------------------

**Temporary directory location and retention**

.. tabs::

    .. tab:: 中文

        临时目录默认作为系统临时目录的子目录创建。基本名称将为 ``pytest-NUM``，其中 ``NUM`` 在每次测试运行时递增。
        默认情况下，超过 3 个临时目录的条目将被删除。
        此行为可以通过 :confval:`tmp_path_retention_count` 和 :confval:`tmp_path_retention_policy` 进行配置。

        使用 ``--basetemp`` 选项将在每次运行之前删除该目录，这意味着仅保留最近一次运行的临时目录。

        您可以像这样覆盖默认的临时目录设置：

        .. code-block:: bash

            pytest --basetemp=mydir

        .. warning::

            ``mydir`` 的内容将被完全删除，因此请确保仅使用一个目录来实现此目的。

        在使用 ``pytest-xdist`` 在本地机器上分发测试时，会自动为子进程配置 `basetemp` 目录，以便所有临时数据都位于单个每次测试运行的临时目录下。

    .. tab:: 英文

        Temporary directories are by default created as sub-directories of
        the system temporary directory.  The base name will be ``pytest-NUM`` where
        ``NUM`` will be incremented with each test run.
        By default, entries older than 3 temporary directories will be removed.
        This behavior can be configured with :confval:`tmp_path_retention_count` and
        :confval:`tmp_path_retention_policy`.

        Using the ``--basetemp``
        option will remove the directory before every run, effectively meaning the temporary directories
        of only the most recent run will be kept.

        You can override the default temporary directory setting like this:

        .. code-block:: bash

            pytest --basetemp=mydir

        .. warning::

            The contents of ``mydir`` will be completely removed, so make sure to use a directory
            for that purpose only.

        When distributing tests on the local machine using ``pytest-xdist``, care is taken to
        automatically configure a `basetemp` directory for the sub processes such that all temporary
        data lands below a single per-test run temporary directory.

.. _`py.path.local`: https://py.readthedocs.io/en/latest/path.html
