.. _exit-codes:

退出码
========================================================

**Exit codes**

.. tabs::

    .. tab:: 中文

        运行 ``pytest`` 可能会导致六种不同的退出代码：

        :退出代码 0: 所有测试已成功收集并通过
        :退出代码 1: 测试已收集并运行，但部分测试失败
        :退出代码 2: 测试执行被用户中断
        :退出代码 3: 执行测试时发生内部错误
        :退出代码 4: pytest 命令行用法错误
        :退出代码 5: 没有收集到测试

        这些退出代码由 :class:`pytest.ExitCode` 枚举表示。退出代码作为公共 API 的一部分，可以直接导入并访问：

        .. code-block:: python

            from pytest import ExitCode

        .. note::

            如果您希望在某些情况下自定义退出代码，特别是在未收集到测试时，请考虑使用
            `pytest-custom_exit_code <https://github.com/yashtodi94/pytest-custom_exit_code>`__ 插件。

    .. tab:: 英文

        Running ``pytest`` can result in six different exit codes:

        :Exit code 0: All tests were collected and passed successfully
        :Exit code 1: Tests were collected and run but some of the tests failed
        :Exit code 2: Test execution was interrupted by the user
        :Exit code 3: Internal error happened while executing tests
        :Exit code 4: pytest command line usage error
        :Exit code 5: No tests were collected

        They are represented by the :class:`pytest.ExitCode` enum. The exit codes being a part of the public API can be imported and accessed directly using:

        .. code-block:: python

            from pytest import ExitCode

        .. note::

            If you would like to customize the exit code in some scenarios, specially when
            no tests are collected, consider using the
            `pytest-custom_exit_code <https://github.com/yashtodi94/pytest-custom_exit_code>`__
            plugin.
