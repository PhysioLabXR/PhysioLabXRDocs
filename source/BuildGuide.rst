*****
RenaLabApp Build Documentation
*****
This is a guide for building an executable for RenaLabApp and using it. 

How to build an executable
########
#. Use auto-py-to-exe library to build the executable

   Install: ``pip install auto-py-to-exe``

   Run: ``auto-py-to-exe``

   Once run, the following looking window will be opened.

   .. image:: media/auto_py_to_exe.png
      :width: 540px

#. Import the following build configuration. You can download the file here :download:`build_configuration.json <build_configuration.json>`

.. raw:: html

   <form id="myForm">
     <label for="path">Enter Path:</label>
     <input type="text" id="path" name="path">
     <input type="submit" value="Submit">
   </form>
   <script>
   document.getElementById('myForm').addEventListener('Download', function(event) {
     event.preventDefault(); // Prevent form submission

     // Get the entered path
     var path = document.getElementById('path').value;

     // Replace the path with JSON template
     var jsonTemplate = { "path": path }; // Replace with your JSON template
     var modifiedJson = JSON.stringify(jsonTemplate);

     // Create a download link for the modified JSON
     var element = document.createElement('a');
     element.setAttribute('href', 'data:application/json;charset=utf-8,' + encodeURIComponent(modifiedJson));
     element.setAttribute('download', 'modified.json');
     element.style.display = 'none';
     document.body.appendChild(element);
     element.click();
     document.body.removeChild(element);
   });
   </script>


.. note::
    you should also replace the Script Location in the auto-py-to-exe GUI or the filenames in the json file to be the main.py path on your machine

.. literalinclude:: build_configuration.json

#. Select appropriate output directory and script location, and click on the “Convert .Py to Exe” button.

#. Check if the executable has been created successfully in the desired location.

How to run the executable
########
Your root directory structure will look like this after a successful build.

.. image:: media/build_directory_example.png

Go into the target build directory. Click on the circled icon in the following image to run the executable. 

The executable is named “main” in this example, but it will have different names if you chose different name options as you were building the executable.

.. image:: media/build_executable_directory_example.png
