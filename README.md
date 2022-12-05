# ansible-package_facts

Here, we will check if a package is installed or not in the client server using an ansible module called `package_facts`. 
the purpose of this module is to return information about installed packages as facts.

In order to check this, launch 2 amazon linux instances, one will be the master instance which will have ansible installed in it and the other one will be the test server.

## Ansible installation in the master instance

Use the below command to install Ansible in the master instance.

```
sudo amazon-linux-extras install ansible2 -y
```

Verify the installation by using the command:

```
ansible --version
```

## Key file creation

Create a key file in the working directorty and add key in it:

```
vim aws.pem
```

Change permission of the key file:

```
chmod 400 aws.pem
```

## hosts file creation

Create a hosts file inside the working directory and add the remote server in it:

```
vim hosts
```

Add the below contents:

```
[amazon]    
remote-server-ip ansible_user="ec2-user" ansible_port=22 ansible_ssh_private_key_file="aws.pem"
```

## Check connectivity

Check connectivity to the remote instance by using any of the below commands:

```
ansible -i hosts amazon -f 1 -m ping
```

or 

```
ansible -i hosts all -f 1 -m ping
```

## Playbook file creation

Create a playbook file as shown below to verify if PHP package is installed in the client server or not.

```
vim package_facts.yml
```

Add the below contents in it:

```
- name: "Listing packages using package_facts"
  hosts: amazon
  become: true
  tasks:
    
    - name: "listing PHP package in client instance"
      package_facts:
        manager: auto
        strategy: all

    - name: "checking for PHP package"
      when: "'php-fpm' in ansible_facts.packages"
      debug:
        msg: "php is available in the server"

    - name: "Installing php if not available"
      when: "'php-fpm' not in ansible_facts.packages" 
      shell: amazon-linux-extras install php7.4 -y     
```

## Executing the playbook file

Before executing the playbook file, check if there are any syntax errors by using the command:

```
ansible-playbook -i hosts package_facts.yml --syntax-check
```

If there are no errors, execute the playbook file:

```
ansible-playbook -i hosts package_facts.yml 
```

## Output

After, execution, we will receve the below output if PHP is installed in the remote server.

![output](https://github.com/anandu79/ansible-package_facts-package-information-as-facts/blob/main/images/output.jpg)

If PHP is not available in the remote instance, it will be automatically installed after the playbook has finished execution.



