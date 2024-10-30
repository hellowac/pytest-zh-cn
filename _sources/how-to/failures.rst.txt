.. _how-to-handle-failures:

如何处理测试失败
=============================

**How to handle test failures**

.. _maxfail:

第一次（或 N 次）失败后停止
---------------------------------------------------

**Stopping after the first (or N) failures**

.. tabs::

    .. tab:: 中文

        在前 (N) 次失败后停止测试过程：

        .. code-block:: bash

            pytest -x           # stop after first failure
            pytest --maxfail=2  # stop after two failures

    .. tab:: 英文

        To stop the testing process after the first (N) failures:

        .. code-block:: bash

            pytest -x           # stop after first failure
            pytest --maxfail=2  # stop after two failures


.. _pdb-option:

与 :doc:`python:library/pdb` 一起使用 pytest
-------------------------------------------

**Using** :doc:`python:library/pdb` **with pytest**

在失败时转到 :doc:`pdb <python:library/pdb>`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Dropping to** :doc:`pdb <python:library/pdb>` **on failures**

.. tabs::

    .. tab:: 中文

        Python 自带了一个内置的调试器 :doc:`pdb <python:library/pdb>`。``pytest`` 允许通过命令行选项进入 :doc:`pdb <python:library/pdb>` 提示符：

        .. code-block:: bash

            pytest --pdb

        这将在每次失败（或 KeyboardInterrupt）时调用 Python 调试器。
        通常你可能只想在第一个失败的测试中进行调试，以便理解某个失败的情况：

        .. code-block:: bash

            pytest -x --pdb   # 第一个失败时进入 PDB，然后结束测试会话
            pytest --pdb --maxfail=3  # 前三个失败进入 PDB

        请注意，任何失败时的异常信息都会存储在 ``sys.last_value``、 ``sys.last_type`` 和 ``sys.last_traceback`` 中。在交互式使用中，这允许使用任意调试工具进行事后调试。也可以手动访问异常信息，例如：

        .. code-block:: python

            >>> import sys
            >>> sys.last_traceback.tb_lineno
            42
            >>> sys.last_value
            AssertionError('assert result == "ok"',)

    .. tab:: 英文

        Python comes with a builtin Python debugger called :doc:`pdb <python:library/pdb>`.  ``pytest``
        allows one to drop into the :doc:`pdb <python:library/pdb>` prompt via a command line option:

        .. code-block:: bash

            pytest --pdb

        This will invoke the Python debugger on every failure (or KeyboardInterrupt).
        Often you might only want to do this for the first failing test to understand
        a certain failure situation:

        .. code-block:: bash

            pytest -x --pdb   # drop to PDB on first failure, then end test session
            pytest --pdb --maxfail=3  # drop to PDB for first three failures

        Note that on any failure the exception information is stored on
        ``sys.last_value``, ``sys.last_type`` and ``sys.last_traceback``. In
        interactive use, this allows one to drop into postmortem debugging with
        any debug tool. One can also manually access the exception information,
        for example::

            >>> import sys
            >>> sys.last_traceback.tb_lineno
            42
            >>> sys.last_value
            AssertionError('assert result == "ok"',)


.. _trace-option:

在一个测试开始时转到 :doc:`pdb <python:library/pdb>`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Dropping to** :doc:`pdb <python:library/pdb>` **at the start of a test**

.. tabs::

    .. tab:: 中文

        ``pytest`` 允许人们在每个测试开始时通过命令行选项立即进入 :doc:`pdb <python:library/pdb>` 提示符：

        .. code-block:: bash

            pytest --trace

        这将在每个测试开始时调用 Python 调试器。

    .. tab:: 英文

        ``pytest`` allows one to drop into the :doc:`pdb <python:library/pdb>` prompt immediately at the start of each test via a command line option:

        .. code-block:: bash

            pytest --trace

        This will invoke the Python debugger at the start of every test.

.. _breakpoints:

设置断点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Setting breakpoints**

.. tabs::

    .. tab:: 中文

        .. versionadded: 2.4.0

        在代码中设置断点，可以使用原生 Python 调用 ``import pdb; pdb.set_trace()``。pytest 会自动禁用该测试的输出捕获：

        * 其他测试的输出捕获不受影响。
        * 任何已捕获的先前测试输出将按原样处理。
        * 在结束调试会话（通过 ``continue`` 命令）时，输出捕获会恢复。

    .. tab:: 英文

        .. versionadded: 2.4.0

        To set a breakpoint in your code use the native Python ``import pdb;pdb.set_trace()`` call in your code and pytest automatically disables its output capture for that test:

        * Output capture in other tests is not affected.
        * Any prior test output that has already been captured and will be processed as such.
        * Output capture gets resumed when ending the debugger session (via the ``continue`` command).


.. _`breakpoint-builtin`:

使用内置断点函数
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Using the builtin breakpoint function**

.. tabs::

    .. tab:: 中文

        Python 3.7 引入了一个内置的 ``breakpoint()`` 函数。pytest 支持 ``breakpoint()`` 的使用，具有以下行为：

        - 当调用 ``breakpoint()`` 且 ``PYTHONBREAKPOINT`` 设置为默认值时，pytest 将使用自定义的内部 PDB 调试界面，而不是系统默认的 ``Pdb``。
        - 测试完成后，系统将恢复使用系统默认的 ``Pdb`` 调试界面。
        - 通过 ``--pdb`` 参数传递给 pytest 时，自定义内部 Pdb 调试界面将用于 ``breakpoint()`` 调用、测试失败或未处理的异常。
        - 可以使用 ``--pdbcls`` 指定一个自定义的调试类。

    .. tab:: 英文

        Python 3.7 introduces a builtin ``breakpoint()`` function.
        Pytest supports the use of ``breakpoint()`` with the following behaviours:

        - When ``breakpoint()`` is called and ``PYTHONBREAKPOINT`` is set to the default value, pytest will use the custom internal PDB trace UI instead of the system default ``Pdb``.
        - When tests are complete, the system will default back to the system ``Pdb`` trace UI.
        - With ``--pdb`` passed to pytest, the custom internal Pdb trace UI is used with both ``breakpoint()`` and failed tests/unhandled exceptions.
        - ``--pdbcls`` can be used to specify a custom debugger class.


.. _faulthandler:

故障处理
-------------

**Fault Handler**

.. tabs::

    .. tab:: 中文

        .. versionadded:: 5.0

        标准模块 :mod:`faulthandler` 可用于在发生段错误或超时后导出 Python 堆栈跟踪。

        除非在命令行中指定了 ``-p no:faulthandler``，否则该模块会自动为 pytest 运行启用。

        此外，:confval:`faulthandler_timeout=X<faulthandler_timeout>` 配置选项可以用于在测试花费时间超过 ``X`` 秒后导出所有线程的堆栈跟踪（在 Windows 上不可用）。

        .. note::

            此功能已集成自外部 `pytest-faulthandler <https://github.com/pytest-dev/pytest-faulthandler>`__ 插件，但有以下两点小差异：

            * 禁用它时，使用 ``-p no:faulthandler`` 而不是 ``--no-faulthandler``：前者适用于所有插件，从而节省了一个选项。

            * ``--faulthandler-timeout`` 命令行选项已成为 :confval:`faulthandler_timeout` 配置选项。仍然可以通过命令行使用 ``-o faulthandler_timeout=X`` 进行配置。

    .. tab:: 英文

        .. versionadded:: 5.0

        The :mod:`faulthandler` standard module
        can be used to dump Python tracebacks on a segfault or after a timeout.

        The module is automatically enabled for pytest runs, unless the ``-p no:faulthandler`` is given
        on the command-line.

        Also the :confval:`faulthandler_timeout=X<faulthandler_timeout>` configuration option can be used
        to dump the traceback of all threads if a test takes longer than ``X``
        seconds to finish (not available on Windows).

        .. note::

            This functionality has been integrated from the external
            `pytest-faulthandler <https://github.com/pytest-dev/pytest-faulthandler>`__ plugin, with two
            small differences:

            * To disable it, use ``-p no:faulthandler`` instead of ``--no-faulthandler``: the former
            can be used with any plugin, so it saves one option.

            * The ``--faulthandler-timeout`` command-line option has become the
            :confval:`faulthandler_timeout` configuration option. It can still be configured from
            the command-line using ``-o faulthandler_timeout=X``.


.. _unraisable:

关于无法引发的异常和未处理的线程异常的警告
-------------------------------------------------------------------

**Warning about unraisable exceptions and unhandled thread exceptions**

.. tabs::

    .. tab:: 中文

        .. versionadded:: 6.2

        未处理的异常指在无法传递给调用者的情况下引发的异常。最常见的情况是在 :meth:`__del__ <object.__del__>` 实现中引发的异常。

        未处理的线程异常是在 :class:`~threading.Thread` 中引发且未处理的异常，导致线程异常终止。

        这两类异常通常被视为程序错误, 但可能因未导致程序崩溃而未被注意到。Pytest 可以检测这些情况，并在测试运行摘要中显示警告。

        这些插件在 pytest 运行中自动启用，除非在命令行中指定 ``-p no:unraisableexception`` （用于未引发的异常）或 ``-p no:threadexception`` （用于线程异常）。

        可以使用 :ref:`pytest.mark.filterwarnings ref` 标记有选择地关闭这些警告。警告类别为 :class:`pytest.PytestUnraisableExceptionWarning` 和 :class:`pytest.PytestUnhandledThreadExceptionWarning`。

    .. tab:: 英文

        .. versionadded:: 6.2

        Unhandled exceptions are exceptions that are raised in a situation in which
        they cannot propagate to a caller. The most common case is an exception raised
        in a :meth:`__del__ <object.__del__>` implementation.

        Unhandled thread exceptions are exceptions raised in a :class:`~threading.Thread`
        but not handled, causing the thread to terminate uncleanly.

        Both types of exceptions are normally considered bugs, but may go unnoticed
        because they don't cause the program itself to crash. Pytest detects these
        conditions and issues a warning that is visible in the test run summary.

        The plugins are automatically enabled for pytest runs, unless the
        ``-p no:unraisableexception`` (for unraisable exceptions) and
        ``-p no:threadexception`` (for thread exceptions) options are given on the
        command-line.

        The warnings may be silenced selectively using the :ref:`pytest.mark.filterwarnings ref`
        mark. The warning categories are :class:`pytest.PytestUnraisableExceptionWarning` and
        :class:`pytest.PytestUnhandledThreadExceptionWarning`.
