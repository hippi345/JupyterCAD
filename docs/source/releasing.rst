.. _releasing:

======================
Releasing JupyterCAD
======================

This project uses `Semantic Versioning <https://semver.org/>`_. The version string
lives in ``python/jupytercad/jupytercad/__init__.py`` (see ``[tool.hatch.version]`` in
the root ``pyproject.toml``).

Version bumping
---------------

The root ``pyproject.toml`` sets ``version-cmd = "python scripts/bump-version.py"``; the
file is ``python/jupytercad/scripts/bump-version.py``. It:

- sets the Python version with ``hatch version`` in ``python/jupytercad/``
- writes the matching version to the root ``package.json`` and bumps workspace packages
  with ``yarn run bump:js:version`` (``lerna version``)

Python pre-releases use ``a``, ``b``, and ``rc`` suffixes (for example ``3.5.0rc0``).
npm uses ``-alpha.``, ``-beta.``, and ``-rc.`` instead.

GitHub Actions workflows
------------------------

Release automation uses
`jupyter_releaser <https://jupyter-releaser.readthedocs.io/en/latest/>`__ via:

- `.github/workflows/prep-release.yml` (**Step 1: Prep Release**)
- `.github/workflows/publish-release.yml` (**Step 2: Publish Release**)

You need admin access on ``jupytercad/JupyterCAD`` to run them.

Step 1: Prep Release
~~~~~~~~~~~~~~~~~~~~

#. Open the `Actions <https://github.com/jupytercad/JupyterCAD/actions>`__ tab.
#. Run **Step 1: Prep Release**.
#. Set **New Version Specifier** to the target version (for example ``3.5.0``). The
   default ``next`` bumps the patch level.

The workflow updates ``CHANGELOG.md``, bumps versions on the target branch, and creates
a **draft** GitHub release. The job log prints the draft release URL.

.. note::

   Use a full version such as ``3.5.0``, not a part name such as ``minor`` or ``patch``.
   Part names can break **Step 2: Publish Release**.

Review the draft release
~~~~~~~~~~~~~~~~~~~~~~~~

#. Open the draft from the job log or the
   `releases <https://github.com/jupytercad/JupyterCAD/releases>`__ page.
#. Edit the release notes if needed (typos, non-user-facing PRs, bot contributors).

.. danger::

   Do not click **Publish release** in the GitHub UI. Publication is Step 2.

Step 2: Publish Release
~~~~~~~~~~~~~~~~~~~~~~~

#. Run **Step 2: Publish Release** in Actions.
#. Paste the draft **release URL** from Step 1 when prompted (optional fields can stay
   empty if the URL is set).
#. Confirm PyPI, npm, and the GitHub release when the workflow finishes.

Step 2 uses the ``release`` environment and trusted publishing for PyPI (see
``publish-release.yml``).

conda-forge
~~~~~~~~~~~

After PyPI updates, conda-forge usually opens a PR on
`jupytercad-feedstock <https://github.com/conda-forge/jupytercad-feedstock>`__. If the
bot is slow, file an issue titled ``@conda-forge-admin, please update version``. Update
``recipe/meta.yaml`` by hand when dependencies change.

Published artifacts
-------------------

**PyPI:** ``jupytercad``, ``jupytercad_app``, ``jupytercad_core``, ``jupytercad_lab``,
``jupytercad_lite``

**npm:** ``@jupytercad/base``, ``@jupytercad/schema``, ``@jupytercad/opencascade``,
``@jupytercad/occ-worker``, ``@jupytercad/jupytercad-core``,
``@jupytercad/jupytercad-lab``, ``@jupytercad/jupytercad-app``

**conda-forge:** `jupytercad-feedstock <https://github.com/conda-forge/jupytercad-feedstock>`__

Wheels and sdists are attached to each
`GitHub release <https://github.com/jupytercad/JupyterCAD/releases>`__.

Manual release
--------------

See ``RELEASE.md`` at the repository root for manual PyPI and npm uploads. Maintainers
normally use ``jupyter_releaser`` instead.

Troubleshooting
---------------

If Step 2 fails after Step 1 used ``minor`` or ``patch`` as the version specifier, re-run
Step 1 with a numeric version (for example ``3.5.0``). Delete the bad draft release first
if one was created.
