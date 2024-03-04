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
