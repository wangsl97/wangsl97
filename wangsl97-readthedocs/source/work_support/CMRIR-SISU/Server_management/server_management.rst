.. How to configure and manage the server

Server Configuration and maintenance
=====================================

The OS (operating system) of the centre's server is Linux (ubuntu18.04). 
Therefore, the following commands only apply to this server cluster. Here 
I provide general server operation instructions and solutions to common problems.

------------------
Configure new account
------------------

When adding users on the server, the administrator needs to properly configure 
the server in advance according to the situation of the server to avoid causing 
too many users on a single server and reducing work efficiency.

.. note::
    Before creating a new user, please determine the following user's information:

    - **The name of the user:** For example, **new_user**. Make sure that it is different from the existing users' name.
    
    - **User's password:** Set up a password with some complexity. It is recommended to use a `strong password generator <https://www.strongpasswordgenerator.org/>`__ to obtain a random 12 complex password.
    
    - **Remote Access Password:** Password for the remote desktop access via VNC.


Create the new account
------------------

Create a new user as an ``administrator``. You will be asked to Enter the current 
administrator password once, then the new user's password twice, and enter "Enter" to set the default options for other options.

.. code-block:: shell
    :linenos:

    #!/bin/bash
    sudo adduser new_user  # new_user changed by the administrator

VNCserver configuration
------------------

Launch the VNC service for the new account. Sequentially add new port 
to the new account at the end of the file, such ``:20=new_user``.

.. code-block:: shell
    :linenos:

    #!/bin/bash
    cd /etc/tigervnc
    sudo nano vncserver.users

Enable the VNC service for the new account. The port is the same as the port 
created just now.

.. code-block:: shell
    :linenos:

    #!/bin/bash
    sudo systemctl enable vncserver@:20
    sudo systemctl restart vncserver@:20

Set the VNC's password for the new account.

.. code-block:: shell
    :linenos:

    #!/bin/bash
    su new_user
    vncpasswd

Create personal folder
------------------
To avoid crowding the space of ``home``, it is recommended to create a 
personal directory for the user under the dedicated folder, and set the access 
and view permissions. **This operation requires first switching to ``root``.**

.. code-block:: shell
    :linenos:

    #!/bin/bash
    su   # input root's password

    cd /disk1
    mkdir new_user
    chown new_user new_user/

Congratulations! Enjoy the journey on the server.

-----------------------
Delete the expired account
-----------------------

Here's how to delete an expired account. **Note that data cannot be recovered once **
**deleted, so please delete with caution.**

Terminate the expired user's active process
------------------

If the user is currently logged in or has a running process, it should be 
terminated first to avoid data corruption:

.. code-block:: shell
    :linenos:

    #!/bin/bash
    pkill -u "expired_user"  # Terminate all processes of the user


Completely delete user and home directory (Recommended)
----------------------

The home directory usually contains the user's personal files, configuration, 
etc., if there is no special demand, it is recommended to delete them together.

.. code-block:: shell
    :linenos:

    #!/bin/bash
    userdel -r "expired_user"


Delete user's personal directory
----------------------

.. code-block:: shell
    :linenos:

    #!/bin/bash
    su   # input root's password

    cd /disk1
    rm -r expired_user



------------
Handling the situation of lagging in VNC remote desktop
------------

VNC Remote desktops will stall or even freeze due to unstable network connections 
or too many processes running at the same time. At this point, the user needs to 
contact the administrator for assistance. Then, the administrator needs to find 
the vnc port like ``20`` corresponding to the user and run the following command 
to solve the problem.

.. code-block:: shell
    :linenos:

    #!/bin/bash
    sudo systemctl restart vncserver@:20
    sudo systemctl enable vncserver@:20

.. warning::
    It is important to note that after the above command is run, the user's original 
    remote desktop will be stopped and cleared, but the saved data will not be deleted. 
    Please confirm with the user before executing.

