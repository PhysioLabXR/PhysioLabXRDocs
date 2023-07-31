Contribute to this Doc
=================

If you would like to add contents to PhysioLabXR's documentation, you can follow these instructions
on how to make changes and compile them

1.Fork the the `RealityNavigationDocs repo <https://github.com/ApocalyVec/RealityNavigationDocs>`_.
2.Clone the forked repo to your local directory, with command like this:

   .. code-block::

       git clone https://github.com/<GitHubUserName>/RealityNavigationDocs

3.Go to <PathToTheClonedRepo>/source, you can edit existing pages here or add new pages. You could include a new
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

6.View your changes by openning <PathToTheClonedRepo>/build/html/index.html

7.Submit your change by creating a pull request in GitHub. Read `here <https://docs.github.com/en/github/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork>`_
on how to create pull request from fork.
