How to write properties
========================

In this tutorial, we will learn how to write properties and test them with DroidChecker.

In mobile apps, a property defines the expected behavior of the app. 
Then, if the app violates the property, it means a bug is found.

At high level, a property consists of three key components **<P, I, Q>**, where (1) *P* is a precondition, 
(2) *I* is an interaction scenario which defines how to perform the app functionality, 
and (3) *Q* is a postcondition which defines the expected behavior.

In DroidChecker, a property is defined by applying the ``@rule`` decorator on a function. 

To define the precondition of the property, you can use the ``@precondition`` decorator on the  ``rule``-decorated function.

The postcondition is defined by the ``assert`` statement in the ``rule``-decorated function.

For mobile apps, we can get properties from multiple sources, such as the app's specification, the app's documentation, the app's test cases, the app's bug reports, etc.

Let's start with a simple example on how to get a property, write the property in DroidChecker, and test the property by DroidChecker.

Example 1
---------

This example will show how to get a property from the app `Transistor <https://f-droid.org/packages/org.y20k.transistor/>`_

`Transistor <https://f-droid.org/packages/org.y20k.transistor/>`_ is an app for listening to radio stations over the internet.

Here is a bug report from the app `#239 <https://codeberg.org/y20k/transistor/issues/239>`_, where a user complained that he cannot delete a radio station.

Then, from this bug report, we can get a property:

After deleting a radio station, the radio station should be deleted.

From the bug report, we can get a property as follows:

- **P (Precondition)**: The app has at least one radio station.
- **I (Interaction scenario)**: Delete the radio station.
- **Q (Postcondition)**: The radio station is deleted.

Let's write the property in DroidChecker.

.. code:: Python

    @precondition(
         lambda self: d(resourceId="org.y20k.transistor:id/station_name").exists() and 
         not d(text="Find Station").exists()
    )
    @rule()
    def delete_should_work(self):
        selected_station = random.choice(d(resourceId="org.y20k.transistor:id/station_name"))
        station_name = selected_station.get_text()
        selected_station.swipe("left")
        d(text="Remove").click()
        assert not d(text=station_name).exists(), "delete station still exists"

As we need to add stations to the app before deleting them.
These stations needed to be added manually before testing the property.

To do this, we can use the following code:

.. code:: Python

    @initialize()
    def set_up(self):
        for _ in range(3):
            d(text="Add new station").click()
            station_name_prefix = ["bbc", "new", "swi","chn"]
            selected_station_name_prefix = random.choice(station_name_prefix)
            d(resourceId="org.y20k.transistor:id/search_src_text").set_text(selected_station_name_prefix)
            time.sleep(3)
            random.choice(d(resourceId="org.y20k.transistor:id/station_name")).click()
            d(text="Add").click()

Here, we add three stations to the app before testing the property.
Note that we use the ``@initialize`` decorator to define the setup function.
Then, Droidcheker will execute the setup function before testing the property.

.. note::

    This feature can be used to set up the app's initial state before testing the property. 
    For example, use this feature to pass the login process, add data to the app, etc.
    If you don't need to set up the app's initial state, you can skip it.

