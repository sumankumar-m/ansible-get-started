2Introduction
In this lab, you will learn about Ansible configuration files and how to customize Ansible's behavior. Ansible uses configuration files to define various settings that control its operation. You'll explore the default configuration, create a custom configuration file, and understand how different configuration options affect Ansible's behavior. By the end of this lab, you'll have hands-on experience with configuring Ansible for different scenarios, which is crucial for tailoring Ansible to your specific needs and environment.

Certainly! Below are the necessary codes and configurations for the steps described in the lab.

### 1. **Check Default Ansible Configuration**
Default Configuration File Location: 

Ansible looks for configuration files in multiple locations. One common location is /etc/ansible/ansible.cfg, which may or may not exist in your system.

Check if there's a system-wide Ansible configuration file:

```bash
cat /etc/ansible/ansible.cfg
```

If the file does not exist, you will get an error, which is normal in many environments.

Checking the Custom Configuration: 
After creating the custom ansible.cfg, the ansible-config dump --only-changed command shows only the settings that differ from the default configuration. This confirms that your settings were applied correctly.


Next, to view all the current Ansible configuration settings, run the following command:

```bash
ansible-config dump
```

This will give you a long list of settings. Key ones to look for include:
- `DEFAULT_HOST_LIST`
- `DEFAULT_REMOTE_USER`
- `DEFAULT_BECOME_METHOD`

Press `Q` to exit the output.

### 2. **Create a Minimal Inventory File**

Now, create a simple inventory file that will define the localhost:

```bash
echo "localhost ansible_connection=local" > /home/lab/project/inventory
```

This command creates a minimal inventory file located at `/home/lab/project/inventory`.

### 3. **Create a Custom Ansible Configuration File**

Create a custom configuration file `ansible.cfg` in the `/home/lab/project` directory:

```bash
nano /home/lab/project/ansible.cfg
```

Add the following content:

```ini
[defaults]
inventory = /home/lab/project/inventory
remote_user = lab
host_key_checking = False
stdout_callback = yaml

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```

### Explanation of Configuration File:
- **[defaults]** section:
  - `inventory`: Defines the path to your inventory file.
  - `remote_user`: The default SSH user (in this case `lab`).
  - `host_key_checking`: Disabled for testing environments (set to `False` to skip SSH key verification).
  - `stdout_callback`: Sets the output format to YAML for better readability.

- **[privilege_escalation]** section:
  - `become`: Enables privilege escalation (i.e., allows using `sudo`).
  - `become_method`: Uses `sudo` for privilege escalation.
  - `become_user`: Specifies the user to become, in this case, `root`.
  - `become_ask_pass`: Disables the password prompt for `sudo`.

### 4. **Verify Custom Configuration**

To verify that Ansible is using your custom configuration file, run:

```bash
ansible-config dump --only-changed
```

This command will show only the configuration settings that have been changed from the default settings. You should see something like:

```bash
DEFAULT_BECOME(/home/lab/project/ansible.cfg) = True
DEFAULT_BECOME_ASK_PASS(/home/lab/project/ansible.cfg) = False
DEFAULT_BECOME_METHOD(/home/lab/project/ansible.cfg) = sudo
DEFAULT_BECOME_USER(/home/lab/project/ansible.cfg) = root
DEFAULT_HOST_LIST(/home/lab/project/ansible.cfg) = ['/home/lab/project/inventory']
DEFAULT_REMOTE_USER(/home/lab/project/ansible.cfg) = lab
DEFAULT_STDOUT_CALLBACK(/home/lab/project/ansible.cfg) = yaml
HOST_KEY_CHECKING(/home/lab/project/ansible.cfg) = False
```

### 5. **Create and Run a Playbook to Test the Configuration**

Create a playbook to test the custom configuration settings. Create a file called `test_config.yml`:

```bash
nano /home/lab/project/test_config.yml
```

Add the following content:

```yaml
---
- name: Test Custom Configuration
  hosts: all
  tasks:
    - name: Display remote user
      debug:
        msg: "Connected as user: {{ ansible_user }}"

    - name: Display privilege escalation info
      debug:
        msg: "Privilege escalation is {{ 'enabled' if ansible_become | default(false) else 'disabled' }}"

    - name: Show Ansible configuration
      debug:
        msg: "Inventory file: {{ lookup('config', 'DEFAULT_HOST_LIST') }}"

    - name: Check if become is enabled in ansible.cfg
      command: grep "become = True" /home/lab/project/ansible.cfg
      register: become_check
      changed_when: false
      failed_when: false

    - name: Display become setting from ansible.cfg
      debug:
        msg: "Become is {{ 'enabled' if become_check.rc == 0 else 'disabled' }} in ansible.cfg"
```

### Explanation of Playbook:
- The playbook runs the following tasks:
  - **Display remote user**: Shows the SSH user that Ansible is using.
  - **Display privilege escalation info**: Displays whether privilege escalation is enabled or not.
  - **Show Ansible configuration**: Displays the path to the inventory file configured in `ansible.cfg`.
  - **Check become setting**: Uses `grep` to search for the `become = True` line in the `ansible.cfg` file and reports whether privilege escalation is enabled.

Now, run the playbook:

```bash
ansible-playbook /home/lab/project/test_config.yml
```

### 6. **Update `ansible.cfg` to Avoid Deprecation Warning**

To avoid the deprecation warning related to the Python interpreter, add the following line to the `[defaults]` section in the `ansible.cfg` file:

```ini
interpreter_python = /usr/bin/python3
```

Your updated `ansible.cfg` should look like this:

```ini
[defaults]
inventory = /home/lab/project/inventory
remote_user = lab
host_key_checking = False
stdout_callback = yaml
interpreter_python = /usr/bin/python3

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```

### 7. **Run the Playbook Again**

After updating the `ansible.cfg` file, re-run the playbook to confirm that the deprecation warning has been resolved:

```bash
ansible-playbook /home/lab/project/test_config.yml
```

Now, you should see the output without the deprecation warning, and it should display your configuration settings correctly.

---

### Summary of Key Configuration Options:

- **[defaults]** section:
  - `inventory`: Path to the inventory file.
  - `remote_user`: Default SSH user for connections.
  - `host_key_checking`: Disables SSH host key verification.
  - `stdout_callback`: Sets output format to YAML for better readability.
  - `interpreter_python`: Specifies the Python interpreter to use (`python3`).

- **[privilege_escalation]** section:
  - `become`: Enables privilege escalation.
  - `become_method`: Method used for privilege escalation (e.g., `sudo`).
  - `become_user`: User to escalate to (usually `root`).
  - `become_ask_pass`: Disables the password prompt for `sudo`.

By customizing the Ansible configuration, you can tailor it to your environment and specific use cases.
