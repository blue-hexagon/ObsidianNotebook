# Adhoc Commands
`ansible -i <invetory_file> <group> -m command -a <command> == ansible -i <invetory_file> <group> -a <command>`
	
use the `shell` module, if you need to use ad-hoc commands that utilize piping, but try to avoid it as it's not idempotent.

# Loop construct
```ansible
- name: Create users
  user:
    name: "{{ item }}"
    state: present
  with_items:
    - user1
    - user2
    - user3
```
# With Items
```ansible
vars:
  users:
    - user1
    - user2
    - user3

tasks:
- name: Create users
  user:
    name: "{{ item }}"
    state: present
  with_items: "{{ users }}"
```
# Loading vars.yml files
```ansible
- hosts: <group>
  become: yes
  
  vars:
    - vars.yml
    - another_vars.yml
```
# Handler
* Handlers are only called, if the task results in a change.
* Handlers are run at the end of the playbook.
	* If you want to run it immediately after the task that notifies the handler, use `flush_handlers`.
* Now what if you don't flush handlers, and your script fails after the next task? Answer: the handler wont get called! - You can fix that by calling the playbook wiith `--force-handlers`.
```ansible
---
...ansible

	handlers:
		- name: <handler-task-name>
		  <module>: <do-something>
	tasks:
		- name: <task-name>
		  <module>: <do-something>
		  notify: |
		    <handler-task-name> # Will trigger the handler with `handler-task-name`
		- name: Make sure handler is flushed immediately.
		  meta: flush_handlers
```
Handlers can also notify other handlers by chaining them.
```ansible
handlers:
  - name: restart apache
    service:
      name: httpd
      state: restarted
    notify: restart memcached
    
  - name: restart memcached
    service:
      name: memcached
      state: restarted
```
# Conditionals
`when`, `changed_when`, `failed_when`
```ansible
- name: Start NodeJS app
  command: ...
  when: <registered_var>.stdout.find(<string>) == -1
```
Use `changed_when: false` to enforce idempotency when using the command module for something that doesn't change anything.
# Tags
Run tasks with specific tags, from a playbook: 
```ansible
  - name: restart memcached
    service:
      name: memcached
      state: restarted
    tags: [restartable, services]
```
`ansible-playbook ... --tags=services`
# Try-Except Blocks
```ansible
 tasks:
   - name: Handle the error
     block:       
       - name: Print a message
         ansible.builtin.debug:
           msg: 'I execute normally'

       - name: Force a failure
         ansible.builtin.command: /bin/false

       - name: Never print this
         ansible.builtin.debug:
           msg: 'I never execute, due to the above task failing, :-('
     rescue:   
       - name: Print when errors
         ansible.builtin.debug:
           msg: 'I caught an error, can do stuff here to fix it, :-)'
	 always:       
	   - name: Always do this
         ansible.builtin.debug:
           msg: "This always executes, :-)"
```
# Importing Tasks
*Can be used for importing tasks, handlers and pre-tasks etc.*
```ansible
handlers:
  - import_tasks: handlers/apache.yml
```
**Use `include_tasks` if you need to use dynamic variables**
# Import Playbook
Use at the top-level scope.
```ansible
- import_playbook: <playbook-name>.yml
```
# Variables
## Task Environment Variables
```ansible
vars:
  proxy_vars:
    http_proxy: ...
    https_proxy: ...
```
## Global Envrionment Variables
These will apply to each task in a play.
```ansible
proxy_vars:
  http_proxy: ...
  https_proxy: ...
```
## Vars Files
Used to load variables from multiple files, instead of from a single file.
```
vars_files:
  - file_path_one/file.yml
  - file_path_two/file.yml
```
## /vars vs. /defaults
`defaults/` are easy to override and most commonly used to modify behaviour (i.e., port number or default user).
`vars/` are used by the role and not likely to be changed (i.e. a list of packages, lookup table, machine image).
### Include Vars
Used for dynamic inventory loading.
```ansible
pre_tasks:
  - debug: var=ansible_os_family
  - name: Load the first vars file found
    include_vars:
      - "vars/apache_{{ ansible_os_family }}.yml"
      - "vars/apache_default.yml"
```
# Hosts
```ansible
[<group>]
x.x.x.x

[<group>:vars]
ansible_user=root
ansible_ssh_private_key_file=<path>

```
# Capture (Env) Variable
```ansible
# tasks
- name: Capture env variable value for $HOME
  shell: 'source ~/.bash_profile/ && echo "${HOME}"'
  register: home_env_var

- debug: msg="Users home is {{ home_env_var.stdout }}"
```
# Roles
Directory structure:
```plaintext
roles/
  <role-name>
    meta/
      main.yml # <-- `dependencies: []` 
    tasks/
      main.yml # <-- `task list`
```

Now you can add roles to a playbook, for executing the roles tasks (add them at the same scope level as tasks and vars):
`roles: [<role-name>]`
You can also use roles in the `tasks` scope, by including roles in the tasks list - this way you have more control over the execution order. To do this use: `- include_role: <role-name>`.
## Options
There are more options, like adding `vars`, `files`, `defaults` folders and more.
# Fail & Assert
```ansible
---
- hosts: 127.0.0.1
  gather_facts: no
  connection: local

  vars:
    should_fail_via_fail: true
    should_fail_via_assert: true
    should_fail_via_complex_assert: true

  tasks:
    - name: Fail if conditions warrant a failure.
      fail:
        msg: "There was an epic failure"
      when: should_fail_via_fail
      
    - name: Stop playbook if an assertion isn't validated.
      assert:
        that: "should_fail_via_assert != true"
        
    - name: Assertions can have contains conditions.
      assert:
        that:
          - should_fail_via_fail != true
          - should_fail_via_assert != true
          - should_fail_via_complex_assert != true
```
# Misc
## Level of Testing
1. Is this valid YAML
2. Is this valid Ansible - is it a valid playbook
3. Does this Ansible playbook run in a fresh environment
4. Does this Ansible playbook run idempotently and will it run against production multiple times without breaking things.
5. Staing/pre-prod environment that runs parallell to the production environment and rebuild that environment from scratch every time you make a change to make sure everything works correctly.
![[ansible-testing-spectrum.png]]
## Ansible Galaxy
```ansible
# Install a role globally
ansible-galaxy role install <role> # <gerlingguy.homebrev>
```
Specifying a role path in `ansible.cfg` tells Ansible where to find and install roles - use: `roles_path = ./roles`. Chain paths together like in Bash, using `:` as a seperator.
### Passing requirements.yml
`requirements.yml`:
```ansible
---
roles:
  - name: elliotweiser.osx-command-line-tools
    version: 2.3.0
  - name: gerlingguy.homebrev
    version: 3.1.0
```
Followed by: `ansible-galaxy -r requirements.yml` where `-r` specifies that we want to use a requirements file.
### Role Initialization
- Run: `ansible-galaxy role init <role-name>`
- Using `molecule`, run: `molecule init role <role-name>`
## Molecule
`molecule test`
`molecule login`
Ep. 7: https://www.youtube.com/watch?v=FaXVZ60o8L8
## Jinja / YAML
YAML and Jinja doesn't distinguish between single and double-quotes - you can use both.
You can turn off become on a task if you don't need it for that task - `become: false`.
You can define a task, without specifying a name:
```ansible
  - name: another task
    <command>: ...
  - debug: msg="A task without a name"
  - name: another task
    <command>: ...
```
## Python Dict-Style Properties
* `{{ var.x }}` is equal to `{{ var['x'] }}`
* Works with both YAML and Jinja
* Useful when you have properties like `rsa-key` as you cannot do `x.rsa-key`.
## Commands
`ansible-playbook <playbook> --syntax-check # check playbook for syntax error`
## Playbook Run Order
* Pre-tasks
* Roles
* Tasks
* Post-tasks