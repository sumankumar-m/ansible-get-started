#  Ansible Apt Module

## Introduction
------------

In this exercise, you will explore the Ansible Apt module, which allows you to manage packages on Debian-based systems using the Apt package manager. The Apt module provides a wide range of options to install, update, and remove packages, as well as manage repositories.

* * *


## Install a Package
-----------------

In this step, you will use the Ansible Apt module to install a package on a target system.

First, create a new Ansible playbook file called `/home/lab/project/apt-module-playbook.yaml` and open it in a text editor.  
Add the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Install a package
          become: yes
          apt:
            name: docker-compose
            state: present
```

This is an Ansible playbook for installing a package called `docker-compose` on a local host. The use of privileged access to perform the task is specified via `become: yes`. In the `apt` module, `name` specifies that the package to be installed is named `docker-compose`, and `state: present` ensures that the package is in the installed state.

Then, run the playbook using the following command:

```bash
    ansible-playbook apt-module-playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Install a package] *******************************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, check if `docker-compose` was successfully installed and see the `docker-compose` version.

```bash
    docker-compose --version
```

Example output:

```bash
    docker-compose version 1.29.2, build unknown
```
Successfully install a package on the target system using the Apt module.

## Update a Package
----------------

In this step, you will use the Ansible Apt module to update a package on a target system.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Update a package
          become: yes
          apt:
            name: docker-compose
            state: latest
```

This is an Ansible playbook for updating a package called `docker-compose` on the local host. The use of privileged access to perform the task is specified via `become: yes`. In the `apt` module, `name` specifies that the package to be updated is named `docker-compose` and `state: latest` indicates that the package is to be updated to the latest version.

Then, run the playbook using the following command:

```bash
    ansible-playbook apt-module-playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Update a package] ********************************************************
    ok: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, check if `docker-compose` was successfully updated and view `docker-compose` version.

```bash
    docker-compose --version
```

Example output:

```bash
    docker-compose version 1.29.2, build unknown
```

Successfully update a package on the target system using the Apt module.

## Remove a Package
----------------

In this step, you will use the Ansible Apt module to remove a package from a target system.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Remove a package
          become: yes
          apt:
            name: docker-compose
            state: absent
```

This is an Ansible playbook for removing a package called `docker-compose` on the local host. The `become: yes` specifies that privileged access is used to perform the task. In the `apt` module, `name` specifies that the package to be removed is named `docker-compose`, and `state: absent` ensures that the package is removed in the uninstalled state.

Then, run the playbook using the following command:

```bash
    ansible-playbook apt-module-playbook.yaml
```

Example output:

```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Remove a package] ********************************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, check if `docker-compose` was successfully removed.

```bash
    docker-compose --version
```

Example output:

```
    zsh: command not found: docker-compose
```

Successfully remove a package from the target system using the Apt module.

## Summary
-------

Congratulations! You have successfully completed the Ansible Apt module lab. You have learned how to install, update, and remove packages.

The Apt module is a powerful tool that enables you to automate package management tasks on Debian-based systems. With this knowledge, you can efficiently manage software installations and updates.
