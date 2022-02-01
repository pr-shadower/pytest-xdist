.. _parallelization:

Running tests across multiple CPUs
==================================

To send tests to multiple CPUs, use the ``-n`` (or ``--numprocesses``) option::

    pytest -n 8

Pass ``-n auto`` to use as many processes as your computer has CPU cores. This
can lead to considerable speed ups, especially if your test suite takes a
noticeable amount of time.

* ``--maxprocesses=maxprocesses``: limit the maximum number of workers to
  process the tests.

* ``--max-worker-restart``: maximum number of workers that can be restarted
  when crashed (set to zero to disable this feature).

The test distribution algorithm is configured with the ``--dist`` command-line option:

.. _distribution modes:

* ``--dist load`` **(default)**: Sends pending tests to any worker that is
  available, without any guaranteed order.

* ``--dist loadscope``: Tests are grouped by **module** for *test functions*
  and by **class** for *test methods*. Groups are distributed to available
  workers as whole units. This guarantees that all tests in a group run in the
  same process. This can be useful if you have expensive module-level or
  class-level fixtures. Grouping by class takes priority over grouping by
  module.

* ``--dist loadfile``: Tests are grouped by their containing file. Groups are
  distributed to available workers as whole units. This guarantees that all
  tests in a file run in the same worker.

* ``--dist loadgroup``: Tests are grouped by the ``xdist_group`` mark. Groups are
  distributed to available workers as whole units. This guarantees that all
  tests with same ``xdist_group`` name run in the same worker.

  .. code-block:: python

      @pytest.mark.xdist_group(name="group1")
      def test1():
          pass

      class TestA:
          @pytest.mark.xdist_group("group1")
          def test2():
              pass

  This will make sure ``test1`` and ``TestA::test2`` will run in the same worker.
  Tests without the ``xdist_group`` mark are distributed normally as in the ``--dist=load`` mode.

* ``--dist no``: The normal pytest execution mode, runs one test at a time (no distribution at all).