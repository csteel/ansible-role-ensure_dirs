
ensure_dirs
===========


The **ensure_dirs** role is used as a dependancy to ensure for the creation or removal of directories on the local and/or remote system(s). The role also allows for setting the directory(ies) owner(s), group(s) and permissions and can be used to recursivly ensure permissions.


Usage Notes
-----------

When using a relative paths for remote directories the directory is created in, or is relative to, the ansible ssh users home directory.

When using with a relative path on the local system, for example "test/test1", on the local system the director will be created in, or relative too, the directory in which the ansible-playbook command is run.


Requirements
------------

In most cases, except for perhaps testing, you would use this role as a dependancy for another role via that roles meta/main.yml **dependancies []** option by passing one dictionary for the remote directories and / or a dictionary for any local directories you want to manipulate.


Role Variables
--------------

**ensure_dirs** uses two main variables in the form of Python dictionaries:

    ensure_dirs_on_remote []
    ensure_dirs_on_local []

The dependant role should create corresponding dictionary variables for one or both of the above variables. Generally these would be created in that roles **defaults** and/or **vars** directory.

For example, if you use the dependant roles default/main.yml you would describe the directories. In the example below the remote dictionary is called  **another_roles_remote_directories_description**. In **another_roles_remote_directories_description** you can describe as many directories as desired.

At minimum each directory description must include a **name** and a **path**. If it is not specified, the **state** will be set to **directory**. Valid options for **state** are **"directory"** to ensure for the creation of a directory, or **"absent"** to ensure for the removal of a directory. See [Ansible - file - Sets attributes of files](http://docs.ansible.com/ansible/file_module.html) for all possible options.

In our example **remote_directory_one** serves as a placeholder for our directory and **directory** the name and relative path of the directory. The placeholed **remote_directory_two** describes a directory using all the options currently supported for this role. 

    # file: roles/dependant_role/defaults/main.yml
    #
    # The my_remote_directories var contains the description of all of the
    # remote directories you want to ensure for
    #
    
    another_roles_remote_directories_description:
    
    # Directory definition using minimal amount of directory vars
    
      remote_directory_one:
    
        state   : "directory"
        path    : "dir_01"
    
    # Example using all possible directory variables:
    
      remote_directory_two:
    
        state   : "directory"
        path    : "relatve/subdir"
        owner   : "bob"
        group   : "sales"
        mode    : "0750"
        recurse : "true"

### roles/dependant_role/meta/main.yml

In the dependant roles meta/main.yml we add our dependency(ies) and pass the Python dictionary(ies) that define(s) our our local and/or remote directory(ies). It might look something like this:

Note: In this example we have moved the dependencies variable to the top of the roles meta/main.yml file so that it is readily noticed.

    ---
    # file: roles/dependant_role/meta/main.yml in dependant role
    dependencies:
    
    - { role: ensure_dirs, 
            ensure_dirs_on_remote: "{{ another_roles_remote_directories_description }}",
            ensure_dirs_on_local : "{{ another_roles_local_directories_description }}"
      }
    # other meta/main.yml content would follow...

### roles/dependant_role/defaults/main.yml example

In the dependant roles defaults directory we could use two groupings of variables, one
for the directories we want to ensure for on remote systems and one grouping for
directories we want on the local system where Ansible is being run.

    ---
    # file: roles/dependant_role/defaults/main.yml
    # 
    # * to ensure for the removal of a directory set the state to be "absent"
    # * to ensure for creation of directory set the state to be "directory"
    # * The name or label of the collection of variables describing a directory,
    #   for example **dir_home_test_1**, should be unique. Other than that it
    #   acts only as a place holder.
    
    another_roles_remote_directories_description:
    
      dir_home_test_1:
    
        state       : "absent"
        path        : "directory_to_remove"
        owner       : "{{ ansible_ssh_user }}"
        group       : "{{ ansible_ssh_user }}"
        mode        : "0644"
    
     dir_home_test_2:
    
        state       : "directory"
        path        : "creates_a_remote_relative_directory_in_the_ansible_ssh_users_home"
        owner       : "{{ ansible_ssh_user }}"
        group       : "{{ ansible_ssh_user }}"
        mode        : "0644"
    
    another_roles_local_directories_description:
    
      dir_home_test_3:
    
        state       : "absent"
        path        : "relative_to_ansible_project"
        owner       : "{{ ansible_ssh_user }}"
        group       : "{{ ansible_ssh_user }}"
        mode        : "0644"


Realworld Example
-----------------

### roles/shorewall/meta/main.yml

---
# file: roles/shorewall/meta/main.yml
dependencies:
-   { role: ensure_dirs, 
        ensure_dirs_on_remote: "{{ shorewall_ensure_dirs_on_remote }}",
        ensure_dirs_on_local : "{{ shorewall_ensure_dirs_on_local }}"
    }
```

### roles/shorewall/defaults/main.yml

​```yaml
---
# file: roles/shorewall/defaults/main.yml

shorewall_ensure_dirs_on_remote:

  home_user_sys_sw_rpms:

    state          : "directory"
    path           : "/home/ansible/sys/sw/rpms/shorewall"
    owner          : '{{ shorewall_deployment_user_name }}'
    group          : '{{ shorewall_deployment_user_name }}'
    mode           : '0755'

# this could be called `shorewall_ensure_dirs_on_local_all_os`

shorewall_ensure_dirs_on_local:

  ace_fetched_files_dir:

    state       : "directory"
    path        : "../ace_fetched_files"
    owner       : '{{ shorewall_controller_user_name }}'
    group       : '{{ shorewall_controller_user_name }}'
    mode        : '0755'
```


Dependencies
------------

None, **ensure_dirs** is normally used as a dependancy for other roles that require directory manipulations.


Example Playbook
----------------

Under most circumstance this role does not require it's own playbook.


## License

MIT

## Author Information

Christopher Steel
Systems Administrator
McGill Centre for Integrative Neuroscience
Montreal Neurological Institute
McGill University
3801 University Street
Montréal, QC, Canada H3A 2B4
Tel. No. +1 514 398-2494
E-mail: christopherDOTsteel@mcgill.ca
[MCIN](http://mcin-cnim.ca/), [theneuro.ca](http://theneuro.ca)

