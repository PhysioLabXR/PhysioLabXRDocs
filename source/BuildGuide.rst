***********************************
RenaLabApp Build Documentation
***********************************
This is a guide for building an executable for RenaLabApp and using it.


Windows
***********************************

How to build an executable
################################


1. Use auto-py-to-exe library to build the executable

   Install: ``pip install auto-py-to-exe``

   Run: ``auto-py-to-exe``

   Once run, the following looking window will be opened.

   .. image:: media/auto_py_to_exe.png
      :width: 540px

2. Import the following build configuration. You can Specify the root path of your project and download the file here

.. raw:: html

    <form id="myForm">
        <label for="path">Enter Your Project Root Path:</label>
        <input type="text" id="path" name="path">
        <input type="submit" value="Download">
    </form>
    <script>
    document.getElementById('myForm').addEventListener('submit', function(event) {
        event.preventDefault(); // Prevent form submission

        fetch("./_static/build_configuration.json")
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
                replaceValue(jsonData, "<Your/Project/Root/Path>");
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



.. literalinclude:: _static/build_configuration.json

3. Select appropriate output directory and script location, and click on the “Convert .Py to Exe” button.

#. Check if the executable has been created successfully in the desired location.

How to run the executable
################################
Your root directory structure will look like this after a successful build.

.. image:: media/build_directory_example.png

Go into the target build directory. Click on the circled icon in the following image to run the executable.

The executable is named “main” in this example, but it will have different names if you chose different name options as you were building the executable.

.. image:: media/build_executable_directory_example.png

MacOS
***********************************

How to build an executable
################################

1. Use pyinstaller library to build the executable

There is a known issue with pyinstaller on MacOS. See details about this issue `here <https://github.com/pyinstaller/pyinstaller/issues/7789>`_. To build the app on MacOS, you will need to install pyinstaller from a develop version of pyinstaller. To do so, follow the steps below.

   Install: ``PYINSTALLER_COMPILE_BOOTLOADER=1 pip install git+https://github.com/pyinstaller/pyinstaller.git@develop``

2. Run this command in terminal at the root directory:

.. raw:: html

   <div class="command-line">
       <pre>
           <code class="language-bash">
               $ pyinstaller --noconfirm --onedir --console --add-data "Your/project/root/directory/rena/scripting:rena/scripting/" --add-data "Your/project/root/directory/media:media/" --add-data "Your/project/root/directory/Presets:Presets/" --paths "Your/project/root/directory/rena/arduino" --paths "Your/project/root/directory/rena/deprecated" --paths "Your/project/root/directory/rena/examples" --paths "Your/project/root/directory/rena/interfaces" --paths "Your/project/root/directory/rena/scripting" --paths "Your/project/root/directory/rena/sub_process" --paths "Your/project/root/directory/rena/threadings" --paths "Your/project/root/directory/rena/ui" --paths "Your/project/root/directory/rena/utils" --paths "Your/project/root/directory/rena" --add-data "Your/project/root/directory/ui:ui/"  "Your/project/root/directory/rena/PhysioLabXR.py"
           </code>
       </pre>
   </div>

   <div class="user-input">
       <input type="text" id="replacement-input" placeholder="Replace 'abcd'">
       <button onclick="replaceText()">Replace</button>
   </div>

.. raw:: html

   <script>
   function replaceText() {
       var replacement = document.getElementById("replacement-input").value;
       var codeBlocks = document.querySelectorAll('.language-bash');
       codeBlocks.forEach(function(block) {
           block.textContent = block.textContent.replace(/abcd/g, replacement);
       });
   }
   </script>

   Once run, the following looking window will be opened.

   .. image:: media/auto_py_to_exe.png
      :width: 540px