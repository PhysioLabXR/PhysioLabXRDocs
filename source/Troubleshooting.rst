.. _troubleshooting:

################
Troubleshooting
################

This page covers the common issues that you may encounter while installing and using PhysioLabXR.
If you encounter any other issues not covered here, please feel free to report your
issue `here <https://github.com/PhysioLabXR/PhysioLabXR/issues>`_. You can also message us on our Slack channel,
there will always be someone there to help.


Issue:
*******
**PhysioLabXR doesn't start on Linux. Gives error: qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in "/tmp/_MEIYcBbKq/cv2/qt/plugins" even though it was found.**

**Solution:** This issue is because of the compatibility issue between PyQT and open-cv. To solve this issue, please run the following command in the terminal:

```
apt install libxcb-cursor0
```


Issue:
*******
When attempting to execute ```pip install -r requirements.dev.txt``` within the PycharmProjects/PhysioLabXR directory, such an error occurs:
**'Error: distutils.errors.DistutilsPlatformError: Microsoft Visual C++ 14.0 or greater is required.'**

**Solution:** Please follow the `link <https://visualstudio.microsoft.com/visual-cpp-build-tools/>`_ to download the "vs_Build Tools" and run the installer to complete the installation.
Look for an option similar to "C++ build tools" or "Visual C++ build tools" in the list of available components. Make sure this option is checked. It's usually part of the workload named "Desktop development with C++".

Then open Windows PowerShell and navigate to the directory where PhysioLabXR is located. If the error **'running scripts is disabled on this system'** occurs when attempting to run the command ```./venv/Scripts/activate```,  resolve this issue by following these steps:



Open PowerShell as an Administrator.

Enter the following command:
```Set-ExecutionPolicy -ExecutionPolicy RemoteSigned```.

When prompted with the question, 'Do you want to change the execution policy?', type **'A'** and press **Enter**.

Next, navigate to the directory where PhysioLabXR is located. Input the command: ```./venv/Scripts/activate```. Then, execute: ```pip install -r requirements.dev.txt```.

If encountering the error **'Microsoft Visual C++ 14.0 or greater is required. Get it with "Microsoft C++ Build Tools": https://visualstudio.microsoft.com/visual-cpp-build-tools/'**, refer to this guide on `Stack Overflow <https://stackoverflow.com/questions/64261546/how-to-solve-error-microsoft-visual-c-14-0-or-greater-is-required-when-inst_>`_ or to the solution provided below.

Execute the previously downloaded **vc_buildTools installer**. Click on **"Modify"**. In the **"Individual Components"** tab,
search for **"windows"**, and select **"Windows 11 SDK"** (or "Windows 10 SDK" depending on your system).
Next, search for **"C++ x64/x86 build tools"**, ensure that only **"C++ x64/x86 build tools"** is selected. Click on **"Modify"** and allow vc_build tools to install. Once the installation is successful, it's recommended to reboot your computer. After restarting, the issue should be resolved.





 
