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

#. Import the following build configuration. You can Specify the root path of your project and download the file here

.. raw:: html

    <form id="myForm">
        <label for="path">Enter Your Project Root Path:</label>
        <input type="text" id="path" name="path">
        <input type="submit" value="Download">
    </form>
    <script>
    document.getElementById('myForm').addEventListener('submit', function(event) {
        event.preventDefault(); // Prevent form submission

        fetch("./_downloads/94a77c9ff969934e03cd02435bd42735/build_configuration.json")
            .then(response => response.json())
            .then(jsonData => {

                console.log(jsonData);
                var user_path = document.getElementById('path').value;
                function replaceValue(obj, path) {
                    for (var key in obj) {
                        if (typeof obj[key] === "object") {
                            replaceValue(obj[key], path); // Recursive call for nested objects
                        } else if (typeof obj[key] === "string") {
                            if (obj[key].includes(path))
                                obj[key] = obj[key].replace(path, user_path); // Replace the root path
                        }
                    }
                }
                replaceValue(jsonData, "C:/Users/jpsuh/PycharmProjects/RenaLabApp");
                var modifiedJson = JSON.stringify(jsonData, null, 2);

                // Create a download link for the modified JSON
                var element = document.createElement('a');
                element.setAttribute('href', 'data:application/json;charset=utf-8,' + encodeURIComponent(modifiedJson));
                element.setAttribute('download', 'build_configuration.json');
                element.style.display = 'none';
                document.body.appendChild(element);
                element.click();
                document.body.removeChild(element);
            })
            .catch(error => {
                console.log('Error fetching JSON file:', error);
            });

    });
    </script>



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
