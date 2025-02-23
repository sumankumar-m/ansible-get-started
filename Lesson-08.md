# Manage multiple inventory sources

## Introduction

In this exercise, you will learn how to work with multiple inventories in Ansible. Managing multiple inventories is a common scenario in complex environments with different groups of hosts or separate environments. You will learn how to define and organize multiple inventories, access hosts from different inventories, and perform operations across multiple inventories.


## Create Inventory Directory and Files

Let's start by creating a directory to store our inventory files and then create two separate inventory files for development and production environments.

First, create a new directory called inventory in the /home/lab/project path:

```bash
mkdir -p /home/lab/project/inventory
```

This command creates a new directory called inventory. The -p option ensures that the parent directories are created if they don't exist.

Now, create two inventory files: dev_inventory.ini and prod_inventory.ini inside the inventory directory:

```bash
touch /home/lab/project/inventory/dev_inventory.ini
touch /home/lab/project/inventory/prod_inventory.ini
```

The touch command creates empty files if they don't exist, or updates the modification time if they do exist.

Next, we'll add content to these inventory files. Open dev_inventory.ini in a text editor (you can use nano or any other text editor you're comfortable with):

```bash
nano /home/lab/project/inventory/dev_inventory.ini
```

Add the following content:

```ini
[dev]
localhost ansible_connection=local
```

Save the file and exit the editor (in nano, you can do this by pressing Ctrl+X, then Y, then Enter).

Now, open prod_inventory.ini:

```bash
nano /home/lab/project/inventory/prod_inventory.ini
```

Add the following content:

```ini
[prod]
localhost ansible_connection=local
```

Save and exit the editor.

In these inventory files:

    [dev] and [prod] are group names. They help organize hosts into logical groups.
    localhost is the hostname. We're using localhost to simulate different environments on the same machine.
    ansible_connection=local tells Ansible to run commands locally instead of trying to SSH into the machine. This is useful for running Ansible against the same machine it's installed on.

In a real-world scenario, you would typically have different IP addresses or hostnames for your development and production servers instead of using localhost for both.

## Create a Playbook for Multiple Inventories

In this step, we'll create a playbook that can work with both our development and production inventories. In Ansible, a playbook is a YAML file that defines a set of tasks to be executed on hosts.

First, let's create a new file called multi_env_playbook.yaml in the /home/lab/project directory:

```bash
touch /home/lab/project/multi_env_playbook.yaml
```

Now, let's open the multi_env_playbook.yaml file in a text editor. We'll use nano, which is a simple command-line text editor:

```bash
nano /home/lab/project/multi_env_playbook.yaml
```

Copy and paste the following content into the file:

```yaml
---
- name: Demonstrate multi-environment playbook
  hosts: all
  gather_facts: no
  tasks:
    - name: Print environment type
      debug:
        msg: "This host belongs to the {{ 'production' if 'prod' in group_names else 'development' }} environment"

    - name: Show hostname
      command: hostname
      register: hostname_output

    - name: Display hostname
      debug:
        msg: "The hostname is: {{ hostname_output.stdout }}"
```

After pasting, press Ctrl + X, then Y, and finally Enter to save and exit nano.

Let's break down this playbook to understand each part:

    --- at the top of the file indicates the start of a YAML document.
    name: Demonstrate multi-environment playbook is a descriptive name for this play.
    hosts: all means this playbook will run on all hosts in the inventory.
    gather_facts: no skips the fact-gathering phase to speed up execution. Fact gathering is when Ansible collects information about the target hosts.
    tasks: begins the list of tasks to be executed.
    The first task uses the debug module to print a message. It uses a conditional statement to determine if the host is in the production or development environment:
        group_names is a special variable that contains a list of all groups the current host belongs to.
        If 'prod' is in the group_names list, it prints "production", otherwise "development".
    The second task runs the hostname command and stores its output in a variable called hostname_output.
    The third task displays the hostname using the debug module. It accesses the stored output using hostname_output.stdout.

## Run the Playbook with Development Inventory

Now that we have our playbook and inventories set up, let's run the playbook using the development inventory.

Execute the following command in your terminal:

```bash
ansible-playbook -i /home/lab/project/inventory/dev_inventory.ini /home/lab/project/multi_env_playbook.yaml
```

Let's break down this command:

    ansible-playbook is the command to run an Ansible playbook.
    -i /home/lab/project/inventory/dev_inventory.ini specifies the inventory file to use. The -i option stands for "inventory".
    /home/lab/project/multi_env_playbook.yaml is the path to our playbook file.

This command tells Ansible to use the dev_inventory.ini file and run the multi_env_playbook.yaml playbook.

You should see output similar to the following:

```
PLAY [Demonstrate multi-environment playbook] *******************************************

TASK [Print environment type] *********************************************************
ok: [localhost] => {
    "msg": "This host belongs to the development environment"
}

TASK [Show hostname] ******************************************************************
changed: [localhost]

TASK [Display hostname] ***************************************************************
ok: [localhost] => {
    "msg": "The hostname is: 66d80191e483433f91fbdca9"
}

PLAY RECAP ****************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

This output shows that:

    The playbook ran successfully on the development environment.
    It correctly identified that the host belongs to the development environment.
    It successfully retrieved and displayed the hostname.
    The "PLAY RECAP" shows a summary of the playbook execution. Here, "ok=3" means three tasks were executed successfully, and "changed=1" means one task made a change to the system (running the hostname command).

## Run the Playbook with Production Inventory

Now, let's run the same playbook using the production inventory to see how it behaves differently.

Execute the following command in your terminal:

```bash
ansible-playbook -i /home/lab/project/inventory/prod_inventory.ini /home/lab/project/multi_env_playbook.yaml
```

This command is similar to the previous one, but it uses the prod_inventory.ini file instead of dev_inventory.ini.

You should see output similar to the following:

```
PLAY [Demonstrate multi-environment playbook] *********************************

TASK [Print environment type] *************************************************
ok: [localhost] => {
    "msg": "This host belongs to the production environment"
}

TASK [Show hostname] **********************************************************
changed: [localhost]

TASK [Display hostname] *******************************************************
ok: [localhost] => {
    "msg": "The hostname is: lab-instance"
}

PLAY RECAP ********************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Notice that this time, the playbook correctly identifies that it's running in the production environment. This is because we're using the production inventory file, which defines the host under the [prod] group.

The rest of the output is similar to what we saw with the development inventory, which is expected since we're still running on the same local machine.

## Run the Playbook with Both Inventories

Finally, let's run the playbook using both inventories at once. This demonstrates how Ansible can work with multiple inventories simultaneously.

Execute the following command in your terminal:

```bash
ansible-playbook -i /home/lab/project/inventory/dev_inventory.ini -i /home/lab/project/inventory/prod_inventory.ini /home/lab/project/multi_env_playbook.yaml
```

This command includes both inventory files using multiple -i options. Ansible will combine these inventories when running the playbook.

You should see output similar to the following:

```
PLAY [Demonstrate multi-environment playbook] *******************************************************************************************************************************************************************************************************************************************

TASK [Print environment type] ***********************************************************************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "This host belongs to the production environment"
}

TASK [Show hostname] ********************************************************************************************************************************************************************************************************************************************************************
changed: [localhost]

TASK [Display hostname] *****************************************************************************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "The hostname is: 66d80191e483433f91fbdca9"
}

PLAY RECAP ******************************************************************************************************************************************************************************************************************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

You might notice that even though we included both inventories, the playbook only ran once. This is because both inventories refer to the same localhost, and Ansible de-duplicates hosts by default. If there were different hosts in each inventory, Ansible would run the playbook for each unique host.

The environment is identified as "production" because the production inventory file is specified last in the command. Ansible uses the last occurrence of a host when determining group membership and variable values. If we reversed the order of the -i options, the output would show "development".

This approach of using multiple inventories can be very useful in real-world scenarios where you might have common hosts across environments, or when you want to run a playbook against a combination of hosts from different inventory files.