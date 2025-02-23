# Ansible Playbook - My First Playbook

## Introduction

In this repository, you will find an example of a basic Ansible playbook. This playbook creates a directory, writes a file into it, and displays the content of the file with the current timestamp.

Ansible playbooks are YAML files that define a set of tasks to be executed on remote or local hosts. This specific playbook demonstrates how to:

- Define and use variables in an Ansible playbook.
- Create directories and files using the `file` and `copy` modules.
- Use the `debug` module to display dynamic content.

## Prerequisites

Before running the playbook, ensure that you have the following:

- Ansible installed on your local machine. If not, you can follow the [Ansible installation guide](https://docs.ansible.com/ansible/latest/installation_guide/index.html).

## Playbook Code

Create a new file `first_playbook.yml` in your `/home/lab/project/` directory and add the following content:

```yaml
---
- name: My First Playbook
  hosts: localhost
  connection: local
  vars:
    dir_path: /home/lab/project/test_directory
    file_content: "Hello from Ansible! The time is {{ ansible_date_time.iso8601 }}"

  tasks:
    - name: Create a directory
      file:
        path: "{{ dir_path }}"
        state: directory
        mode: "0755"

    - name: Create a file
      copy:
        content: "{{ file_content }}"
        dest: "{{ dir_path }}/hello.txt"

    - name: Display file content
      debug:
        msg: "The content of the file is: {{ file_content }}"
```

### Breakdown of Playbook

1. **YAML Structure**: The playbook begins with `---` which marks the start of the YAML document.
2. **Name**: `My First Playbook` is a description of the playbook's purpose.
3. **Hosts**: `localhost` specifies that this playbook will run on the local machine. In real-world scenarios, you may define remote hosts or groups.
4. **Connection**: `local` tells Ansible to execute the playbook locally instead of through SSH.
5. **Variables**: 
   - `dir_path`: Path to the directory to be created.
   - `file_content`: Content of the file, which includes a timestamp.
6. **Tasks**:
   - **Create Directory**: Uses the `file` module to create a directory at the specified path with the desired permissions.
   - **Create File**: Uses the `copy` module to write the specified content into a file.
   - **Display File Content**: Uses the `debug` module to output the content of the file to the console.

## Running the Playbook

To run the playbook, execute the following command:

```bash
ansible-playbook /home/lab/project/first_playbook.yml
```

You should see output similar to the following:

```
PLAY [My First Playbook] ******************************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [Create a directory] ******************************************************
changed: [localhost]

TASK [Create a file] ***********************************************************
changed: [localhost]

TASK [Display file content] ****************************************************
ok: [localhost] => {
    "msg": "The content of the file is: Hello from Ansible! The time is 2023-06-09T12:34:56Z"
}

PLAY RECAP *********************************************************************
localhost                  : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

- The **"changed"** status indicates that the directory and file were successfully created.
- The **"msg"** field in the output of the debug task will display the file content along with the current timestamp.

## Verifying the Results

To verify that the directory and file were created, use the following commands:

```bash
ls -l /home/lab/project/test_directory
cat /home/lab/project/test_directory/hello.txt
```

- The `ls` command will list the contents of the `test_directory`.
- The `cat` command will display the content of the `hello.txt` file.

## Key Takeaways

- **YAML Format**: Ansible playbooks use YAML format to define automation tasks.
- **Variables**: Variables can make your playbook more flexible by allowing easy changes to paths, file contents, and other values.
- **Modules**: In this example, we used the `file`, `copy`, and `debug` modules. Ansible has many modules for different tasks, and you can use them to perform almost any system management task.
- **Task Execution**: Each task in the playbook is executed in order, and Ansible will report on the results after each task completes.

## Conclusion

This lab demonstrated how to create and execute a basic Ansible playbook. You learned about the key elements of playbooks, including variables, tasks, and modules. You also saw how to use Ansible’s debug module to output dynamic information during playbook execution. 

As you progress with Ansible, playbooks will become a key tool in automating complex infrastructure tasks. Experiment with modifying the playbook, adding new tasks, or exploring more Ansible modules to gain further experience.

## Next Steps

- Experiment with adding more tasks to the playbook.
- Explore other Ansible modules such as `yum`, `apt`, `service`, `template`, and `shell` for different types of system management tasks.
- Learn about playbook roles, conditionals, and loops for more advanced automation.

The Ansible documentation is an excellent resource to continue learning: [Ansible Documentation](https://docs.ansible.com/)
