.. _contribute:

##############################
How to Contribute
##############################


Run from source
*********************

To run PhysioLab\ :sup:`XR` from source, you need to install python3.8+.
Clone the project from `its repo page <https://github.com/physiolabxr/physiolabxr>`_.

Navigation to the root folder of the downloaded repo, install all the required packages with the following command::

   pip install -r requirements.txt

Run main.py to start the app::

   python physiolabxr/PhysioLabXR.py

Notes for working with PyQt
---------------------------------

Threading
^^^^^^^^^

When creating QThread, you must always not let the QThread to be garbage-collected while the thread is active. One way
of doing this is to save the thread's reference to be a permanent object's attribute, like a GUI controller, like::

    Class MyGUIWidget(QtWidgets.QWidget):
        ...

        def start_a_thread(self):
            thread: QThread = create_a_thread()
            thread.start()

The above code would cause the program to silently crash, because the thread object is garbage-collected after the function
returns. Instead do::

    Class MyGUIWidget(QtWidgets.QWidget):
        ...

        def start_a_thread(self):
            self.thread: QThread = create_a_thread()
            self.thread.start()

, by saving the thread object to a permanent attribute, the thread object will not be garbage-collected and lives with
the GUI controller object.


Contribute to the documentation
********************************


If you would like to add contents to PhysioLabXR's documentation, you can follow these instructions
on how to make changes and compile them:

1.Fork the the `RealityNavigationDocs repo <https://github.com/ApocalyVec/RealityNavigationDocs>`_.
2.Clone the forked repo to your local directory:

   .. code-block::

       git clone https://github.com/<GitHubUserName>/RealityNavigationDocs

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
