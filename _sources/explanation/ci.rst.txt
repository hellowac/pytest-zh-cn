.. _`ci-pipelines`:

CI Pipelines
============

原理
---------

**Rationale**

.. tabs::

    .. tab:: 中文

        在 CI 流水线中的测试目标与本地测试不同。实际上，您可以快速编辑一些代码并在计算机上重新运行测试，但在 CI 流水线中则无法这样做。CI 流水线在单独的服务器上运行，并由特定操作触发。

        基于这一观察，pytest 可以检测何时处于 CI 环境，并调整其某些行为。

    .. tab:: 英文

        The goal of testing in a CI pipeline is different from testing locally. Indeed,
        you can quickly edit some code and run your tests again on your computer, but
        it is not possible with CI pipeline. They run on a separate server and are
        triggered by specific actions.

        From that observation, pytest can detect when it is in a CI environment and
        adapt some of its behaviours.

如何检测 CI
------------------

**How CI is detected**

.. tabs::

    .. tab:: 中文

        当以下任一环境变量被设置时，pytest 知道它处于 CI 环境，无论其值如何：

        * `CI`: 被许多 CI 系统使用。
        * `BUILD_NUMBER`: 被 Jenkins 使用。

    .. tab:: 英文

        Pytest knows it is in a CI environment when either one of these environment variables are set,
        regardless of their value:

        * `CI`: used by many CI systems.
        * `BUILD_NUMBER`: used by Jenkins.

对 CI 的影响
-------------

**Effects on CI**

.. tabs::

    .. tab:: 中文

        目前，pytest 在 CI 环境中的影响是有限的。

        当检测到 CI 环境时，简短测试摘要信息的输出不再被截断到终端大小，即将显示完整消息。

        .. code-block:: python

                # test_ci.py 的内容
                import pytest


                def test_db_initialized():
                    pytest.fail(
                        "故意失败以演示，Lorem ipsum dolor sit amet, "
                        "consectetur adipiscing elit. Cras facilisis, massa in suscipit "
                        "dignissim, mauris lacus molestie nisi, quis varius metus nulla ut ipsum."
                    )


        在本地运行时，没有任何额外选项，将输出：

        .. code-block:: pytest

            $ pytest test_ci.py
            ...
            ========================= short test summary info ==========================
            FAILED test_backends.py::test_db_initialized[d2] - Failed: 故意失败...

        *(注意被截断的文本)*


        而在 CI 上运行将输出：

        .. code-block:: pytest

            $ export CI=true
            $ pytest test_ci.py
            ...
            ========================= short test summary info ==========================
            FAILED test_backends.py::test_db_initialized[d2] - Failed: 故意失败
            以演示，Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras
            facilisis, massa in suscipit dignissim, mauris lacus molestie nisi, quis varius
            metus nulla ut ipsum.

    .. tab:: 英文

        For now, the effects on pytest of being in a CI environment are limited.

        When a CI environment is detected, the output of the short test summary info is no longer truncated to the terminal size i.e. the entire message will be shown.

        .. code-block:: python

                # content of test_ci.py
                import pytest


                def test_db_initialized():
                    pytest.fail(
                        "deliberately failing for demo purpose, Lorem ipsum dolor sit amet, "
                        "consectetur adipiscing elit. Cras facilisis, massa in suscipit "
                        "dignissim, mauris lacus molestie nisi, quis varius metus nulla ut ipsum."
                    )


        Running this locally, without any extra options, will output:

        .. code-block:: pytest

            $ pytest test_ci.py
            ...
            ========================= short test summary info ==========================
            FAILED test_backends.py::test_db_initialized[d2] - Failed: deliberately f...

        *(Note the truncated text)*


        While running this on CI will output:

        .. code-block:: pytest

            $ export CI=true
            $ pytest test_ci.py
            ...
            ========================= short test summary info ==========================
            FAILED test_backends.py::test_db_initialized[d2] - Failed: deliberately failing
            for demo purpose, Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras
            facilisis, massa in suscipit dignissim, mauris lacus molestie nisi, quis varius
            metus nulla ut ipsum.
