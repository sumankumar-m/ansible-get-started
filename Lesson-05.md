# Ansible Inventory Management Lab

## Introduction
In this lab, you will learn how to create and configure Ansible inventory files. Ansible uses inventory files to define and organize the hosts it manages. You will explore the basic structure of an inventory file, create groups, establish group hierarchies, and assign variables to groups. These skills are essential for effectively managing your infrastructure with Ansible.

## Creating a Basic Inventory

The lab environment has been pre-configured with SSH access to the local machine. You can use localhost as the target host for all Ansible operations in this lab.

### Step 1: Create a Basic Inventory File

Create a new file named `inventory` in the `/home/lab/project` directory:

```bash
nano /home/lab/project/inventory
```

Add the following content to the file:

```ini
localhost ansible_connection=local
```

#### Explanation:
- `localhost`: This is the name of the host. In this case, it's the local machine.
- `ansible_connection=local`: This tells Ansible to connect to the host locally, rather than via SSH. This is useful for managing the local machine.

Save the file and exit the editor (in nano, press `Ctrl+X`, then `Y`, then `Enter`).

### Step 2: Test the Inventory with the Ping Module

Now, let's test the inventory using Ansible's ping module. The ping module doesn't use the ICMP ping protocol; instead, it verifies that Ansible can connect to the host and execute Python code.

Run this command:

```bash
ansible -i inventory -m ping all
```

#### Explanation:
- `-i inventory`: Specifies the inventory file to use.
- `-m ping`: Tells Ansible to use the ping module.
- `all`: Targets all hosts in the inventory.

You should see output indicating a successful connection:

```json
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

This output confirms that Ansible successfully connected to the localhost and executed the ping module.

## Grouping Hosts

Ansible allows you to organize hosts into groups. This is useful for applying configurations or running tasks on multiple hosts simultaneously. Let's modify our inventory to include a group.

### Step 3: Create a Group

Edit the inventory file:

```bash
nano /home/lab/project/inventory
```

Update the content to:

```ini
[webservers]
localhost ansible_connection=local
```

#### Explanation:
- `[webservers]`: This defines a group called `webservers` that includes our localhost.
- Any hosts listed under this group are part of the `webservers` group.

Save and exit the editor.

### Step 4: Test the Group with the Ping Module

Now, let's test the group using the ping module:

```bash
ansible -i inventory -m ping webservers
```

You should see the same successful output as before, but now we're explicitly targeting the `webservers` group.

## Creating Group Hierarchies

Ansible supports nested groups, allowing you to create hierarchies. This is useful for organizing hosts based on their roles or environments. Let's add another group and create a hierarchy.

### Step 5: Create a Group Hierarchy

Edit the inventory file:

```bash
nano /home/lab/project/inventory
```

Update the content to:

```ini
[webservers]
localhost ansible_connection=local

[production:children]
webservers
```

#### Explanation:
- `[production:children]`: Creates a new group called `production` that includes all hosts in the `webservers` group. The `:children` suffix tells Ansible that this group is a group of groups, not a group of hosts.
- Any host in the `webservers` group is also implicitly in the `production` group.

Save and exit the editor.

### Step 6: Test the Group Hierarchy

Test the new group hierarchy:

```bash
ansible -i inventory -m ping production
```

This command should ping all hosts in the `production` group, which includes all hosts in the `webservers` group. In our case, it's still just the localhost, but this structure allows for powerful host management.

## Assigning Variables to Groups

Ansible allows you to assign variables to groups in the inventory file. These variables can be used in playbooks and templates, making your Ansible code more flexible and reusable.

### Step 7: Assign Variables to the Group

Edit the inventory file:

```bash
nano /home/lab/project/inventory
```

Update the content to:

```ini
[webservers]
localhost ansible_connection=local

[production:children]
webservers

[webservers:vars]
http_port=80
```

#### Explanation:
- `[webservers:vars]`: This assigns the variable `http_port` with a value of `80` to all hosts in the `webservers` group.

Save and exit the editor.

### Step 8: Create a Playbook to Display the Variable

Create a new playbook called `show_http_port.yml`:

```bash
nano /home/lab/project/show_http_port.yml
```

Add the following content:

```yaml
---
- name: Show HTTP Port
  hosts: webservers
  tasks:
    - name: Display HTTP Port
      debug:
        msg: "The HTTP port is {{ http_port }}"
```

#### Explanation:
- **Targets** the `webservers` group.
- **Tasks** include displaying the value of `http_port` using the `debug` module.
- The variable `{{ http_port }}` is used to reference the value we defined in the inventory.

Save and exit the editor.

### Step 9: Run the Playbook

Run the playbook:

```bash
ansible-playbook -i inventory show_http_port.yml
```

You should see output displaying the HTTP port value:

```json
PLAY [Show HTTP Port] ********************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Display HTTP Port] ******************************************************
ok: [localhost] => {
    "msg": "The HTTP port is 80"
}

PLAY RECAP ********************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

This demonstrates that the variable we defined in the inventory is accessible in our playbook.

## Summary

In this lab, you have learned the fundamentals of Ansible inventory management. You:
- Created a basic inventory file and learned how to use the Ansible ping module to verify connectivity.
- Explored how to group hosts and create group hierarchies, which are essential for organizing and managing larger infrastructures.
- Learned how to assign variables to groups and use them in a simple playbook.

These skills form the foundation of working with Ansible and will be invaluable as you move on to more complex automation tasks. Effective inventory management is key to scalable and maintainable Ansible projects.

---

### Next Steps:
- Experiment with more complex inventories, hierarchical structures, and advanced variable usage.
- Explore dynamic inventories and inventory plugins.
- Continue practicing with playbooks and expand your knowledge to automate a variety of tasks.

