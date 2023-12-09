# Basic Ansible Guide

- **Target Audience:** Systems engineers, IT administrators, and anyone in IT.
- **Common Challenges:** Repetitive tasks like creating hosts, applying configurations, patching servers, migrations, deploying applications, and security audits.
- **Solution:** Ansible - a powerful IT automation tool.

#### Ansible's Strengths
- **Ease of Learning:** Simple for everyone in IT.
- **Versatility:** Automates simple to complex deployments.
- **Script-Free Automation:** Replaces complex scripts with concise Ansible playbooks.
- **Flexibility:** Easily modify playbooks for local hosts, database servers, web servers, cloud environments, or disaster recovery setups.

#### Use Cases and Examples
- Restart hosts in a specific order (e.g., web servers before database servers) by creating a playbook for sequential restart, invoking it whenever needed.
- Setup a complex infrastructure spanning public/private clouds with hundreds of VMs. Provision VMs on Amazon and VMware, configure applications, set up communication, modify files, install applications, and configure firewall rules using playbooks.

#### Ansible Integration
- **Built-in Modules:** Numerous Ansible modules support various operations.
- **Environment Integration:** Easily integrate with CMDB databases for VM targeting or trigger automation from tools like ServiceNow.

- **Documentation:** [docs.ansible.com](https://docs.ansible.com)

#### Ansible Configuration Files

#### Introduction

- Ansible configuration files are located at `/etc/ansible/ansible.cfg` by default.
- The configuration file governs the default behavior of Ansible using parameters.
- Sections include default, inventory, privileges_escalation, ssh_connection, and colors.
- Options and values are specified within each section.

#### Configuration Options

- Default parameters include inventory file location, log file location, modules/roles/plugins directory, etc.
- Parameters may include the default location of the Ansible inventory file, log file location, modules/roles/plugins directory, whether facts should be gathered by default, SSH connection timeout, and the number of hosts targeted simultaneously.

#### Configuration Override

- Override default parameters by copying the default configuration file into specific directories.
- Make necessary changes in the copied files for specific requirements.
- Alternatively, use environment variables to specify the location of a configuration file before running an Ansible playbook.
- **Configuration file priority:** environment variable > local directory > user's home directory > default Ansible configuration file.

#### Use Case
Imagine you have different playbooks for web, database, and networking with specific settings for each. You can copy the default configuration file into each playbook directory and make necessary changes. You can also override the configuration file's location using the `ansible_config` environment variable.

#### Single Parameter Override

- Override a single parameter using an environment variable.
- Determine the environment variable by converting the parameter to uppercase and prefixing `ANSIBLE_`.
- Set environment variables using the `export` command for persistence.

#### Finding Configuration Information

- Use `ansible-config list` to see a list of configurations, their default values, and the values you can set.
- Use `ansible-config view` to see the currently active configuration file.
- Troubleshoot configurations using `ansible-config dump` to display current settings and their sources.

## Configuring Inventory in Ansible

#### Introduction

- Ansible works with one or multiple systems simultaneously.
- Connectivity to servers is established using SSH for Linux and PowerShell Remoting for Windows.
- Ansible is agentless, meaning no additional software needs to be installed on target machines.
- Information about target systems is stored in an inventory file.

#### Inventory File Format

- Default inventory file is located at `etc/ansible/hosts`.
- Sample inventory file in INI format.
- Servers listed one after the other.
- Servers can be grouped using square brackets.
- Multiple groups can be defined in a single inventory file.

#### Alias for Servers

- Servers can be referred to using an alias (e.g., web server or database server).
- Use `ansible_host` parameter to assign FQDN or IP address to the alias.
- Other inventory parameters: `ansible_connection`, `ansible_port`, `ansible_user`, `ansible_ssh_pass`.

#### Inventory Parameters

- `ansible_connection`: Defines how Ansible connects to the target server (SSH or WIN RM).
- `ansible_port`: Defines the port to connect to (default is 22 for SSH).
- `ansible_user`: Defines the user used to make remote connections (default is root for Linux).
- `ansible_ssh_pass`: Defines the SSH password for Linux.

#### Security Considerations

- Best practice is to set up SSH key-based passwordless authentication.
- For basic setup, a username and password can be used.

## Grouping in Ansible

#### Introduction

- INI and YAML inventory formats cater to different organizational needs.
- Choose the format that suits project complexity.
- Grouping in Ansible provides flexibility in managing servers.

#### INI Format

- Simple and straightforward organizational chart.
- Example of an INI format inventory.
  ```ini
  [web_servers]
  web1 ansible_host=192.168.1.10 ansible_user=your_username ansible_ssh_pass=your_password
  
  [database_servers]
  db1 ansible_host=192.168.1.20 ansible_user=your_username ansible_ssh_pass=your_password
  
  [load_balancers]
  lb1 ansible_host=192.168.1.30 ansible_user=your_username ansible_ssh_pass=your_password
  ```

#### YAML Format

- More structured and flexible organizational chart.
- Example of a YAML format inventory.
  ```yaml
  all:
  children:
    web_servers:
      hosts:
        web1:
          ansible_host: 192.168.1.10
          ansible_user: your_username
          ansible_ssh_private_key_file: /path/to/your/private/key

    database_servers:
      hosts:
        db1:
          ansible_host: 192.168.1.20
          ansible_user: your_username
          ansible_ssh_private_key_file: /path/to/your/private/key

    load_balancers:
      hosts:
        lb1:
          ansible_host: 192.168.1.30
          ansible_user: your_username
          ansible_ssh_private_key_file: /path/to/your/private/key

  ```

#### Grouping Feature

- Grouping allows categorization of servers based on roles, locations, etc.
- Efficiently manage and operate on a set of servers at once.
- Parent-to-child relationships for more structured organization.

#### Parent-Child Relationships

- Example of parent-to-child relationships in INI format.
  ```ini
  [web_servers]
  web1 ansible_host=192.168.1.10 ansible_user=your_username ansible_ssh_private_key_file=/path/to/your/private/key
  
  [database_servers]
  db1 ansible_host=192.168.1.20 ansible_user=your_username ansible_ssh_private_key_file=/path/to/your/private/key
  
  [load_balancers]
  lb1 ansible_host=192.168.1.30 ansible_user=your_username ansible_ssh_private_key_file=/path/to/your/private/key
  
  [all:children]
  web_servers
  database_servers
  load_balancers
  ```
- Use `:children` suffix to define child groups.
- Define common configurations at the parent group level and specific configurations at the child group level.

#### YAML Format for Grouping

- Use `hosts` keyword for defining groups and `children` keyword for parent-child relationships.

#### Grouping Strategies

- Group servers based on roles, locations, or any criteria relevant to the organization.
- Example: Grouping web servers together.
- Common configurations can be applied to the entire group.

#### Parent-Child Relationships

- Parent groups and child groups for more structured organization.

#### INI Format

- Groups defined using square brackets.
- Parent-child relationships defined using `:children` suffix.

#### YAML Format

- Groups defined using the `hosts` keyword.
- Parent-child relationships defined using the `children` keyword.

#### Summary

- Ansible's grouping feature simplifies server management.
- Choose grouping strategies based on project complexity.
- Utilize parent-to-child relationships for structured organization.

#### Geographic Grouping

- Grouping servers based on geographic location.
- Example: Web servers in the U.S. and Europe.

#### Configuration Flexibility

- Grouping allows flexibility in applying configurations.
- Common configurations at the parent group level.
- Location-specific configurations at the child group level.

## YAML Introduction

#### Overview

- Ansible Playbooks are written in YAML.
- YAML (YAML Ain't Markup Language) is a human-readable data serialization format.
- YAML is used for configuration data in Ansible.

#### YAML Basics

- Key-value pairs are defined with a colon, separating the key and value.
- Use a space followed by a colon to differentiate between key and value.
- Arrays are represented using a dash (-) for each element.
- Dictionaries group properties under an item with aligned spaces.
- Spaces are crucial in YAML to represent data correctly.

#### Key Concepts

- Lists contain dictionaries, and dictionaries can contain lists.
- Use dictionaries for single objects with properties.
- Use lists for multiple items of the same type of object.
- A list of dictionaries represents multiple objects with different properties.
- Understanding when to use a dictionary, list, or list of dictionaries is crucial.

#### Differences Between Dictionary and List

- Dictionaries are unordered collections, order of properties doesn't matter.
- Lists are ordered collections, order of items matters.
- Comments in YAML start with a hash (#) symbol.

#### Notes

- Dictionaries are unordered, lists are ordered.
- Order of properties in dictionaries doesn't matter.
- Order of items in lists matters.
- Lines starting with a hash are comments in YAML.

```yaml
# Example of a Dictionary
car_properties:
  color: "blue"
  model:
    name: "sedan"
    year: 2022
  transmission: "automatic"
  price: 25000

# Example of a Dictionary within Another Dictionary
detailed_car_properties:
  banana:
    calories: 105
    fat: 0.4g
    carbs: 27g
  apple:
    calories: 95
    fat: 0.3g
    carbs: 25g

# Example of a List of Strings
car_names:
  - "blue_sedan"
  - "red_convertible"
  - "black_suv"

# Example of a List of Dictionaries
cars_information:
  - color: "blue"
    model: "sedan"
    transmission: "automatic"
    price: 25000
  - color: "red"
    model: "convertible"
    transmission: "manual"
    price: 30000
  - color: "black"
    model: "suv"
    transmission: "automatic"
    price: 35000
```
## Ansible Variables

- Variables in Ansible store values that vary with different items.
- Used for storing information like hostnames, usernames, passwords, etc.
- Variables are defined in inventory files, playbooks, or dedicated variable files.

#### Examples of Variables in Inventory
```ini
[web_servers]
web1 ansible_host=192.168.1.10 ansible_user=your_username ansible_ssh_pass=your_password

[database_servers]
db1 ansible_host=192.168.1.20 ansible_user=your_username ansible_ssh_pass=your_password

[load_balancers]
lb1 ansible_host=192.168.1.30 ansible_user=your_username ansible_ssh_pass=your_password
```

### Using Variables in Playbooks

- Variables can be defined inside playbooks using the vars directive.
- Variables can also be stored in separate files dedicated to variables.
- Variable interpolation is done using double curly braces {{ variable_name }}.

#### Example Playbook with Variables
```yaml
- name: Configure DNS Entry
  hosts: web_servers
  vars:
    dns_server: 8.8.8.8
  tasks:
    - name: Add DNS Entry
      lineinfile:
        path: /etc/resolv.conf
        line: "nameserver {{ dns_server }}"
```

#### Organizing Variables in Host Variable File (web.yml)
```yaml
dns_server: 8.8.8.8
```

### Jinja2 Templating
- Ansible uses Jinja2 Templating to interpolate variables.
- Enclose variables in double curly braces: ```{{ variable_name }}```.

### Variable Types
1. String Variables: Sequences of characters.
  ```yaml
  username: admin
  ```
2. Number Variables: Hold integer or floating-point values.
  ```yaml
  max_connections: 100
  ```
3. Boolean Variables: Hold truthy or falsy values.
  ```yaml
  debug_mode: true
  ```
4. List Variables: Hold an ordered collection of values.
  ```yaml
  packages:
    - package1
    - package2
    - package3
  ```
5. Dictionary Variables: Hold key-value pairs.
  ```yaml
  user:
    name: John
    age: 30
  ```

### Variable Precedence

- Variables defined at different levels have different precedence.
- Host variables take precedence over group variables.
- Play-level variables override variables set at other levels.
- Extra variables passed through the command line have the highest precedence.

### Variable Scope

- Variable scope determines where a variable is accessible.
- Host Scope: Accessible within a play run for that host.
- Play Scope: Accessible only within the play it is defined.
- Global Scope: Variables accessible throughout the playbook execution.

### Magic Variables

Magic variables help access information across hosts.

**Examples:**
- hostvars: Get variables from other hosts.
- groups: Get all hosts under a group.
- group_names: Get groups a host is part of.
- inventory_hostname: Get the name configured for the host in the inventory file.

### Ansible Facts

- Facts are information collected by Ansible about target machines.
- Collected using the setup module.
- Stored in the ansible_facts variable.
- Facts include system details, network info, device info, date and time, etc.
- Facts are automatically gathered when running a playbook unless explicitly disabled.
- Disable facts gathering in a playbook using gather_facts: no.
- Facts gathering behavior is controlled by the Gathering setting in the Ansible configuration file.

```yaml
- name: Print Ansible Facts
  hosts: all
  tasks:
    - name: Debug Ansible Facts
      debug:
        var: ansible_facts
```

## Playbooks in Ansible

- Ansible Playbooks are Ansible's orchestration language.
- Playbooks define what Ansible should do, ranging from simple commands to complex infrastructure deployment.
- Written in YAML format.

### Playbook Structure

- A play defines activities on a single or a group of hosts.
- A task is a single action (e.g., executing a command, installing a package).

#### Example Playbook:

```yaml
- name: Play 1
  hosts: localhost
  tasks:
    - name: Print Date
      command: date
    - name: Run Script
      script: my_script.sh
    - name: Install httpd
      yum:
        name: httpd
        state: present
    - name: Start Web Server
      service:
        name: httpd
        state: started
```

### Playbook Development Tips
- Pay attention to YAML format.
- Host parameter indicates on which host the operations will run.
- Modules (e.g., command, script, yum, service) define actions.
- Playbooks are executed using ansible-playbook command.
- Crucial to verify playbooks before executing in production.
- **Check Mode:** ```ansible-playbook playbook.yml --check```. Executes without making actual changes.
- **Diff Mode:** ```ansible-playbook playbook.yml --diff```. Shows differences between current and expected state.
- **Syntax Check:** ```ansible-playbook playbook.yml --syntax-check```. Checks playbook syntax.

### Ansible Lint
- Command-line tool for linting Ansible playbooks, roles, and collections.
- Checks for errors, bugs, stylistic issues.
- Ensures consistency and quality across playbooks.

### Conditionals in Ansible
- Used to run tasks based on specified conditions.
- Examples: Checking OS family for package management.
```yaml
- name: Install NGINX
  hosts: all
  tasks:
    - name: Install NGINX on Debian
      apt:
        name: nginx
        state: present
      when: ansible_os_family == 'Debian'

    - name: Install NGINX on Red Hat
      yum:
        name: nginx
        state: present
      when: ansible_os_family == 'RedHat'
```

### Ansible Facts
- System-specific variables collected during playbook execution.
- Example: Using facts to determine OS and install specific software.
```yaml
- name: Install Software
  hosts: all
  tasks:
    - name: Install NGINX on Ubuntu 18.04
      apt:
        name: nginx
        state: present
      when:
        - ansible_facts['os_family'] == 'Debian'
        - ansible_facts['distribution_major_version'] == '18'
```

### Loops in Ansible
- Use loops to iterate over tasks multiple times.
- Example: Creating multiple users using a loop.
```yaml
- name: Create Users
  hosts: all
  tasks:
    - name: Create Users
      user:
        name: "{{ item.name }}"
        uid: "{{ item.uid }}"
      loop:
        - { name: 'Joe', uid: 1001 }
        - { name: 'George', uid: 1002 }
        - { name: 'Ravi', uid: 1003 }
```

### Visualization of Loop:
```
Task 1: Create User Joe with UID 1001
Task 2: Create User George with UID 1002
Task 3: Create User Ravi with UID 1003
```
- With Directives: Use loop for simple loops, and with_items for older playbooks.
- Advantage of with Directives: Extensive options available, e.g., with_files, with_url, with_mongodb.

```yaml
---
- name: Install packages
  hosts: your_target_hosts
  become: yes  # Run tasks with elevated privileges (sudo)

  # Using loop directive
  tasks:
    - name: Install packages using loop
      package:
        name: "{{ item }}"
        state: present
      loop:
        - httpd  # Apache HTTP server
        - nginx   # Nginx web server
        - mariadb # MariaDB database server

  # Using with_items (deprecated)
  tasks:
    - name: Install packages using with_items (deprecated)
      package:
        name: "{{ item }}"
        state: present
      with_items:
        - httpd
        - nginx
        - mariadb
```

## Ansible Modules Overview

#### System Modules
- Modify users, groups, IP tables, firewall configurations, logical volume groups, etc.
- Operations include starting, stopping, or restarting services.

#### Command Modules
- Execute commands or scripts on a host.
- Examples: command, expect, script.
- Use key-value pairs for module name and parameters.

#### File Modules
- Work with files.
- Examples: ACL, archive, un-archive, find, lineinfile, replace.

#### Database Modules
- Work with databases (MongoDB, MySQL, MSSQL, PostgreSQL).
- Add or remove databases, modify configurations.

#### Cloud Modules
- Vast collection for various cloud providers (Amazon, Azure, Docker, Google, Openstack, VMware).
- Tasks include creating/destroying instances, networking and security configuration, managing containers, etc.

#### Windows Modules
- Specifically for Ansible in a Windows environment.
- Examples: wincopy, wincommand, managing services, users, and registry.
- Ansible Directives and Examples

##### Command Module Example
```yaml
- name: Execute commands
  hosts: your_target_hosts
  tasks:
    - name: Run commands with loop
      command: "{{ item }}"
      loop:
        - date
        - cat /etc/resolv.conf

    - name: Change directory and run command
      command: cat resolv.conf
      args:
        chdir: /etc

    - name: Conditional command based on folder existence
      command: mkdir /path/to/folder
      args:
        creates: /path/to/folder
```

##### Script Module Example
```yaml
- name: Execute script on remote nodes
  hosts: your_target_hosts
  tasks:
    - name: Run script
      script: /path/to/script.sh
      args:
        arg1: value1
        arg2: value2
```

##### Service Module Example
```yaml
- name: Manage services
  hosts: your_target_hosts
  tasks:
    - name: Start PostgreSQL service
      service:
        name: postgresql
        state: started

    - name: Start HTTPD and Nginx services
      service:
        name: "{{ item }}"
        state: started
      loop:
        - httpd
        - nginx
```

##### Lineinfile Module Example
```yaml
- name: Add DNS server to resolv.conf
  hosts: your_target_hosts
  tasks:
    - name: Add DNS server entry
      lineinfile:
        path: /etc/resolv.conf
        line: "nameserver 8.8.8.8"
```

## Ansible Plugins Overview

#### Inventory Plugin
- Fetch real-time information about cloud resources.
- Ensures dynamic inventory management.

#### Module Plugin
- Provision cloud resources with custom configurations.
- Integrates with cloud provider's API for specific requirements.

#### Action Plugin
- Simplifies load balancer management.
- Handles API calls for configuring load balancing rules, SSL certificates, and health checks.

#### Other Plugins
- Lookup plugins: Fetch data from external sources.
- Filter plugins: Data manipulation and transformation.
- Connection plugins: Connect and communicate with target systems.
- Dynamic inventory plugins: Retrieve inventory information from various sources.
- Callback plugins: Hooks into Ansible's execution lifecycle.

### Modules and Plugins Index

#### Features and Benefits
- Search and Filtering: Find modules/plugins based on keywords, categories, or criteria.
- Detailed Documentation: Each entry includes user manuals, examples, and guidelines.
- Version Compatibility: Information on supported Ansible versions to avoid compatibility issues.
- Community Contributions: Collective wisdom from the Ansible community.

## Ansible Handlers

- Manual intervention for service restart after configuration changes.
- Ansible handlers automate the restart process.
- Handlers associated with specific events or notifications.
- Eliminate manual intervention, reduce errors, and improve efficiency.

#### Example Playbook with Handlers
```yaml
- name: Example Playbook
  hosts: your_target_hosts
  tasks:
    - name: Copy Application Code
      copy:
        src: /path/to/application
        dest: /app
      notify: Restart Application Service

  handlers:
    - name: Restart Application Service
      service:
        name: application_service
        state: restarted
```

## Ansible Roles

- Roles in Ansible for organizing and reusing code.
- Assign roles to servers for specific functionalities.
- Roles structure tasks, variables, defaults, handlers, and templates.
- Roles best practices for code organization.
- Share roles with the community via Ansible Galaxy.

### Creating Roles

- Use ansible-galaxy init to create a role skeleton.
- Organize tasks, variables, defaults, handlers, and templates directories.
- Place role in the roles directory of your playbook or at /etc/ansible/roles.

### Using Roles in Playbooks

- Specify roles in the roles directive in a playbook.
- Roles can be in the roles directory of the playbook or /etc/ansible/roles.
- Roles can be shared and reused within the Ansible community.

### Installing Roles from Ansible Galaxy

- Use ansible-galaxy install to install roles.
- Roles are extracted to the default roles directory (/etc/ansible/roles).

### Ansible Collections

- Ansible Collections package and distribute Ansible content.
- Include modules, roles, plugins, and related assets.
- Created by the community and vendors for expanded functionality.

### Benefits of Ansible Collections

- **Expanded Functionality:** Example: Using AWS collection for managing AWS resources.
- **Modularity and Re-usability:** Create custom collections with roles, modules, and plugins.
- Encourages code re-usability in different playbooks.
- **Simplified Distribution and Management:** Versioning and dependency management using a requirements.yml file.
- Centralized management of collections and dependencies.

## Jinja2 Templating in Ansible

- Templating engines customize content using variables.
- Examples include customizing emails, generating webpages, etc.
- In IT, widely used in Ansible to customize playbooks and configurations.

### Basics of Jinja2
- Fully-featured templating engine for Python.
- Variables and templates: e.g., customizing emails.
- Jinja2 filters: modify variable values (e.g., uppercase, lowercase).
- 
- Example:
  ```jinja
  The name is {{ my_name }}.              # Basic variable substitution
  The name is {{ my_name|upper }}.        # Uppercase filter
  The name is {{ my_name|lower }}.        # Lowercase filter
  The name is {{ my_name|title }}.        # Title case filter
  ```
  
#### List and Set Based Filters
```jinja
Smallest number: {{ numbers|min }}       # MIN filter
Largest number: {{ numbers|max }}        # MAX filter
Unique numbers: {{ numbers|unique }}     # Unique filter
Random number: {{ 1|random(100) }}       # Random filter
Joined words: {{ words|join(', ') }}     # Join filter
```
#### Conditionals and Loops in Jinja2
```jinja
{% for num in numbers %}
{{ num }}
{% endfor %}
```
#### Example of an if statement:
```jinja
{% if num == 2 %}
{{ num }}
{% endif %}
```

### Ansible's Extension of Jinja2

- Ansible extends Jinja2 with its own set of filters.
- Adds filters specific to infrastructure use cases.

#### File-related Filters
```jinja
File name from path: {{ '/etc/hosts'|basename }}           # Linux
File name from path: {{ 'C:\\Windows\\hosts'|win_basename }}  # Windows
Drive letter: {{ 'C:\\Windows\\hosts'|win_splitdrive|first }}
```

#### How Jinja2 Works in Playbooks
- Ansible processes playbooks through Jinja2 templating.
- Variables are interpolated before execution.

### Working with Templates in Playbooks

- Convert files to Jinja2 templates (e.g., index.html.j2).
- Use template module instead of copy for variable interpolation.

```yaml
- name: Copy Webpage
  hosts: web_servers
  tasks:
    - name: Copy Webpage Template
      template:
        src: index.html.j2
        dest: /var/www/html/index.html
```

#### Using Templates in Roles
Place templates in the templates directory within roles.

```
roles/
  my_role/
    templates/
      nginx.conf.j2
```
