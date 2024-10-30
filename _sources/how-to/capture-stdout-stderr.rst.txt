
.. _`captures`:

如何捕获 stdout/stderr 输出
=========================================================

**How to capture stdout/stderr output**

默认 stdout/stderr/stdin 捕获行为
---------------------------------------------------------

**Default stdout/stderr/stdin capturing behaviour**

.. tabs::

    .. tab:: 中文

        在测试执行期间，发送到 ``stdout`` 和 ``stderr`` 的任何输出都会被捕获。如果一个测试或设置方法失败，相应的捕获输出通常会与失败的回溯一起显示。（此行为可以通过 ``--show-capture`` 命令行选项进行配置）。

        此外， ``stdin`` 被设置为一个 "null" 对象，这会在尝试从中读取时失败，因为在运行自动化测试时，通常不希望等待交互式输入。

        默认情况下，捕获是通过拦截对低级文件描述符的写入来完成的。这允许捕获简单打印语句的输出以及由测试启动的子进程的输出。

    .. tab:: 英文

        During test execution any output sent to ``stdout`` and ``stderr`` is
        captured.  If a test or a setup method fails its according captured
        output will usually be shown along with the failure traceback. (this
        behavior can be configured by the ``--show-capture`` command-line option).

        In addition, ``stdin`` is set to a "null" object which will
        fail on attempts to read from it because it is rarely desired
        to wait for interactive input when running automated tests.

        By default capturing is done by intercepting writes to low level
        file descriptors.  This allows to capture output from simple
        print statements as well as output from a subprocess started by
        a test.

.. _capture-method:

设置捕获方法或禁用捕获
-------------------------------------------------

**Setting capturing methods or disabling capturing**

.. tabs::

    .. tab:: 中文

        ``pytest`` 可以通过三种方式执行输出捕获：

        * ``fd`` （文件描述符）级别捕获（默认）：所有写入操作系统文件描述符 1 和 2 的内容将被捕获。

        * ``sys`` 级别捕获：仅捕获写入 Python 文件 ``sys.stdout`` 和 ``sys.stderr`` 的内容。不执行对文件描述符的写入捕获。

        * ``tee-sys`` 捕获：Python 对 ``sys.stdout`` 和 ``sys.stderr`` 的写入将被捕获，但这些写入也会传递给实际的 ``sys.stdout`` 和 ``sys.stderr``。这允许输出进行“实时打印”，并供插件使用，如 junitxml（在 pytest 5.4 中新增）。

        .. _`disable capturing`:

        您可以通过命令行影响输出捕获机制：

        .. code-block:: bash

            pytest -s                  # 禁用所有捕获
            pytest --capture=sys       # 用内存文件替换 sys.stdout/stderr
            pytest --capture=fd        # 同时将文件描述符 1 和 2 指向临时文件
            pytest --capture=tee-sys   # 结合 'sys' 和 '-s'，捕获 sys.stdout/stderr
                                    # 并将其传递给实际的 sys.stdout/stderr

    .. tab:: 英文

        There are three ways in which ``pytest`` can perform capturing:

        * ``fd`` (file descriptor) level capturing (default): All writes going to the operating system file descriptors 1 and 2 will be captured.

        * ``sys`` level capturing: Only writes to Python files ``sys.stdout``
          and ``sys.stderr`` will be captured.  No capturing of writes to
          filedescriptors is performed.

        * ``tee-sys`` capturing: Python writes to ``sys.stdout`` and ``sys.stderr``
          will be captured, however the writes will also be passed-through to
          the actual ``sys.stdout`` and ``sys.stderr``. This allows output to be
          'live printed' and captured for plugin use, such as junitxml (new in pytest 5.4).

        .. _`disable capturing`:

        You can influence output capturing mechanisms from the command line:

        .. code-block:: bash

            pytest -s                  # disable all capturing
            pytest --capture=sys       # replace sys.stdout/stderr with in-mem files
            pytest --capture=fd        # also point filedescriptors 1 and 2 to temp file
            pytest --capture=tee-sys   # combines 'sys' and '-s', capturing sys.stdout/stderr
                                    # and passing it along to the actual sys.stdout/stderr

.. _printdebugging:

使用打印语句进行调试
---------------------------------------------------

**Using print statements for debugging**

.. tabs::

    .. tab:: 中文

        默认捕获 stdout/stderr 输出的一个主要好处是，您可以使用打印语句进行调试：

        .. code-block:: python

            # test_module.py 的内容


            def setup_function(function):
                print("setting up", function)


            def test_func1():
                assert True


            def test_func2():
                assert False

        运行此模块将精确显示失败函数的输出，并隐藏其他函数的输出：

        .. code-block:: pytest

            $ pytest
            =========================== 测试会话开始 ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            收集了 2 项

            test_module.py .F                                                    [100%]

            ================================= 失败 =================================
            ________________________________ test_func2 ________________________________

                def test_func2():
            >       assert False
            E       assert False

            test_module.py:12: AssertionError
            -------------------------- 捕获的 stdout 设置 ---------------------------
            setting up <function test_func2 at 0xdeadbeef0001>
            ========================= 短测试摘要信息 ==========================
            FAILED test_module.py::test_func2 - assert False
            ======================= 1 个失败，1 个通过，耗时 0.12s ========================

    .. tab:: 英文

        One primary benefit of the default capturing of stdout/stderr output
        is that you can use print statements for debugging:

        .. code-block:: python

            # content of test_module.py


            def setup_function(function):
                print("setting up", function)


            def test_func1():
                assert True


            def test_func2():
                assert False

        and running this module will show you precisely the output
        of the failing function and hide the other one:

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py .F                                                    [100%]

            ================================= FAILURES =================================
            ________________________________ test_func2 ________________________________

                def test_func2():
            >       assert False
            E       assert False

            test_module.py:12: AssertionError
            -------------------------- Captured stdout setup ---------------------------
            setting up <function test_func2 at 0xdeadbeef0001>
            ========================= short test summary info ==========================
            FAILED test_module.py::test_func2 - assert False
            ======================= 1 failed, 1 passed in 0.12s ========================

从测试函数访问捕获的输出
---------------------------------------------------

**Accessing captured output from a test function**

.. tabs::

    .. tab:: 中文

        :fixture:`capsys`、 :fixture:`capsysbinary`、 :fixture:`capfd` 和 :fixture:`capfdbinary` 这些 fixture 允许访问在测试执行期间创建的 ``stdout`` / ``stderr`` 输出。

        以下是一个执行一些输出相关检查的示例测试函数：

        .. code-block:: python

            def test_myoutput(capsys):  # 或使用 "capfd" 进行文件描述符级捕获
                print("hello")
                sys.stderr.write("world\n")
                captured = capsys.readouterr()
                assert captured.out == "hello\n"
                assert captured.err == "world\n"
                print("next")
                captured = capsys.readouterr()
                assert captured.out == "next\n"

        ``readouterr()`` 调用会快照当前的输出——捕获将继续进行。在测试函数完成后，原始流将被恢复。以这种方式使用 :fixture:`capsys` 可以让您的测试不必关心设置/重置输出流，同时也能很好地与 pytest 自己的逐个测试捕获进行交互。

        ``readouterr`` 的返回值变更为具有两个属性的 ``namedtuple``， ``out`` 和 ``err``。

        如果待测试的代码写入非文本数据（``bytes``），您可以使用 :fixture:`capsysbinary` fixture 来捕获，这样 ``readouterr`` 方法将返回 ``bytes``。

        如果您想在文件描述符级别进行捕获，可以使用 :fixture:`capfd` fixture，它提供相同的接口，但还允许捕获直接写入操作系统级输出流（FD1 和 FD2）的库或子进程的输出。类似于 :fixture:`capsysbinary`， :fixture:`capfdbinary` 也可以用于在文件描述符级别捕获 ``bytes``。

        要在测试中暂时禁用捕获，捕获 fixture 提供了一个 ``disabled()`` 方法，可以作为上下文管理器使用，在 ``with`` 块中禁用捕获：

        .. code-block:: python

            def test_disabling_capturing(capsys):
                print("此输出被捕获")
                with capsys.disabled():
                    print("输出未被捕获，直接写入 sys.stdout")
                print("此输出也被捕获")

    .. tab:: 英文

        The :fixture:`capsys`, :fixture:`capsysbinary`, :fixture:`capfd`, and :fixture:`capfdbinary` fixtures
        allow access to ``stdout``/``stderr`` output created during test execution.

        Here is an example test function that performs some output related checks:

        .. code-block:: python

            def test_myoutput(capsys):  # or use "capfd" for fd-level
                print("hello")
                sys.stderr.write("world\n")
                captured = capsys.readouterr()
                assert captured.out == "hello\n"
                assert captured.err == "world\n"
                print("next")
                captured = capsys.readouterr()
                assert captured.out == "next\n"

        The ``readouterr()`` call snapshots the output so far -
        and capturing will be continued.  After the test
        function finishes the original streams will
        be restored.  Using :fixture:`capsys` this way frees your
        test from having to care about setting/resetting
        output streams and also interacts well with pytest's
        own per-test capturing.

        The return value from ``readouterr`` changed to a ``namedtuple`` with two attributes, ``out`` and ``err``.

        If the code under test writes non-textual data (``bytes``), you can capture this using
        the :fixture:`capsysbinary` fixture which instead returns ``bytes`` from
        the ``readouterr`` method.

        If you want to capture at the file descriptor level you can use
        the :fixture:`capfd` fixture which offers the exact
        same interface but allows to also capture output from
        libraries or subprocesses that directly write to operating
        system level output streams (FD1 and FD2). Similarly to :fixture:`capsysbinary`, :fixture:`capfdbinary` can be
        used to capture ``bytes`` at the file descriptor level.


        To temporarily disable capture within a test, the capture fixtures
        have a ``disabled()`` method that can be used
        as a context manager, disabling capture inside the ``with`` block:

        .. code-block:: python

            def test_disabling_capturing(capsys):
                print("this output is captured")
                with capsys.disabled():
                    print("output not captured, going directly to sys.stdout")
                print("this output is also captured")
