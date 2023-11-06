Developer Notes
#####################################################

This is a collection of notes for developers of this project, with tips and cautions when developing this project.

Working with PyQt
*********************

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
