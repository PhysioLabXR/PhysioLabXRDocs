.. _contribute:

##############################
How to Contribute
##############################

We’re excited that you’re interested in contributing to PhysioLab\ :sup:`XR`!
Whether you want to add new features, fix bugs, improve performance, or enhance the documentation,
your contributions are welcome and valuable. This guide will walk you through the process of getting
started with development, running the app from source, making changes, and submitting a pull request (PR).

To get started, you’ll first want to set up the project by running it from source. The next section will guide you through the process of running the app from source.
Once you’ve made your changes, you can follow the steps outlined below to submit your contribution as a PR.

If you would like to contribute to the documentation,
please refer to the :ref:`contribute to the docs <contribute to the docs>`
section on this page for specific instructions on how to update the docs.

We look forward to seeing your contributions and helping to improve **PhysioLab\ :sup:`XR`** together!

.. _run from source:

Run from source
*********************

To get started with PhysioLab\ :sup:`XR` development, you will want to run the app from source. The following section will
guide you through the process of running the app from source.

Running PhysioLab\ :sup:`XR` from source is the best way to develop and test the app. Follow these steps to run the app
from source:

1. You need python 3.9, 3.10, or 3.11 installed on your computer. You can download python from `python.org <https://www.python.org/downloads/>`_.

2. Clone the project from `its repo page <https://github.com/physiolabxr/physiolabxr>`_. Unzip it.

3. Install the required packages, we recommend using a virtual environment so that the packages installed for PhysioLab\ :sup:`XR`
don't conflict with other python projects on your computer. Here's how to do it with ``pip``
and `venv <https://docs.python.org/3/library/venv.html>`_.

    * In terminal, cd to the root directory of cloned PhysioLab\ :sup:`XR` and run ``python -m venv venv``
      This will create a virtual environment named ``venv`` in the root directory of PhysioLab\ :sup:`XR`.
    * Note that you don't have to create the venv in the root directory of PhysioLab\ :sup:`XR`, you can create it anywhere
      you want. If you do, you need to change the path in the following steps accordingly.
    * Activate the virtual environment by running ``source venv/bin/activate`` on MacOS/Linux or ``venv\Scripts\activate.bat`` on Windows.
    * Install the required packages by running ``pip install -r requirements.txt``. If you run into troubles installing the packages, please refer to the :ref:`Troubleshooting <troubleshooting>` page.

.. note::

    There is a known issue on installing on Windows. If you encounter the error **'Microsoft Visual C++ 14.0 or greater is required.'**,
    please refer to the :ref:`this troubleshoot topic <ms visual c++>` for a solution.

4. With packages installed (either in your base or in the virtual environment), from inside the cloned directory, run the following command to navigate to the physiolabxr directory:

    .. code-block::

        cd physiolabxr

5. Run the following command to start the app:

    .. code-block::

        python PhysioLabXR.py

.. note::

    Some test cases such as the benchmarking test cases require additional packages. To install these additional packages,
    you need to run the following command::

       pip install -r requirements.dev.txt


Submit a pull request
*********************

If you would like to contribute to PhysioLab\ :sup:`XR`, you can do so by submitting a pull request (PR). Here's how you can do it:

1. **Fork the Repository**:
   Start by forking the official **PhysioLab\ :sup:`XR`** repository from GitHub. This will create a copy of the repository under your GitHub account.

2. **Clone the Forked Repository**:
   Clone your forked repository to your local machine:

   .. code-block::

       git clone https://github.com/<YourGitHubUserName>/physiolabxr

3. **Create a New Branch**:
   Before making any changes, create a new branch from the `main` branch for your feature or bugfix:

   .. code-block::

       git checkout -b <your-feature-branch>

   Make sure to use a descriptive name for your branch.

4. **Make Your Changes**:
   Implement your changes to the codebase, whether it's fixing a bug, adding a feature, or updating the documentation.

5. **Add Test Cases**:
   Add tests in the `tests/` directory to verify your changes. Ensure that your tests cover key functionalities and edge cases.

6. **Commit Your Changes**:
   After making your changes, commit them with a clear and concise commit message:

   .. code-block::

       git add .
       git commit -m "Add feature XYZ or Fix bug ABC"

7. **Push Your Branch**:
   Push your branch to your forked repository:

   .. code-block::

       git push origin <your-feature-branch>

8. **Create a Pull Request**:
   Once you've pushed your branch, go to the original **PhysioLab\ :sup:`XR`** repository on GitHub and open a pull request from your branch. Include a clear description of the changes you've made, along with any relevant details.

   Refer to this GitHub guide on how to create a pull request:
   `Creating a pull request from a fork <https://docs.github.com/en/github/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork>`_.

9. **Wait for Review**:
   Once you have submitted your pull request, the maintainers will review your changes and may request additional modifications or clarifications. Be responsive to feedback and make the necessary changes to ensure your PR can be merged.

10. **Merge**:
   After approval and passing all tests, your pull request will be merged into the main codebase!

Checklist before submitting a pull request:
--------------------------------------------

- [ ] Your code follows the repository standards (see :ref:`Repository Standards <repository-standards>`).
- [ ] Test cases are included, and all tests pass.
- [ ] The PR includes a clear description of your changes.
- [ ] Documentation has been updated, if applicable.
- [ ] No conflicts exist with the main branch.

And that's it! You've successfully created a pull request and contributed to **PhysioLab\ :sup:`XR`**. Thank you for your contribution!


.. _contribute to the docs:

Contribute to the documentation
********************************

If you would like to change PhysioLab\ :sup:`XR`'s documentation, you can follow these instructions
on how to make changes, compile them, and bring them online.:

1.Fork the the `PhysioLabXRDocs repo <https://github.com/PhysioLabXR/PhysioLabXRDocs>`_.

2.Clone the forked repo to your local directory:

   .. code-block::

       git clone https://github.com/<GitHubUserName>/PhysioLabXRDocs

3.Go to <PathToTheClonedRepo>/source, where you can edit existing pages or add new pages. You could include a new
page in "Further Information" in index.rst for easier navigation.

4.Navigate to the root directory of the cloned repo and install the packages need for compiling the webpage

   .. code-block::

        pip install -r requirements.txt

5.In the root directory of the project, compile the html using the command

* MacOS/Linux:

   .. code-block::

        make html
* Windows:

   .. code-block::

        ./make.bat html

6.View your changes by opening <PathToTheClonedRepo>/build/html/index.html.

7.Submit your changes by creating a pull request in GitHub. Read `this entry <https://docs.github.com/en/github/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork>`_
on how to create pull request from fork.


Related topics
----------------

- :ref:`Native Device Stream Plugin <stream using native plugin>`
- :ref:`Repository Standards <repository-standards>`
- :ref:`Code of Conduct <code-of-conduct>`
- :ref:`Engineering Tips and Notes <engineering-tips-and-notes>`
- :ref:`License <license>`
