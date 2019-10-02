var-loader
=========

This role includes variables from specific directory.

Idea
----

While developing CI/CD for quite a big project, we found that it's very convenient to store environment variables in directories specific for each environment and then load them. Then we found that it's useful to split credentials and common variables into separate directories. The directories just have a structure like this:

    .
    ├── common-vars
    │   ├── project-A
    │   │   └── dev
    │   └── project-B
    │       └── dev
    ├── credentials
    │   ├── project-A
    │   │   ├── dev
    │   │   └── prod
    │   └── project-B
    │       ├── dev
    │       └── prod
    └── env-vars
        ├── project-A-env01
        ├── project-A-env02
        ├── project-A-prod
        ├── project-B-env01
        ├── project-B-env02
        └── project-B-prod


We put hosts from the same environment into one group (let's call it f.e. `project-a-env01`), and specify in the appropriate file under `group_vars` the directories to load variables from (like in example below).

The benefit of this approach is that the included variables always have the maximum priority (except of `--extra-vars`), so you've always got an understanding where to look for your environment paramters.

Role Variables
--------------

var_loader_root: here directories with environments are stored

var_loader_group_name: directory with environment variables, this path is relative to var_loader_root

var_loader_src: if specified, the role will load variables just from this directory

Role Defaults
-------------

var_loader_src: "{{ var_loader_root }}/{{ var_loader_group_name }}"

Example Playbook
----------------

This snippet will include all the `*.yml` files from playbook_dir/vars/dev:

    - hosts: group-containing-all-hosts-for-dev-env
      roles:
        - role: freehck.var_loader
          var_loader_root: "{{ playbook_dir }}/vars"
          var_loader_group_name: "dev"

This snippet will include all the `*.yml` files from /home/user/my-env

    - hosts: group-containing-all-hosts-for-dev-env
      roles:
        - role: freehck.var_loader
          var_loader_src: "/home/user/my-env"

This snippet will include all the `*.yml` files from: playbook_dir/creds/dev, playbook_dir/env-vars/dev, playbook_dir/common-vars/my-dev-env

    --- playbook.yml ---
    - hosts: group-containing-all-hosts-for-dev-env
      roles:
        # load credentials
        - role: freehck.var_loader
          var_loader_root: "{{ playbook_dir }}/creds"
          var_loader_group_name: "{{ credentials_library }}"
        # load variables specific for this environment group (group of similar environments)
        - role: freehck.var_loader
          var_loader_root: "{{ playbook_dir }}/common-vars"
          var_loader_group_name: "{{ env_group_name }}"
        # load environment specific variables
        - role: freehck.var_loader
          var_loader_root: "{{ playbook_dir }}/env-vars"
          var_loader_group_name: "{{ env_name }}"
          
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
