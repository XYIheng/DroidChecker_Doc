How to setup Android SDK Environment
==============
|To take Linux as an example.

1. Install the Android Command Line Tools.
    Go to `Android Developer <https://developer.android.com/studio>`_ and Download the tools that match your operating system.

    .. image:: ../images/android-command-line-tool.png
        :align: center

    |

    |Here we use ``wget`` to download the tool, you can download it directly if you want.

    You can copy the download link copy from the `Android Developer <https://developer.android.com/studio>`_ website mentioned above.
    And then use following commands.

    .. code-block:: bash

        wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip
        mkdir -p Android/cmdline-tools
        unzip commandlinetools-linux-11076708_latest.zip -d Android/cmdline-tools
        mv Android/cmdline-tools/cmdline-tools Android/cmdline-tools/latest

2. Configure the related environment.

    Install Java. (If you have already installed and configured it before, you should
    check if your JDK match the Command Line Tools and then skip the following steps about Java)

    |Here we use ``JDK-17`` to match the latest tool available now.

    .. code-block:: bash

        sudo apt install openjdk-17-jdk

    Open your ``.bashrc`` file.

    .. code-block:: bash

        sudo nano ~/.bashrc

    add the following contents at the end of the file.

    .. code-block:: bash

        export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
        export JRE_HOME=$JAVA_HOME/jre
        export PATH=$PATH:$JAVA_HOME/bin
        export ANDROID_HOME=$HOME/Android
        export PATH="$ANDROID_HOME/emulator:$ANDROID_HOME/tools:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/tools/bin:$ANDROID_HOME/cmdline-tools/latest:$ANDROID_HOME/platform-tools:$PATH"

    |Ensure that the ``PATH`` configuration matches the path where you have stored the relevant tools.

    Finally,  reloads the ``.bashrc`` file, applying changes to the current terminal session immediately.

    .. code-block:: bash

        source ~/.bashrc

3. Verify if ``sdkmanager`` is installed successfully.
    .. code-block:: bash

        sdkmanager --update
        sdkmanager --list
        sdkmanager --licenses

    If you get information similar to the following, the installation is successful.

    .. image:: ../images/sdkmanager-licenses.png
        :align: center

    |

    Common commands for sdkmanager. You can learn from `this link <https://developer.android.com/tools/sdkmanager>`_.

4. Common Issues.
    a. CPU acceleration status: This user doesn't have permissions to use KVM (/dev/kvm)，ERROR: x86 emulation currently requires hardware acceleration!

        .. image:: ../images/issues1.png
            :align: center

        |

        Follow the first solution in the `link <https://stackoverflow.com/questions/37300811/android-studio-dev-kvm-device-permission-denied>`_.
        Then, Just log in again.

        .. code-block:: bash

            sudo adduser $USER kvm
            sudo chown $USER -R /dev/kvm
