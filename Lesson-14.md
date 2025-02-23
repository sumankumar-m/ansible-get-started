#  Ansible Shell Module


## Introduction
------------

In this exercise, you will learn how to use the Ansible Shell module to execute shell commands on remote hosts. The Shell module is useful when you need to run shell commands that are not covered by existing Ansible modules or when you require more flexibility and control over the execution.

* * *

## Execute a Simple Shell Command
------------------------------

In this step, you will execute a simple shell command on a remote host using the Ansible Shell module.

First, complete `/home/lab/project/simple_shell_command.yml` file.  
Add the following content to the playbook file:

```yaml
    - name: Execute a Simple Shell Command
      hosts: localhost
      gather_facts: false
    
      tasks:
        - name: Execute ls -l command
          shell: ls -l
          register: command_output
    
        - name: Display command output
          debug:
            var: command_output.stdout_lines
```

*   `hosts: localhost`: The target host for executing this ansible playbook is `localhost`.
*   `gather_facts: false`: Disables the gathering of host information.
*   `shell: ls -l`: Specifies the command to execute.

In summary, the purpose of this playbook is to execute the ls -l command on the local host and display the output of the command.

Then, display the output of the command in the Ansible playbook.

```bash
    ansible-playbook /home/lab/project/simple_shell_command.yml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
  
 
    PLAY [Execute a Simple Shell Command] ******************************************
    
    TASK [Execute ls -l command] ***************************************************
    changed: [localhost]
    
    TASK [Display command output] **************************************************
    ok: [localhost] => {
        "command_output.stdout_lines": [
            "total 16",
            "-rwxrwxrwx 1 lab root 285 Mar  8 13:33 complex_shell_commands.yml",
            "-rwxrwxrwx 1 lab root 221 Mar  8 13:33 handle_command_failure.yml",
            "-rwxrwxrwx 1 lab root 222 Mar  8 13:33 pass_variables.yml",
            "-rwxrwxrwx 1 lab root 266 Mar  8 13:36 simple_shell_command.yml"
        ]
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Here `"command_output.stdout_lines":[...]` is the output of `ls -l` on the target host.

## Pass Variables to Shell Commands
--------------------------------

In this step, you will learn how to pass variables from your Ansible playbook to shell commands executed using the Shell module.

First, complete `/home/lab/project/pass_variables.yml` file.  
Add the following content to the playbook file:

```yaml
    - name: Pass Variables to Shell Commands
      hosts: localhost
      gather_facts: false
      vars:
        directory_path: /home/lab
    
      tasks:
        - name: Execute ls command with a variable
          shell: ls "{{ directory_path }}"
          register: variable_command_output
    
        - name: Display variable command output
          debug:
            var: variable_command_output.stdout_lines
```

*   `hosts: localhost`: The target host for executing this ansible playbook is `localhost`.
*   `vars`: Define a variable named `directory_path` with the value `/home/lab`.
*   `shell: ls "{{ directory_path }}"`: Execute the `ls` command to output the contents of the variable named `directory_path`.

In summary, this playbook executes the ls command with a variable representing a directory path (/home/lab) on the localhost and displays the output of the command.

Then, display the output of the command in the Ansible playbook.

```bash
    ansible-playbook /home/lab/project/pass_variables.yml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [Pass Variables to Shell Commands] ****************************************
    
    TASK [Execute ls command with a variable] **************************************
    changed: [localhost]
    
    TASK [Display variable command output] *****************************************
    ok: [localhost] => {
        "variable_command_output.stdout_lines": [
            "Code",
            "Desktop",
            "golang",
            "project"
        ]
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Here `"variable_command_output.stdout_lines": [...]` is the output of `ls /home/lab` on the target host.

## Handle Command Failure
----------------------

In this step, you will learn how to handle command failure when executing shell commands using the Shell module. You will handle cases where the shell command returns a non-zero exit status, indicating a failure.

First, complete `/home/lab/project/handle_command_failure.yml` file.  
Add the following content to the playbook file:
```yaml
    - name: Handle Command Failure
      hosts: localhost
      gather_facts: false
    
      tasks:
        - name: Execute a failing command
          shell: failing-command
          register: failing_command_output
          ignore_errors: yes
    
        - name: Handle command failure
          debug:
            msg: "The command failed. Performing fallback action."
          when: failing_command_output.failed
```

*   `hosts: localhost`: The target host for executing this ansible playbook is `localhost`.
*   `shell: failing-command`: This line uses the shell module to execute the command failing-command. The output of this command will be captured and stored in the failing\_command\_output variable. Since the `failing-command` script does not exist, this command must fail.
*   `ignore_errors: yes`: This line instructs Ansible to ignore any errors that occur during the execution of the failing-command. This allows the playbook to continue executing even if the command fails.
*   `debug`: This module uses the debug module to display a message that says "The command failed. Performing fallback action." This message is only be displayed when the failing\_command\_output.failed condition is met, indicating that the previous command execution failed.

In summary, the playbook attempts to execute a command that is expected to fail, ignores any errors that occur during its execution, and then displays a message indicating that the command failed and a fallback action will be taken based on the failure status of the command.

Then, display the output of the command in the Ansible playbook.

```bash
    ansible-playbook /home/lab/project/handle_command_failure.yml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [Handle Command Failure] **************************************************
    
    TASK [Execute a failing command] ***********************************************
    fatal: [localhost]: FAILED! => {"changed": true, "cmd": "failing-command", "delta": "0:00:00.009169", "end": "2024-03-08 13:46:22.701946", "msg": "non-zero return code", "rc": 127, "start": "2024-03-08 13:46:22.692777", "stderr": "/bin/sh: line 1: failing-command: command not found", "stderr_lines": ["/bin/sh: line 1: failing-command: command not found"], "stdout": "", "stdout_lines": []}
    ...ignoring
    
    TASK [Handle command failure] **************************************************
    ok: [localhost] => {
        "msg": "The command failed. Performing fallback action."
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=1
```

The presence of `"msg": "The command failed. Performing fallback action."` indicates that the execution of this `failing-command` command failed.

## Execute Complex Shell Commands
------------------------------

In this step, you will execute more complex shell commands using the Shell module. You will explore the usage of command redirection, pipelines, and complex command structures.

First, complete `/home/lab/project/complex_shell_commands.yml` file.  
Add the following content to the playbook file:

```yaml
    - name: Execute Complex Shell Commands
      hosts: localhost
      gather_facts: false
    
      tasks:
        - name: Execute complex command
          shell: ls -l /home/lab | grep 'project' > /home/lab/output.txt
          register: complex_command_output
    
        - name: Display complex command output
          debug:
            var: complex_command_output.stdout_lines
```

*   `hosts: localhost`: The target host for executing this ansible playbook is `localhost`.
*   `shell`: Redirect the output of `ls -l /home/lab` to the `/home/lab/output.txt` file and use `grep` to filter the content about `project`.

In summary, this playbook executes a complex shell command on the localhost, filters and redirects the output to a file, and then displays the output of the command execution.

Then, execute the ansible playbook command.
```bash
    ansible-playbook /home/lab/project/complex_shell_commands.yml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [Execute Complex Shell Commands] ******************************************
    
    TASK [Execute complex command] *************************************************
    changed: [localhost]
    
    TASK [Display complex command output] ******************************************
    ok: [localhost] => {
        "complex_command_output.stdout_lines": []
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Finally, view the `/home/lab/output.txt` file content:

```bash
    cat /home/lab/output.txt
```

Example output:
```
    drwxr-xr-x 1 lab lab 4096 Mar 8 13:49 project
```

## Summary
-------

Congratulations! You have successfully completed the Ansible Shell Module lab. You have learned how to use the Shell module to execute shell commands on remote hosts, pass variables to commands, handle command failures, and execute complex shell commands.

The Shell module provides great flexibility and control over executing shell commands in your Ansible playbooks. However, be cautious when using it, as running arbitrary shell commands may have security implications. Always ensure that you trust the commands and sanitize any user-provided inputs.

Now that you have a good understanding of the Shell module, you can leverage it to perform various automation tasks and manage your remote hosts efficiently. Happy Ansible scripting!