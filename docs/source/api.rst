User API 
========

UI events
..........

.. note::

   Note that currently, we use `uiautomator2 <https://github.com/openatx/uiautomator2>`_ to interact with the app. 
   You can find more information in `uiautomator2 <https://github.com/openatx/uiautomator2>`_.
   You can also use other tools to interact with the app, which can be easily implemented by modifying the `dsl.py`.

For example, to send the click event to the app, you can use the following code:

.. code-block:: Python

   d(resourceId="player_playback_button").click()


``d`` is the object of the uiautomator2.
``resourceId`` sets the resource id of the element.
``click()`` sends the click event to the element.

Here are some common operations:

* click

   .. code-block:: Python

      d(text="OK").click()
  
* long_click

   .. code-block:: Python

      d(text="OK").long_click()

* edit text

   .. code-block:: Python

      d(text="OK").set_text("text")

* rotate device

   .. code-block:: Python

      d.rotate("l") # or left
      d.rotate("r") # or right

* press [key]

   .. code-block:: Python

      d.press("home")
      d.press("back")

We use selector to identify the UI object in the current window.  

Selector 
.........

you can also look at `uiautomator2 Selector <https://github.com/openatx/uiautomator2?tab=readme-ov-file#selector>`_.
Selector is a handy mechanism to identify a specific UI object in the current window.  
Selector supports below parameters.

*  `text`, `textContains`, `textMatches`, `textStartsWith`
*  `className`, `classNameMatches`
*  `description`, `descriptionContains`, `descriptionMatches`, `descriptionStartsWith`
*  `checkable`, `checked`, `clickable`, `longClickable`
*  `scrollable`, `enabled`,`focusable`, `focused`, `selected`
*  `packageName`, `packageNameMatches`
*  `resourceId`, `resourceIdMatches`
*  `index`, `instance`  

Initialize
...........

We use ``@initialize`` to pass the welcome page or the login page of the app.
For example, in OmniNotes, we can use ``@initialize`` to specify a function and wrtite the corresponding UI events to pass the welcome page.

.. code-block:: Python

   @initialize()
   def pass_welcome_pages(self):
      # click next button 5 times
      for _ in range(5):
         d(resourceId="it.feio.android.omninotes.alpha:id/next").click()
      # click done button
      d(resourceId="it.feio.android.omninotes.alpha:id/done").click()


The, after testing started, this function will be executed first to pass the welcome page.

Run multiple properties together
..................................

Suppose we have several properties in different files, we can run them together by specifying multiple files in the command line.

.. code-block:: console

   kea -f [property_file_name1] [property_file_name2] -a [apk_file_name]

Optional arguments
....................

Kea provides the following options. please consult ``kea -h`` for a full list.

``-f``: The test files that contain the properties.

``-a --apk``: The apk file of the app under test.

``-d --device_serial``: The serial number of the device used in the test. (use 'adb devices' to find)

``-o --output``: The output directory of the execution results.

``-p --policy``: The policy name of the exploration. ("random" or "mutate")

``-t --timeout``: The maximum testing time.

``-n``: Every n events, then restart the app.
