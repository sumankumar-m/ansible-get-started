#  Ansible Local Action Module

## Introduction
------------

 In this exercise, you will dive into the usage of the Ansible Local Action module. The Local Action module allows you to run tasks on the control machine where Ansible is being executed. This is useful when you need to perform operations locally, such as executing shell commands or running scripts.

Let's get started!

* * *


## Executing a Simple Command Locally
----------------------------------

In this step, you will learn how to use the Ansible Local Action module to execute a simple command on the control machine. This will help you understand the basic usage and syntax of the module.

First, complete `/home/lab/project/execute_local_command.yml` file.  
Add the following content to the playbook file:

```yaml
    - name: Executing a Simple Command Locally
      gather_facts: false
      hosts: localhost
    
      tasks:
        - name: Print a message locally
          local_action:
            module: command
            cmd: echo "Hello, World!"
          register: result
    
        - name: Debug the output
          debug:
            var: result.stdout
```

*   `gather_facts`: This specifies whether Ansible should gather facts about the target hosts. In this case, it's set to `false`, meaning facts won't be gathered.
*   `hosts`: This specifies the target host on which to run the playbook. In this case, the playbook will run on the local host because the target host is `localhost`.
*   `tasks`: This is a list of tasks to be executed.
*   `local_action`: This indicates that the action should be performed locally on the control machine where Ansible is being run.
*   `module`: This specifies the Ansible module to use for the action. In this case, it's the `command` module.
*   `cmd`: This is the actual command to be executed. In this case, it's the shell command `echo "Hello, World!"`, which will print the message "Hello, World!" to the console.
*   `register`: This registers the output of the command in the `result` variable for later use in the playbook.
*   `debug`: This is the Ansible module used to print debugging information.
*   `var`: This is a parameter for the `debug` module that specifies the variable to be debugged. In this case, it's `result.stdout`, which contains the standard output of the command executed in the previous task.

In summary, this playbook executes a simple command `echo "Hello, World!"` locally on the control machine, registers the output, and then prints the output using the `debug` module.

Then, display the output of the command in the Ansible playbook.
```bash
    ansible-playbook /home/lab/project/execute_local_command.yml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [Local Action Module Challenge] *******************************************
    
    TASK [Print a message locally] *************************************************
    changed: [localhost]
    
    TASK [Debug the output] ********************************************************
    ok: [localhost] => {
        "result.stdout": "Hello, World!"
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Here `"result.stdout": "Hello, World!"` is the output of `echo "Hello, World!"` command.

## Executing a Command with Variable Substitution
----------------------------------------------

In this step, you will enhance your understanding of the Local Action module by incorporating variable substitution. This will allow you to dynamically change the command being executed based on variables defined in your playbook.

First, complete `/home/lab/project/execute_command_with_variable_substitution.yml` file.  
Add the following content to the playbook file:

```yaml
    - name: Executing a Command with Variable Substitution
      gather_facts: false
      hosts: localhost
      vars:
        message: "Hello, Ansible!"
    
      tasks:
        - name: Print a message with variable substitution
          local_action:
            module: command
            cmd: echo "{{ message }}"
          register: result
    
        - name: Debug the output
          debug:
            var: result.stdout
```

*   `vars`: This section is used to define variables. In this playbook, it defines a variable named `message` with the value `"Hello, Ansible!"`.
*   `local_action`: This indicates that the action should be performed locally on the control machine where Ansible is being run.
*   `module`: This specifies the Ansible module to use for the action. In this case, it's the `command` module.
*   `cmd`: This is the actual command to be executed. In this case, it's the shell command `echo "{{ message }}"`, where `{{ message }}` is replaced with the value of the `message` variable defined earlier.
*   `register`: This registers the output of the command in the `result` variable for later use in the playbook.
*   `debug`: This is the Ansible module used to print debugging information.
*   `var`: This is a parameter for the `debug` module that specifies the variable to be debugged. In this case, it's `result.stdout`, which contains the standard output of the command executed in the previous task.

In summary, this playbook executes the command `echo "{{ message }}"` locally on the control machine, where `{{ message }}` is replaced with the value of the `message` variable defined in the vars section. It registers the output of the command and then prints the output using the `debug` module.

Then, display the output of the command in the Ansible playbook.
```bash
    ansible-playbook /home/lab/project/execute_command_with_variable_substitution.yml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [Local Action Module Challenge] *******************************************
    
    TASK [Print a message with variable substitution] ******************************
    changed: [localhost]
    
    TASK [Debug the output] ********************************************************
    ok: [localhost] => {
        "result.stdout": "Hello, Ansible!"
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Here `"result.stdout": "Hello, Ansible!"` is the output of `echo "{{ message }}"` command.

## Executing a Complex Local Action
--------------------------------

In this step, you will explore a more complex usage of the Ansible Local Action module. You will execute a shell script locally and pass arguments to the script.

First, complete `/home/lab/project/execute_complex_local_action.yml` file.  
Add the following content to the playbook file:
```yaml
    - name: Executing a Complex Local Action
      gather_facts: false
      hosts: localhost
    
      tasks:
        - name: Execute a shell script with arguments
          local_action:
            module: shell
            cmd: ./script.sh 5 7
          register: result
    
        - name: Debug the output
          debug:
            var: result.stdout
```

*   `local_action`: This indicates that the action should be performed locally on the control machine where Ansible is being run.
*   `module`: This specifies the Ansible module to use for the action. In this case, it's the `shell` module.
*   `cmd`: This is the actual shell command to be executed. In this case, it's `./script.sh 5 7`, which executes a shell script named `script.sh` with arguments `5` and `7`.
*   `register`: This registers the output of the command in the `result` variable for later use in the playbook.
*   `debug`: This is the Ansible module used to print debugging information.
*   `var`: This is a parameter for the `debug` module that specifies the variable to be debugged. In this case, it's `result.stdout`, which contains the standard output of the command executed in the previous task.

In summary, this playbook executes a shell script `script.sh` with arguments `5` and `7` locally on the control machine. It registers the output of the script and then prints the output using the `debug` module.

> **Tips:** We have prepared this shell script for you, it is in the `/home/lab/project/` directory and is called `script.sh`. The function of this script is to calculate the sum of two parameters.

Then, display the output of the command in the Ansible playbook.
```bash
    ansible-playbook /home/lab/project/execute_complex_local_action.yml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [Local Action Module Challenge] *******************************************
    
    TASK [Execute a shell script with arguments] ***********************************
    changed: [localhost]
    
    TASK [Debug the output] ********************************************************
    ok: [localhost] => {
        "result.stdout": "The sum of 5 and 7 is 12."
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Here `"result.stdout": "The sum of 5 and 7 is 12."` is the output of `./script.sh 5 7` script.

## Summary
-------

Congratulations on completing the Ansible Local Action Module Lab! In this lab, you learned how to use the Ansible Local Action module to execute commands and scripts locally. Starting with simple command execution, you progressed to incorporating variable substitution and executing complex shell scripts with arguments.

By completing this lab, you have gained valuable experience in leveraging the Local Action module for tasks that need to be performed on the control machine. Keep exploring Ansible and its modules to further enhance your automation capabilities.

Happy automating!
