###################################
Build PhysioLabXR
###################################

In this guide, you will learn how to build PhysioLab\ :sup:`XR` from source code.
You can build PhysioLabXR on Windows, MacOS, and Linux.



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


#. Use pyinstaller library to build the executable

   There is a known issue with pyinstaller on MacOS working with ``PyQt >= 6.5``. See details about this issue `here <https://github.com/pyinstaller/pyinstaller/issues/7789>`_. To build the app on MacOS, you will need to install pyinstaller from a develop version of pyinstaller. To do so, run the following command in your terminal.

.. raw:: html

   <div class="command-line">
        <pre>
           <code class="language-bash">
               PYINSTALLER_COMPILE_BOOTLOADER=1 pip install git+https://github.com/pyinstaller/pyinstaller.git@develop
           </code>
        </pre>
   </div>


.. raw:: html

   <div class="indented-block">
            <p>
                2. Complete the following command by typing in the input box with
                <strong class="user-input">
                    <input type="text" id="replacement-input" placeholder="Your project root path" oninput="replaceText()">
                </strong>
                . Then run this command in terminal at the root directory of your project.
            </p>
   </div>

   <style>
        .indented-block {
            margin-left: 20px; /* Adjust the value to control the indentation */
        }
   </style>




.. raw:: html

   <div class="command-line">
        <pre>
           <code class="language-bash">
               pyinstaller --noconfirm --onedir --console --add-data "<span class="user-replace-text">ROOT_PATH</span>/rena/scripting:rena/scripting/" --add-data "<span class="user-replace-text">ROOT_PATH</span>/media:media/" --add-data "<span class="user-replace-text">ROOT_PATH</span>/Presets:Presets/" --paths "<span class="user-replace-text">ROOT_PATH</span>/rena/arduino" --paths "<span class="user-replace-text">ROOT_PATH</span>/rena/deprecated" --paths "<span class="user-replace-text">ROOT_PATH</span>/rena/examples" --paths "<span class="user-replace-text">ROOT_PATH</span>/rena/interfaces" --paths "<span class="user-replace-text">ROOT_PATH</span>/rena/scripting" --paths "<span class="user-replace-text">ROOT_PATH</span>/rena/sub_process" --paths "<span class="user-replace-text">ROOT_PATH</span>/rena/threadings" --paths "<span class="user-replace-text">ROOT_PATH</span>/rena/ui" --paths "<span class="user-replace-text">ROOT_PATH</span>/rena/utils" --paths "<span class="user-replace-text">ROOT_PATH</span>/rena" --add-data "<span class="user-replace-text">ROOT_PATH</span>/ui:ui/"  "<span class="user-replace-text">ROOT_PATH</span>/rena/PhysioLabXR.py"
           </code>
        </pre>
   </div>

   <style>
   .user-replace-text{
       background-color: lightgray;
       font-weight: bold;
   }

   .user-input input[type="text"] {
       padding: 6px;
       border: 1px solid #ccc;
       border-radius: 4px;
       font-size: 14px;
   }

   .highlighted-text {
       background-color: yellow;
       font-weight: bold;
   }

    </style>

   <script>
   function replaceText() {
       var replacement = document.getElementById("replacement-input").value;
       var codeBlocks = document.querySelectorAll('.user-replace-text');
        if (replacement == "") {
            replacement = "ROOT_PATH";
        }
       codeBlocks.forEach(function(block) {

           block.textContent = replacement;
       });
   }
   </script>

Once run, a ``build`` and a ``disk`` folder will be generated in the root path. The stand alone app is in the ``disk/PhysioLabXR`` folder.

