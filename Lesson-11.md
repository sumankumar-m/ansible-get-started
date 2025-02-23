# Ansible Jinja2 Templates

## Introduction

A Jinja template is simply a text file. Jinja can generate any text-based format (HTML, XML, CSV, LaTeX, etc.). A Jinja template doesn’t need to have a specific extension: .html, .xml, or any other extension is just fine. For Ansible, in general, '.j2' extension is used.

Ansible uses Jinja2 templating to enable dynamic expressions and access to variables and facts. You can use templating with the template module. For example, you can create a template for a configuration file, then deploy that configuration file to multiple environments and supply the correct data (IP address, hostname, version) for each environment. You can also use templating in playbooks directly, by templating task names and more. You can use all the standard filters and tests included in Jinja2. Ansible includes additional specialized filters for selecting and transforming data, tests for evaluating template expressions, and Lookup plugins for retrieving data from external sources such as files, APIs, and databases for use in templating. This knowledge will enable you to create more dynamic and efficient Ansible playbooks and roles.

All templating happens on the Ansible control node before the task is sent and executed on the target machine. This approach minimizes the package requirements on the target (jinja2 is only required on the control node). It also limits the amount of data Ansible passes to the target machine. Ansible parses templates on the control node and passes only the information needed for each task to the target machine, instead of passing all the data on the control node and parsing it on the target.

### Note:
```
Files and data used by the template module must be utf-8 encoded.
```

There are a few kinds of delimiters. The default Jinja delimiters are configured as follows:

    {% ... %} for Statements

    {{ ... }} for Expressions to print to the template output

    {# ... #} for Comments not included in the template output


### Examples:
```
    {% if ABC=XYZ %}
        Do this!
      {% else %}
        Do something!
    {% endif %}
```

### Another example with for loop
```
  {% for domain in Domain_list %}
    search {{ domain }}
  {% endfor %}
```

## Understanding Jinja2 Basics

Jinja2 is a modern and designer-friendly templating language for Python, modeled after Django's templates. In Ansible, Jinja2 is used to template files, create dynamic play structures, and more.

Let's start by creating a simple Jinja2 template. First, navigate to your project directory:

```bash
cd ~/project
```

Now, let's create a directory for our templates:

```bash
mkdir templates
cd templates
```

Create a new file named hello.j2:

```bash
nano hello.j2
```

Add the following content:

```jinja2
Hello, {{ name }}!

Today is {{ ansible_date_time.date }}.
Your home directory is {{ ansible_env.HOME }}.
```

Save and exit the nano editor (Ctrl+X, then Y, then Enter).

In this template:

    {{ name }} is a variable that will be replaced with an actual value when the template is rendered.
    {{ ansible_date_time.date }} and {{ ansible_env.HOME }} are Ansible facts that will be automatically populated.

Now, let's create a playbook to use this template. Go back to the project root and create a new file named use_template.yml:

you may use your favorite editor, like nano or vi, to edit the files 

```bash
cd ~/project
nano use_template.yml
```

Add the following content:

```yaml
---
- name: Use Jinja2 template
  hosts: localhost
  vars:
    name: "Ansible Learner"
  tasks:
    - name: Create file from template
      template:
        src: templates/hello.j2
        dest: /tmp/hello.txt
```

This playbook uses the template module to render our Jinja2 template and create a file at /tmp/hello.txt.

Run the playbook:
```bash
ansible-playbook use_template.yml
```

You should see the rendered template with the variable and facts replaced with actual values.

## Using Jinja2 Filters

Jinja2 filters are functions that can be applied to variables. They are separated from the variable by a pipe symbol (|). Ansible includes many built-in filters, and you can also create custom ones.

Let's create a new template that demonstrates some common filters. Create a new file named filters.j2:

```bash
cd ~/project/templates
nano filters.j2
```

Add the following content:

```jinja2
1. Uppercase filter: {{ name | upper }}
2. Length filter: {{ name | length }}
3. Default filter: {{ optional_var | default('No value provided') }}
4. Join filter: {{ fruits | join(', ') }}
5. Random item: {{ fruits | random }}
6. First item: {{ fruits | first }}
7. JSON encode: {{ complex_data | to_json }}

```

Now, let's create a playbook to use this template. Go back to the project root and create a new file named use_filters.yml:

```bash
cd ~/project
nano use_filters.yml
```

Add the following content:

```yml
---
- name: Use Jinja2 filters
  hosts: localhost
  vars:
    name: "ansible learner"
    fruits:
      - apple
      - banana
      - cherry
    complex_data:
      key1: value1
      key2:
        - item1
        - item2
  tasks:
    - name: Create file from template
      template:
        src: templates/filters.j2
        dest: /tmp/filters.txt

    - name: Display file contents
      command: cat /tmp/filters.txt
      register: file_contents

    - name: Show file contents
      debug:
        var: file_contents.stdout_lines
```

This playbook applies various Jinja2 filters to our variables and displays the results.

```bash
ansible-playbook use_filters.yml
```

The output will show you how each filter modifies the input data. This demonstrates how filters can be used to manipulate data within your templates.


## Jinja2 Conditionals

Jinja2 allows you to use conditional statements in your templates. This is particularly useful when you need to include or exclude content based on certain conditions.

Create a new template file named conditionals.j2:

```bash
cd ~/project/templates
nano conditionals.j2
```

Add the following content:

```jinja2
{% if user_type == 'admin' %}
Welcome, Administrator!
You have full access to the system.
{% elif user_type == 'user' %}
Welcome, User!
You have limited access to the system.
{% else %}
Welcome, Guest!
You have read-only access to the system.
{% endif %}

{% if fruits %}
Available fruits:
{% for fruit in fruits %}
  - {{ fruit }}
{% endfor %}
{% else %}
No fruits available.
{% endif %}
```

Now, let's create a playbook to use this template. Go back to the project root and create a new file named use_conditionals.yml:

```bash
cd ~/project
nano use_conditionals.yml
```

 Add the following content:

```yaml
---
- name: Use Jinja2 conditionals
  hosts: localhost
  vars:
    user_type: "admin"
    fruits:
      - apple
      - banana
      - cherry
  tasks:
    - name: Create file from template (admin)
      template:
        src: templates/conditionals.j2
        dest: /tmp/conditionals_admin.txt

    - name: Create file from template (user)
      template:
        src: templates/conditionals.j2
        dest: /tmp/conditionals_user.txt
      vars:
        user_type: "user"

    - name: Create file from template (guest)
      template:
        src: templates/conditionals.j2
        dest: /tmp/conditionals_guest.txt
      vars:
        user_type: "guest"
        fruits: []

    - name: Display file contents
      command: "cat {{ item }}"
      loop:
        - /tmp/conditionals_admin.txt
        - /tmp/conditionals_user.txt
        - /tmp/conditionals_guest.txt
      register: file_contents

    - name: Show file contents
      debug:
        var: file_contents.results[item].stdout_lines
      loop: [0, 1, 2]
```

This playbook creates three different files using the same template but with different variable values, demonstrating how the conditional statements in the template work.

Run the playbook:

```bash
ansible-playbook use_conditionals.yml
```

The output will show you how the template content changes based on the different conditions.

## Jinja2 Loops

Jinja2 provides looping constructs that allow you to iterate over lists and dictionaries. This is particularly useful when you need to generate repetitive content based on variable data.

Create a new template file named loops.j2:

```bash
cd ~/project/templates
nano loops.j2
```

Add the following content:

```jinja2
1. Simple loop:
{% for item in simple_list %}
   - {{ item }}
{% endfor %}

2. Loop with index:
{% for item in simple_list %}
   {{ loop.index }}. {{ item }}
{% endfor %}

3. Loop over a dictionary:
{% for key, value in simple_dict.items() %}
   {{ key }}: {{ value }}
{% endfor %}

4. Nested loop:
{% for category, items in nested_dict.items() %}
   {{ category }}:
   {% for item in items %}
     - {{ item }}
   {% endfor %}
{% endfor %}

5. Conditional in loop:
{% for item in simple_list %}
   {% if loop.first %}First item: {% endif %}
   {% if loop.last %}Last item: {% endif %}
   {{ item }}
{% endfor %}
```

Now, let's create a playbook to use this template. Go back to the project root and create a new file named use_loops.yml:

```bash
cd ~/project
nano use_loops.yml
```

Add the following content:

```yaml
---
- name: Use Jinja2 loops
  hosts: localhost
  vars:
    simple_list:
      - apple
      - banana
      - cherry
    simple_dict:
      name: John Doe
      age: 30
      city: New York
    nested_dict:
      fruits:
        - apple
        - banana
      vegetables:
        - carrot
        - potato
  tasks:
    - name: Create file from template
      template:
        src: templates/loops.j2
        dest: /tmp/loops.txt

    - name: Display file contents
      command: cat /tmp/loops.txt
      register: file_contents

    - name: Show file contents
      debug:
        var: file_contents.stdout_lines
```

This playbook demonstrates various ways to use loops in Jinja2 templates.

Run the playbook:

```bash
ansible-playbook use_loops.yml
```

The output will show you how the different loop constructs in the template generate content based on the provided variables.


## Advanced Jinja2 Techniques

In this final step, we'll explore some advanced Jinja2 techniques, including macros and filters. Macros are reusable template snippets, similar to functions in programming languages.

Create a new template file named advanced.j2:

```bash
cd ~/project/templates
nano advanced.j2
```

Add the following content:

```jinja2
{% macro render_list(title, items) %}
{{ title }}:
{% for item in items %}
  - {{ item }}
{% endfor %}
{% endmacro %}

1. Using a macro:
{{ render_list("Fruits", fruits) }}

{{ render_list("Vegetables", vegetables) }}

2. Using set:
{% set greeting = "Hello, " + name + "!" %}
{{ greeting }}

3. Custom filter (defined in playbook):
{{ complex_data | to_nice_json }}

4. Whitespace control:
{% for item in fruits -%}
    {{ item }}
{%- if not loop.last %}, {% endif %}
{%- endfor %}

5. Raw block (no processing):
{% raw %}
This {{ will_not_be_processed }}
{% endraw %}

6. Include another template:
{% include 'simple_include.j2' %}
```

Now, let's create the simple include file. Create a new file named simple_include.j2:

```bash
nano simple_include.j2
```

Add the following content:

```jinja2
This is content from an included template.
Current user: {{ ansible_user_id }}
```

Now, let's create a playbook to use this advanced template. Go back to the project root and create a new file named use_advanced.yml:

```bash
cd ~/project
nano use_advanced.yml
```

Add the following content:

```yaml
---
- name: Use advanced Jinja2 techniques
  hosts: localhost
  vars:
    name: "Ansible Expert"
    fruits:
      - apple
      - banana
      - cherry
    vegetables:
      - carrot
      - potato
      - broccoli
    complex_data:
      key1: value1
      key2:
        - item1
        - item2
    custom_filters:
      to_nice_json: "{{ '(' ~ ((complex_data | to_json) | replace('\"', '\\\"')) ~ ')' | from_json | to_nice_json }}"
  tasks:
    - name: Create file from template
      template:
        src: templates/advanced.j2
        dest: /tmp/advanced.txt

    - name: Display file contents
      command: cat /tmp/advanced.txt
      register: file_contents

    - name: Show file contents
      debug:
        var: file_contents.stdout_lines
```

This playbook demonstrates advanced Jinja2 techniques, including macros, custom filters, whitespace control, and including other templates.

Run the playbook:

```bash
ansible-playbook use_advanced.yml
```
The output will show you how these advanced Jinja2 techniques work in practice.