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

.. _ms visual c++:

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


Issue:
*******
When attempting to run PhysioLabXR, this error occurs:
**PyQT6 could not find QT platform plugin "cocoa" in ""**

**Solution:** This issue occurs from time to time on macOS.
It is a problem related to the PyQT6 library. It is not entirely clear why this happens, but it is a known issue.
There's a few online threads on this issue, which we recommend checking out for more information.

- `Locate the libqcocoa.dylib <https://stackoverflow.com/questions/76898551/qt-qpa-plugin-could-not-find-the-qt-platform-plugin-cocoa-in>`_
- `Install opencv-python-headless instead of opencv-python <https://github.com/ageitgey/face_recognition/issues/1041>`_


Issue:
*******
When trying to plot a streaming data, you encounter error regarding "legend, unable to make color from ...", and the
plots is not showing.

**Solution:** This issue is highly likely due to the version of package pyqtgraph. PhysioLabXR
uses a `custom version <https://github.com/PhysioLabXR/pyqtgraph/tree/physio>`_ of the `original pyqtgraph package <https://github.com/pyqtgraph/pyqtgraph>`_.
This version support plotting matrix of data as multiple line charts as opposed to the original version, which only
support plotting one line at a time, adding significant overhead to plotting streams with a large number of channels.

To fix this, you will want to uninstall the original pyqtgraph package and install the custom version.

.. code-block:: bash

    pip uninstall pyqtgraph
    pip install git+https://github.com/physiolabxr/pyqtgraph.git@physio



Issue:
*******
When attempting to execute ```pip install -r requirements.txt``` within the PhysioLabXR-Community directory after activating the virtual environment, such errors occurs:

- **'Error compiling Cython file: src/numpy_formathandler.pyx:22:42: 'Py_intptr_t' is not a type identifier'**
- Additionally, there are multiple warnings about the Cython directive `'language_level'` not being set, which could lead to compatibility issues.
- **ERROR: Failed building wheel for PyOpenGL_accelerate**
- **ERROR: Could not build wheels for PyOpenGL_accelerate, which is required to install pyproject.toml-based projects**


Example Snapshot:

.. image:: media/Troubleshooting_PyOpenGLIssue.png
   :alt: Troubleshooting_PyOpenGLIssue
   :width: 600px
   :height: 600px
   :align: center



**Solution:**
This issue appears to have been fixed in version 3.1.8 of `PyOpenGL-accelerate`, specifically in commit `f897b0e`. Details about this release can be found here: `PyOpenGL Release 3.1.8 <https://github.com/mcfletch/pyopengl/releases/tag/release-3.1.8>`_.

However, this version has not yet been published on PyPI, so it is not available for direct installation via `pip install`.

Likely Workarounds Until the Release on PyPI:

1. **Install from Git Tag**:

   You can install the package directly from the GitHub repository using the release tag `release-3.1.8`:

   ```bash
   pip install git+https://github.com/mcfletch/pyopengl@release-3.1.8
   ```

2. **Install from Tarball**:

   Download the tarball for release 3.1.8 from the release page and install it manually. The tarball can be found in the list of assets on the release page.

   Example command to install from the tarball:

   ```bash
   pip install https://github.com/mcfletch/pyopengl/archive/refs/tags/release-3.1.8.tar.gz
   ```

For further information, refer to the related discussion on GitHub: `Issue #118 <https://github.com/mcfletch/pyopengl/issues/118#issuecomment-2342054510>`_.


