.. _repository-standards:

======================================================
PhysioLabXR Repository Standards v0.1
======================================================

Introduction
------------

This document describes the standards we follow within the codebase. We intentionally keep this document short, and you should go through the entire thing, even if there are parts not directly related to you, because programming is not usually a clean-cut job with defined boundaries.

*A good programmer should know their code by heart; a great programmer, in addition, understands everyone’s needs in a project.*

By standards, we do not mean rigid rules that you must check off a list. Instead, think of them as guidelines on how to approach writing clean, easy-to-maintain, and scalable projects.

This is an evolving list of guidelines, so feel free to comment, propose edits, and, of course, happy coding!
– Leo, Nov. 2023

Standards
---------

**General style:** We strongly adhere to `PEP 8, the python style guide <https://peps.python.org/pep-0008/>`_ (please read!). Pull requests will be reviewed for their adherence to style. We encourage all contributors to be meticulous about style and give style recommendations to your fellow programmers.

Documentation
-------------

In-code documentation (docstrings) should use the `numpy docstring style <https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_numpy.html>`_.

Merging
-------

- Every user feature must be **end-to-end tested**, with its test cases added to ``tests/tests.sh`` before merging.

Versioning
----------

- The versioning of PhysioLabXR follows `semantic versioning rules <https://semver.org/>`_.

Code aesthetics
---------------

*“Any fool can write code that a computer can understand. Good programmers write code that humans can understand.”*
― **Martin Fowler**

- **Naming things:** Follow Python's style standard:
  - Variables should use `snake_case <https://en.wikipedia.org/wiki/Snake_case>`_.
  - Classes should use `CamelCase <https://en.wikipedia.org/wiki/Camel_case>`_.

  Names (e.g., variables, functions) should be human-readable but not too verbose. When in doubt, prioritize readability. Watch this video on `naming styles <https://www.youtube.com/watch?v=-J3wNP6u5YU&pp=ygUOY29kZSBhZXN0aGV0aWM%3D>`_.

- **Don’t optimize until necessary:**
  *“The real problem is that programmers have spent far too much time worrying about efficiency in the wrong places and at the wrong times; premature optimization is the root of all evil (or at least most of it) in programming.”*
  ― **Donald Knuth**

  Micro-optimization is the biggest time sink for early-stage development. Focus more on features and code scalability, and optimize when needed. Macro-optimization should be done with a profiler. This does not mean you shouldn't write efficient code! Watch this video on `premature optimization <https://www.youtube.com/watch?v=tKbV6BpH-C8&pp=ygUOY29kZSBhZXN0aGV0aWM%3D>`_.

- **In-code comments:** Minimize the use of in-code comments. Your code should be readable without relying on comments. If your code is not readable without comments, revise it by making variable names more descriptive or using enums instead of constants. Check out `this video on code aesthetics <https://www.youtube.com/watch?v=Bf7vDBBOBUA&pp=ygUOY29kZSBhZXN0aGV0aWM%3D>`_.

- **Always favor readable code over excessive comments:**
  If you find yourself writing complex one-liners and needing comments to explain them, consider simplifying the code. Use comments primarily when performance reasons make the code less clear.

- **If statements:**
  Use if statements sparingly. Ideally, your if/else clauses should not contain identical code. Aim to transform special cases into normal cases with smart coding practices.

- **Syntax sugar in Python:**
  Python offers many syntactic sugars that help you write more concise and readable code. Learn more about Python syntactic sugar `here <https://melodious-clover-094.notion.site/Python-Syntax-Sugars-0b9ab235f3a84ca6bbac22b4fa0e7ebc?pvs=4>`_.
