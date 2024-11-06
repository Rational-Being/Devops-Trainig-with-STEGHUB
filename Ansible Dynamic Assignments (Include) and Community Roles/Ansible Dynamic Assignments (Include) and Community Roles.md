
# Ansible Dynamic Assignments (Include) and Community Roles

#### IMPORTANT NOTICE: Ansible is an actively developing software project, visit the Ansible Documentation for the latest updates on modules and their usage.

The last two projects have already equipped me with some knowledge and skills on Ansible, allowing me to perform configurations using playbooks, roles, and imports. Now, I will continue configuring my UAT servers, learning and practicing new Ansible concepts and modules.

In this project, I will be introduced to dynamic assignments by using the `include` module.

Now, what is the difference between static and dynamic assignments?

Well, from the previous project, I can already tell that static assignments use the `import` Ansible module. The module that enables dynamic assignments is `include`.

Hence,

- `import` = Static
- `include` = Dynamic

When the `import` module is used, all statements are pre-processed at the time playbooks are parsed. Meaning, when I execute the `site.yml` playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that, during actual execution, if any statement changes, such statements will not be considered. Hence, it is static.

On the other hand, when the `include` module is used, all statements are processed only during execution of the playbook. Meaning, after the statements are parsed, any changes to the statements encountered during execution will be used.

Take note that in most cases, it is recommended to use static assignments for playbooks because they are more reliable. With dynamic ones, it is harder to debug playbook problems due to their dynamic nature. However, I can use dynamic assignments for environment-specific variables as I will be introducing in this project.


## Introducing Dynamic Assignment Into Our structure

In your https://github.com/<your-name>/ansible-config-mgt GitHub repository start a new branch and call it `dynamic-assignments`.

![01 new branch](https://github.com/user-attachments/assets/f1a34d11-b7e9-4d96-9377-d93cbe9c0b96)

Create a new folder, name it `dynamic-assignments`. Then inside this folder, create a new file and name it `env-vars.yml`. We will instruct `site.yml` to include this playbook later. For now, let us keep building up the structure.

![02 create directories](https://github.com/user-attachments/assets/edfe7422-f8d3-4f81-a191-0e38ed676985)

Your GitHub shall have following structure by now.
```
├── dynamic-assignments  
│   └── env-vars.yml  
├── inventory  
│   └── dev  
│   └── stage  
│   └── uat  
│   └── prod  
└── playbooks  
│   └── site.yml  
└── roles (optional folder)  
│   └──...(optional subfolders & files)  
└── static-assignments  
│   └── common.yml  
```

Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.

For this reason, we will now create a folder to keep each environment's variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables.

![03 crete env direc](https://github.com/user-attachments/assets/60d334f4-51e3-4cc2-a48b-e087b692347d)

Your layout should now look like this.
```
├── dynamic-assignments  
│   └── env-vars.yml  
├── env-vars  
│   └── dev.yml  
│   └── stage.yml  
│   └── uat.yml  
│   └── prod.yml  
├── inventory  
│   └── dev  
│   └── stage  
│   └── uat  
│   └── prod  
├── playbooks  
│   └── site.yml  
└── static-assignments  
│   └── common.yml  
│   └── webservers.yml  
```

Now paste the instruction below into the env-vars.yml file.

```yaml
---
- name: collate variables from env specific file, if it exists
  hosts: all
  tasks:
    - name: looping through list of available files
      include_vars: "{{ item }}"
      with_first_found:
        - files:
            - dev.yml
            - stage.yml
            - prod.yml
            - uat.yml
          paths:
            - "{{ playbook_dir }}/../env-vars"
      tags:
        - always
```

Notice 3 things to notice here:

- We used `include_vars` syntax instead of `include`, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the `include` module is deprecated and variants of `include_*` must be used. These are:
    - `include_role`
    - `include_tasks`
    - `include_vars`

In the same version, variants of `import` were also introduced, such as:
    - `import_role`
    - `import_tasks`

- We made use of a special variables `{{ playbook_dir }}` and `{{ inventory_file }}`. `{{ playbook_dir }}` will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. `{{ inventory_file }}` on the other hand will dynamically resolve to the name of the inventory file being used, then append `.yml` so that it picks up the required file within the `env-vars` folder.
- We are including the variables using a loop. `with_first_found` implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment-specific env file does not exist.

### Update site.yml with dynamic assignments

Update `site.yml` file to make use of the dynamic assignment. (At this point, we cannot test it yet. We are just setting the stage for what is yet to come. So hang on to your hats)

`site.yml` should now look like this.

```yaml
---
- hosts: all
  - name: Include dynamic variables
    tasks:
      import_playbook: ../static-assignments/common.yml
      include: ../dynamic-assignments/env-vars.yml
    tags:
      - always

- hosts: webservers
  - name: Webserver assignment
    import_playbook: ../static-assignments/webservers.yml
```

**Community Roles**  
Now it is time to create a role for MySQL database - it should install the MySQL package, create a database and configure users. But why should we re-invent the wheel? There are tons of roles that have already been developed by other open-source engineers out there. These roles are actually production-ready, and dynamic to accommodate most of Linux flavors. With Ansible Galaxy again, we can simply download a ready-to-use ansible role, and keep going.

**Download MySQL Ansible Role**  
You can browse available community roles [here](https://galaxy.ansible.com).  
We will be using a MySQL role developed by `geerlingguy`.  

On Jenkins-Ansible server, make sure that `git` is installed with `git --version`, then go to the 'ansible-config-mgt' directory and run:

```bash
git init
git pull https://github.com/<your-name>/ansible-config-mgt.git
git remote add origin https://github.com/<your-name>/ansible-config-mgt.git
git branch roles-feature
git switch roles-feature
```

Inside the roles directory, create your new MySQL role with:

```bash
ansible-galaxy install geerlingguy.mysql
```
![04 ansible galxy mysql](https://github.com/user-attachments/assets/c4783580-bb79-4a3f-bd60-6949ba5ad558)

And rename the folder to `mysql`:

```bash
mv geerlingguy.mysql/ mysql
```

![05 mv geerlingguy](https://github.com/user-attachments/assets/5636ef7c-df68-4a99-8fe1-6bef354b6edc)

**Read the `README.md` file and edit the role's configuration to use the correct credentials for MySQL required for the tooling website.**

![06 edit greenguy](https://github.com/user-attachments/assets/f77f3479-ccc3-4ab3-a675-52333e33d2fa)


![07 edit geerguy](https://github.com/user-attachments/assets/6e16767a-4e20-407e-b0dd-d08bc3422da9)


Now it is time to upload the changes into your GitHub:

```bash
git add .
git commit -m "Commit new role files into GitHub"
git push --set-upstream origin roles-feature
```

![08 git add mysql](https://github.com/user-attachments/assets/ddd1b8bb-ba94-4819-9ff8-4737d5f82632)

![09 git add 2](https://github.com/user-attachments/assets/71169349-7492-46dc-97c3-97b38ef04334)

![10 pull merge](https://github.com/user-attachments/assets/78ff50e4-b3cd-4238-81f7-34aa5d36b13f)

Now, if you are satisfied with your codes, you can create a Pull Request and merge it to the `main` branch on GitHub.


**Load Balancer roles**  
We want to be able to choose which Load Balancer to use, Nginx or Apache, so we need to have two roles respectively:

- Nginx
- Apache

**To implement this in Ansible, here’s a detailed approach for creating and managing the load balancer roles, configuring conditions, and setting up the environment variables.**

### Step 1: Create the Roles
1. **Nginx Role**:
   - Inside your roles directory, create an `nginx` role:
     ```bash
     ansible-galaxy init nginx
     ```
   - In `nginx/defaults/main.yml`, add the following variables:
     ```yaml
     enable_nginx_lb: false
     load_balancer_is_required: false
     ```
   - Implement any tasks to set up Nginx as a load balancer in `nginx/tasks/main.yml`.

2. **Apache Role**:
   - Similarly, create an `apache` role:
     ```bash
     ansible-galaxy init apache
     ```
   - In `apache/defaults/main.yml`, add these variables:
     ```yaml
     enable_apache_lb: false
     load_balancer_is_required: false
     ```
   - Add the necessary tasks to set up Apache as a load balancer in `apache/tasks/main.yml`.

### Step 2: Configure `loadbalancers.yml` Playbook
This playbook will apply either the Nginx or Apache load balancer role based on the condition set by environment variables.

Create `loadbalancers.yml` in your playbooks directory:
```yaml
- hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }
```

### Step 3: Configure `site.yml`
The `site.yml` playbook will import `loadbalancers.yml` and conditionally enable the load balancer if `load_balancer_is_required` is set to `true`.

In `site.yml`:
```yaml
- name: Load balancers assignment
  hosts: lb
  import_playbook: loadbalancers.yml
  when: load_balancer_is_required
```

### Step 4: Define Environment Variables
1. **Create Environment-Specific Variable Files**:
   - In `env-vars/uat.yml` (or another environment file), add the following variables:
     ```yaml
     enable_nginx_lb: true
     enable_apache_lb: false
     load_balancer_is_required: true
     ```
   - Similarly, for testing Apache instead, you could set:
     ```yaml
     enable_nginx_lb: false
     enable_apache_lb: true
     load_balancer_is_required: true
     ```

2. **Configure Inventory Files**:
   - In your inventory file (e.g., `uat`), set the environment variable file for UAT:
     ```ini
     [lb]
     loadbalancer1 ansible_host=192.168.1.10

     [lb:vars]
     ansible_user=your_user
     ansible_ssh_private_key_file=/path/to/private_key
     ansible_become=true
     env_vars_file=env-vars/uat.yml
     ```

### Step 5: Update `static-assignments/loadbalancers.yml`
Update this file to import the environment variables from `env-vars/uat.yml` as shown:
```yaml
- hosts: lb
  vars_files:
    - "{{ env_vars_file }}"
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }
```

### Step 6: Run and Test
To test in a specific environment, specify the inventory and environment variables:
```bash
ansible-playbook -i inventory/uat site.yml
```

This setup allows you to enable either the Nginx or Apache load balancer role in specific environments based on variables defined in the `env-vars` file. Adjusting the configuration in these files enables you to dynamically control which load balancer is activated in each environment.





