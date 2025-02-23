# Ansible Ad-hoc Commands Lab

## Introduction

In this lab, you will learn about **Ansible ad-hoc commands**. Ad-hoc commands are quick, one-off tasks that you can run without the need for a full playbook. They are perfect for simple operations like checking system status, managing files, or executing quick commands across multiple servers. By the end of this lab, you'll understand how to use ad-hoc commands effectively, which is crucial for performing quick tasks and troubleshooting in your Ansible-managed environment.

## Skills Graph

### Lab Skills
- Ansible Ad-Hoc Commands
  - Execute Commands
  - Transfer Files
  - Manage Files/Directories
  - Network Test
  - Define Inventory Groups
  - Disk Space Reporting

### Ansible
- Module Operations
- Inventory Management

### Linux
- System Information and Monitoring

## Understanding Ansible Ad-hoc Command Structure

Let's start by understanding the basic structure of an Ansible ad-hoc command. The general syntax is:

```bash
ansible [pattern] -m [module] -a "[module options]"
```

Where:

- `[pattern]` is the host or group of hosts from your inventory that you want to target.
- `-m [module]` specifies which Ansible module to use.
- `-a "[module options]"` provides arguments to the module.

Before we start running commands, let's ensure we have a proper inventory file. Create or edit the inventory file:

```bash
nano /home/lab/project/inventory
```

Add the following content:

```ini
localhost ansible_connection=local

[webservers]
localhost

[dbservers]
localhost
```

Save and exit the editor.

Now, let's try a simple ad-hoc command to ping all hosts in our inventory:

```bash
ansible all -i /home/lab/project/inventory -m ping
```

You should see output similar to this:

```json
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

This command uses the **ping** module to check connectivity to all hosts in the inventory. The `all` keyword targets all hosts.

### Explanation:
- `all` is the pattern, targeting all hosts in the inventory.
- `-i /home/lab/project/inventory` specifies the inventory file to use.
- `-m ping` tells Ansible to use the ping module.

## Running Commands with the Command Module

One of the most commonly used modules in ad-hoc commands is the **command** module. It allows you to run arbitrary commands on the target hosts.

Let's use the command module to check the disk space on all hosts:

```bash
ansible all -i /home/lab/project/inventory -m command -a "df -h"
```

You should see output showing the disk usage on your local machine.

Now, let's try running a command only on the **webservers** group:

```bash
ansible webservers -i /home/lab/project/inventory -m command -a "uptime"
```

This command will show the uptime of all hosts in the **webservers** group.

The command module is the default module, so you can omit `-m command` if you're using this module. For example:

```bash
ansible dbservers -i /home/lab/project/inventory -a "free -m"
```

This command will show the memory usage on all hosts in the **dbservers** group.

> **Note:** The **command** module doesn't support shell variables or operations like `|`, `>`, `<`, `&`. For those, you'd need to use the **shell** module.

## Using Other Useful Modules in Ad-hoc Commands

While the **command** module is versatile, Ansible provides many other modules that can be very useful in ad-hoc commands. Let's explore a few of them.

### 1. The `copy` module:
This module is used to copy files from the local machine to the remote hosts. Let's create a simple file and copy it to all hosts:

```bash
echo "Hello from Ansible" > /home/lab/project/hello.txt
ansible all -i /home/lab/project/inventory -m copy -a "src=/home/lab/project/hello.txt dest=/tmp/hello.txt"
```

This command copies the `hello.txt` file from the local machine to the `/tmp` directory on all remote hosts.

### 2. The `file` module:
This module is used to manage files and directories. Let's create a directory on all webservers:

```bash
ansible webservers -i /home/lab/project/inventory -m file -a "path=/tmp/test_dir state=directory mode=0755"
```

This command creates a directory named `test_dir` in the `/tmp` directory on all webservers, with permissions set to `0755`.

### 3. The `setup` module:
This module is used to gather facts about the remote hosts. It's automatically run at the beginning of playbooks but can also be used in ad-hoc commands:

```bash
ansible dbservers -i /home/lab/project/inventory -m setup
```

This command will display a large amount of information about the hosts in the **dbservers** group. To limit the output, you can use filters:

```bash
ansible dbservers -i /home/lab/project/inventory -m setup -a "filter=ansible_distribution*"
```

This will only show facts related to the OS distribution.

## Summary

In this lab, you've learned about **Ansible ad-hoc commands** and how to use them effectively. Here are the key takeaways:

- Ad-hoc commands are quick, one-off tasks that can be run without creating a full playbook.
- The basic structure of an ad-hoc command is:

```bash
ansible [pattern] -m [module] -a "[module options]"
```

- The **ping** module is useful for checking connectivity to hosts.
- The **command** module allows you to run arbitrary commands on target hosts. It's the default module, so `-m command` can be omitted.
- Other useful modules for ad-hoc commands include:
  - **copy**: for copying files to remote hosts
  - **file**: for managing files and directories
  - **setup**: for gathering facts about remote hosts
- You can target specific groups of hosts by using the group names defined in your inventory file.

Ad-hoc commands are great for quick tasks, but for more complex or repeatable operations, **playbooks** are more suitable.

Ad-hoc commands are a powerful feature of Ansible that allow you to perform quick tasks across your infrastructure. They're particularly useful for troubleshooting, gathering information, or making quick changes. However, for more complex or repeatable tasks, it's better to use playbooks.

As you continue working with Ansible, practice using different modules in ad-hoc commands. Explore the Ansible documentation to learn about more modules and their uses. Remember, while ad-hoc commands are powerful, they don't provide the idempotency and reproducibility of playbooks, so use them judiciously in production environments.

```

This Markdown is formatted with all necessary code blocks for GitHub, making it easier to read and execute.
