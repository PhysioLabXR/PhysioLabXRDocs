.. _contribute:

##############################
How to Contribute
##############################

To get started with PhysioLab\ :sup:`XR` development, you will want to run the app from source. The following section will
guide you through the process of running the app from source.

if you want to contribute to the documentation,
please refer to the :ref:`contribute to the docs <contribute to the docs>` section on this page


.. _run from source:

Run from source
*********************

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
    * Install the required packages by running ``pip install -r requirements.txt``.

4. With packages installed and (optional) the virtual environment activated.
Navigate to the physiolabxr folder. Run PhysioLabXR.py to start the app::

   cd physiolabxr
   python PhysioLabXR.py

Some test cases such as the benchmarking test cases require additional packages. To install these additional packages,
you need to run the following command::

   pip install -r requirements.dev.txt



.. _contribute to the docs:

Contribute to the documentation
********************************


If you would like to add contents to PhysioLabXR's documentation, you can follow these instructions
on how to make changes and compile them:

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
