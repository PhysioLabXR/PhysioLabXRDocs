


######################
PhyScrip API Reference
######################


Logging in PhyScript
====================

Logging a fatal error with `logging.fatal(msg)` anywhere in the script process, including the user script will cause
the main app to issue a kill signal to the user script process.

Places this naturally occur is when:
1. an exception is raised when `__init__` for the `RenaScript` class is called.
2. an exception is raised when the `compile_rpc` is called.