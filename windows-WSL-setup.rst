.. _windows_WSL_setup:

Windows WSL setup
=================

WSL is Windows Subsystem for Linux.

Installation of WSL
-------------------

The WSL is installed following this guide,
https://docs.microsoft.com/en-us/windows/wsl/install-win10.

In the following, it is assumed that you will use Ubuntu 18.04:
https://www.microsoft.com/ja-jp/p/ubuntu-1804-lts/9n9tngvndl3q?rtc=1&activetab=pivot:overviewtab.

ssh connection to VM
--------------------

You can open your WSL-Ubuntu and ssh-login to the Quantum Mobile VM by

::

   % ssh 127.0.0.1 -p 2222 -l max

where the password is ``moritz``. The IP address can be modified at
``Settings->Newtork->Port Forwarding``

.. image:: virtual-box-WSL-pf.jpg
           :width: 50%
           :align: center

X forwarding
------------

X forwarding via ssh is useful to open graphical tools in Quantum
Mobule to display it on your computer. For this you need to install
VcXsrv. This is downloaded at
https://sourceforge.net/projects/vcxsrv/. After the installation, open
VcXsrv once and just clicking next, next, ..., then to resident and
the icon (X mark) would appear at taskbar notification area.

On WSL-Ubuntu, x11-apps is needed to be installed by

::

   $ sudo apt-get install x11-apps

The ``DISPLAY`` shell environment has to be set to connect to VcXsrv
by

::

   $ export DISPLAY=:0.0

To confirm VcXsrv is working, you can try to open xeyes by

::

   $ xeyes

If succeeded, you will see eyes are watching your cursor.

X forwarding is activated with ``-X`` or ``--Y`` option of ssh::

   $ ssh 127.0.0.1 -p 2222 -l max -X -R 6010:localhost:6000

RemoteForward (``-R``) is necessary to connect ``DISPLAY=:10.0`` in VM
to ``DISPLAY=:0.0`` on WSL-Ubuntu. In VM (not WSL-Ubuntu but Quantum
Mobile), you have to also set the ``DISPLAY`` shell environment::

   max@qmobile$ export DISPLAY=:10.0

Now to confirm X-forwarding working, open xeyes in VM

::

   max@qmobile$ xeyes

To use X-forwarding, ``DISPLAY=:0.0`` on WSL-Ubuntu and
``DISPLAY=:10.0`` on Quantum Mobile VM are necessary
everytime. Instead of setting everytime, these can be written in
``~/.bashrc`` in WSL-Ubuntu

::

   % echo 'export DISPLAY=:0.0' >> ~/.bashrc

and in Quantum Mobile VM

::

   % echo 'export DISPLAY=:10.0' >> ~/.bashrc


ssh login using public and private keys
---------------------------------------

Setting up a pair of public and private ssh keys, you can login to Quantum
Mobile without a passphrase. This pair of keys are generated by
``ssh-keygen`` that is included in macOS. More information is found at
https://aiida-tutorials.readthedocs.io/en/latest/pages/2019_ISSP_Chiba_Japan/sections/setup.html#linux-and-macos.

Save ssh long setting to ``.ssh/config``
----------------------------------------

All above settings are integrated into a ``.ssh/config`` like::

   Host aiidatutorial
      Hostname 127.0.0.1
      Port 2222
      User max
      IdentityFile ~/.ssh/aiida_tutorial
      ForwardX11 yes
      ForwardX11Trusted yes
      LocalForward 8888 localhost:8888
      LocalForward 5000 localhost:5000
      RemoteForward 6010 localhost:6000
      ServerAliveInterval 120

Then you can login by

::

   % ssh aiidatutorial

Here ``aiidatutorial`` is the nickname and can be modified even shorter
name.