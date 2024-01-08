.. _troubleshooting:

#####################################################
Troubleshooting
#####################################################

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


