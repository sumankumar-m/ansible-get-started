#  Ansible get_url Module

## Introduction
------------

In this lab, you will explore the Ansible get\_url module, which allows you to download files from the internet and save them on remote hosts. The get\_url module provides various options to customize the download process, including authentication, headers, and checksum verification.

* * *


## Download a File From a URL
--------------------------

In this step, you will use the Ansible get\_url module to download a file from a URL and save it on a remote host.

First, create a new Ansible playbook file called `/home/lab/project/get_url-module-playbook.yaml` and open it in a text editor.  
Add the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Download a file from a URL
          get_url:
            url: "https://releases.ansible.com/ansible/ansible-2.9.25.tar.gz"
            dest: "/tmp/ansible-2.9.25.tar.gz"
```

*   `hosts`: This specifies the target host on which to run the playbook. In this case, the playbook will run on the local host because the target host is `localhost`.
*   `tasks`: This is a list of tasks to run.
*   `name`: This is the descriptive name for the task that identifies the purpose of the task.
*   `get_url`: This is a module in Ansible used to download files from the internet.
*   `url`: This parameter specifies the URL from which to download the file. In this case, it's downloading `Ansible` version 2.9.25 from the official Ansible releases.
*   `dest`: This parameter specifies the destination path on the target host where the downloaded file will be saved. In this case, it's saving the file to `/tmp/ansible-2.9.25.tar.gz`.

In summary, this playbook downloads Ansible version 2.9.25 tarball from the specified URL and saves it to `/tmp/ansible-2.9.25.tar.gz` on the local host.

Then, run the playbook with the following command:

```bash
    ansible-playbook get_url-module-playbook.yaml
```

Observe the output to see if the file was successfully downloaded and saved on the remote host.

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Download a file from a URL] **********************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Verify that the file has been successfully downloaded and saved on the remote host.

```bash
    ll /tmp/ansible-2.9.25.tar.gz
```

Example output:
```
    -rw-rw-r-- 1 lab lab 14M Mar 15 13:03 /tmp/ansible-2.9.25.tar.gz
```

## Customize HTTP Request Headers
------------------------------

In this step, you will use the Ansible get\_url module to customize the HTTP request headers sent during the file download process.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Download a file from a URL with custom headers
          get_url:
            url: "https://releases.ansible.com/ansible/ansible-2.9.25.tar.gz"
            dest: "/tmp/ansible-2.9.25-1.tar.gz"
            headers:
              Custom-Header-1: Value1
              Custom-Header-2: Value2
```

*   `get_url`: This is a module in Ansible used to download files from the internet.
*   `url`: This parameter specifies the URL from which to download the file. In this case, it's downloading `Ansible` version 2.9.25 from the official Ansible releases.
*   `dest`: This parameter specifies the destination path on the target host where the downloaded file will be saved. In this case, it's saving the file to `/tmp/ansible-2.9.25-1.tar.gz`.
*   `headers`: This is an optional parameter that allows you to specify custom headers to be included in the HTTP request sent to the server when downloading the file. In this case, it's set to include two custom headers: `Custom-Header-1` with a value of `Value1`, and `Custom-Header-2` with a value of `Value2`. These headers might be used for authentication, authorization, or other purposes as required by the server hosting the file.

In summary, this playbook will download the Ansible version 2.9.25 tarball from the specified URL with custom headers included in the HTTP request, and save it as `ansible-2.9.25-1.tar.gz` in the `/tmp` directory on the local host.

Then, run the playbook with the following command:

```bash
    ansible-playbook get_url-module-playbook.yaml
```

Observe the output to see if the file was successfully downloaded and saved on the remote host, and check if the custom headers were included in the HTTP request. 

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] **************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Download a file from a URL with custom headers] **************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Verify that the file has been successfully downloaded and saved on the remote host.
```bash
    ll /tmp/ansible-2.9.25-1.tar.gz
```

Example output:
```
    -rw-rw-r-- 1 lab lab 14M Mar 15 13:17 /tmp/ansible-2.9.25-1.tar.gz
```

## Verify File Integrity With Checksum
-----------------------------------

In this step, you will use the Ansible get\_url module to verify the integrity of the downloaded file using a checksum.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Download a file and verify checksum
          get_url:
            url: "https://releases.ansible.com/ansible/ansible-2.9.25.tar.gz"
            dest: "/tmp/ansible-2.9.25-2.tar.gz"
            checksum: "sha256:https://releases.ansible.com/ansible/ansible-2.9.25.tar.gz.sha"
```

*   `get_url`: This is a module in Ansible used to download files from the internet.
*   `url`: This parameter specifies the URL from which to download the file. In this case, it's downloading `Ansible` version 2.9.25 from the official Ansible releases.
*   `dest`: This parameter specifies the destination path on the target host where the downloaded file will be saved. In this case, it's saving the file to `/tmp/ansible-2.9.25-2.tar.gz`.
*   `checksum`: This parameter is used to specify the checksum of the file being downloaded, along with its type. In this case, it's set to `sha256:https://releases.ansible.com/ansible/ansible-2.9.25.tar.gz.sha`, indicating that Ansible should verify the `SHA256` checksum of the downloaded file against the checksum provided at the specified URL. This ensures the integrity of the downloaded file.

In summary, this playbook will download the Ansible version 2.9.25 tarball from the specified URL, save it as `ansible-2.9.25-2.tar.gz` in the `/tmp` directory on the local host, and verify its integrity by comparing its `SHA256` checksum with the checksum provided at the specified URL.

Then, run the playbook with the following command:

```bash
    ansible-playbook get_url-module-playbook.yaml
```

Observe the output to see if the file was successfully downloaded and saved on the remote host, and check if the checksum verification passed.  

Example output:

```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Download a file and verify checksum] *************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Verify that the file has been successfully downloaded and saved on the remote host.

```bash
    ll /tmp/ansible-2.9.25-2.tar.gz
```

Example output:
```
    -rw-rw-r-- 1 lab lab 14M Mar 15 13:31 /tmp/ansible-2.9.25-2.tar.gz
```

## Summary
-------

Congratulations! You have successfully completed the Ansible get\_url module lab. You have learned how to download files from a URL, save them on remote hosts, customize HTTP request headers, and verify file integrity using a checksum.

The get\_url module is a versatile tool that allows you to automate file downloads and handle various aspects of the download process. With this knowledge, you can enhance your Ansible playbooks and efficiently manage file distribution across your infrastructure.

Continue exploring the Ansible documentation and experimenting with different modules to expand your automation capabilities. Happy Ansible-ing!