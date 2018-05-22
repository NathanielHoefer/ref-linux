===============================================================================
SSH
===============================================================================

.. contents:: Table of Contents

SSH Capabilities
****************

- Open up remote shell
- Connection forwarder (Local, Remote, Dynamic)
- Transport protocol
- Force command execution on a per-public key basis

Connection Forward
******************

Remote Port Forwarding
----------------------

Give external access to local client

.. code-block:: bash
    :caption: Syntax:
    :name: Remote Port Forwarding Syntax

    local="<local host>:<local port>"  # or "localhost:<local port>"
    remote="<:<remote port>"  # "*" for all interfaces (default = loopback only)
    ssh -R ${remote}:${local} <remote host>

.. code-block:: none
    :caption: Example:
    :name: Remote Port Fowarding Example

    ssh -R 8042:localhost:8042 10.10.10.10


Local Port Forwarding
---------------------

Access remote resources as though it is local

.. code-block:: bash
    :caption: Syntax:
    :name: Local Port Forwarding Syntax

    local="localhost:<local port>"  # or just "<local port>" (default = localhost)
    remote="<private host>:<private port>"
    ssh -L ${local}:${remote} <remote host>


Dynamic Port Forwarding
-----------------------

Allows configuration for entire machine to route all traffic over ssh connection which can act as a super simple VPN.
- Protection in public connections
- Get around region protected content

.. code-block:: none
    :caption: Syntax:
    :name: Dynamic Port Forwarding Syntax

    ssh -[fN]D <port> <remote host>

X11 Forwarding
--------------

Forwards X11 data to allow for GUI interaction with remote host.

Agent Forwarding
----------------

Forwards private key to a server

.. code-block:: none
    :caption: Syntax:
    :name: Agent Forwarding Syntax

    ssh -A <remote host>


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Run Remote Command
******************

Allows to run a single command to remote host and prints output as though it was executed locally. That means that the output can then be piped.

.. code-block:: none
    :caption: Syntax:
    :name: Remote Command Syntax

    ssh <remote host> -- <command>


Force Remote Commands
---------------------

Prefix ssh public keys with options in ~/.ssh/authorized_keys to execute commands for specific connections. Can be used to limit commands, terminals, etc.

.. code-block:: none
    :caption: Syntax:
    :name: Force Remote Command Syntax

    *~/.ssh/authorized_keys*
    command="<command>" ssh-rsa <public key>


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Support Escape Sequences
************************

Escape sequences are used when in a remote shell to manage the session.

- ``~?`` - Lists the ssh escape sequences
- ``~.`` - Terminates connection, Ex: cat a long binary file
- ``~#`` - Lists forwarded connections


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


SSH Config File
***************

``~/.ssh/config``
    The config file that ssh references when determining hosts and allows for additional configuration. It resolves the host starting from the first listed host, through the last. Think of aliases.

Use Case
    To utilize configurations per host

.. code-block:: none
    :caption: Syntax:
    :name: SSH Config File Syntax

    Host <names space delinated>
        <options>
        <options>
        ...

    Host <other names>
        ...

    Host *
        ...

.. code-block:: none
    :caption: Example:
    :name: SSH Config File Example

    Host github github.example.com
        User git
        ProxyCommand ssh -q bastion-east nc github %p
        IdentityFile ~/.ssh/git.pub

ProxyCommand
------------

Grabs the standard handles (stdin, stdout, etc.) of one host, and directing it through another host to my client.

Use Case
    There is one secure bastion that you can connect to, which then can connect to the desired host.
    Instead of ``ssh`` into the bastion, then ssh'ing from the bastion to the desired host, use ``ProxyCommand`` to make the client think it is directly talking with the desired host.

.. code-block:: none
    :caption: Syntax:
    :name: ProxyCommand Syntax

    *~/.ssh/config*

    Host <names>
        HostName <Name of desired host>
        User <name>
        ProxyCommand ssh -q <bastion host> nc %h %p
    # where %h = HostName, %p = port, %r = remote user name

    *Shell*
    ssh -o ProxyCommand='ssh <bastion host> nc <desired host> <port>'


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Other SSH Tasks
***************

- ControlMaster / ControlPath
- Compression
- Keep alive
- SSHFS (Fuse)

SSH Debugging
*************

- Add option ``-vv`` (very verbose) ``-vvv`` (very very verbose)
- Private key must not be readable by anyone else::

    chmod 600 ~/.ssh/id_?sa

- File ``~/.ssh/authorizede_keys`` must be readable but not writable by others::

    chmod 644 ~/.ssh/authorized_keys

Resources
*********

`The Black Magic of SSH <https://vimeo.com/54505525>`_ by *Bo Jeanes*

