
.. _bash_completion:

如何设置 bash 补全
=============================

**How to set up bash completion**

.. tabs::

    .. tab:: 中文

        当使用 bash 作为你的 shell 时，``pytest`` 可以使用 argcomplete
        (https://kislyuk.github.io/argcomplete/) 进行自动补全。
        为此，必须安装 **并且** 启用 ``argcomplete``。

        使用以下命令安装 argcomplete：

        .. code-block:: bash

            sudo pip install 'argcomplete>=0.5.7'

        要全局激活所有启用 argcomplete 的 Python 应用程序，请运行：

        .. code-block:: bash

            sudo activate-global-python-argcomplete

        要永久（但不是全局）激活 ``pytest``，请使用：

        .. code-block:: bash

            register-python-argcomplete pytest >> ~/.bashrc

        要一次性激活仅针对 ``pytest`` 的 argcomplete，请使用：

        .. code-block:: bash

            eval "$(register-python-argcomplete pytest)"

    .. tab:: 英文

        When using bash as your shell, ``pytest`` can use argcomplete
        (https://kislyuk.github.io/argcomplete/) for auto-completion.
        For this ``argcomplete`` needs to be installed **and** enabled.

        Install argcomplete using:

        .. code-block:: bash

            sudo pip install 'argcomplete>=0.5.7'

        For global activation of all argcomplete enabled python applications run:

        .. code-block:: bash

            sudo activate-global-python-argcomplete

        For permanent (but not global) ``pytest`` activation, use:

        .. code-block:: bash

            register-python-argcomplete pytest >> ~/.bashrc

        For one-time activation of argcomplete for ``pytest`` only, use:

        .. code-block:: bash

            eval "$(register-python-argcomplete pytest)"
