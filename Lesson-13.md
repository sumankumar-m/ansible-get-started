#  Ansible Command Module


## Introduction
------------

In this exercise, you will explore the Ansible Command module, a powerful tool for executing commands on remote hosts. The Command module allows you to interact with the command line directly from your Ansible playbooks and tasks, providing a versatile way to manage remote systems. Throughout this lab, you will learn how to use the Ansible Command module to execute various commands, work with variables and arguments, and capture command output.

* * *

## Create a Simple Ansible Playbook
--------------------------------

In this step, you will create your first Ansible playbook using the Command module to execute a simple command.

First, navigate to the project directory:
```bash
    cd ~/project
```

Now, create a new file called `simple_command.yml` using a text editor of your choice. For example, you can use the `nano` editor:
```bash
    nano simple_command.yml
```

Add the following content to the file:

```yaml
    ---
    - name: Execute a simple command
      hosts: localhost
      tasks:
        - name: Run 'ls' command
          command: ls -l
```

Let's break down this playbook:

*   The `hosts: localhost` line specifies that the playbook will run on the local machine.
*   The `tasks` section contains a list of tasks to be executed.
*   The `command: ls -l` line uses the Command module to run the `ls -l` command, which lists files and directories in long format.

Save the file and exit the editor (in nano, press Ctrl+X, then Y, then Enter).

Now, run the playbook with the following command:
```bash
    ansible-playbook simple_command.yml
```

You should see output similar to this:

```
    PLAY [Execute a simple command] ************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Run 'ls' command] ********************************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

This output indicates that the playbook ran successfully, executing the `ls -l` command on your local machine.

Use Variables with the Command Module
-------------------------------------

In this step, you will learn how to use variables with the Ansible Command module. Variables allow you to make your playbooks more flexible and reusable.

Create a new file called `variable_command.yml`:
```bash
    nano variable_command.yml
```

Add the following content to the file:

```yaml
    ---
    - name: Use variables with the Command module
      hosts: localhost
      vars:
        file_path: /etc/passwd
        line_count: 5
      tasks:
        - name: Display the last few lines of a file
          command: "tail -n {{ line_count }} {{ file_path }}"
          register: command_output
    
        - name: Show the command output
          debug:
            var: command_output.stdout_lines
```
This playbook introduces several new concepts:

*   The `vars` section defines variables that can be used throughout the playbook.
*   We use `{{ variable_name }}` syntax to reference variables within the command.
*   The `register` keyword saves the output of the command to a variable named `command_output`.
*   The `debug` module is used to display the contents of the `command_output` variable.

Save the file and exit the editor.

Now, run the playbook:

```bash
    ansible-playbook variable_command.yml
```

You should see output similar to this:
```
    PLAY [Use variables with the Command module] ***********************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Display the last few lines of a file] ************************************
    changed: [localhost]
    
    TASK [Show the command output] *************************************************
    ok: [localhost] => {
        "command_output.stdout_lines": [
            "games:x:5:60:games:/usr/games:/usr/sbin/nologin",
            "man:x:6:12:man:/var/cache/man:/usr/sbin/nologin",
            "lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin",
            "mail:x:8:8:mail:/var/mail:/usr/sbin/nologin",
            "news:x:9:9:news:/var/spool/news:/usr/sbin/nologin"
        ]
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

This output shows the last 5 lines of the `/etc/passwd` file, demonstrating how variables can be used with the Command module.

## Capture and Process Command Output
----------------------------------

In this step, you will learn how to capture the output of a command and process it further using Ansible.

Create a new file called `process_output.yml`:

```bash
    nano process_output.yml
```

Add the following content to the file:

```yaml
    ---
    - name: Capture and process command output
      hosts: localhost
      tasks:
        - name: Get disk usage information
          command: df -h
          register: df_output
    
        - name: Display all partitions
          debug:
            msg: "{{ df_output.stdout_lines }}"
    
        - name: Find root partition
          set_fact:
            root_partition: "{{ df_output.stdout_lines | select('match', '\\s+/$') | first | default('') }}"
    
        - name: Display root partition information
          debug:
            msg: "Root partition: {{ root_partition }}"
          when: root_partition != ''
    
        - name: Extract usage percentage
          set_fact:
            root_usage: "{{ root_partition.split()[-2].rstrip('%') | int }}"
          when: root_partition != ''
    
        - name: Display root partition usage
          debug:
            msg: "Root partition is {{ root_usage }}% full"
          when: root_partition != ''
    
        - name: Check if root partition is over 80% full
          fail:
            msg: "Warning: Root partition is over 80% full!"
          when: root_partition != '' and root_usage > 80
    
        - name: Display message if root partition not found
          debug:
            msg: "Root partition (/) not found in df output"
          when: root_partition == ''
```

This playbook is more robust and handles cases where the root partition might not be easily detectable:

*   We display all partitions to see what's available.
*   We use a more flexible pattern to find the root partition.
*   We add checks to handle cases where the root partition isn't found.
*   We use the `default('')` filter to avoid errors when the root partition isn't found.

Save the file and exit the editor.

Now, run the playbook:

```bash
    ansible-playbook process_output.yml
```

You should see output similar to this:

```
    PLAY [Capture and process command output] ************************************************
    
    TASK [Gathering Facts] *******************************************************************
    ok: [localhost]
    
    TASK [Get disk usage information] ********************************************************
    changed: [localhost]
    
    TASK [Display all partitions] ************************************************************
    ok: [localhost] => {
        "msg": [
            "Filesystem      Size  Used Avail Use% Mounted on",
            "overlay          20G  618M   20G   4% /",
            "tmpfs            64M     0   64M   0% /dev",
            "tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup",
            "shm              64M  128K   64M   1% /dev/shm",
            "/dev/vdb        100G   17G   84G  17% /etc/hosts"
        ]
    }
    
    TASK [Find root partition] ***************************************************************
    ok: [localhost]
    
    TASK [Display root partition information] ************************************************
    skipping: [localhost]
    
    TASK [Extract usage percentage] **********************************************************
    skipping: [localhost]
    
    TASK [Display root partition usage] ******************************************************
    skipping: [localhost]
    
    TASK [Check if root partition is over 80% full] ******************************************
    skipping: [localhost]
    
    TASK [Display message if root partition not found] ***************************************
    ok: [localhost] => {
        "msg": "Root partition (/) not found in df output"
    }
    
    PLAY RECAP *******************************************************************************
    localhost                  : ok=7    changed=1    unreachable=0    failed=1    skipped=1    rescued=0    ignored=0
```

This output shows all the partitions, identifies the root partition, and checks its usage. The exact values may differ on your system.

Working with Command Module Options
-----------------------------------

In this step, you will explore some of the options available with the Ansible Command module to control its behavior.

Create a new file called `command_options.yml`:

```bash
    nano command_options.yml
```

Add the following content to the file:

```yaml
    ---
    - name: Explore Command module options
      hosts: localhost
      tasks:
        - name: Run a command with a specific working directory
          command: pwd
          args:
            chdir: /tmp
    
        - name: Run a command with environment variables
          command: echo $MY_VAR
          environment:
            MY_VAR: "Hello from Ansible"
    
        - name: Run a command and ignore errors
          command: ls /nonexistent_directory
          ignore_errors: yes
    
        - name: Run a command with a timeout
          command: sleep 2
          async: 5
          poll: 0
          register: sleep_result
    
        - name: Check sleep command status
          async_status:
            jid: "{{ sleep_result.ansible_job_id }}"
          register: job_result
          until: job_result.finished
          retries: 5
          delay: 1
```

This playbook demonstrates various options of the Command module:

*   `chdir`: Changes the working directory before executing the command.
*   `environment`: Sets environment variables for the command.
*   `ignore_errors`: Continues playbook execution even if the command fails.
*   `async` and `poll`: Runs the command asynchronously with a timeout.

Save the file and exit the editor.

Now, run the playbook:

```bash
    ansible-playbook command_options.yml
```

You should see output similar to this:

```
    PPLAY [Explore Command module options]
    
    TASK [Gathering Facts]
    ok: [localhost]
    
    TASK [Run a command with a specific working directory]
    changed: [localhost]
    
    TASK [Run a command with environment variables]
    changed: [localhost]
    
    TASK [Run a command and ignore errors]
    fatal: [localhost]: FAILED! => {"changed": true, "cmd": ["ls", "/nonexistent_directory"], "delta": "0:00:00.006113", "end": "2024-09-06 09:40:43.373350", "msg": "non-zero return code", "rc": 2, "start": "2024-09-06 09:40:43.367237", "stderr": "ls: cannot access '/nonexistent_directory': No such file or directory", "stderr_lines": ["ls: cannot access '/nonexistent_directory': No such file or directory"], "stdout": "", "stdout_lines": []}
    ...ignoring
    
    TASK [Run a command with a timeout]
    changed: [localhost]
    
    TASK [Check sleep command status]
    FAILED - RETRYING: Check sleep command status (10 retries left).
    FAILED - RETRYING: Check sleep command status (9 retries left).
    FAILED - RETRYING: Check sleep command status (8 retries left).
    FAILED - RETRYING: Check sleep command status (7 retries left).
    FAILED - RETRYING: Check sleep command status (6 retries left).
    FAILED - RETRYING: Check sleep command status (5 retries left).
    FAILED - RETRYING: Check sleep command status (4 retries left).
    FAILED - RETRYING: Check sleep command status (3 retries left).
    FAILED - RETRYING: Check sleep command status (2 retries left).
    FAILED - RETRYING: Check sleep command status (1 retries left).
    fatal: [localhost]: FAILED! => {"ansible_job_id": "5877920468.2517", "attempts": 10, "changed": false, "finished": 0, "started": 1}
    
    PLAY RECAP
```

This output demonstrates the different behaviors of the Command module options we explored.

Using the Command Module in a Docker-friendly Scenario
------------------------------------------------------

In this final step, you will use the Ansible Command module in a more realistic Docker-friendly scenario: checking the status of the SSH service and managing it if necessary.

Create a new file called `check_service_docker.yml`:

```bash
    nano check_service_docker.yml
```

Add the following content to the file:

```yaml
    ---
    - name: Check and manage SSH service in Docker
      hosts: localhost
      become: yes ## This allows Ansible to use sudo
      tasks:
        - name: Check SSH service status
          command: service ssh status
          register: ssh_status
          ignore_errors: yes
    
        - name: Display SSH service status
          debug:
            msg: "SSH service status: {{ ssh_status.stdout }}"
    
        - name: Start SSH service if not running
          command: service ssh start
          when: ssh_status.rc != 0
    
        - name: Verify SSH service is running
          command: service ssh status
          register: ssh_status_after
    
        - name: Display final SSH service status
          debug:
            msg: "SSH service status is now: {{ ssh_status_after.stdout }}"
    
        - name: Check if SSH port is listening
          command: netstat -tuln | grep :22
          register: ssh_port_check
          ignore_errors: yes
    
        - name: Display SSH port status
          debug:
            msg: "SSH port 22 is {{ 'open' if ssh_port_check.rc == 0 else 'closed' }}"
```

This playbook performs the following actions:

1.  Checks the status of the SSH service using the `service` command.
2.  Displays the current status of the service.
3.  Starts the service if it's not running.
4.  Verifies the service status after the potential start.
5.  Displays the final status of the service.
6.  Checks if the SSH port (22) is listening.
7.  Displays the status of the SSH port.

Save the file and exit the editor.

Now, run the playbook with sudo privileges:

```bash
    sudo ansible-playbook check_service_docker.yml
```

You should see output similar to this:
```
    PLAY [Check and manage SSH service in Docker] *****************************************
    
    TASK [Gathering Facts] ****************************************************************
    ok: [localhost]
    
    TASK [Check SSH service status] *******************************************************
    changed: [localhost]
    
    TASK [Display SSH service status] *****************************************************
    ok: [localhost] => {
        "msg": "SSH service status: * sshd is running"
    }
    
    TASK [Start SSH service if not running] ***********************************************
    skipping: [localhost]
    
    TASK [Verify SSH service is running] **************************************************
    changed: [localhost]
    
    TASK [Display final SSH service status] ***********************************************
    ok: [localhost] => {
        "msg": "SSH service status is now: * sshd is running"
    }
    
    TASK [Check if SSH port is listening] *************************************************
    changed: [localhost]
    
    TASK [Display SSH port status] ********************************************************
    ok: [localhost] => {
        "msg": "SSH port 22 is open"
    }
    
    PLAY RECAP ****************************************************************************
    localhost                  : ok=6    changed=3    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```

This output shows that the SSH service was already running, so it didn't need to be started. The playbook successfully checked and verified the service status, and also confirmed that the SSH port is open.

## Summary
-------

In this lab, you have explored the versatility and power of the Ansible Command module. You've learned how to:

1.  Create simple Ansible playbooks using the Command module to execute basic commands.
2.  Use variables with the Command module to make your playbooks more flexible and reusable.
3.  Capture and process command output, allowing you to make decisions based on command results.
4.  Work with various Command module options to control its behavior, such as changing directories, setting environment variables, and handling errors.
5.  Apply the Command module in a real-world scenario by checking and managing a system service.

These skills form a solid foundation for using Ansible to automate system administration tasks and manage remote hosts efficiently. As you continue to work with Ansible, you'll find the Command module to be a versatile tool in your automation toolkit.

Remember that while the Command module is powerful, it's often better to use specialized Ansible modules (like the `service` module for managing services) when available. These specialized modules provide better idempotency and can handle more complex scenarios out of the box.
