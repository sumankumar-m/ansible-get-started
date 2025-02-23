#  Ansible Cron Module

## Introduction
------------

Welcome to the Ansible Cron Module Lab! In this lab, you will learn how to automate the scheduling of tasks using the Ansible Cron module. The lab consists of several steps, each building upon the previous one to gradually increase the complexity of managing cron jobs with Ansible. By the end of this lab, you will have a solid understanding of how to use the Ansible Cron module for scheduling tasks.

* * *


## Create a Simple Cron Job
------------------------

In this step, you will create a simple cron job using the Ansible Cron module. The cron job will execute a shell command at a specified interval.

First, create a new Ansible playbook file called `/home/lab/project/cron_module_playbook.yaml` and open it in a text editor.  
Add the following content to the playbook file:
```yaml
    - hosts: localhost
      tasks:
        - name: Create a simple cron job
          cron:
            name: my_cron_job
            minute: "*/5"
            job: /home/lab/project/script.sh
```

*   `cron`: This is the Ansible module used to manage cron jobs on the target host.
*   `minute`: This sets the scheduling interval for the cron job. In this example, it means "every 5 minutes". The `*/5` syntax means "every minute divisible by 5".
*   `job`: This specifies the command or script to be executed by the cron job. In this case, it's `/home/lab/project/script.sh`, indicating that the script `script.sh` located in the `/home/lab/project/` directory will be executed by the cron job.

The `cron` module in Ansible is used to manage cron jobs on Unix-like systems. It provides a convenient way to automate tasks by scheduling them to run at specific times or intervals. In this configuration, it's used to create a cron job named "my\_cron\_job" that runs the script `/home/lab/project/script.sh` every 5 minutes.

Next, create a simple script file called `/home/lab/project/script.sh` with the following content:
```bash
    #!/bin/bash
    echo "This is a simple script."
```

Then, run the playbook with the following command:
```bash
    ansible-playbook cron_module_playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Create a simple cron job] ************************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, verify that the cron job is created by checking the crontab on the target system.

```bash
    crontab -l
```

Example output:
```
    #Ansible: my_cron_job
    */5 * * * * /home/lab/project/script.sh
```

## Schedule a Task with Custom Time
--------------------------------

In this step, you will schedule a task with a custom time using the Ansible Cron module. You will specify the exact minute, hour, day, month, and weekday for the cron job.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:
```yaml
    - hosts: localhost
      tasks:
        - name: Schedule a task with custom time
          cron:
            name: custom_cron_job
            minute: "0"
            hour: "9"
            job: /home/lab/project/script.sh
```

*   `cron`: This is the Ansible module used to manage cron jobs on the target host.
*   `minute`: This specifies the minute at which the cron job should run. In this case, "0" means the job will run at the beginning of the hour.
*   `hour`: This specifies the hour at which the cron job should run. Here, "9" means the job will run at 9:00 AM (local server time).
*   `job`: This specifies the command or script to be executed by the cron job. In this example, the script `/home/lab/project/script.sh` will be run.

In this configuration, it's used to schedule a task named "custom\_cron\_job" to run the script `/home/lab/project/script.sh` at 9:00 AM every day. The job is scheduled to run at minute "0" of the 9th hour.

Then, run the playbook with the following command:
```bash
    ansible-playbook cron_module_playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Schedule a task with custom time] ****************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, verify that the cron job is created by checking the crontab on the target system.
```bash
    crontab -l
```

Example output:
```
    #Ansible: my_cron_job
    */5 * * * * /home/lab/project/script.sh
    #Ansible: custom_cron_job
    0 9 * * * /home/lab/project/script.sh
```

## Ensure a Cron Job Runs as a Specific User
-----------------------------------------

In this step, you will learn how to ensure that a cron job runs as a specific user using the Ansible Cron module. This allows you to control the execution of the cron job and run it with the desired user privileges.

First, modify the existing task to include the `user` parameter and specify the desired user:
```yaml
    - hosts: localhost
      tasks:
        - name: Schedule a task with custom time
          cron:
            name: custom_cron_job
            minute: "0"
            hour: "9"
            job: /home/lab/project/script.sh
            user: lab
```

*   `cron`: This is the Ansible module used to manage cron jobs on the target host.
*   `minute`: This specifies the minute at which the cron job should run. In this case, "0" means the job will run at the beginning of the hour.
*   `hour`: This specifies the hour at which the cron job should run. Here, "9" means the job will run at 9:00 AM (local server time).
*   `job`: This specifies the command or script to be executed by the cron job. In this example, the script `/home/lab/project/script.sh` will be run.
*   `user`: This specifies the user account under which the cron job will be scheduled. It ensures that the cron job is associated with the specified user.

In this configuration, it's used to schedule a task named "custom\_cron\_job" to run the script `/home/lab/project/script.sh` at 9:00 AM every day. The job is scheduled to run at minute "0" of the 9th hour and is associated with the user account "lab".

Then, run the playbook with the following command:
```bash
    ansible-playbook cron_module_playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Schedule a task with custom time] ****************************************
    ok: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, verify that the cron job is created by checking the crontab on the target system.

```bash
    crontab -l
```

Example output:
```
    #Ansible: my_cron_job
    */5 * * * * /home/lab/project/script.sh
    #Ansible: custom_cron_job
    0 9 * * * /home/lab/project/script.sh
```

## Remove a Cron Job
-----------------

In this step, you will learn how to remove a cron job using the Ansible Cron module. This allows you to clean up unwanted or obsolete cron jobs from the target system.

First, modify the existing playbook file by removing all content and adding the following content to the playbook file:

```yaml
    - hosts: localhost
      tasks:
        - name: Remove the cron job
          cron:
            name: my_cron_job
            state: absent
```

*   `cron`: This is the Ansible module used to manage cron jobs on the target host.
*   `name`: This specifies the name of the cron job to be removed. It's named "my\_cron\_job" in this example.
*   `state`: This parameter indicates the desired state of the cron job. In this case, `absent` means that the cron job should not exist. Therefore, this task will remove the cron job named "my\_cron\_job" if it exists on the target system.

In this configuration, it's used to remove a cron job named "my\_cron\_job" from the target system if it exists. The task ensures that the specified cron job is absent from the system.

Then, run the playbook with the following command:

```bash
    ansible-playbook cron_module_playbook.yaml
```

Example output:
```
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that
    the implicit localhost does not match 'all'
    
    PLAY [localhost] ***************************************************************
    
    TASK [Gathering Facts] *********************************************************
    ok: [localhost]
    
    TASK [Remove the cron job] *****************************************************
    changed: [localhost]
    
    PLAY RECAP *********************************************************************
    localhost : ok=2 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

Finally, verify that the cron job is removed by checking the crontab on the target system.

```bash
    crontab -l
```

Example output:
```
    #Ansible: custom_cron_job
    0 9 * * * /home/lab/project/script.sh
```

You can see that `my_cron_job` was successfully deleted.

## Summary
-------

Congratulations! You have completed the Ansible Cron Module Lab. Throughout the lab, you learned how to create cron jobs, schedule tasks with custom time, ensure cron jobs run as specific users, remove cron jobs, and use the crontab directive. You can now leverage the power of the Ansible Cron module to automate the scheduling of tasks in your infrastructure. Keep practicing and exploring more advanced features to enhance your Ansible skills. Well done!