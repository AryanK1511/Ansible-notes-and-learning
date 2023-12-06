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
