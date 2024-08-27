How to write properties
========================

In this tutorial, we will learn how to write properties and test them with DroidChecker.

In mobile apps, a property defines the expected behavior of the app. 
Then, if the app violates the property, it means a bug is found.

A property consists of three key components **<P, I, Q>**, where (1) *P* is a precondition, (2) *I* is an interaction scenario which defines how to perform
the app functionality, and (3) *Q* is a postcondition which defines the expected behavior.