Common Issues
=============

Windows
-------

**Issue 1: Installation Errors**

If you encounter installation errors on Windows, ensure that you have administrative privileges and that your system meets the software's requirements. Double-check that you are using the correct installer for your system architecture (32-bit or 64-bit).

**Issue 2: Compatibility with Antivirus Software**

Certain antivirus programs may interfere with software installations and operations. Try temporarily disabling your antivirus or adding exceptions to prevent conflicts.

**Issue 3: DLL Errors**

DLL (Dynamic Link Library) errors can occur when a required system file is missing or corrupted. You can often resolve this issue by reinstalling or updating the software, or by repairing your Windows installation.

macOS
-----

**Issue 1: Gatekeeper Blocking Installation**

macOS includes a security feature called Gatekeeper that may prevent the installation of software from unidentified developers. To bypass this, right-click the application and select "Open," or adjust your Gatekeeper settings in System Preferences > Security & Privacy.

**Issue 2: Homebrew or Package Manager Conflicts**

If you are using a package manager like Homebrew, conflicts with system libraries or installations can occur. Check for library version conflicts and consult the package manager's documentation for troubleshooting.

**Issue 3: App Permissions**

Some macOS apps require specific permissions to access certain resources (e.g., camera, microphone). If an app isn't working as expected, review and adjust its permissions in System Preferences > Security & Privacy > Privacy.

Linux
-----

**Issue 1: Unable to pip install pyaudio**

install dev version python3 by running ``sudo apt install python3-dev``
then create a vertual environment from terminal at project root path: ``python3 -m venv venv``, this is to ensure the vertual environment can inherit the python-dev package.

**Issue 2: pylsl library is not found**

Manually download pylsl library `here <https://github.com/sccn/liblsl/releases>`_ and copy all lsllib.so files to the pylsl package directory ``venv/lib/pylsl/lib``

If a command is not recognized, it might be missing or not in your system's PATH. Verify that you have the necessary packages and binaries installed and properly configured.

