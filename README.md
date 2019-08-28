env-loader
=========

This role includes variables from specific directory.

Role Variables
--------------

env_loader_root: here directories with environments are stored
env_loader_env_name: directory with environment variables, this path is relative to env_loader_root
env_loader_src: if specified, the role will load variables just from this directory

Role Defaults
-------------

env_loader_src: "{{ env_loader_root }}/{{ env_loader_env_name }}"

Example Playbook
----------------

This snippet will include all the `*.yml` files from {{ playbook_dir }}/vars/dev:

	- hosts: group-containing-all-hosts-for-dev-env
	  roles:
		  - role: env-loader
		    env_loader_root: "{{ playbook_dir }}/vars"
			env_loader_env_name: "dev"

This snippet will include all the `*.yml` files from /home/user/my-env

	- hosts: group-containing-all-hosts-for-dev-env
	  roles:
		  - role: env-loader
		    env_loader_src: "/home/user/my-env"

This snippet will include all the `*.yml` files from: {{ playbook_dir }}/creds/dev, {{ playbook_dir }}/env-vars/dev, {{ playbook_dir }}/common-vars/my-dev-env

	--- playbook. yml ---
	- hosts: group-containing-all-hosts-for-dev-env
	  roles:
		  # load credentials
		  - role: env-loader
			env_loader_root: "{{ playbook_dir }}/creds"
			env_loader_env_name: "{{ credentials_library }}
		  # load environment specific variables
		  - role: env-loader
			env_loader_root: "{{ playbook_dir }}/env-vars"
			env_loader_env_name: "{{ env_name }}"
		  # load variables specific for this environment group (group of similar environments)
		  - role: env-loader
			env_loader_root: "{{ playbook_dir }}/common-vars"
			env_loader_env_name: "{{ env_group_name }}"
	
	--- group_vars/group-containing-all-hosts-for-dev-env.yml ---
	credentials_library: "dev"
	env_group_name: "dev"
	env_name: "my-dev-env"


License
-------

MIT

Author Information
------------------

Dmitrii Kashin, <freehck@freehck.ru>
