Quick example
==============

If you have downloaded our project and configured the environment, you only need to enter "example/" to execute our sample property with the following command:

```
cd example
droidchecker -f example.py -a omninotes.apk -t 100
```

That's it! 

``-f``: the file path of the property

``-a``: the file path of the apk

``-t``: the timeout of this execution.

You can see the test results ``bug_report.html`` in the "output" directory.

Bug report
............


You can use the browser (Google Chrome, Firefox, etc.) to open ``bug_report.html`` file in the output directory. 

In the bug report, you can see the following information:

1. The screenshots of the execution trace. 
    It shows the UI state of the app during the test, which can help you identify and reproduce the bug. Under each screenshot, you can see the event index and the event type (e.g., click, long click) that executed on the UI state.  
2. The bug list. 
    It shows the bug link of the bug. You can click the link to jump to the first screenshot of the property that caused the bug. 