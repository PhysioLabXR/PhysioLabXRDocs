
"""""""""""""""""
Example use
"""""""""""""""""

...........
Using RenaLabApp to stream fNIRS data from PsychoPy and NIRx
...........

.. contents:: Overview
   :depth: 3

===================
Introduction
===================
In this example, we use RenaLabApp to stream a classic fNIRS Stroop effect experiment. Stroop effect experiment let participant input their judgment when experiencing color stroop. This will cause an effect in the prefrontal cortex, whereas the hypothesis is that when text and color are not congruent, there will be activation in the prefrontal region.

In each experiment, we want to have six trails. In each trial there are six cases where we ask participants to input the color of the displayed text. The cases can be:

- **Congruent** : the text is congruent with the text color. For example: ``red`` (color in red).

- **Incongruent** : the text is incongruent with the text color. For example:  ``red`` (color in green).

There is a 50% congruent rate.



===================
Generate script using PsychoPy
===================

----------------------
Install PsychoPy
----------------------

Install PsychoPy from the `download page <https://www.psychopy.org/download.html>`_ accoording to the instructions.


----------------------
Create experiment using PyschoPy
----------------------

Setting Trail
--------------------------
In PsychoPy, create an experiment routine by first setting up the trail.

In the ``components`` part at the right side panel, select ``Text`` to create a fixed text to control the time for each trial. This ensures the functionality to have rest time between each trail.

Add another ``Text`` to display the prompt, to make the word change every trail, create a excel that contains all the conditions we want in each trail.

A sample list is this:

+--------+--------+-----------+--------+
| word   | color  | congruent | corAns |
+========+========+===========+========+
| red    | red    | 1         |r       |
+--------+--------+-----------+--------+
| red  | green    | 0         |g       |
+--------+--------+-----------+--------+
| green  | green  | 1         |g       |
+--------+--------+-----------+--------+
| green  | blue   | 0         |b       |
+--------+--------+-----------+--------+
| blue   |blue    | 1         |b       |
+--------+--------+-----------+--------+
| blue   | red    | 0         |r       |
+--------+--------+-----------+--------+


=========
Examples
=========

--------
Comments
--------

.. This is a comment
   Special notes that are not shown but might come out as HTML comments

------
Images
------

Add an image with:

.. image:: screenshots/file.png
   :height: 100
   :width: 200
   :alt: alternate text

You can inline an image or other directive with the |customsub| command.

.. |customsub| image:: image/image.png
              :alt: (missing image text)

-----
Lists
-----

- Bullet are made like this
- Point levels must be consistent
    * Sub-bullets
        + Sub-sub-bullets
- Lists

Term
    Definition for term
Term2
    Definition for term 2

:List of Things:
    item1 - these are 'field lists' not bulleted lists
    item2
    item 3

:Something: single item
:Someitem: single item

-----------------
Preformatted text
-----------------

A code example prefix must always end with double colon like it's presenting something::

    Anything indented is part of the preformatted block
   Until
  It gets back to
 Allll the way left

Now we're out of the preformatted block.

------------
Code blocks
------------

There are three equivalents: ``code``, ``sourcecode``, and ``code-block``.

.. code:: python

   import os
   print(help(os))

.. sourcecode::

  # Equivalent

.. code-block::

  # Equivalent

-----
Links
-----

Web addresses by themselves will auto link, like this: https://www.devdungeon.com

You can also inline custom links: `Google search engine <https://www.google.com>`_

This is a simple link_ to Google with the link defined separately.

.. _link: https://www.google.com

This is a link to the `Python website`_.

.. _Python website: http://www.python.org/

This is a link back to `Section 1`_. You can link based off of the heading name
within a document.

---------
Footnotes
---------

Footnote Reference [1]_

.. [1] This is footnote number one that would go at the bottom of the document.

Or autonumbered [#]

.. [#] This automatically becomes second, based on the 1 already existing.

-----------------
Lines/Transitions
-----------------

Any 4+ repeated characters with blank lines surrounding it becomes an hr line, like this.

====================================

------
Tables
------

+--------+--------+--------+
| Time   | Number | Value  |
+========+========+========+
| 12:00  | 42     | 2      |
+--------+--------+--------+
| 23:00  | 23     | 4      |
+--------+--------+--------+

----------------------
Preserving line breaks
----------------------

Normally you can break the line in the middle of a paragraph and it will
ignore the newline. If you want to preserve the newlines, use the ``|`` prefix
on the lines. For example:

| These lines will
| break exactly
| where we told them to.
