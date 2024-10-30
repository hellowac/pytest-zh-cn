============================
贡献
============================

**Contributing**

.. tabs::

    .. tab:: 中文

         我们非常欢迎并赞赏您的贡献。每一点帮助都很重要，所以不要犹豫！

    .. tab:: 英文

         我们非常欢迎并赞赏您的贡献。每一点帮助都很重要，所以不要犹豫！


.. _submitfeedback:

功能请求和反馈
-----------------------------

**Feature requests and feedback**

.. tabs::

    .. tab:: 中文

      您喜欢 pytest 吗？在 Twitter 或您的博客文章中分享您的喜爱吧！

      我们也希望听到您的建议和主张。欢迎
      `将它们作为问题提交 <https://github.com/pytest-dev/pytest/issues>`_ 并且：

      * 详细解释它们应该如何工作。
      * 尽可能缩小范围。这将使其更容易实现。

    .. tab:: 英文

      Do you like pytest?  Share some love on Twitter or in your blog posts!

      We'd also like to hear about your propositions and suggestions.  Feel free to
      `submit them as issues <https://github.com/pytest-dev/pytest/issues>`_ and:

      * Explain in detail how they should work.
      * Keep the scope as narrow as possible.  This will make it easier to implement.


.. _reportbugs:

报告错误
-----------

**Report bugs**

.. tabs::

    .. tab:: 中文

         报告 pytest 的错误请在 `issue tracker <https://github.com/pytest-dev/pytest/issues>`_ 中进行。

         如果您要报告错误，请包括以下内容：

         * 您的操作系统名称和版本。
         * 有关您本地设置的任何详细信息，这可能有助于故障排除，特别是 Python 解释器版本、已安装的库和 pytest 版本。
         * 详细的重现错误的步骤。

         如果您能够编写一个当前失败但应该通过的演示测试（xfail），那也是一个非常有用的提交，即使您无法修复该错误。

    .. tab:: 英文

      Report bugs for pytest in the `issue tracker <https://github.com/pytest-dev/pytest/issues>`_.

      If you are reporting a bug, please include:

      * Your operating system name and version.
      * Any details about your local setup that might be helpful in troubleshooting,
      specifically the Python interpreter version, installed libraries, and pytest
      version.
      * Detailed steps to reproduce the bug.

      If you can write a demonstration test that currently fails but should pass
      (xfail), that is a very useful commit to make as well, even if you cannot
      fix the bug itself.


.. _fixbugs:

修复错误
--------

**Fix bugs**

.. tabs::

    .. tab:: 中文

         查看 `GitHub issues for bugs <https://github.com/pytest-dev/pytest/labels/type:%20bug>`_。
         另见适合新贡献者的 `"good first issue" issues <https://github.com/pytest-dev/pytest/labels/good%20first%20issue>`_。

         :ref:`与开发者交谈 <contact>` 以了解您如何修复特定的错误。要表明您将处理某个特定问题，请在该问题上添加相关评论。

         不要忘记检查您最喜欢的插件的错误追踪器！

    .. tab:: 英文

         Look through the `GitHub issues for bugs <https://github.com/pytest-dev/pytest/labels/type:%20bug>`_.
         See also the `"good first issue" issues <https://github.com/pytest-dev/pytest/labels/good%20first%20issue>`_
         that are friendly to new contributors.

         :ref:`Talk <contact>` to developers to find out how you can fix specific bugs. To indicate that you are going
         to work on a particular issue, add a comment to that effect on the specific issue.

         Don't forget to check the issue trackers of your favourite plugins, too!

.. _writeplugins:

实现功能
------------------

**Implement features**

.. tabs::

    .. tab:: 中文

         查看 `GitHub issues for enhancements <https://github.com/pytest-dev/pytest/labels/type:%20enhancement>`_。

         :ref:`与开发者交谈 <contact>` 以了解您如何实现特定功能。

    .. tab:: 英文

         Look through the `GitHub issues for enhancements <https://github.com/pytest-dev/pytest/labels/type:%20enhancement>`_.

         :ref:`Talk <contact>` to developers to find out how you can implement specific
         features.

编写文档
-------------------

**Write documentation**

.. tabs::

    .. tab:: 中文

         Pytest 总是需要更多的文档。具体需要什么呢？

         * 更多补充文档。如果您发现某些地方不清楚，请告诉我们。
         * 文档翻译。目前我们只有英语版本。
         * 文档字符串。永远不会有太多文档字符串。
         * 博客文章、论文等 -- 都非常受欢迎。

         您也可以直接在 GitHub 网页界面编辑文档文件，而无需使用本地副本。这对于小的修复非常方便。

         .. note::
            使用以下命令在本地构建文档：

            .. code:: bash

               $ tox -e docs

            构建的文档应位于 ``doc/en/_build/html``，
            其中 'en' 指的是文档语言。

         Pytest 有一个 API 参考，大部分内容是从被记录项目的文档字符串中 `自动生成的 <https://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html>`_。Pytest 使用 `Sphinx 文档字符串格式 <https://sphinx-rtd-tutorial.readthedocs.io/en/latest/docstrings.html>`_。
         例如：

         .. code-block:: python

            def my_function(arg: ArgType) -> Foo:
               """执行重要操作。

               这里有更详细的信息，以与主题行分开的段落形式呈现。
               使用完整的句子 -- 句子以大写字母开头，以句号结尾。

               可以包含注解文档：

               :param short_arg: 一个决定某些内容的参数。
               :param long_arg:
                     一个长说明，跨越多行，如此溢出。
               :returns: 结果。
               :raises ValueError:
                     发生此错误时的详细信息。

               .. versionadded:: 6.0

               当使用类型提示时（如本例），上面的注解中包含类型并不是必需的。
               """

    .. tab:: 英文

         Pytest could always use more documentation.  What exactly is needed?

         * More complementary documentation.  Have you perhaps found something unclear?
         * Documentation translations.  We currently have only English.
         * Docstrings.  There can never be too many of them.
         * Blog posts, articles and such -- they're all very appreciated.

         You can also edit documentation files directly in the GitHub web interface,
         without using a local copy.  This can be convenient for small fixes.

         .. note::
            Build the documentation locally with the following command:

            .. code:: bash

               $ tox -e docs

            The built documentation should be available in ``doc/en/_build/html``,
            where 'en' refers to the documentation language.

         Pytest has an API reference which in large part is
         `generated automatically <https://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html>`_
         from the docstrings of the documented items. Pytest uses the
         `Sphinx docstring format <https://sphinx-rtd-tutorial.readthedocs.io/en/latest/docstrings.html>`_.
         For example:

         .. code-block:: python

            def my_function(arg: ArgType) -> Foo:
               """Do important stuff.

               More detailed info here, in separate paragraphs from the subject line.
               Use proper sentences -- start sentences with capital letters and end
               with periods.

               Can include annotated documentation:

               :param short_arg: An argument which determines stuff.
               :param long_arg:
                     A long explanation which spans multiple lines, overflows
                     like this.
               :returns: The result.
               :raises ValueError:
                     Detailed information when this can happen.

               .. versionadded:: 6.0

               Including types into the annotations above is not necessary when
               type-hinting is being used (as in this example).
               """


.. _submitplugin:

向 pytest-dev 提交插件
--------------------------------

**Submitting Plugins to pytest-dev**

.. tabs::

    .. tab:: 中文

         pytest 核心、支持代码和一些插件的开发在 ``pytest-dev`` 组织下的仓库中进行：

         - `pytest-dev on GitHub <https://github.com/pytest-dev>`_

         所有 pytest-dev 贡献者团队成员对所有包含的仓库具有写入访问权限。pytest 核心和插件通常使用 `pull requests`_ 向相应的仓库进行开发。

         ``pytest-dev`` 组织的目标是：

         * 为流行的 pytest 插件提供一个中央位置
         * 分担一些维护责任（以防维护者不再希望维护某个插件）

         您可以通过订阅 `pytest-dev 邮件列表 <https://mail.python.org/mailman/listinfo/pytest-dev>`_ 并发送一封邮件，指向您现有的 pytest 插件仓库来提交您的插件，该仓库必须具有以下内容：

         - 在 PyPI 上存在，并带有包含 ``pytest-`` 前缀的名称、版本号、作者、简短和详细描述的打包元数据。

         - 一个 `tox 配置 <https://tox.readthedocs.io/en/latest/config.html#configuration-discovery>`_，用于使用 `tox <https://tox.readthedocs.io>`_ 运行测试。

         - 一个 ``README``，描述如何使用插件以及其运行的平台。

         - 一个 ``LICENSE`` 文件，包含许可信息，并在其打包元数据中有相应的信息。

         - 一个用于错误报告和增强请求的问题跟踪器。

         - 一个 `changelog <https://keepachangelog.com/>`_。

         如果没有贡献者强烈反对且有两人同意，则可以将该仓库转移到 ``pytest-dev`` 组织。

         以下是仓库转移通常的流程（以名为 ``joedoe/pytest-xyz`` 的仓库为例）：

         * ``joedoe`` 将仓库所有权转移给 ``pytest-dev`` 管理员 ``calvin``。
         * ``calvin`` 创建 ``pytest-xyz-admin`` 和 ``pytest-xyz-developers`` 团队，邀请 ``joedoe`` 作为 **维护者** 加入两个团队。
         * ``calvin`` 将仓库转移到 ``pytest-dev`` 并配置团队访问权限：

         - ``pytest-xyz-admin`` **管理员** 访问权限；
         - ``pytest-xyz-developers`` **写入** 访问权限；

         ``pytest-dev/Contributors`` 团队对所有项目具有写入访问权限，且每个项目管理员都在其中。我们建议每个插件至少有三个人具有发布到 PyPI 的权限。

         仓库所有者可以放心，任何 ``pytest-dev`` 管理员都不会发布您的仓库或以任何方式取得所有权，除非在极少数情况下，某人在数月的联系尝试后变得没有响应。如前所述，目标是共享维护，避免“插件放弃”。

    .. tab:: 英文

         Development of the pytest core, support code, and some plugins happens
         in repositories living under the ``pytest-dev`` organisations:

         - `pytest-dev on GitHub <https://github.com/pytest-dev>`_

         All pytest-dev Contributors team members have write access to all contained
         repositories.  Pytest core and plugins are generally developed
         using `pull requests`_ to respective repositories.

         The objectives of the ``pytest-dev`` organisation are:

         * Having a central location for popular pytest plugins
         * Sharing some of the maintenance responsibility (in case a maintainer no
         longer wishes to maintain a plugin)

         You can submit your plugin by subscribing to the `pytest-dev mail list
         <https://mail.python.org/mailman/listinfo/pytest-dev>`_ and writing a
         mail pointing to your existing pytest plugin repository which must have
         the following:

         - PyPI presence with packaging metadata that contains a ``pytest-``
         prefixed name, version number, authors, short and long description.

         - a  `tox configuration <https://tox.readthedocs.io/en/latest/config.html#configuration-discovery>`_
         for running tests using `tox <https://tox.readthedocs.io>`_.

         - a ``README`` describing how to use the plugin and on which
         platforms it runs.

         - a ``LICENSE`` file containing the licensing information, with
         matching info in its packaging metadata.

         - an issue tracker for bug reports and enhancement requests.

         - a `changelog <https://keepachangelog.com/>`_.

         If no contributor strongly objects and two agree, the repository can then be
         transferred to the ``pytest-dev`` organisation.

         Here's a rundown of how a repository transfer usually proceeds
         (using a repository named ``joedoe/pytest-xyz`` as example):

         * ``joedoe`` transfers repository ownership to ``pytest-dev`` administrator ``calvin``.
         * ``calvin`` creates ``pytest-xyz-admin`` and ``pytest-xyz-developers`` teams, inviting ``joedoe`` to both as **maintainer**.
         * ``calvin`` transfers repository to ``pytest-dev`` and configures team access:

         - ``pytest-xyz-admin`` **admin** access;
         - ``pytest-xyz-developers`` **write** access;

         The ``pytest-dev/Contributors`` team has write access to all projects, and
         every project administrator is in it. We recommend that each plugin has at least three
         people who have the right to release to PyPI.

         Repository owners can rest assured that no ``pytest-dev`` administrator will ever make
         releases of your repository or take ownership in any way, except in rare cases
         where someone becomes unresponsive after months of contact attempts.
         As stated, the objective is to share maintenance and avoid "plugin-abandon".


.. _`pull requests`:
.. _pull-requests:

准备拉取请求
-----------------------

**Preparing Pull Requests**

简短版本
~~~~~~~~~~~~~

**Short version**

.. tabs::

    .. tab:: 中文

         #. Fork 该仓库。
         #. 如有必要，从上游获取标签（如果仅克隆了主分支 `git fetch --tags https://github.com/pytest-dev/pytest`）。
         #. 启用并安装 `pre-commit <https://pre-commit.com>`_ 以确保遵循风格指南和代码检查。
         #. 遵循 `PEP-8 <https://www.python.org/dev/peps/pep-0008/>`_ 进行命名。
         #. 测试使用 ``tox`` 运行::

            tox -e linting,py39

            上述测试环境通常足以覆盖大多数本地情况。

         #. 编写一个 ``changelog`` 条目：``changelog/2574.bugfix.rst``，使用问题 ID 号
            和 ``feature``、``improvement``、``bugfix``、``doc``、``deprecation``、
            ``breaking``、``vendor`` 或 ``trivial`` 中的一个作为问题类型。

         #. 除非您的更改是微不足道或文档修复（例如，拼写错误或小部分的重述），请将自己添加到 ``AUTHORS`` 文件中，按字母顺序排列。

    .. tab:: 英文

         #. Fork the repository.
         #. Fetch tags from upstream if necessary (if you cloned only main `git fetch --tags https://github.com/pytest-dev/pytest`).
         #. Enable and install `pre-commit <https://pre-commit.com>`_ to ensure style-guides and code checks are followed.
         #. Follow `PEP-8 <https://www.python.org/dev/peps/pep-0008/>`_ for naming.
         #. Tests are run using ``tox``::

            tox -e linting,py39

            The test environments above are usually enough to cover most cases locally.

         #. Write a ``changelog`` entry: ``changelog/2574.bugfix.rst``, use issue id number
            and one of ``feature``, ``improvement``, ``bugfix``, ``doc``, ``deprecation``,
            ``breaking``, ``vendor`` or ``trivial`` for the issue type.


         #. Unless your change is a trivial or a documentation fix (e.g., a typo or reword of a small section) please
            add yourself to the ``AUTHORS`` file, in alphabetical order.


完整版本
~~~~~~~~~~~~

**Long version**

.. tabs::

    .. tab:: 中文

         什么是“拉取请求”？它通知项目的核心开发者您希望审核和合并的更改。拉取请求存储在 `GitHub 服务器 <https://github.com/pytest-dev/pytest/pulls>`_ 上。一旦您发送了拉取请求，我们可以讨论其潜在的修改，甚至稍后添加更多的提交。在 `GitHub 帮助中心 <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests>`_ 中有一个优秀的教程，介绍了拉取请求的工作原理。

         以下是简单的概述，包含 pytest 特定的内容：

         #. Fork 该 `pytest GitHub 仓库 <https://github.com/pytest-dev/pytest>`__。可以使用 ``pytest`` 作为您的 fork 仓库名称，因为它将存放在您的用户名下。

         #. 使用 `git <https://git-scm.com/>`_ 在本地克隆您的 fork 并创建一个分支::

            $ git clone git@github.com:YOUR_GITHUB_USERNAME/pytest.git
            $ cd pytest
            $ git fetch --tags https://github.com/pytest-dev/pytest
            # 现在，从“main”创建自己的分支：

               $ git checkout -b your-bugfix-branch-name main

            由于我们使用“major.minor.micro”版本号，bug 修复通常会在微版本中发布，而特性会在小版本中发布，不兼容的更改则会在大版本中发布。

            您将需要标签来进行本地测试，因此请确保从主仓库获取标签。如果您怀疑没有获取到，请将主仓库设置为 upstream 并获取标签::

            $ git remote add upstream https://github.com/pytest-dev/pytest
            $ git fetch upstream --tags

            如果您需要 Git 的帮助，请遵循此快速入门指南： https://git.wiki.kernel.org/index.php/QuickStart

         #. 安装 `pre-commit <https://pre-commit.com>`_ 及其钩子到 pytest 仓库::

            $ pip install --user pre-commit
            $ pre-commit install

            之后，``pre-commit`` 将在您提交时运行。

            https://pre-commit.com/ 是一个管理和维护多语言预提交钩子的框架，以确保代码风格和格式一致。

         #. 安装 tox

            Tox 用于运行所有测试，并将自动设置虚拟环境以运行测试。
            （将隐式使用 https://virtualenv.pypa.io/en/latest/）::

            $ pip install tox

         #. 运行所有测试

            您的系统需要有 Python 3.8 或更高版本。现在，运行测试只需发出以下命令::

            $ tox -e linting,py39

            该命令将通过“tox”工具在 Python 3.9 上运行测试，并进行“lint”编码风格检查。

         #. 现在，您可以编辑本地工作副本，并根据需要再次运行测试。请遵循 `PEP-8 <https://www.python.org/dev/peps/pep-0008/>`_ 进行命名。

            您可以向 ``tox`` 传递不同的选项。例如，要在 Python 3.9 上运行测试并向 pytest 传递选项（例如在失败时进入 pdb），您可以这样做::

            $ tox -e py39 -- --pdb

            或者仅在 Python 3.9 上运行特定测试模块中的测试::

            $ tox -e py39 -- testing/test_config.py

            提交时，如果需要，``pre-commit`` 将重新格式化文件。

         #. 如果您更喜欢直接运行测试，而不是使用 ``tox``，我们建议创建一个虚拟环境并使用带有 ``dev`` 扩展的可编辑安装::

               $ python3 -m venv .venv
               $ source .venv/bin/activate  # Linux
               $ .venv/Scripts/activate.bat  # Windows
               $ pip install -e ".[dev]"

            之后，您可以编辑文件并正常运行 pytest::

               $ pytest testing/test_config.py

         #. 在 ``changelog`` 中创建一个新的变更日志条目。文件应命名为 ``<issueid>.<type>.rst``，
            其中 *issueid* 是与更改相关的问题编号，*type* 是以下之一：
            ``feature``、``improvement``、``bugfix``、``doc``、``deprecation``、``breaking``、``vendor`` 或 ``trivial``。 如果更改不影响 pytest 的文档行为，您可以跳过创建变更日志条目。

         #. 如果您还没有在 ``AUTHORS`` 文件中，请按字母顺序添加自己。

         #. 一旦您的测试通过并且对更改感到满意，请提交和推送::

            $ git commit -a -m "<commit message>"
            $ git push -u

         #. 最后，通过 GitHub 网站提交拉取请求，使用以下数据::

            head-fork: YOUR_GITHUB_USERNAME/pytest
            compare: your-branch-name

            base-fork: pytest-dev/pytest
            base: main

    .. tab:: 英文

         What is a "pull request"?  It informs the project's core developers about the
         changes you want to review and merge.  Pull requests are stored on
         `GitHub servers <https://github.com/pytest-dev/pytest/pulls>`_.
         Once you send a pull request, we can discuss its potential modifications and
         even add more commits to it later on. There's an excellent tutorial on how Pull
         Requests work in the
         `GitHub Help Center <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests>`_.

         Here is a simple overview, with pytest-specific bits:

         #. Fork the
            `pytest GitHub repository <https://github.com/pytest-dev/pytest>`__.  It's
            fine to use ``pytest`` as your fork repository name because it will live
            under your user.

         #. Clone your fork locally using `git <https://git-scm.com/>`_ and create a branch::

            $ git clone git@github.com:YOUR_GITHUB_USERNAME/pytest.git
            $ cd pytest
            $ git fetch --tags https://github.com/pytest-dev/pytest
            # now, create your own branch off "main":

               $ git checkout -b your-bugfix-branch-name main

            Given we have "major.minor.micro" version numbers, bug fixes will usually
            be released in micro releases whereas features will be released in
            minor releases and incompatible changes in major releases.

            You will need the tags to test locally, so be sure you have the tags from the main repository. If you suspect you don't, set the main repository as upstream and fetch the tags::

            $ git remote add upstream https://github.com/pytest-dev/pytest
            $ git fetch upstream --tags

            If you need some help with Git, follow this quick start
            guide: https://git.wiki.kernel.org/index.php/QuickStart

         #. Install `pre-commit <https://pre-commit.com>`_ and its hook on the pytest repo::

            $ pip install --user pre-commit
            $ pre-commit install

            Afterwards ``pre-commit`` will run whenever you commit.

            https://pre-commit.com/ is a framework for managing and maintaining multi-language pre-commit hooks
            to ensure code-style and code formatting is consistent.

         #. Install tox

            Tox is used to run all the tests and will automatically setup virtualenvs
            to run the tests in.
            (will implicitly use https://virtualenv.pypa.io/en/latest/)::

            $ pip install tox

         #. Run all the tests

            You need to have Python 3.8 or later available in your system.  Now
            running tests is as simple as issuing this command::

            $ tox -e linting,py39

            This command will run tests via the "tox" tool against Python 3.9
            and also perform "lint" coding-style checks.

         #. You can now edit your local working copy and run the tests again as necessary. Please follow `PEP-8 <https://www.python.org/dev/peps/pep-0008/>`_ for naming.

            You can pass different options to ``tox``. For example, to run tests on Python 3.9 and pass options to pytest
            (e.g. enter pdb on failure) to pytest you can do::

            $ tox -e py39 -- --pdb

            Or to only run tests in a particular test module on Python 3.9::

            $ tox -e py39 -- testing/test_config.py


            When committing, ``pre-commit`` will re-format the files if necessary.

         #. If instead of using ``tox`` you prefer to run the tests directly, then we suggest to create a virtual environment and use
            an editable install with the ``dev`` extra::

               $ python3 -m venv .venv
               $ source .venv/bin/activate  # Linux
               $ .venv/Scripts/activate.bat  # Windows
               $ pip install -e ".[dev]"

            Afterwards, you can edit the files and run pytest normally::

               $ pytest testing/test_config.py

         #. Create a new changelog entry in ``changelog``. The file should be named ``<issueid>.<type>.rst``,
            where *issueid* is the number of the issue related to the change and *type* is one of
            ``feature``, ``improvement``, ``bugfix``, ``doc``, ``deprecation``, ``breaking``, ``vendor``
            or ``trivial``. You may skip creating the changelog entry if the change doesn't affect the
            documented behaviour of pytest.

         #. Add yourself to ``AUTHORS`` file if not there yet, in alphabetical order.

         #. Commit and push once your tests pass and you are happy with your change(s)::

            $ git commit -a -m "<commit message>"
            $ git push -u

         #. Finally, submit a pull request through the GitHub website using this data::

            head-fork: YOUR_GITHUB_USERNAME/pytest
            compare: your-branch-name

            base-fork: pytest-dev/pytest
            base: main


编写测试
~~~~~~~~~~~~~

**Writing Tests**

.. tabs::

    .. tab:: 中文

         为插件或 pytest 本身编写测试通常使用 `pytester fixture <https://docs.pytest.org/en/stable/reference/reference.html#pytester>`_，作为“黑盒”测试。

         例如，为确保简单测试通过，您可以编写：

         .. code-block:: python

            def test_true_assertion(pytester):
               pytester.makepyfile(
                     """
                     def test_foo():
                        assert True
               """
               )
               result = pytester.runpytest()
               result.assert_outcomes(failed=0, passed=1)


         另外，您可以使用 *glob-like* 表达式根据实际输出进行检查：

         .. code-block:: python

            def test_true_assertion(pytester):
               pytester.makepyfile(
                     """
                     def test_foo():
                        assert False
               """
               )
               result = pytester.runpytest()
               result.stdout.fnmatch_lines(["*assert False*", "*1 failed*"])

         在选择文件以编写新测试时，请查看现有文件，看看是否有一个文件看起来很合适。例如，关于 ``--lf`` 选项的回归测试应该放在 ``test_cacheprovider.py`` 中，因为该选项是在 ``cacheprovider.py`` 中实现的。如果不确定，可以打开一个 PR，提出您的最佳猜测，我们可以在代码中讨论这个问题。

    .. tab:: 英文

         Writing tests for plugins or for pytest itself is often done using the `pytester fixture <https://docs.pytest.org/en/stable/reference/reference.html#pytester>`_, as a "black-box" test.

         For example, to ensure a simple test passes you can write:

         .. code-block:: python

            def test_true_assertion(pytester):
               pytester.makepyfile(
                     """
                     def test_foo():
                        assert True
               """
               )
               result = pytester.runpytest()
               result.assert_outcomes(failed=0, passed=1)


         Alternatively, it is possible to make checks based on the actual output of the termal using
         *glob-like* expressions:

         .. code-block:: python

            def test_true_assertion(pytester):
               pytester.makepyfile(
                     """
                     def test_foo():
                        assert False
               """
               )
               result = pytester.runpytest()
               result.stdout.fnmatch_lines(["*assert False*", "*1 failed*"])

         When choosing a file where to write a new test, take a look at the existing files and see if there's
         one file which looks like a good fit. For example, a regression test about a bug in the ``--lf`` option
         should go into ``test_cacheprovider.py``, given that this option is implemented in ``cacheprovider.py``.
         If in doubt, go ahead and open a PR with your best guess and we can discuss this over the code.

加入开发团队
----------------------------

**Joining the Development Team**

.. tabs::

    .. tab:: 中文

      任何成功完成了一个不需要开发团队额外工作即可合并的拉取请求的人，若愿意，可以获得提交访问权限（如果我们忘记询问，请友好地提醒我们）。这并不意味着您的贡献工作流程有所改变：每个人都要经过相同的拉取请求和审核流程，没有人会在未经批准的情况下合并自己的拉取请求。不过，这确实意味着您可以更全面地参与开发过程，因为您可以在审核其他贡献者的拉取请求后自己合并它们。

    .. tab:: 英文

         Anyone who has successfully seen through a pull request which did not
         require any extra work from the development team to merge will
         themselves gain commit access if they so wish (if we forget to ask please send a friendly
         reminder).  This does not mean there is any change in your contribution workflow:
         everyone goes through the same pull-request-and-review process and
         no-one merges their own pull requests unless already approved.  It does however mean you can
         participate in the development process more fully since you can merge
         pull requests from other contributors yourself after having reviewed
         them.


合并/压缩指南
-----------------------

**Merge/squash guidelines**

.. tabs::

    .. tab:: 中文

         当一个拉取请求被批准并准备合并到 ``main`` 分支时，可以选择 *合并(merge)* 提交而不改变，或 *压缩(squash)* 所有提交为一个单一的提交。

         以下是关于如何进行的指南，基于单个拉取请求提交历史的示例：

         1. 杂项提交：

            * ``Implement X``
            * ``Fix test_a``
            * ``Add myself to AUTHORS``
            * ``fixup! Fix test_a``
            * ``Update tests/test_integration.py``
            * ``Merge origin/main into PR branch``
            * ``Update tests/test_integration.py``

            在这种情况下，建议使用**Squash**合并策略：提交历史有点杂乱（并不是贬义，通常人们只是因为知道更改最终会被压缩在一起而进行提交），因此将所有内容压缩为一个提交是最佳选择。您必须清理提交信息，确保其包含有用的细节。

         2. 与同一主题相关的独立提交：

            * ``Implement X``
            * ``Add myself to AUTHORS``
            * ``Update CHANGELOG for X``

            在这种情况下，建议使用 **Squash** 合并策略：虽然提交历史不像上述示例那样“杂乱”，但单个提交总体上并没有太多价值，特别是在几个月/几年后查看更改时。

         3. 每个提交都有自己的主题（重构、重命名等），但仍有一个更大的主题/目的。

            * ``Refactor class X in preparation for feature Y``
            * ``Remove unused method``
            * ``Implement feature Y``

            在这种情况下，建议使用**Merge**策略：每个提交本身都是有价值的，即使它们整体上服务于一个共同的主题。稍后查看历史时，单独将未使用方法的删除记录在其提交中是有用的，并且可以提供更多信息（例如，它最初是如何变得未使用的）。

         4. 每个提交都有自己的主题，但除了改进代码库（使用更现代的技术、改进类型、去除杂乱等）之外没有更大的主题/目的。

            * ``Improve internal names in X``
            * ``Add type annotations to Y``
            * ``Remove unnecessary dict access``
            * ``Remove unreachable code due to EOL Python``

            在这种情况下，建议使用 **Merge** 策略：每个提交本身都是有价值的，每个提交中的信息在长期内都是有价值的。

         正如所提到的，这些是总体指南，而不是刻在石头上的规则。此主题在 `#12633 <https://github.com/pytest-dev/pytest/discussions/12633>`_ 中进行了讨论。

         *回退拉取请求* （如从 ``backport`` 标签自动创建的请求）应始终 **压缩** ，因为它们保留了原始拉取请求的作者。

    .. tab:: 英文

         When a PR is approved and ready to be integrated to the ``main`` branch, one has the option to *merge* the commits unchanged, or *squash* all the commits into a single commit.

         Here are some guidelines on how to proceed, based on examples of a single PR commit history:

         1. Miscellaneous commits:

            * ``Implement X``
            * ``Fix test_a``
            * ``Add myself to AUTHORS``
            * ``fixup! Fix test_a``
            * ``Update tests/test_integration.py``
            * ``Merge origin/main into PR branch``
            * ``Update tests/test_integration.py``

            In this case, prefer to use the **Squash** merge strategy: the commit history is a bit messy (not in a derogatory way, often one just commits changes because they know the changes will eventually be squashed together), so squashing everything into a single commit is best. You must clean up the commit message, making sure it contains useful details.

         2. Separate commits related to the same topic:

            * ``Implement X``
            * ``Add myself to AUTHORS``
            * ``Update CHANGELOG for X``

            In this case, prefer to use the **Squash** merge strategy: while the commit history is not "messy" as in the example above, the individual commits do not bring much value overall, specially when looking at the changes a few months/years down the line.

         3. Separate commits, each with their own topic (refactorings, renames, etc), but still have a larger topic/purpose.

            * ``Refactor class X in preparation for feature Y``
            * ``Remove unused method``
            * ``Implement feature Y``

            In this case, prefer to use the **Merge** strategy: each commit is valuable on its own, even if they serve a common topic overall. Looking at the history later, it is useful to have the removal of the unused method separately on its own commit, along with more information (such as how it became unused in the first place).

         4. Separate commits, each with their own topic, but without a larger topic/purpose other than improve the code base (using more modern techniques, improve typing, removing clutter, etc).

            * ``Improve internal names in X``
            * ``Add type annotations to Y``
            * ``Remove unnecessary dict access``
            * ``Remove unreachable code due to EOL Python``

            In this case, prefer to use the **Merge** strategy: each commit is valuable on its own, and the information on each is valuable in the long term.


         As mentioned, those are overall guidelines, not rules cast in stone. This topic was discussed in `#12633 <https://github.com/pytest-dev/pytest/discussions/12633>`_.


         *Backport PRs* (as those created automatically from a ``backport`` label) should always be **squashed**, as they preserve the original PR author.


为下一个补丁版本反向移植错误修复
------------------------------------------------

**Backporting bug fixes for the next patch release**

.. tabs::

    .. tab:: 中文

         Pytest 每隔几周或几个月会发布一次功能版本。在这之间，会对之前的功能版本进行补丁发布，仅包含错误修复。错误修复通常是修复回归，但也可能是任何应该在下一个功能版本之前达到用户的更改。

         假设最新的发布版本是 1.2.3，而您想在 1.2.4 中包含一个错误修复（请查看 https://github.com/pytest-dev/pytest/releases 以获取实际的最新发布）。这个过程如下：

         #. 首先，确保在 ``main`` 分支中修复了该错误，使用常规的拉取请求，如上所述。如果错误修复不再适用于 ``main``，则此步骤不适用。

         自动方法：

         在您想要回退的拉取请求上添加 ``backport 1.2.x`` 标签。这将创建一个针对 ``1.2.x`` 分支的回退拉取请求。

         手动方法：

         #. ``git checkout origin/1.2.x -b backport-XXXX`` # 在这里使用主要拉取请求编号

         #. 在拉取请求的 *merged* 消息中找到合并提交，例如：

            nicoddemus merged commit 0f8b462 into pytest-dev:main

         #. ``git cherry-pick -x -m1 REVISION`` # 使用您在上面找到的修订版（``0f8b462``）。

         #. 打开一个目标为 ``1.2.x`` 的拉取请求：

            * 用 ``[1.2.x]`` 前缀消息。
            * 删除拉取请求正文，它通常包含重复的提交信息。

    .. tab:: 英文

         Pytest makes a feature release every few weeks or months. In between, patch releases
         are made to the previous feature release, containing bug fixes only. The bug fixes
         usually fix regressions, but may be any change that should reach users before the
         next feature release.

         Suppose for example that the latest release was 1.2.3, and you want to include
         a bug fix in 1.2.4 (check https://github.com/pytest-dev/pytest/releases for the
         actual latest release). The procedure for this is:

         #. First, make sure the bug is fixed in the ``main`` branch, with a regular pull
            request, as described above. An exception to this is if the bug fix is not
            applicable to ``main`` anymore.

         Automatic method:

         Add a ``backport 1.2.x`` label to the PR you want to backport. This will create
         a backport PR against the ``1.2.x`` branch.

         Manual method:

         #. ``git checkout origin/1.2.x -b backport-XXXX`` # use the main PR number here

         #. Locate the merge commit on the PR, in the *merged* message, for example:

            nicoddemus merged commit 0f8b462 into pytest-dev:main

         #. ``git cherry-pick -x -m1 REVISION`` # use the revision you found above (``0f8b462``).

         #. Open a PR targeting ``1.2.x``:

            * Prefix the message with ``[1.2.x]``.
            * Delete the PR body, it usually contains a duplicate commit message.


谁负责反向移植
~~~~~~~~~~~~~~~~~~~~~~~~

**Who does the backporting**

.. tabs::

    .. tab:: 中文

         如上所述，错误应首先在 ``main`` 上修复（除非在极少数情况下错误仅发生在以前的版本）。那么，谁应该执行上述回退程序呢？

         1. 如果错误是由核心开发者修复的，那么该核心开发者有主要责任进行回退。
         2. 然而，合并通常是由其他维护者完成的，在这种情况下，如果他们有时间，进行回退程序是很友好的。
         3. 对于由非维护者提交的错误，通常预计核心开发者会进行回退，通常是合并了 ``main`` 上的拉取请求的那位开发者。
         4. 如果非维护者注意到在 ``main`` 上修复的错误但尚未回退（由于维护者忘记应用 *needs backport* 标签，或者单纯遗漏），他们也可以打开一个带有回退的拉取请求。这个过程简单，并且确实有助于项目的维护。

         上述所有内容并不是规则，而只是关于我们应期待回退的一些指导方针/建议。

         回退应 **被压缩(squashed)** （而不是 **合并(merged)** ），因为这样可以正确保留原始拉取请求的作者。

    .. tab:: 英文

         As mentioned above, bugs should first be fixed on ``main`` (except in rare occasions
         that a bug only happens in a previous release). So, who should do the backport procedure described
         above?

         1. If the bug was fixed by a core developer, it is the main responsibility of that core developer
            to do the backport.
         2. However, often the merge is done by another maintainer, in which case it is nice of them to
            do the backport procedure if they have the time.
         3. For bugs submitted by non-maintainers, it is expected that a core developer will to do
            the backport, normally the one that merged the PR on ``main``.
         4. If a non-maintainers notices a bug which is fixed on ``main`` but has not been backported
            (due to maintainers forgetting to apply the *needs backport* label, or just plain missing it),
            they are also welcome to open a PR with the backport. The procedure is simple and really
            helps with the maintenance of the project.

         All the above are not rules, but merely some guidelines/suggestions on what we should expect
         about backports.

         Backports should be **squashed** (rather than **merged**), as doing so preserves the original PR author correctly.

处理过时的问题/PR
-------------------------

**Handling stale issues/PRs**

.. tabs::

    .. tab:: 中文

         过时的问题/拉取请求是指pytest贡献者询问了问题/变更，但作者在相对较长的时间内没有回应/实施，或者讨论因人们似乎失去兴趣而停止。

         人们不回答问题或实施请求变更的原因有很多：他们可能忙碌、失去兴趣或只是忘记了，但事实是这在开源软件中非常常见。

         pytest团队非常重视每一个问题和拉取请求，但由于这是一个高流量项目，每天都会提交许多问题和拉取请求，我们试图通过定期关闭它们来减少过时的问题和拉取请求。当以这种方式关闭问题/拉取请求时，这绝不是对该问题/拉取请求所涉及主题的否定，而只是我们清理队列、使维护者的工作更易管理的一种方式。提交者如果认为合适，始终可以在稍后重新打开问题/拉取请求。

    .. tab:: 英文

         Stale issues/PRs are those where pytest contributors have asked for questions/changes
         and the authors didn't get around to answer/implement them yet after a somewhat long time, or
         the discussion simply died because people seemed to lose interest.

         There are many reasons why people don't answer questions or implement requested changes:
         they might get busy, lose interest, or just forget about it,
         but the fact is that this is very common in open source software.

         The pytest team really appreciates every issue and pull request, but being a high-volume project
         with many issues and pull requests being submitted daily, we try to reduce the number of stale
         issues and PRs by regularly closing them. When an issue/pull request is closed in this manner,
         it is by no means a dismissal of the topic being tackled by the issue/pull request, but it
         is just a way for us to clear up the queue and make the maintainers' work more manageable. Submitters
         can always reopen the issue/pull request in their own time later if it makes sense.

何时关闭
~~~~~~~~~~~~~

**When to close**

.. tabs::

    .. tab:: 中文

         以下是维护者在决定何时因缺乏活动而关闭问题/拉取请求时使用的一些一般规则：

         * 标记为 ``question`` 或 ``needs information`` 的问题：在14天无活动后关闭。
         * 标记为 ``proposal`` 的问题：在六个月无活动后关闭。
         * 拉取请求：一个月后，考虑提醒作者、更新关联问题或考虑关闭。对于几乎完成的拉取请求，团队应考虑完成并合并它。

         上述内容 **不是硬性规则** ，而仅仅是 **指导方针** ，可以根据具体情况（且通常会）逐案审查。

    .. tab:: 英文

         Here are a few general rules the maintainers use deciding when to close issues/PRs because
         of lack of inactivity:

         * Issues labeled ``question`` or ``needs information``: closed after 14 days inactive.
         * Issues labeled ``proposal``: closed after six months inactive.
         * Pull requests: after one month, consider pinging the author, update linked issue, or consider closing. For pull requests which are nearly finished, the team should consider finishing it up and merging it.

         The above are **not hard rules**, but merely **guidelines**, and can be (and often are!) reviewed on a case-by-case basis.

关闭拉取请求
~~~~~~~~~~~~~~~~~~~~~

**Closing pull requests**

.. tabs::

    .. tab:: 中文

         在关闭拉取请求时，需要承认提交者所展现出的时间、努力和兴趣。正如之前提到的，团队并不想完全拒绝一个停滞的拉取请求，而只是想清理我们的队列，因此在关闭一个变得无效的拉取请求时，像下面这样的消息是适当的：

            Hi <contributor>,

            首先，我们要感谢你在此项目上投入的时间和努力，pytest团队对此深表感激。

            然而，我们注意到你已经有一段时间没有更新这个PR了。pytest是一个活跃的项目，每天都有许多问题和PR被打开，因此我们这些维护者很难追踪哪些PR准备合并，哪些需要审查，或者哪些需要更多关注。

            所以出于这些原因，我们认为现在最好关闭这个PR，但这仅仅是为了清理我们的队列，并不是对你修改的拒绝。我们仍然鼓励你在准备好时重新打开这个PR（这只需轻点一下按钮）。

            再次感谢你在此项目上的努力，希望你在未来能再次关注这个问题！

            <bye>

    .. tab:: 英文

         When closing a Pull Request, it needs to be acknowledging the time, effort, and interest demonstrated by the person which submitted it. As mentioned previously, it is not the intent of the team to dismiss a stalled pull request entirely but to merely to clear up our queue, so a message like the one below is warranted when closing a pull request that went stale:

            Hi <contributor>,

            First of all, we would like to thank you for your time and effort on working on this, the pytest team deeply appreciates it.

            We noticed it has been awhile since you have updated this PR, however. pytest is a high activity project, with many issues/PRs being opened daily, so it is hard for us maintainers to track which PRs are ready for merging, for review, or need more attention.

            So for those reasons we, think it is best to close the PR for now, but with the only intention to clean up our queue, it is by no means a rejection of your changes. We still encourage you to re-open this PR (it is just a click of a button away) when you are ready to get back to it.

            Again we appreciate your time for working on this, and hope you might get back to this at a later time!

            <bye>

关闭问题
--------------

**Closing issues**

.. tabs::

    .. tab:: 中文

         当提交拉取请求以修复某个问题时，请在PR描述和/或提交中添加类似 ``closes #XYZW`` 的文本（其中 ``XYZW`` 是问题编号）。有关更多信息，请参见 `GitHub文档 <https://help.github.com/en/github/managing-your-work-on-github/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword>`_。

         当问题是由于用户错误（例如对功能的误解）引起时，请礼貌地向用户解释所提出的问题实际上并不是问题，并请他们在没有进一步问题时关闭该问题。如果原始请求者没有回复，则将按照上面“处理无效问题/PR”_部分中所述进行处理。

    .. tab:: 英文

         When a pull request is submitted to fix an issue, add text like ``closes #XYZW`` to the PR description and/or commits (where ``XYZW`` is the issue number). See the `GitHub docs <https://help.github.com/en/github/managing-your-work-on-github/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword>`_ for more information.

         When an issue is due to user error (e.g. misunderstanding of a functionality), please politely explain to the user why the issue raised is really a non-issue and ask them to close the issue if they have no further questions. If the original requester is unresponsive, the issue will be handled as described in the section `Handling stale issues/PRs`_ above.
