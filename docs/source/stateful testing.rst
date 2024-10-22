Stateful Testing
========================

With Kea’s stateful testing, we can use ``Bundle`` to store some data
when we want to search for some data manipulation error. In other words,
Kea tries to generate not just data but entire tests. When we define a set of basic data manipulation actions
that can be combined, and then Kea will attempt to discover sequences of those actions that lead to a failure.

|Here we reference `Hypothesis's Stateful Testing <https://hypothesis.readthedocs.io/en/latest/stateful.html>`_.

In Kea, we can use stateful testing when we write some properties.
We just need to call the class method of ``Kea`` to instantiate the ``Bundle``.
Just like following codes:

.. code:: Python

    _files = Kea.set_bundle("files")
    _notes = Kea.set_bundle("notes")

The Bundle class contains the following functions:

* add(value: str)

Add a new value to the current Bundle object.

.. code-block:: Python

    self._files.add(file_name)

* delete(value: str)

Delete a value from the current Bundle object.

.. code-block:: Python

    self._files.delete(selected_file_name)

* update(value: str, new_value: str)

Update the value from ``value`` to ``new_value``

.. code-block:: Python

    self._files.update(file_name, new_name)

* get_all_data()

This function will return a list of values of current Bundle object.

.. code-block:: Python

    self._files.get_all_data()

* get_random_value(value_len: int = 10)

This function will randomly generate a value and return. So we can call it before using the ``add`` and ``update`` function.

.. code-block:: Python

    file_name = self._files.get_random_value()
    self._files.add(file_name)

* get_random_data()

This function will randomly select a value from the existing values in the current Bundle object.
So we can call it before using the ``delete`` and ``update`` function.

.. code-block:: Python

    file_name = self._files.get_random_data()
    self._files.delete(selected_file_name)

Here is a complete example to show how to use Kea's stateful testing when we define property.
This example will show how to use stateful testing in the app `Amaze <https://github.com/TeamAmaze/AmazeFileManager>`_, it is a file management app,
allows users to manipulate files or folders on Android devices. These properties are defined for testing whether data manipulation of
the file system have some errors. Stateful testing is essential in this situation, we use stateful testing to store all the folders created by Kea,
and can manipulate them along the whole testing process.

.. code-block:: Python

    from kea.main import *

    class Test2(Kea):
        _files = Kea.set_bundle("files")

        @initialize()
        def set_up(self):
            if d(text="Allow").exists():
                d(text="Allow").click()
            if d(text="GRANT").exists():
                d(text="GRANT").click()
            if d(text="ALLOW").exists():
                d(text="ALLOW").click()

        @precondition(lambda self: d(resourceId="com.amaze.filemanager:id/sd_main_fab").exists())
        @rule()
        def create_file_should_exist(self):
            d(resourceId="com.amaze.filemanager:id/pathbar").click()
            d(resourceId="com.amaze.filemanager:id/lin").child(index = 7).click()
            d(description="Navigate up").click()
            d(resourceId="com.amaze.filemanager:id/design_menu_item_text", textContains="Internal Storage").click()
            d(resourceId="com.amaze.filemanager:id/sd_main_fab").click()
            d(resourceId="com.amaze.filemanager:id/sd_label", text="Folder").click()
            file_name = self._files.get_random_value()
            d.send_keys(file_name, clear=True)
            d(resourceId="com.amaze.filemanager:id/md_buttonDefaultPositive").click()
            self._files.add(file_name)
            d(scrollable=True).scroll.to(resourceId="com.amaze.filemanager:id/firstline", text=file_name)
            assert d(text=file_name).exists()

        @precondition(lambda self: d(resourceId="com.amaze.filemanager:id/sd_main_fab").exists() and self._files.get_all_data())
        @rule()
        def del_file_should_disappear(self):
            d(resourceId="com.amaze.filemanager:id/pathbar").click()
            d(resourceId="com.amaze.filemanager:id/lin").child(index=7).click()
            d(description="Navigate up").click()
            d(resourceId="com.amaze.filemanager:id/design_menu_item_text", textContains="Internal Storage").click()
            file_name = self._files.get_random_data()
            d(scrollable=True).scroll.to(resourceId="com.amaze.filemanager:id/firstline", text = file_name)
            selected_file = d(resourceId="com.amaze.filemanager:id/firstline", text = file_name)
            selected_file_name = selected_file.get_text()
            selected_file.right(resourceId="com.amaze.filemanager:id/properties").click()
            d(text="Delete").click()
            d(resourceId="com.amaze.filemanager:id/md_buttonDefaultPositive").click()
            self._files.delete(selected_file_name)
            d(resourceId="com.amaze.filemanager:id/pathbar").click()
            d(resourceId="com.amaze.filemanager:id/lin").child(index=7).click()
            d(scrollable=True).scroll.to(resourceId="com.amaze.filemanager:id/firstline", text=file_name)
            assert not d(text=selected_file_name).exists()

        @precondition(lambda self: d(resourceId="com.amaze.filemanager:id/sd_main_fab").exists() and self._files.get_all_data())
        @rule()
        def change_filename_should_follow(self):
            d(resourceId="com.amaze.filemanager:id/pathbar").click()
            d(resourceId="com.amaze.filemanager:id/lin").child(index=7).click()
            d(description="Navigate up").click()
            d(resourceId="com.amaze.filemanager:id/design_menu_item_text", textContains="Internal Storage").click()
            file_name = self._files.get_random_data()
            new_name = self._files.get_random_value()
            d(scrollable=True).scroll.to(resourceId="com.amaze.filemanager:id/firstline", text=file_name)
            selected_file = d(resourceId="com.amaze.filemanager:id/firstline", text=file_name)
            selected_file.right(resourceId="com.amaze.filemanager:id/properties").click()
            d(text="Rename").click()
            d.send_keys(new_name, clear=True)
            d(resourceId="com.amaze.filemanager:id/md_buttonDefaultPositive").click()
            self._files.update(file_name, new_name)
            d(resourceId="com.amaze.filemanager:id/pathbar").click()
            d(resourceId="com.amaze.filemanager:id/lin").child(index=7).click()
            d(scrollable=True).scroll.to(resourceId="com.amaze.filemanager:id/firstline", text=new_name)
            assert d(text=new_name).exists()
            d(resourceId="com.amaze.filemanager:id/pathbar").click()
            d(resourceId="com.amaze.filemanager:id/lin").child(index=7).click()
            d(scrollable=True).scroll.to(resourceId="com.amaze.filemanager:id/firstline", text=file_name)
            assert not d(text=file_name).exists()

.. note::

    The  above method can use for both single property file and multiple property files.
    If we only want to use stateful testing for single one, we can use ``_files = Bundle("files")`` directly
    to instantiate Bundle instead of using class method of Kea ``_files = Kea.set_bundle("files")``.