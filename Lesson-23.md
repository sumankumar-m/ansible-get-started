#  Ansible Script Module

## Introduction
------------

In this lab, you will learn how to use the Ansible Script module to execute custom scripts on remote hosts. The Script module allows you to run scripts written in any programming language on the target hosts, providing flexibility and customization options in your automation tasks.

* * *


## Execute a Simple Script
-----------------------

In this step, you will execute a simple script on a remote host using the Ansible Script module.

First, create a new Ansible playbook file called `/home/lab/project/script-module-playbook.yaml` and open it in a text editor.  
Add the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Execute a simple script
          script: /home/lab/project/simple_script.sh
          register: script_output
    
        - name: Display script output
          debug:
            var: script_output.stdout_lines
```

*   `hosts`: This specifies the target host on which to run the playbook. In this case, the playbook will run on the local host because the target host is "localhost".
*   `tasks`: This is a list of tasks to run.
*   `name`: This is the descriptive name for the task that identifies the purpose of the task.
*   `script`: This is the actual command that the task executes. It instructs Ansible to run the script named "simple\_script.sh" located in the "/home/lab/project/" directory on the target host.
*   `register`: This registers the output of the script in the script\_output variable for later use in the playbook.
*   `debug`: This is the Ansible module used to print debugging information.
*   `var`: This is a parameter for the debug module that displays the standard output of the script from the previous task. script\_output.stdout\_lines contains each line of the script's output.

In summary, the main purpose of this playbook is to run a script on the local host and display the standard output of the script after it is run. The script\_output registered variable contains the output of the script, which is then printed using the Debug module.

Then, create a simple script file called `/home/lab/project/simple_script.sh` with the following content:

```bash
    #!/bin/bash
    echo "This is a simple script."
```

Make sure the script file is executable (`chmod +x simple_script.sh`) and place the script file in the `/home/lab/project/` directory specified in the playbook.

Finally, run the playbook with the following command:

```bash
    chmod +x simple_script.sh
    ansible-playbook script-module-playbook.yaml
```

Observe the output of the script displayed in the playbook execution output.

Example output:
```
    ...
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Execute a simple script] *************************************************
    changed: [localhost]
    
    TASK [Display script output] ***************************************************
    ok: [localhost] => {
        "script_output.stdout_lines": [
            "This is a simple script."
        ]
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Here `"This is a simple script."` is the output of the `simple_script.sh` script.

## Pass Variables to Scripts
-------------------------

In this step, you will learn how to pass variables from your Ansible playbook to scripts executed using the Script module.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Execute script with a variable
          script: /home/lab/project/var_script.sh "{{ message }}"
          register: variable_script_output
    
        - name: Display variable script output
          debug:
            var: variable_script_output.stdout_lines
```

*   `script`: This is the actual command that the task will execute. And the `{{ message }}` is a variable that is passed to the script. The double curly braces indicate that this is an Ansible variable, and its value will be dynamically replaced during execution.
*   `register`: This registers the output of the script into the variable "variable\_script\_output" for later use in the playbook.
*   `debug`: This is the Ansible module used for printing debugging information.
*   `var`: This parameter for the debug module displays the standard output of the script from the previous task. variable\_script\_output.stdout\_lines contains each line of the script's output.

In summary, this playbook executes a script on the local host, passing a variable called "message" to the script. The output of the script is registered in the variable variable\_script\_output, and then it is printed using the debug module.

Then, create a simple script file called `/home/lab/project/var_script.sh` with the following content:

```
    #!/bin/bash
    echo $1
```

Make sure the script file is executable (`chmod +x var_script.sh`) and place the script file in the `/home/lab/project/` directory specified in the playbook.

Finally, by passing the `message` variable as an extra variable using the `-e` option when running the playbook.

```bash
    chmod +x var_script.sh
    ansible-playbook script-module-playbook.yaml -e "message=Hello,Ansible"
```

Observe the output of the script with the provided message.  

Example output:
```
    ...
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Execute script with a variable] ******************************************
    changed: [localhost]
    
    TASK [Display variable script output] ******************************************
    ok: [localhost] => {
        "variable_script_output.stdout_lines": [
            "Hello,Ansible"
        ]
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Here `"Hello,Ansible"` is the output of the `simple_script.sh` script.

## Handle Script Failure
---------------------

In this step, you will learn how to handle script failure when executing scripts using the Script module. You will handle cases where the script returns a non-zero exit status, indicating a failure.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Execute a failing script
          script: /home/lab/project/failing_script.sh
          register: failing_script_output
          ignore_errors: yes
    
        - name: Handle script failure
          debug:
            msg: "The script failed. Performing fallback action."
          when: failing_script_output.failed
```

*   `script`: This is the actual command that the task will execute.
*   `register`: This registers the output of the script into the variable "failing\_script\_output" for later use in the playbook.
*   `ignore_errors`: This parameter is set to `yes` to allow Ansible to continue executing subsequent tasks even if this script task fails. The failure will be recorded in the failing\_script\_output variable.
*   `debug`: This is the Ansible module used for printing debugging information.
*   `msg`: This parameter for the debug module prints a message indicating that the script has failed. This task will only be executed when the previous task (failing\_script\_output) has failed.
*   `when`: This condition ensures that this task is only executed when the script in the first task has failed. If the script succeeds, this task will be skipped.

In summary, this playbook attempts to execute a script that may fail. If the script fails, the playbook handles the failure by printing a debug message and performing a fallback action. The `ignore_errors: yes` parameter allows the playbook to continue even if the script fails, and the condition `when: failing_script_output.failed` ensures that the handling task is only executed when needed.

Then, create a failing script file called `/home/lab/project/failing_script.sh` with the following content:
```
    #!/bin/bash
    exit 1
```

Make sure the script file is executable (`chmod +x failing_script.sh`) and place the script file in the `/home/lab/project/` directory specified in the playbook.

Finally, run the playbook and observe that the failing script is executed but ignored due to `ignore_errors: yes`.

```bash
    chmod +x failing_script.sh
    ansible-playbook script-module-playbook.yaml
```

The playbook will display a debug message indicating the failure and performing a fallback action.

Example output:
```
    ...
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Execute a failing script] ************************************************
    fatal: [localhost]: FAILED! => {"changed": true, "msg": "non-zero return code", "rc": 1, "stderr": "", "stderr_lines": [], "stdout": "", "stdout_lines": []}
    ...ignoring
    
    TASK [Handle script failure] ***************************************************
    ok: [localhost] => {
        "msg": "The script failed. Performing fallback action."
    }
    
    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=1
```

The `"msg": "The script failed. Performing fallback action."` indicates that the `failing_script.sh` script failed to execute.

## Summary
-------

Congratulations! You have successfully completed the Ansible Script Module lab. You have learned how to use the Script module to execute custom scripts on remote hosts, pass variables to scripts, and handle script failures.

The Script module provides great flexibility and customization options in your automation tasks by allowing you to leverage the power of custom scripts. However, make sure to write secure and reliable scripts and validate any inputs to prevent security vulnerabilities.

Now that you have a good understanding of the Script module, you can incorporate it into your Ansible playbooks to automate various tasks and achieve more advanced automation scenarios. Happy scripting with Ansible!