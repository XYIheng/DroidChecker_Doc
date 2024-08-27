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

This example will show how to get a property from the app `OmniNotes <https://github.com/federicoiosue/Omni-Notes/>`_

`OmniNotes <https://github.com/federicoiosue/Omni-Notes/>`_ is an app for taking and managing notes.

Here is a bug report from the app `#634 <https://github.com/federicoiosue/Omni-Notes/issues/634>`_, where a user complained that when he removed a tag, it removed other tags that sharing the same prefix.

Then, from this bug report, we can get a property:

After removing the tag, the tag should be successfully removed and the note content should remain unchanged.

From the bug report, we can get a property as follows:

- **P (Precondition)**: The tag exists.
- **I (Interaction scenario)**: Remove the note tag from the tag list.
- **Q (Postcondition)**: The tag is removed and the note content remains unchanged.

Let's write the property in DroidChecker.

.. code-block:: RST
  :linenos:

  .. code-block:: javascript
    :linenos:

    code . . .


.. code-block:: Python
  :linenos:

    @precondition(lambda self: d(resourceId="it.feio.android.omninotes:id/menu_tag").exists() and
                   "#" in d(resourceId="it.feio.android.omninotes:id/detail_content").info["text"]
                   )
    @rule()
    def rule_remove_tag_from_note_shouldnot_affect_content(self):
        
        origin_content = d(resourceId="it.feio.android.omninotes:id/detail_content").info["text"]
        d(resourceId="it.feio.android.omninotes:id/menu_tag").click()
        selected_tag = random.choice(d(className="android.widget.CheckBox",checked=True))
        select_tag_name = "#"+ selected_tag.right(resourceId="it.feio.android.omninotes:id/md_title").info["text"].split(" ")[0]
        selected_tag.click()
        d(text="OK").click()
        new_content = d(resourceId="it.feio.android.omninotes:id/detail_content").info["text"].strip().replace("Content", "")
        origin_content_exlude_tag = origin_content.replace(select_tag_name, "").strip()

        assert not d(textContains=select_tag_name).exists()           
        assert new_content == origin_content_exlude_tag

The ``@precondition`` decorator defines when the property should be tested.
Here, ``d(resourceId="it.feio.android.omninotes:id/menu_tag").exists()`` checks if the tag button exists and 
``"#" in d(resourceId="it.feio.android.omninotes:id/detail_content").info["text"]`` checks if the note content contains a tag. 


The ``@rule`` decorator defines the property.
Here, the interaction scenario is to remove a tag.

The postcondition is defined by the ``assert`` statement.
Here, we check if the tag is removed and content remains unchanged.

That's it! This is a property that should be held by the app `Transistor <https://f-droid.org/packages/org.y20k.transistor/>`_.

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
    For example, use this feature to pass the login, add data to the app, etc.
    If you don't need to set up the app's initial state, you can skip it.

Here, we have already learned how to write a property in DroidChecker.

To test this property, we need to put the property in a class, which inherits from the ``AndroidCheck`` class.

.. code:: Python
    
    import random
    from droidchecker.main import *

    class Test(AndroidCheck):

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

Here, we put the property in the ``Test`` class, which inherits from the ``AndroidCheck`` class.

We put this file transistor_239.py in the ``example`` directory
Then, you can test the property by running the following command:

.. code:: console

    droidchecker -f transistor_239.py -a transistor.apk -t 300

That's it! You have learned how to write a property and test it with DroidChecker.

When we test this property, we quickly find a new bug that violates this property.

