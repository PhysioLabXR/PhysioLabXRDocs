Engineering WIKI
#####################################################

This is the engineering WIKI of this project,
with tips and cautions when developing this project.

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
returns. Instead, do::

    Class MyGUIWidget(QtWidgets.QWidget):
        ...

        def start_a_thread(self):
            self.thread: QThread = create_a_thread()
            self.thread.start()

By saving the thread object to a permanent attribute, the thread object will not be garbage-collected and lives with
the GUI controller object.

Combobox
^^^^^^^^

For pyqt combobox that are created in .ui files and empty, do NOT define the field “CurrrentIndex” in PyQt .ui files,
Otherwise, you may get this::

    Terminating app due to uncaught exception 'NSRangeException', reason: '*** -[__NSArrayM objectAtIndexedSubscript:]: index 0 beyond bounds for empty array'

, when adding items to this combobox.

This is a known issue with macOS, some times you will get this issue event when you do not define the field “CurrrentIndex”.
When this happens, you can try add a *placeholderText* property to your combobox in the .ui file. Thought this seems to
be irrelevant, it does solve the problem, for some reason.

For example, if adding items to this combobox causes the `NSRangeException`::

  <widget class="QComboBox" name="SomeComboBox"/>

You can change it to::

  <widget class="QComboBox" name="SomeComboBox">
   <property name="placeholderText">
    <string>Some Placeholder Text</string>
   </property>
  </widget>