#  Ansible Stat Module

Introduction
------------

In this lab, you will explore the Ansible Stat module, which allows you to gather information about files and directories on remote hosts. The Stat module provides various attributes and information, such as file size, ownership, permissions, and modification timestamps.

* * *


## Get File Information
--------------------

In this step, you will use the Ansible Stat module to gather information about a file on a remote host.

First, create a new Ansible playbook file called `/home/lab/project/stat-module-playbook.yaml` and open it in a text editor.  
Add the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Get file information
          stat:
            path: /home/lab/project/file.txt
          register: file_info
    
        - name: Print file information
          debug:
            msg: |
              File size: {{ file_info.stat.size }}
              Ownership: {{ file_info.stat.uid }}:{{ file_info.stat.gid }}
              Permissions: {{ file_info.stat.mode }}
```

*   `hosts`: This specifies the target host on which to run the playbook. In this case, the playbook will run on the local host because the target host is `localhost`.
*   `tasks`: This is a list of tasks to run.
*   `name`: This is the descriptive name for the task that identifies the purpose of the task.
*   `stat`: This Ansible module gathers information about a file on the remote host specified by the `path` parameter.
*   `register`: Stores the output of the `stat` module in a variable named `file_info` for later use.
*   `debug`: Prints debugging information during playbook execution.
*   `msg`: Outputs a message with the file information retrieved using the `stat` module, including file size, ownership (UID and GID), and permissions.

In summary, this playbook is designed to retrieve information about a specific file located at `/home/lab/project/file.txt` on the local host and print out various details such as size, ownership, and permissions.

Then, create a file called `file.txt` in the `/home/lab/project` directory.
```bash
    echo "This is the content of the file." > /home/lab/project/file.txt
```

Finally, run the playbook with the following command:
```bash
    ansible-playbook stat-module-playbook.yaml
```

Observe the output to see the file size, ownership, and permissions of the specified file.

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Get file information] ****************************************************
    ok: [localhost]
    
    TASK [Print file information] **************************************************
    ok: [localhost] => {
        "msg": "File size: 33\nOwnership: 5000:5000\nPermissions: 0664\n"
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## Check File Existence
--------------------

In this step, you will use the Ansible Stat module to check the existence of a file on a remote host.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Check file existence on remote host
          stat:
            path: /home/lab/project/file.txt
          register: file_info
    
        - name: Print file existence
          debug:
            msg: "File exists: {{ file_info.stat.exists }}"
```

*   `hosts`: This specifies the target host on which to run the playbook. In this case, the playbook will run on the local host because the target host is `localhost`.
*   `tasks`: This is a list of tasks to run.
*   `name`: This is the descriptive name for the task that identifies the purpose of the task.
*   `stat`: This Ansible module gathers information about a file on the remote host specified by the `path` parameter.
*   `register`: Stores the output of the `stat` module in a variable named `file_info` for later use.
*   `debug`: Prints debugging information during playbook execution.
*   `msg`: Outputs a message indicating whether the file exists or not based on the information retrieved using the `stat` module.

In summary, this playbook is designed to check the existence of a specific file located at `/path/on/remote/host/file.txt` on the local host and print a message indicating whether the file exists or not.

Then, run the playbook with the following command:

```bash
    ansible-playbook stat-module-playbook.yaml
```

Observe the output to see if the file `file.txt` exists on the remote host.

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Check file existence on remote host] *************************************
    ok: [localhost]
    
    TASK [Print file existence] ****************************************************
    ok: [localhost] => {
        "msg": "File exists: True"
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## Retrieve File Modification Timestamp
------------------------------------

In this step, you will use the Ansible Stat module to retrieve the modification timestamp of a file on a remote host.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Get file modification timestamp
          stat:
            path: /home/lab/project/file.txt
          register: file_info
    
        - name: Print file modification timestamp
          debug:
            msg: "File modification timestamp: {{ file_info.stat.mtime }}"
```

*   `hosts`: This specifies the target host on which to run the playbook. In this case, the playbook will run on the local host because the target host is `localhost`.
*   `tasks`: This is a list of tasks to run.
*   `name`: This is the descriptive name for the task that identifies the purpose of the task.
*   `stat`: This Ansible module gathers information about a file on the remote host specified by the `path` parameter.
*   `register`: Stores the output of the `stat` module in a variable named `file_info` for later use.
*   `debug`: Prints debugging information during playbook execution.
*   `msg`: Outputs a message containing the modification timestamp of the file retrieved using the `stat` module.

In summary, this playbook is designed to retrieve the modification timestamp of a specific file located at `/home/lab/project/file.txt` on the local host and print out the timestamp.

Then, run the playbook with the following command:

```bash
    ansible-playbook stat-module-playbook.yaml
```

Observe the output to see the modification timestamp of the file `file.txt` in a human-readable format.

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Get file modification timestamp] *****************************************
    ok: [localhost]
    
    TASK [Print file modification timestamp] ***************************************
    ok: [localhost] => {
        "msg": "File modification timestamp: 1710555624.2304714"
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## Summary
-------

Congratulations! You have successfully completed the Ansible Stat Module lab. You have learned how to gather file information, check file existence, and retrieve the modification timestamp using the Stat module in Ansible.

The Stat module is a powerful tool that enables you to gather various attributes and information about files and directories on remote hosts. You can now leverage this module in your Ansible playbooks to perform advanced file-related operations and make informed decisions based on the gathered information.

Continue exploring the Ansible documentation and experimenting with different modules to enhance your automation skills. Happy Ansible-ing!