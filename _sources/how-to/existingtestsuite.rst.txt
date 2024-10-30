.. _existingtestsuite:

如何将 pytest 与现有测试套件结合使用
==============================================

**How to use pytest with an existing test suite**

.. tabs::

    .. tab:: 中文

        Pytest 可以与大多数现有的测试套件一起使用，但它的行为与其他测试运行器（如 Python 的默认 unittest 框架）有所不同。

        在使用本节之前，您需要 :ref:`安装 pytest <getstarted>`。

    .. tab:: 英文

        Pytest can be used with most existing test suites, but its
        behavior differs from other test runners such as Python's
        default unittest framework.

        Before using this section you will want to :ref:`install pytest <getstarted>`.

使用 pytest 运行现有测试套件
---------------------------------------------

**Running an existing test suite with pytest**

.. tabs::

    .. tab:: 中文

        假设您想要为某个现有的仓库贡献代码。在使用某种版本控制工具将代码拉取到您的开发环境中之后（可选地设置虚拟环境），您将想要运行：

        .. code-block:: bash

            cd <repository>
            pip install -e .  # 依赖于环境的替代方案包括
                              # 'python setup.py develop' 和 'conda develop'

        在您的项目根目录下。这将创建指向您代码的符号链接到 site-packages，允许您在测试运行时编辑代码，就像它已经被安装一样。

        以开发模式设置您的项目可以让您避免每次运行测试时都要重新安装，并且比通过修改 sys.path 指向本地代码更不易出错。

        还可以考虑使用 :ref:`tox <use tox>`。

    .. tab:: 英文

        Say you want to contribute to an existing repository somewhere.
        After pulling the code into your development space using some
        flavor of version control and (optionally) setting up a virtualenv
        you will want to run:

        .. code-block:: bash

            cd <repository>
            pip install -e .  # Environment dependent alternatives include
                              # 'python setup.py develop' and 'conda develop'

        in your project root.  This will set up a symlink to your code in
        site-packages, allowing you to edit your code while your tests
        run against it as if it were installed.

        Setting up your project in development mode lets you avoid having to
        reinstall every time you want to run your tests, and is less brittle than
        mucking about with sys.path to point your tests at local code.

        Also consider using :ref:`tox <use tox>`.
