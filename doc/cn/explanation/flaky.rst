
不稳定的测试
-----------

**Flaky tests**

.. tabs::

    .. tab:: 中文

        “间歇性”测试是指表现出间歇性或偶发失败的测试，其行为似乎是非确定性的。有时它会通过，有时会失败，且原因不明。本页讨论了可以帮助识别、修复或缓解这些问题的 pytest 特性以及其他一般策略。

    .. tab:: 英文

        A "flaky" test is one that exhibits intermittent or sporadic failure, that seems to have non-deterministic behaviour. Sometimes it passes, sometimes it fails, and it's not clear why. This page discusses pytest features that can help and other general strategies for identifying, fixing or mitigating them.

不稳定的测试为何是个问题
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Why flaky tests are a problem**

.. tabs::

    .. tab:: 中文

        间歇性测试在使用持续集成（CI）服务器时尤其麻烦，因为在合并新的代码更改之前，所有测试都必须通过。如果测试结果不是可靠的信号——即测试失败并不意味着代码更改导致了测试失败——开发人员可能会对测试结果产生不信任，从而忽视真正的失败。此外，这也会浪费时间，因为开发人员必须重新运行测试套件并调查虚假的失败。

    .. tab:: 英文

        Flaky tests are particularly troublesome when a continuous integration (CI) server is being used, so that all tests must pass before a new code change can be merged. If the test result is not a reliable signal -- that a test failure means the code change broke the test -- developers can become mistrustful of the test results, which can lead to overlooking genuine failures. It is also a source of wasted time as developers must re-run test suites and investigate spurious failures.


潜在根本原因
^^^^^^^^^^^^^^^^^^^^^

**Potential root causes**

系统状态
~~~~~~~~~~~~

**System state**

.. tabs::

    .. tab:: 中文

        广义而言，间歇性测试表明该测试依赖于一些未得到适当控制的系统状态——测试环境没有得到充分隔离。高级别的测试更容易出现间歇性，因为它们依赖于更多的状态。

        间歇性测试有时会在测试套件并行运行时出现（例如使用 `pytest-xdist`）。这可能表明某个测试依赖于测试顺序。

        - 可能是另一个测试未能正确清理自身，留下数据，从而导致间歇性测试失败。
        - 间歇性测试依赖于一个未清理自身的数据，而在并行运行中，之前的测试并不总是存在。
        - 修改全局状态的测试通常无法并行运行。

    .. tab:: 英文

        Broadly speaking, a flaky test indicates that the test relies on some system state that is not being appropriately controlled - the test environment is not sufficiently isolated. Higher level tests are more likely to be flaky as they rely on more state.

        Flaky tests sometimes appear when a test suite is run in parallel (such as use of `pytest-xdist`_). This can indicate a test is reliant on test ordering.

        -  Perhaps a different test is failing to clean up after itself and leaving behind data which causes the flaky test to fail.
        - The flaky test is reliant on data from a previous test that doesn't clean up after itself, and in parallel runs that previous test is not always present
        - Tests that modify global state typically cannot be run in parallel.


过于严格的断言
~~~~~~~~~~~~~~~~~~~~~~~

**Overly strict assertion**

.. tabs::

    .. tab:: 中文

        过于严格的断言可能会导致浮点数比较和时间问题。此时， :func:`pytest.approx` 非常有用。

    .. tab:: 英文

        Overly strict assertions can cause problems with floating point comparison as well as timing issues. :func:`pytest.approx` is useful here.

线程安全
~~~~~~~~~~~~~

**Thread safety**

.. tabs::

    .. tab:: 中文

        pytest 是单线程的，始终在同一线程中顺序执行测试，从不自行生成任何线程。

        即使在运行并行测试的插件情况下，例如 `pytest-xdist`，通常也是通过生成多个 *进程* 来批量运行测试，而不是使用多个线程。

        当然，测试和夹具可以在其测试工作流程中自行生成线程（例如，一个在后台启动服务器线程的夹具，或一个执行生成代码并生成线程的测试），但需要注意以下几点：

        * 确保最终等待任何生成的线程——例如在测试结束时或夹具的拆卸过程中。
        * 避免从多个线程使用 pytest 提供的原语（如 `pytest.warns`、`pytest.raises` 等），因为它们不是线程安全的。

        如果您的测试套件使用线程并且您看到不稳定的测试结果，请不要排除测试隐式使用 pytest 本身的全局状态的可能性。

    .. tab:: 英文

        pytest is single-threaded, executing its tests always in the same thread, sequentially, never spawning any threads itself.

        Even in case of plugins which run tests in parallel, for example `pytest-xdist`_, usually work by spawning multiple *processes* and running tests in batches, without using multiple threads.

        It is of course possible (and common) for tests and fixtures to spawn threads themselves as part of their testing workflow (for example, a fixture that starts a server thread in the background, or a test which executes production code that spawns threads), but some care must be taken:

        * Make sure to eventually wait on any spawned threads -- for example at the end of a test, or during the teardown of a fixture.
        * Avoid using primitives provided by pytest (:func:`pytest.warns`, :func:`pytest.raises`, etc) from multiple threads, as they are not thread-safe.

        If your test suite uses threads and your are seeing flaky test results, do not discount the possibility that the test is implicitly using global state in pytest itself.

相关功能
^^^^^^^^^^^^^^^^

**Related features**

Xfail strict
~~~~~~~~~~~~

**Xfail strict**

.. tabs::

    .. tab:: 中文

        :ref:`pytest.mark.xfail ref` 配合 ``strict=False`` 可以标记一个测试，使其失败不会导致整个构建失败。这可以视为一种手动隔离，长期使用是相当危险的。

    .. tab:: 英文

        :ref:`pytest.mark.xfail ref` with ``strict=False`` can be used to mark a test so that its failure does not cause the whole build to break. This could be considered like a manual quarantine, and is rather dangerous to use permanently.


PYTEST_CURRENT_TEST
~~~~~~~~~~~~~~~~~~~

.. tabs::

    .. tab:: 中文

        :envvar:`PYTEST_CURRENT_TEST` 可能对找出“哪个测试卡住了”很有帮助。有关更多详细信息，请参阅 :ref:`pytest当前测试环境 <pytest current test env>`。

    .. tab:: 英文

        :envvar:`PYTEST_CURRENT_TEST` may be useful for figuring out "which test got stuck".
        See :ref:`pytest current test env` for more details.


插件
~~~~~~~

**Plugins**

.. tabs::

    .. tab:: 中文

        重新运行任何失败的测试可以通过给予它们额外的通过机会来缓解不稳定测试的负面影响，从而使整体构建不失败。多个 pytest 插件支持此功能：

        * `pytest-rerunfailures <https://github.com/pytest-dev/pytest-rerunfailures>`_
        * `pytest-replay <https://github.com/ESSS/pytest-replay>`_: 此插件有助于在本地重现 CI 运行期间观察到的崩溃或不稳定测试。
        * `pytest-flakefinder <https://github.com/dropbox/pytest-flakefinder>`_ - `blog post <https://blogs.dropbox.com/tech/2016/03/open-sourcing-pytest-tools/>`_

        故意随机化测试的插件可以帮助暴露存在状态问题的测试：

    .. tab:: 英文

        Rerunning any failed tests can mitigate the negative effects of flaky tests by giving them additional chances to pass, so that the overall build does not fail. Several pytest plugins support this:

        * `pytest-rerunfailures <https://github.com/pytest-dev/pytest-rerunfailures>`_
        * `pytest-replay <https://github.com/ESSS/pytest-replay>`_: This plugin helps to reproduce locally crashes or flaky tests observed during CI runs.
        * `pytest-flakefinder <https://github.com/dropbox/pytest-flakefinder>`_ - `blog post <https://blogs.dropbox.com/tech/2016/03/open-sourcing-pytest-tools/>`_

        Plugins to deliberately randomize tests can help expose tests with state problems:

* `pytest-random-order <https://github.com/jbasko/pytest-random-order>`_
* `pytest-randomly <https://github.com/pytest-dev/pytest-randomly>`_


其他一般策略
^^^^^^^^^^^^^^^^^^^^^^^^

**Other general strategies**

拆分测试套件
~~~~~~~~~~~~~~~~~~~~

**Split up test suites**

.. tabs::

    .. tab:: 中文

        将单个测试套件拆分为两个（例如单元测试与集成测试）是常见的做法，并仅将单元测试套件用作 CI 门。这也有助于保持构建时间的可控，因为高层次的测试往往更慢。然而，这意味着可能会合并导致构建失败的代码，因此需要额外关注集成测试结果的监控。

    .. tab:: 英文

        It can be common to split a single test suite into two, such as unit vs integration, and only use the unit test suite as a CI gate. This also helps keep build times manageable as high level tests tend to be slower. However, it means it does become possible for code that breaks the build to be merged, so extra vigilance is needed for monitoring the integration test results.


失败时的视频/屏幕截图
~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Video/screenshot on failure**

.. tabs::

    .. tab:: 中文

        对于 UI 测试，这些信息对于了解测试失败时 UI 的状态非常重要。可以将 `pytest-splinter` 与像 `pytest-bdd` 这样的插件一起使用，并且可以在测试失败时 `自动保存截图 <https://pytest-splinter.readthedocs.io/en/latest/#automatic-screenshots-on-test-failure>`_，这有助于定位故障原因。

    .. tab:: 英文

        For UI tests these are important for understanding what the state of the UI was when the test failed. pytest-splinter can be used with plugins like pytest-bdd and can `save a screenshot on test failure <https://pytest-splinter.readthedocs.io/en/latest/#automatic-screenshots-on-test-failure>`_, which can help to isolate the cause.


删除或重写测试
~~~~~~~~~~~~~~~~~~~~~~~~~~

**Delete or rewrite the test**

.. tabs::

    .. tab:: 中文

        如果功能已经被其他测试覆盖，那么可以考虑移除该测试。如果没有覆盖，那么可以尝试在更低的层级重写测试，以消除不稳定性或使其源头更加明显。

    .. tab:: 英文

        If the functionality is covered by other tests, perhaps the test can be removed. If not, perhaps it can be rewritten at a lower level which will remove the flakiness or make its source more apparent.


隔离
~~~~~~~~~~

**Quarantine**

.. tabs::

    .. tab:: 中文

        Mark Lapierre 在2018年的一篇文章中讨论了 `隔离测试的优缺点 <https://dev.to/mlapierre/pros-and-cons-of-quarantined-tests-2emj>`_。

    .. tab:: 英文

        Mark Lapierre discusses the `Pros and Cons of Quarantined Tests <https://dev.to/mlapierre/pros-and-cons-of-quarantined-tests-2emj>`_ in a post from 2018.



失败时重新运行的 CI 工具
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**CI tools that rerun on failure**

.. tabs::

    .. tab:: 中文

        Azure Pipelines（Azure云CI/CD工具，前身为Visual Studio Team Services或VSTS）具有 `识别不稳定测试 <https://docs.microsoft.com/en-us/previous-versions/azure/devops/2017/dec-11-vsts?view=tfs-2017#identify-flaky-tests>`_ 和重新运行失败测试的功能。

    .. tab:: 英文

        Azure Pipelines (the Azure cloud CI/CD tool, formerly Visual Studio Team Services or VSTS) has a feature to `identify flaky tests <https://docs.microsoft.com/en-us/previous-versions/azure/devops/2017/dec-11-vsts?view=tfs-2017#identify-flaky-tests>`_ and rerun failed tests.



研究
^^^^^^^^

**Research**

.. tabs::

    .. tab:: 中文

        这是一个有限的列表，请提交问题或拉取请求以扩展它！

        * Gao, Zebao, Yalan Liang, Myra B. Cohen, Atif M. Memon, and Zhen Wang. "Making system user interactive tests repeatable: When and what should we control?." 在 *软件工程 (ICSE), 2015 IEEE/ACM 第37届IEEE国际会议*, 第1卷, 第55-65页。IEEE, 2015.  `PDF <http://www.cs.umd.edu/~atif/pubs/gao-icse15.pdf>`__
        * Palomba, Fabio, and Andy Zaidman. "Does refactoring of test smells induce fixing flaky tests?." 在 *软件维护与演变 (ICSME), 2017 IEEE国际会议*, 第1-12页。IEEE, 2017. `PDF in Google Drive <https://drive.google.com/file/d/10HdcCQiuQVgW3yYUJD-TSTq1NbYEprl0/view>`__
        * Bell, Jonathan, Owolabi Legunsen, Michael Hilton, Lamyaa Eloussi, Tifany Yung, and Darko Marinov. "DeFlaker: Automatically detecting flaky tests." 在 *2018年国际软件工程会议论文集*. 2018. `PDF <https://www.jonbell.net/icse18-deflaker.pdf>`__
        * Dutta, Saikat and Shi, August and Choudhary, Rutvik and Zhang, Zhekun and Jain, Aryaman and Misailovic, Sasa. "Detecting flaky tests in probabilistic and machine learning applications." 在 *第29届ACM SIGSOFT国际软件测试与分析研讨会 (ISSTA)*, 第211-224页。ACM, 2020. `PDF <https://www.cs.cornell.edu/~saikatd/papers/flash-issta20.pdf>`__

    .. tab:: 英文

        This is a limited list, please submit an issue or pull request to expand it!

        * Gao, Zebao, Yalan Liang, Myra B. Cohen, Atif M. Memon, and Zhen Wang. "Making system user interactive tests repeatable: When and what should we control?." In *Software Engineering (ICSE), 2015 IEEE/ACM 37th IEEE International Conference on*, vol. 1, pp. 55-65. IEEE, 2015.  `PDF <http://www.cs.umd.edu/~atif/pubs/gao-icse15.pdf>`__
        * Palomba, Fabio, and Andy Zaidman. "Does refactoring of test smells induce fixing flaky tests?." In *Software Maintenance and Evolution (ICSME), 2017 IEEE International Conference on*, pp. 1-12. IEEE, 2017. `PDF in Google Drive <https://drive.google.com/file/d/10HdcCQiuQVgW3yYUJD-TSTq1NbYEprl0/view>`__
        *  Bell, Jonathan, Owolabi Legunsen, Michael Hilton, Lamyaa Eloussi, Tifany Yung, and Darko Marinov. "DeFlaker: Automatically detecting flaky tests." In *Proceedings of the 2018 International Conference on Software Engineering*. 2018. `PDF <https://www.jonbell.net/icse18-deflaker.pdf>`__
        *  Dutta, Saikat and Shi, August and Choudhary, Rutvik and Zhang, Zhekun and Jain, Aryaman and Misailovic, Sasa. "Detecting flaky tests in probabilistic and machine learning applications." In *Proceedings of the 29th ACM SIGSOFT International Symposium on Software Testing and Analysis (ISSTA)*, pp. 211-224. ACM, 2020. `PDF <https://www.cs.cornell.edu/~saikatd/papers/flash-issta20.pdf>`__

资源
^^^^^^^^^

**Resources**

.. tabs::

    .. tab:: 中文

        * `Eradicating Non-Determinism in Tests <https://martinfowler.com/articles/nonDeterminism.html>`_ by Martin Fowler, 2011
        * `No more flaky tests on the Go team <https://www.thoughtworks.com/insights/blog/no-more-flaky-tests-go-team>`_ by Pavan Sudarshan, 2012
        * `The Build That Cried Broken: Building Trust in your Continuous Integration Tests <https://www.youtube.com/embed/VotJqV4n8ig>`_ 演讲（视频）by `Angie Jones <https://angiejones.tech/>`_ at SeleniumConf Austin 2017
        * `Test and Code Podcast: Flaky Tests and How to Deal with Them <https://testandcode.com/50>`_ by Brian Okken 和 Anthony Shaw, 2018
        * 微软：

        * `How we approach testing VSTS to enable continuous delivery <https://blogs.msdn.microsoft.com/bharry/2017/06/28/testing-in-a-cloud-delivery-cadence/>`_ by Brian Harry MS, 2017
        * `Eliminating Flaky Tests <https://docs.microsoft.com/en-us/azure/devops/learn/devops-at-microsoft/eliminating-flaky-tests>`_ 博客和演讲（视频）by Munil Shah, 2017

        * 谷歌：

        * `Flaky Tests at Google and How We Mitigate Them <https://testing.googleblog.com/2016/05/flaky-tests-at-google-and-how-we.html>`_ by John Micco, 2016
        * `Where do Google's flaky tests come from? <https://testing.googleblog.com/2017/04/where-do-our-flaky-tests-come-from.html>`_ by Jeff Listfield, 2017

    .. tab:: 英文

        * `Eradicating Non-Determinism in Tests <https://martinfowler.com/articles/nonDeterminism.html>`_ by Martin Fowler, 2011
        * `No more flaky tests on the Go team <https://www.thoughtworks.com/insights/blog/no-more-flaky-tests-go-team>`_ by Pavan Sudarshan, 2012
        * `The Build That Cried Broken: Building Trust in your Continuous Integration Tests <https://www.youtube.com/embed/VotJqV4n8ig>`_ talk (video) by `Angie Jones <https://angiejones.tech/>`_ at SeleniumConf Austin 2017
        * `Test and Code Podcast: Flaky Tests and How to Deal with Them <https://testandcode.com/50>`_ by Brian Okken and Anthony Shaw, 2018
        * Microsoft:

        * `How we approach testing VSTS to enable continuous delivery <https://blogs.msdn.microsoft.com/bharry/2017/06/28/testing-in-a-cloud-delivery-cadence/>`_ by Brian Harry MS, 2017
        * `Eliminating Flaky Tests <https://docs.microsoft.com/en-us/azure/devops/learn/devops-at-microsoft/eliminating-flaky-tests>`_ blog and talk (video) by Munil Shah, 2017

        * Google:

        * `Flaky Tests at Google and How We Mitigate Them <https://testing.googleblog.com/2016/05/flaky-tests-at-google-and-how-we.html>`_ by John Micco, 2016
        * `Where do Google's flaky tests come from? <https://testing.googleblog.com/2017/04/where-do-our-flaky-tests-come-from.html>`_  by Jeff Listfield, 2017


.. _pytest-xdist: https://github.com/pytest-dev/pytest-xdist
