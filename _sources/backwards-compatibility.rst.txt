.. _backwards-compatibility:

向后兼容政策
==============================

**Backwards Compatibility Policy**

.. tabs::

    .. tab:: 中文

         .. versionadded: 6.0

         Pytest 是一个正在积极发展的项目，经历了数十年的演变。我们不断学习新的、更好的结构，以表达与测试相关的不同细节。

         在实施这些修改时，我们努力确保用户和社区/插件作者能够轻松过渡，并不想给他们带来不必要的困扰。

         截至目前，pytest 考虑了多种类型的向后兼容性过渡：

         a) 微不足道：API 可以轻松转换为新机制，并且不会导致问题变化。

            我们尝试无限期支持这些 API，同时通过文档鼓励用户切换到更新或更好的机制。

         b) 过渡性：旧 API 和新 API 不冲突，我们可以通过使用警告来帮助用户过渡，同时支持两者一段较长的时间。

            我们只会在主要版本中开始删除弃用功能（例如，如果我们在 3.0 中弃用某个功能，我们将在 4.0 中开始删除它），并至少保留两个次要版本（例如，如果我们在 3.9 中弃用某个功能，而 4.0 是下一个版本，我们将在 5.0 中开始删除它，而不是在 4.0 中）。

            计划在主要版本 X 中删除的弃用功能将使用警告类 `PytestRemovedInXWarning`（是 :class:`~pytest.PytestDeprecationWarning` 的子类）。

            当弃用期结束时（例如，4.0 发布），我们不会立即删除弃用的功能，而是会使用标准的警告过滤器将 `PytestRemovedInXWarning`（例如，`PytestRemovedIn4Warning`）默认转换为**错误**。这种方法明确表示删除即将发生，并仍然给您时间将弃用功能变为警告而不是错误，以便您可以在自己的时间内处理。在下一个次要版本（例如，4.1）中，该功能将被有效删除。

         c) 真正的破坏应仅在正常过渡不可持续且会导致重要开发或功能延误数年时考虑。此外，它们应限于实际用户数量非常少的 API（例如，仅影响某些插件），并且可以事先与社区进行协调。

            此类即将更改的示例：

            * 删除 ``pytest_runtest_protocol/nextitem`` - :issue:`895`
            * 调整节点树以包含 ``FunctionDefinition``
            * 调整 ``SetupState`` :issue:`895`

            真正的破坏必须首先在一个问题中进行公告，该问题应包含：

            * 变更的详细描述
            * 理由
            * 对用户和插件作者的预期影响（例如在 :issue:`895` 中）

            在问题上没有明确的 *-1* 后，应该跟进一个初步的概念验证拉取请求（POC）。

            这个概念验证既作为评估影响的协调点，也可能为提出过渡解决方案提供灵感。

            在合理的时间后，该拉取请求可以合并以为新的主要版本奠定基础。

            为了使拉取请求从概念验证成熟为接受，它必须包含：
            * 设置弃用错误/警告，帮助用户修复和迁移他们的代码。如果可以在当前系列下引入弃用期，以便在真正的破坏之前，应该在一个单独的拉取请求中引入，并成为当前发布流程的一部分。
            * 对理由的详细描述以及如何在 ``doc/en/deprecations.rst`` 中迁移代码的示例。

    .. tab:: 英文

         .. versionadded: 6.0

         Pytest is an actively evolving project that has been decades in the making.
         We keep learning about new and better structures to express different details about testing.

         While we implement those modifications, we try to ensure an easy transition and don't want to impose unnecessary churn on our users and community/plugin authors.

         As of now, pytest considers multiple types of backward compatibility transitions:

         a) trivial: APIs that trivially translate to the new mechanism and do not cause problematic changes.

            We try to support those indefinitely while encouraging users to switch to newer or better mechanisms through documentation.

         b) transitional: the old and new APIs don't conflict, and we can help users transition by using warnings while supporting both for a prolonged period of time.

            We will only start the removal of deprecated functionality in major releases (e.g., if we deprecate something in 3.0, we will start to remove it in 4.0), and keep it around for at least two minor releases (e.g., if we deprecate something in 3.9 and 4.0 is the next release, we start to remove it in 5.0, not in 4.0).

            A deprecated feature scheduled to be removed in major version X will use the warning class `PytestRemovedInXWarning` (a subclass of :class:`~pytest.PytestDeprecationWarning`).

            When the deprecation expires (e.g., 4.0 is released), we won't remove the deprecated functionality immediately but will use the standard warning filters to turn `PytestRemovedInXWarning` (e.g., `PytestRemovedIn4Warning`) into **errors** by default. This approach makes it explicit that removal is imminent and still gives you time to turn the deprecated feature into a warning instead of an error so it can be dealt with in your own time. In the next minor release (e.g., 4.1), the feature will be effectively removed.

         c) True breakage should only be considered when a normal transition is unreasonably unsustainable and would offset important developments or features by years. In addition, they should be limited to APIs where the number of actual users is very small (for example, only impacting some plugins) and can be coordinated with the community in advance.

            Examples for such upcoming changes:

            * removal of ``pytest_runtest_protocol/nextitem`` - :issue:`895`
            * rearranging of the node tree to include ``FunctionDefinition``
            * rearranging of ``SetupState`` :issue:`895`

            True breakages must be announced first in an issue containing:

            * Detailed description of the change
            * Rationale
            * Expected impact on users and plugin authors (example in :issue:`895`)

            After there's no hard *-1* on the issue it should be followed up by an initial proof-of-concept Pull Request.

            This POC serves as both a coordination point to assess impact and potential inspiration to come up with a transitional solution after all.

            After a reasonable amount of time the PR can be merged to base a new major release.

            For the PR to mature from POC to acceptance, it must contain:
            * Setup of deprecation errors/warnings that help users fix and port their code. If it is possible to introduce a deprecation period under the current series, before the true breakage, it should be introduced in a separate PR and be part of the current release stream.
            * Detailed description of the rationale and examples on how to port code in ``doc/en/deprecations.rst``.


历史
=========

**History**


主要关注平稳过渡 - 立场（6.0 之前）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Focus primary on smooth transition - stance (pre 6.0)**

.. tabs::

    .. tab:: 中文

         保持向后兼容在 pytest 项目中具有非常高的优先级。虽然多年来我们已弃用了一些功能，但大多数仍然得到支持。pytest 中的所有弃用都是因为出现了更简单或更高效的方式来完成相同的任务，使得旧的做法变得不再必要。

         在 pytest 3.0 版本中，我们引入了明确的沟通机制，以说明我们何时会真正移除旧的功能，并礼貌地请您使用新的方式，同时给您足够的时间来调整您的测试或在有合理理由保留弃用功能的情况下提出担忧。

         为了传达这些变化，我们使用自定义警告层次结构发布弃用警告（参见 :ref:`internal-warnings`）。这些警告可以通过标准手段抑制：``-W`` 命令行标志或 ``filterwarnings`` ini 选项（参见 :ref:`warnings`），但我们建议您谨慎且暂时地使用这些选项，并在可能的情况下遵循警告。

         我们只会在主要版本中开始删除弃用功能（例如，如果我们在 3.0 中弃用某个功能，我们将在 4.0 中开始删除它），并至少保留两个次要版本（例如，如果我们在 3.9 中弃用某个功能，而 4.0 是下一个版本，我们将在 5.0 中开始删除它，而不是在 4.0 中）。

         当弃用期结束时（例如，4.0 发布），我们不会立即删除弃用的功能，而是会使用标准的警告过滤器将其默认转换为**错误**。这种方法明确表明删除即将发生，并仍然给您时间将弃用功能转变为警告而不是错误，以便您可以在自己的时间内处理。在下一个次要版本（例如，4.1）中，该功能将被有效删除。

    .. tab:: 英文

         Keeping backwards compatibility has a very high priority in the pytest project. Although we have deprecated functionality over the years, most of it is still supported. All deprecations in pytest were done because simpler or more efficient ways of accomplishing the same tasks have emerged, making the old way of doing things unnecessary.

         With the pytest 3.0 release, we introduced a clear communication scheme for when we will actually remove the old busted joint and politely ask you to use the new hotness instead, while giving you enough time to adjust your tests or raise concerns if there are valid reasons to keep deprecated functionality around.

         To communicate changes, we issue deprecation warnings using a custom warning hierarchy (see :ref:`internal-warnings`). These warnings may be suppressed using the standard means: ``-W`` command-line flag or ``filterwarnings`` ini options (see :ref:`warnings`), but we suggest to use these sparingly and temporarily, and heed the warnings when possible.

         We will only start the removal of deprecated functionality in major releases (e.g. if we deprecate something in 3.0, we will start to remove it in 4.0), and keep it around for at least two minor releases (e.g. if we deprecate something in 3.9 and 4.0 is the next release, we start to remove it in 5.0, not in 4.0).

         When the deprecation expires (e.g. 4.0 is released), we won't remove the deprecated functionality immediately, but will use the standard warning filters to turn them into **errors** by default. This approach makes it explicit that removal is imminent, and still gives you time to turn the deprecated feature into a warning instead of an error so it can be dealt with in your own time. In the next minor release (e.g. 4.1), the feature will be effectively removed.


弃用路线图
-------------------

**Deprecation Roadmap**

.. tabs::

    .. tab:: 中文

         当前已弃用并在之前版本中移除的功能可以在 :ref:`deprecations` 中找到。

         我们通过里程碑和 GitHub 上的 `deprecation <https://github.com/pytest-dev/pytest/issues?q=label%3A%22type%3A+deprecation%22>`_ 及 `removal <https://github.com/pytest-dev/pytest/labels/type%3A%20removal>`_ 标签跟踪未来的弃用和移除功能。

    .. tab:: 英文

         Features currently deprecated and removed in previous releases can be found in :ref:`deprecations`.

         We track future deprecation and removal of features using milestones and the `deprecation <https://github.com/pytest-dev/pytest/issues?q=label%3A%22type%3A+deprecation%22>`_ and `removal <https://github.com/pytest-dev/pytest/labels/type%3A%20removal>`_ labels on GitHub.


Python版本支持
======================

**Python version support**

.. tabs::

    .. tab:: 中文

      已发布的 pytest 版本支持在发布时所有处于积极维护中的 Python 版本：

      ==============  ===================
      pytest 版本      最低 Python 版本
      ==============  ===================
      8.0+            3.8+
      7.1+            3.7+
      6.2 - 7.0       3.6+
      5.0 - 6.1       3.5+
      3.3 - 4.6       2.7, 3.4+
      ==============  ===================

      `Python 版本状态 <https://devguide.python.org/versions/>`__。

    .. tab:: 英文

         Released pytest versions support all Python versions that are actively maintained at the time of the release:

         ==============  ===================
         pytest version  min. Python version
         ==============  ===================
         8.0+            3.8+
         7.1+            3.7+
         6.2 - 7.0       3.6+
         5.0 - 6.1       3.5+
         3.3 - 4.6       2.7, 3.4+
         ==============  ===================

         `Status of Python Versions <https://devguide.python.org/versions/>`__.
