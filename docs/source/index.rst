DroidChecker: a property-based testing tool for mobile apps
============================================================

DroidChecker is a testing tool based on the idea of property-based testing for finding functional bugs in Android apps.
Given a set of properties, DroidChecker automatically generates test cases to check the properties. 
When the property is violated, DroidChecker generates a bug report that shows the bug behavior.
Users can use DroidChecker in the following steps:

1. Specify properties for the app under test.
A property is the expected behavior of the app under certain conditions.
Users can specify properties based on the app's specification, documentation, test cases, or bug reports.
Currently, DroidChecker supports writting properties in Python with multiple APIs.


2. Run DroidChecker with the properties and the app under test. 
DroidChecker will automatically generate test cases to explore the app.
When the precondition of a property is satisfied, DroidChecker will execute the interaction scenario of the property and check the postcondition.
If the postcondition is violated, DroidChecker will generate a bug report.

DroidChecker is intended for use by (1) developers or testers who want to test their mobile apps, and
(2) researchers who want to study the effectiveness of property-based testing for mobile apps.

Contents
--------

.. toctree::

   installation
   setup
   example
   tutorial
   api
   bugs