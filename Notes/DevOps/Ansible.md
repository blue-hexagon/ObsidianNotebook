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
# Variables
`Do not fully comprehend`
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
# Hosts
```ansible
[<group>]
x.x.x.x

[<group>:vars]
ansible_user=root
ansible_ssh_private_key_file=<path>

```
# Capture Env Variable
```ansible
# tasks
- name: Capture env variable value for $HOME
  shell: 'source ~/.bash_profile/ && echo "${HOME}"'
  register: home_env_var

- debug: msg="Users home is {{ home_env_var.stdout }}"
```
# Misc
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
## Commands
`ansible-playbook <playbook> --syntax-check # check playbook for syntax error`
## Playbook Run Order
* Pre-tasks
* Roles
* Tasks
* Post-tasks