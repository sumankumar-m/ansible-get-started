#  Ansible Fetch Module

## Introduction
------------

In this exercise, you will dive into the usage of the Ansible Fetch module. The Fetch module allows you to retrieve files from remote machines and copy them to the control machine where Ansible is being executed. This is useful when you need to collect specific files or artifacts from your managed hosts.

Let's get started!

* * *

## Fetching a Single File
----------------------

In this step, you will learn how to use the Ansible Fetch module to retrieve a single file from a remote machine. This will help you understand the basic usage and syntax of the module.

First, complete `/home/lab/project/fetching_a_single_file.yml` file.  
Open it in a text editor and add the following content to the playbook file:

```yaml
    - name: Fetch Module Lab
      hosts: localhost
      gather_facts: false
    
      tasks:
        - name: Fetch a single file
          fetch:
            src: /home/lab/example/example_1.txt
            dest: /home/lab/project/example_1.txt
            flat: true
```

*   `fetch`: This is the Ansible module used to fetch files from remote hosts.
*   `src`: This specifies the path of the file on the remote host that needs to be fetched. In this example, it's `/home/lab/example/example_1.txt`.
*   `dest`: This specifies the destination path where the fetched file should be stored on the local host. In this example, it's `/home/lab/project/example_1.txt`.
*   `flat`: This parameter controls the behavior of the destination path. When set to `true`, it ensures that the fetched file is placed in the destination directory without creating any subdirectories. This is useful when fetching a single file and wanting to keep the directory structure simple.

The `fetch` module in Ansible is used to fetch files from remote hosts to the local host. In this configuration, it's used to fetch a file named "example\_1.txt" from the remote host's `/home/lab/example/` directory and store it as "example\_1.txt" in the local host's `/home/lab/project/` directory. The `flat: true` parameter ensures that the fetched file is placed directly in the destination directory without any subdirectories.

Then, run the playbook with the following command:

```bash
    ansible-playbook fetching_a_single_file.yml
```

Example output:

```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [Fetch Module Lab] **************************************************
    
    TASK [Fetch a single file] *****************************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=1 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, verify that the `example_1.txt` file has been fetched locally from the remote host.

```bash
    ll /home/lab/project/example_1.txt
```

Example output:

```
    -rw-rw-r-- 1 lab lab 23 Mar 14 10:45 /home/lab/project/example_1.txt
```

## Fetching Multiple Files
-----------------------

In this step, you will enhance your understanding of the Fetch module by fetching multiple files from a remote machine. This will allow you to collect multiple files in a single task.

First, complete `/home/lab/project/fetching_multiple_files.yml` file.  
Open it in a text editor and add the following content to the playbook file:

```yaml
    - name: Fetch Module Lab
      hosts: localhost
      gather_facts: false
    
      tasks:
        - name: Fetch multiple files
          loop:
            - example_2.txt
            - example_3.txt
          fetch:
            src: "/home/lab/example/{{ item }}"
            dest: "/home/lab/project/{{ item }}"
            flat: true
```

*   `loop`: This keyword is used to iterate over a list of items.
*   `fetch`: This is the Ansible module used to fetch files from remote hosts.
*   `src`: This specifies the source path of the file to be fetched. The `{{ item }}` placeholder is replaced with each item from the loop, resulting in fetching `example_2.txt` and `example_3.txt` from the `/home/lab/example/` directory.
*   `dest`: This specifies the destination path where the fetched files should be stored. Again, the `{{ item }}` placeholder is used to ensure that each file is placed in the corresponding directory within `/home/lab/project/`.
*   `flat`: This parameter controls the behavior of the destination path. When set to `true`, it ensures that the fetched file is placed in the destination directory without creating any subdirectories. This is useful when fetching a single file and wanting to keep the directory structure simple.

In this configuration, it's used to fetch multiple files (`example_2.txt` and `example_3.txt`) from the `/home/lab/example/` directory on the local host and store them in the corresponding directories within `/home/lab/project/`. The `loop` construct allows iterating over a list of items and performing the fetch operation for each item in the list.

Then, run the playbook with the following command:

```bash
    ansible-playbook fetching_multiple_files.yml
```

Example output:

```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [Fetch Module Lab] **************************************************
    
    TASK [Fetch multiple files] ****************************************************
    changed: [localhost] => (item=example_2.txt)
    changed: [localhost] => (item=example_3.txt)
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Finally, verify that `example_2.txt` and `example_3.txt` have been fetched locally from the remote host.

```bash
    ll /home/lab/project/ | grep example
```

Example output:

```
    -rw-rw-r-- 1 lab lab 23 Mar 14 10:45 example_1.txt
    -rw-rw-r-- 1 lab lab 23 Mar 14 11:00 example_2.txt
    -rw-rw-r-- 1 lab lab 23 Mar 14 11:00 example_3.txt
```

## Using Variables in Fetch Module
-------------------------------

In this step, you will explore a more advanced usage of the Ansible Fetch module by incorporating variables. You will fetch files based on dynamic values defined in your playbook.

First, complete `/home/lab/project/using_variablesin_fetch_modules.yml` file.  
Open it in a text editor and add the following content to the playbook file:

```yaml
    - name: Fetch Module Lab
      hosts: localhost
      gather_facts: false
      vars:
        file_path: "/home/lab/example/example_4.txt"
        dest_path: "/home/lab/project/example_4.txt"
    
      tasks:
        - name: Fetch files using variables
          fetch:
            src: "{{ file_path }}"
            dest: "{{ dest_path }}"
            flat: true
```

*   `vars`: This section allows defining variables to be used within the playbook.
*   `fetch`: This is the Ansible module used to fetch files from remote hosts.
*   `src`: This specifies the source path of the file to be fetched. It uses the `file_path` variable to dynamically provide the source file path.
*   `dest`: This specifies the destination path where the fetched file should be stored. It uses the `dest_path` variable to dynamically provide the destination file path.
*   `flat`: This parameter controls the behavior of the destination path. When set to `true`, it ensures that the fetched file is placed in the destination directory without creating any subdirectories. This is useful when fetching a single file and wanting to keep the directory structure simple.

In this configuration, it's used to fetch a file from the source file path (`file_path` variable) on the remote host and store it in the destination file path (`dest_path` variable) on the local Ansible control node. The use of variables allows for dynamic specification of file paths, providing flexibility and reusability in the playbook.

Then, run the playbook with the following command:

```bash
    ansible-playbook using_variablesin_fetch_modules.yml
```

Example output:

```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [Fetch Module Lab] **************************************************
    
    TASK [Fetch files using variables] *********************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=1 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Next, verify that the `example_4.txt` file has been fetched locally from the remote host.

```bash
    ll /home/lab/project/example_4.txt
```

Example output:

```
    -rw-rw-r-- 1 lab lab 23 Mar 14 11:16 /home/lab/project/example_4.txt
```

Finally, try specifying `file_path` and `dest_path` with the `-e` option:

```bash
    ansible-playbook using_variablesin_fetch_modules.yml -e file_path="/tmp/example_5.txt" -e dest_path="/tmp/target/example_5.txt"
```

Example output:

```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [Fetch Module Lab] **************************************************
    
    TASK [Fetch files using variables] *********************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=1 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Verify that the `example_5.txt` file has been fetched locally from the remote host.

```bash
    ll /tmp/target/example_5.txt
```

Example output:
```
    -rw-rw-r-- 1 lab lab 23 Mar 14 11:24 /tmp/target/example_5.txt
```

## Summary
-------

Congratulations on completing the Ansible Fetch Module Lab! In this lab, you learned how to use the Ansible Fetch module to retrieve files from remote machines and copy them to the control machine. Starting with fetching a single file, you progressed to fetching multiple files and incorporating variables for dynamic file retrieval.

By completing this lab, you have gained valuable experience in leveraging the Fetch module to collect specific files or artifacts from your managed hosts. Keep exploring Ansible and its modules to further enhance your automation capabilities.

Happy automating!
