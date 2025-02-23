#  Ansible File Module


## Introduction
------------

In this exercise, you will explore the Ansible Copy module, which allows you to copy files and directories to remote hosts. The Copy module provides a flexible and efficient way to transfer files as part of your Ansible automation tasks.

* * *

## Copy a File to Remote Host
--------------------------

In this step, you will copy a file from the control machine to a remote host using the Ansible Copy module.

First, create a new Ansible playbook file called `/home/lab/project/copy-module-playbook.yaml` and open it in a text editor.  
Add the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Copy a file to remote host
          copy:
            src: /home/lab/file.txt
            dest: /home/lab/project/file.txt
```

*   `copy`: Utilizes the Ansible `copy` module, which is used to copy files from the control node (local host) to a remote host.
*   `src`: Specifies the source file path, indicating the path of the file to be copied on the local host.
*   `dest`: Specifies the destination file path, indicating where the file should be copied to on the remote host.

Through this playbook task, the file `/home/lab/file.txt` will be copied to the path `/home/lab/project/file.txt` on the remote host. The Ansible `copy` module facilitates the process of copying files efficiently.

Next, create a file called `file.txt` in the `/home/lab` directory.
```bash
    echo "This is the content of the file." > /home/lab/file.txt
```

Then, run the playbook using the following command:
```bash
    ansible-playbook copy-module-playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Copy a file to remote host] **********************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, verify that the file `file.txt` exists in the specified destination path on the remote host.
```bash
    cat /home/lab/project/file.txt
```

Example output:
```
    This is the content of the file.
```

Here `"This is the content of the file."` is the same as the content of the `/home/lab/file.txt` file, which means that the `/home/lab/file.txt` file has been successfully copied to `/home/lab/project/file.txt`.

## Copy a Directory to Remote Host
-------------------------------

In this step, you will copy a directory from the control machine to a remote host using the Ansible Copy module.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:
```yaml
    - hosts: localhost
      tasks:
        - name: Copy a directory to remote host
          copy:
            src: /home/lab/directory
            dest: /home/lab/project/
```

With the step1 type, if `src` is set to directory, this Playbook task copies the `/home/lab/directory` directory to the `/home/lab/project/` path on the remote host.

Next, create a directory called `directory` in `/home/lab` directory and create a file called `file2.txt` in `directory` directory.

```bash
    mkdir /home/lab/directory
    touch /home/lab/directory/file2.txt
```

Then, run the playbook using the following command:

```bash
    ansible-playbook copy-module-playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Copy a directory to remote host] *****************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, verify that the directory `directory` and its contents exist in the specified destination path on the remote host.
```bash
    tree /home/lab/project
```

Expected output:
```
    /home/lab/project
    ├── copy-module-playbook.yaml
    ├── directory
    │   └── file2.txt
    └── file.txt
    
    1 directory, 3 files
```

You can see that the `directory` and the `file2.txt` file have been successfully copied to the `/home/lab/project` directory on the target host.

## Preserve File Attributes
------------------------

In this step, you will learn how to preserve file attributes, such as permissions and timestamps, when using the Ansible Copy module.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:
```yaml
    - hosts: localhost
      tasks:
        - name: Preserve file attributes
          copy:
            src: /home/lab/file.txt
            dest: /tmp/file.txt
            mode: preserve
```

*   `mode: preserve`: This parameter preserves the mode (permissions) of the source file during the copy process, ensuring that the destination file retains the same permissions as the source file.

Through this playbook task, the file `/home/lab/file.txt` will be copied to `/tmp/file.txt` on the remote host, while preserving the file attributes such as permissions.

Then, run the playbook using the following command:

```bash
    ansible-playbook copy-module-playbook.yaml
```
Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Preserve file attributes] ************************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, verify that the file `file.txt` is copied to the specified destination path on the remote host while preserving its attributes.  
Use `ll` to view `/home/lab/file.txt` details:

```bash
    ll /home/lab/file.txt
```

Example output:
```
    -rw-rw-r-- 1 lab lab 33 Mar 9 08:34 /home/lab/file.txt
```

Use `ll` to view `/tmp/file.txt` details:
```bash
    ll /tmp/file.txt
```

Example output:
```
    -rw-rw-r-- 1 lab lab 33 Mar 9 09:00 /tmp/file.txt
```

This Ansible playbook can be used to preserve permission patterns (permission bits) and owner information during the replication process.

## Handle Permissions
------------------

In this step, you will explore how to handle file permissions when using the Ansible Copy module. You will learn how to set specific permissions for the copied file on the remote host.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Handle permissions
          copy:
            src: /home/lab/file.txt
            dest: /tmp/file1.txt
            mode: "0644"
```

*   `mode`: Replace `"0644"` with the desired permission mode for the file. Refer to the [chmod](https://en.wikipedia.org/wiki/Chmod) documentation for more information on permission modes.

Through this playbook task, the file `/home/lab/file.txt` will be copied to `/tmp/file1.txt` on the remote host, with the specified permissions of `0644`.

Then, run the playbook using the following command:

```bash
    ansible-playbook copy-module-playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Handle permissions] ******************************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, verify that the file `file1.txt` is copied to the specified destination path on the remote host with the set permissions.  
Use `ll` to view `/home/lab/file.txt` details:

```bash
    ll /home/lab/file.txt
```
Example output:
```
    -rw-rw-r-- 1 lab lab 33 Mar 9 08:34 /home/lab/file.txt
```

Use `ll` to view `/tmp/file1.txt` details:
```bash
    ll /tmp/file1.txt
```
Example output:

```
    -rw-r--r-- 1 lab lab 33 Mar 9 09:11 /tmp/file1.txt
```

This Ansible playbook can be used to set specific permissions, such as read, write, and execute, for the file on the remote host.

## Idempotent Copy
---------------

In this step, you will make the copy operation idempotent using the Ansible Copy module. You will ensure that the file is only copied if it doesn't exist on the remote host or if the source file has changed.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:
```yaml
    - hosts: localhost
      tasks:
        - name: Idempotent copy
          copy:
            src: /home/lab/file.txt
            dest: /tmp/file2.txt
            remote_src: yes
```

*   `remote_src: yes`: This parameter specifies that the `src` path is on the remote host, allowing Ansible to transfer files from the remote host to another location.

Through this playbook task, the file `/home/lab/file.txt` located on the remote host will be copied to `/tmp/file2.txt` on the remote host only if it doesn't exist or if the source file has changed.

Next, check if the `/tmp/file2.txt` file exists.
```bash
    ll /tmp/file2.txt
```

Example output:
```
    ls: cannot access '/tmp/file2.txt': No such file or directory
```

Then, run the playbook using the following command:
```bash
    ansible-playbook copy-module-playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Idempotent copy] *********************************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, verify that the file `file2.txt` is copied to the specified destination path on the remote host only if it doesn't exist or if the source file has changed.
```bash
    ll /tmp/file2.txt
```

Example output:
```
    -rw-rw-r-- 1 lab lab 33 Mar 9 08:34 /tmp/file2.txt
```

At this point, run the playbook again.
```bash
    ansible-playbook copy-module-playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Idempotent copy] *********************************************************
    ok: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Note that `changed=1` becomes `changed=0`, proving that this Ansible playbook can realize that the file will only be copied if it doesn't exist on the remote host or if the source file has been changed.

## Summary
-------

Congratulations! You have successfully completed the Ansible Copy Module lab. You have learned how to use the Copy module to copy files and directories to remote hosts, preserve file attributes, handle permissions, and ensure idempotence.

The Copy module is a powerful tool for transferring files in your Ansible automation tasks. It provides various parameters to customize the copy operation based on your requirements.

Now that you have a good understanding of the Copy module, you can leverage its capabilities in your Ansible playbooks to efficiently manage file transfers and synchronization across your infrastructure. Happy copying with Ansible!