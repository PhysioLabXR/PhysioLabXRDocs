###################################
Build PhysioLabXR Executable
###################################

This guide is about how to create executable files from PhysioLab\ :sup:`XR` from the source code.
You can build PhysioLabXR executable for Windows, MacOS, and Linux.

PhysioLab\ :sup:`XR` uses two packages to build the executable: `pyinstaller <https://pyinstaller.org/en/stable/>`_ and
`auto-py-to-exe <https://pypi.org/project/auto-py-to-exe/>`_, where
the later is a GUI for ``pyinstaller`` that make editing the build configuration easier. In either case, complete
build configuration is included in this guide and you can use it to build the executable directly without modifying them.

Should you encounter any issues during the build process. Please visit the `Troubleshooting <https://physiolabxr.readthedocs.io/en/latest/troubleshooting.html>`_ page for help.


General Step
******************

First you need to have a local copy of PhysioLab\ :sup:`XR` source code, and make sure you can run the App from source code.
Follow :ref:`the run from source guide <run from source>` for a step-by-step instruction on how to do it.

Then, follow the platform specific guide to build the executable.


Windows and Ubuntu
***********************************

1. We will use auto-py-to-exe to build the executable. Install it with the command ``pip install auto-py-to-exe``.

2. Open auto-py-to-exe by typing ``auto-py-to-exe`` in the terminal. You will see the following window.

   .. image:: media/auto_py_to_exe.png
      :width: 540px

3. Download the build configuration here:

.. raw:: html

    <form id="myForm">
        <label for="path">Enter your PhysioLab\ :sup:`XR` source code's path (e.g.,C:\PythonProjects\PhysioLabXR) and click the download button:</label>
        <input type="text" id="path" name="path">
        <input type="submit" value="Download for Windows">
        <input type="submit" value="Download for Ubuntu">
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


3. You may change where the final executable is saved under `Settings-> Output Directory`. The default output directory
    is a folder named "output" under source code directory.

   .. image:: media/auto-py-to-exe-output-path.png
      :width: 540px


4. To start the build process, click on the “CONVERT .PY to EXE” button at the bottom of the window.


5. Once the build completes, you can find the executable in the output directory you specified in step 3.


MacOS
***********************************

We will not use auto-py-to-exe to build the executable on MacOS. Instead, we will use ``pyinstaller`` directly, the
following step explains why.

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
               pyinstaller --noconfirm --onedir --console --add-data "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/scripting:physiolabxr/scripting/" --add-data "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/_media:physiolabxr/_media/" --add-data "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/_presets:physiolabxr/_presets/" --paths "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/arduino" --paths "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/deprecated" --paths "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/examples" --paths "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/interfaces" --paths "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/scripting" --paths "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/sub_process" --paths "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/threadings" --paths "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/_ui" --paths "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/utils" --paths "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr" --add-data "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/_ui:physiolabxr/_ui/"  "<span class="user-replace-text">ROOT_PATH</span>/physiolabxr/PhysioLabXR.py"
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

Two folders, "build" and "dist" will be created in the source code directory when the build runs.
Once the build finishes, the Mac executable is in the ``dist/PhysioLabXR`` folder.


How to run the executable
################################

Your root directory structure will look like this after a successful build.

.. image:: media/build_directory_example.png

Go into the target build directory. Click on the circled icon in the following image to run the executable.

The executable is named “main” in this example, but it will have different names if you chose different name options as you were building the executable.

.. image:: media/build_executable_directory_example.png
