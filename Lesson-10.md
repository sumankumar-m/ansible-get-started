# Ansible Conditionals and Loops

## Introduction

In this exercise, you will explore two powerful features of Ansible: conditionals and loops. These concepts allow you to create more dynamic and efficient playbooks by controlling task execution based on specific conditions and repeating tasks for multiple items. By the end of this lab, you'll understand how to use conditionals to make decisions in your playbooks and how to implement loops to perform repetitive tasks efficiently. This knowledge will help you create more flexible and powerful Ansible playbooks for managing your infrastructure.

Setting Up the Environment

Before we dive into Ansible conditionals and loops, let's set up our working environment. This step is crucial as it prepares the groundwork for all our subsequent tasks.

First, let's navigate to the project directory. In the terminal, type:

```bash
cd ~/project
```

This command changes your current directory to ~/project, which is our designated workspace for this lab.

Now, we'll create an inventory file. In Ansible, an inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. For this lab, we'll use a simple inventory that includes only the localhost.

Create a new file named inventory.ini:

```bash
nano inventory.ini
```

This command opens the nano text editor. If you're not familiar with nano, don't worry - it's a simple, user-friendly text editor. The cursor will be placed in the file, ready for you to type.

Now, let's add the following content to the file:

```ini
[local]
localhost ansible_connection=local
```

Let's break this down:

    [local] defines a group named "local"
    localhost is the name of the host
    ansible_connection=local tells Ansible to run commands locally instead of over SSH

To save the file and exit nano:

    Press Ctrl + X
    You'll be asked if you want to save the modified buffer. Press Y for yes.
    Press Enter to confirm the file name.

Next, let's create a directory for our playbooks:

```bash
mkdir playbooks
cd playbooks
```

The mkdir command creates a new directory named "playbooks", and then we use cd to move into this new directory.

Why are we doing this? Organizing your Ansible files into directories is a best practice. It keeps your project organized, especially as it grows larger and more complex.

By following these steps, you've now set up a basic Ansible environment. You have an inventory file that tells Ansible what hosts to work with, and a dedicated directory for your playbooks. This structure will make it easier to manage your Ansible projects as you learn and experiment with more complex features.
Introduction to Conditionals

Conditionals in Ansible allow you to control the execution of tasks based on certain conditions. This is incredibly useful when you need to perform different actions depending on the state of your system or the value of certain variables.

Let's create a simple playbook that demonstrates the use of conditionals:

```bash
nano conditional_example.yml
```

This command opens the nano text editor to create a new file named conditional_example.yml. Now, let's add the following content:

```yaml
---
- name: Conditional Example
  hosts: localhost
  gather_facts: yes
  tasks:
    - name: Check OS family
      debug:
        msg: "This is a Debian-based system"
      when: ansible_os_family == "Debian"

    - name: Check OS family (alternative)
      debug:
        msg: "This is not a Debian-based system"
      when: ansible_os_family != "Debian"
```

Let's break this down:

    --- at the top of the file indicates the start of a YAML document.
    name: Conditional Example gives a name to our playbook.
    hosts: localhost specifies that this playbook will run on the local machine.
    gather_facts: yes tells Ansible to collect information about the system before running tasks. This is important because we'll use these facts in our conditions.
    tasks: begins the list of tasks to be executed.

Each task uses the debug module to print a message, but the execution is controlled by the when clause:

    The first task will only run if the system is Debian-based (ansible_os_family == "Debian").
    The second task will only run if the system is not Debian-based (ansible_os_family != "Debian").

ansible_os_family is a fact gathered by Ansible about the target system. It's used here to demonstrate how conditionals work.

Save and exit the nano editor (Ctrl+X, then Y, then Enter).

Now, let's run the playbook:

```bash
ansible-playbook -i ../inventory.ini conditional_example.yml
```

This command tells Ansible to run our playbook. The -i ../inventory.ini option specifies the inventory file we created earlier.

You should see output indicating whether your system is Debian-based or not. Only one of the debug messages will be displayed, depending on your system's OS family.

This example demonstrates how conditionals can be used to make your playbooks adaptable to different environments. In real-world scenarios, you might use conditionals to install different packages on different OS types, or to skip certain tasks if a file already exists.

Remember, the power of conditionals lies in their ability to make your playbooks flexible and able to handle various scenarios without needing separate playbooks for each case.
Working with Multiple Conditions

In real-world scenarios, you often need to check multiple conditions before executing a task. Ansible allows you to combine multiple conditions using logical operators. Let's create another playbook to demonstrate this more advanced use of conditionals.

Create a new file named multiple_conditions.yml:

```bash
nano multiple_conditions.yml
```

Now, let's add the following content to the file:

```yaml
---
- name: Multiple Conditions Example
  hosts: localhost
  gather_facts: yes
  vars:
    check_memory: true
  tasks:
    - name: Check OS and Memory
      debug:
        msg: "This is a Debian-based system with more than 1GB of memory"
      when:
        - ansible_os_family == "Debian"
        - ansible_memtotal_mb > 1024
        - check_memory | bool

    - name: Print System Information
      debug:
        msg: "OS: {{ ansible_distribution }}, Memory: {{ ansible_memtotal_mb }} MB"
      when: ansible_distribution == "Ubuntu" or ansible_memtotal_mb < 2048
```

Let's break down this playbook:

    We define a variable check_memory at the playbook level. This could be set dynamically or passed as an extra variable when running the playbook.

    The first task uses multiple conditions:
        It checks if the OS family is Debian
        It checks if the total memory is more than 1024 MB (1 GB)
        It checks if the check_memory variable is true

    All these conditions must be true for the task to run. The | in check_memory | bool is a filter that converts the value to a boolean.

    The second task demonstrates the use of the or operator. It will run if either the distribution is Ubuntu OR the total memory is less than 2048 MB (2 GB).

    We're using more ansible facts here: ansible_distribution gives the specific distribution name, and ansible_memtotal_mb provides the total system memory in megabytes.

Save and exit the nano editor.

Now, let's run this playbook:

```bash
ansible-playbook -i ../inventory.ini multiple_conditions.yml
```

Observe the output. Depending on your system's characteristics, you might see one or both of the debug messages.

This example shows how you can create complex conditions to make your playbooks highly adaptable to different scenarios. You can combine various system facts, custom variables, and logical operators to fine-tune when your tasks should run.
Introduction to Loops

Loops in Ansible allow you to repeat a task multiple times with different values. This is extremely useful when you need to perform the same action with multiple items, such as creating several users, installing multiple packages, or creating several directories.

Let's create a playbook to demonstrate the use of loops. Create a new file named loop_example.yml:

```bash
nano loop_example.yml
```

Now, add the following content:

```yaml
---
- name: Loop Example
  hosts: localhost
  vars:
    fruits:
      - apple
      - banana
      - cherry
  tasks:
    - name: Print fruit names
      debug:
        msg: "Current fruit: {{ item }}"
      loop: "{{ fruits }}"

    - name: Create directories
      file:
        path: "/tmp/{{ item }}"
        state: directory
      loop:
        - dir1
        - dir2
        - dir3
```

Let's break this down:

    We define a variable fruits as a list of fruit names.

    The first task uses a loop to iterate over the fruits list. For each iteration, the current value is available as {{ item }}.

    The second task demonstrates how to use a loop with the file module to create multiple directories. We're creating three directories in the /tmp folder.

    Notice that we can use the loop directly in the task (as in the second task) or reference a variable (as in the first task).

Save and exit the nano editor.

Now, let's run this playbook:

```bash
ansible-playbook -i ../inventory.ini loop_example.yml
```

When you run this playbook, you'll see that the first task prints each fruit name, and the second task creates three directories in /tmp.

Loops are a powerful feature in Ansible that can significantly reduce the amount of repetitive code in your playbooks. They're especially useful when working with lists of items like users, packages, or files.
Advanced Loop Techniques

Ansible provides more advanced looping techniques that allow you to work with complex data structures and have more control over the loop process. Let's explore some of these techniques by creating a new playbook.

Create a new file named advanced_loops.yml:

```bash
nano advanced_loops.yml
```

Now, add the following content:

```yaml
---
- name: Advanced Loop Techniques
  hosts: localhost
  vars:
    users:
      - name: alice
        groups: ["developers", "testers"]
      - name: bob
        groups: ["managers", "developers"]
  tasks:
    - name: Create users with groups
      debug:
        msg: "Creating user {{ item.name }} with groups: {{ item.groups | join(', ') }}"
      loop: "{{ users }}"

    - name: Demonstrate loop_control
      debug:
        msg: "Processing item {{ index }} - {{ item }}"
      loop: ["a", "b", "c", "d"]
      loop_control:
        index_var: index

    - name: Loop over dictionary
      debug:
        msg: "{{ key }}: {{ value }}"
      loop: "{{ {'x': 1, 'y': 2, 'z': 3} | dict2items }}"
      vars:
        key: "{{ item.key }}"
        value: "{{ item.value }}"
```

Let's break down these advanced techniques:

    Looping over a list of dictionaries:
    The first task loops over the users list, where each item is a dictionary containing a name and a list of groups. We can access these nested elements using dot notation (item.name, item.groups).

    Using loop_control:
    The second task demonstrates loop_control, which allows us to change the name of the loop variable (default is item) and access the current loop index. Here, we use index_var: index to create a variable index that keeps track of the current iteration number.

    Looping over a dictionary:
    The last task shows how to loop over a dictionary. We use the dict2items filter to convert the dictionary into a list of key-value pairs that can be looped over. Then, we use item.key and item.value to access the dictionary's keys and values.

Save and exit the nano editor.

Now, let's run this playbook:

```bash
ansible-playbook -i ../inventory.ini advanced_loops.yml
```

When you run this playbook, you'll see how these advanced looping techniques work. The output will show:

    User creation messages with their respective groups
    Items being processed with their index
    Key-value pairs from the dictionary

These advanced techniques allow you to work with more complex data structures and give you finer control over your loops. They're particularly useful when dealing with nested data, when you need to keep track of the loop index, or when working with dictionaries.