# Install OpenStack on VM using Kolla-ansible

> Author: SonNVQ - DevOps Intern at Viettel Networks
> 

# 1. Introduction

With the need to install OpenStack on a lightwave development environment for learning and developing purposes, we will use VMWare Workstation to create VMs and install OpenStack with minimum resources. And we will use kolla-ansible, an excellent tool to automatically configure and install OpenStack.

# 2. Requirements

Our OpenStack node must satisfy the following minimum requirements:

- 2 network interfaces
- 8GB main memory
- 40GB disk space

We will also use another deploy node that installed kolla-ansible to remotely install the OpenStack node.

It is recommended that your computer has at least 16GB RAM and a modern CPU generation to minimize the amount of error during the entire process.

# 3. Configure VMWare and create VMs

## 3.1. Network configurations:

To install OpenStack, we need **2 network interfaces**, one for **internal** and another one for **external** connection so we will create these two in VMWare.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled.png)

The **internal** interface should be **Host-only** type and the **external** interface should be **NAT**. We can create these as our intent, it is just a virtual interface.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%201.png)

First, we can add a virtual interface for internal by clicking ‘**Add Network…**’, then select a free profile and click ‘**OK**’ so we can use this profile to create a new virtual network.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%202.png)

We can configure our internal network like this, we are choosing **10.0.5.0/24** as the IP range for our network.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%203.png)

Then we will set DHCP for our network, please click ‘DHCP Settings…’.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%204.png)

As our subnet is 10.0.5.0/24, we should set up DHCP as below.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%205.png)

First, we have an **internal interface** with the **VMnet11** profile.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%206.png)

Do the **same process** for another free VMnet profile as you want to create a **NAT** interface for **external**. Note that we can have **only one NAT profile** in **VMWare**, so if you already have one you can reuse the old one, and maybe edit the subnet before using. Here, I already have a **VMnet8** NAT interface that has subnet **10.0.1.0/24**.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%207.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%208.png)

Ok, after these configurations, we have two network interfaces to install OpenStack:

- VMnet8 - external: **10.0.1.0/24**
- VMnet11 - internal: **10.0.5.0/24**

## 3.2. Create and configure VM

In this note, we will use **Ubuntu Server 22.04 LTS** for both the deploy node and the OpenStack node.

We will install the OpenStack node first, go to ‘File’ > ‘New Virtual Machine…’ to start creating a new VM.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%209.png)

Choose ‘Typical’ and next.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2010.png)

Browse for downloaded Ubuntu Server 22.04 iso.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2011.png)

Choose a name and a folder to store our VM.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2012.png)

Choose a disk size, 40GB is the minimum.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2013.png)

Click on ‘Customize Hardware…’ to set some other configurations.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2014.png)

For ‘**Memory**’, we need **at least** 8GB or 8192MB.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2015.png)

Depending on your computer, choose a suitable ‘Processors’ setting.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2016.png)

For ‘Display’, we should **disable** ‘Accelerate 3D graphics’.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2017.png)

A **vital** part is a network, we have to add **one more Network Adapter** to our VM as below.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2018.png)

The **first Network Adapter** will be set up for **internal** by using the **VMnet11** profile as we set up in the previous part.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2019.png)

The second one(**Network Adapter 2**) we will set up for **external** connection by using **VMnet8** as the previous network setup.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2020.png)

Now, we can click ‘Close’ to get back and then click ‘Finish’ to start the installation process.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2021.png)

## 3.3. Install Ubuntu Server

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2022.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2023.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2024.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2025.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2026.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2027.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2028.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2029.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2030.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2031.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2032.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2033.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2034.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2035.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2036.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2037.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2038.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2039.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2040.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2041.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2042.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2043.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2044.png)

Now we can ssh to our vm using IP 10.0.5.1, it is recommened to use this IP rather than NAT IP because external interface can lost connection in the installing progress. 

### 3.4. Set up SSH Key for VM

Although we can ssh to VM using username and password because it is less secure, especially in production. So we will set up SSH key to securely connect to our VM.

First, in Windows, we have to generate ssh key pair by using ssh-keygen command. SSH supports several public key algorithms for authentication keys. These include:

- `rsa` - an old algorithm based on the difficulty of factoring large numbers. A key size of at least 2048 bits is recommended for RSA; 4096 bits is better. RSA is getting old and significant advances are being made in factoring. Choosing a different algorithm may be advisable. The RSA algorithm may become practically breakable in the foreseeable future. All SSH clients support this algorithm.
- `dsa` - an old US government Digital Signature Algorithm. It is based on the difficulty of computing discrete logarithms. A key size of 1024 would normally be used with it. DSA in its original form is no longer recommended.
- `ecdsa` - a new Digital Signature Algorithm standardized by the US government, using elliptic curves. This is probably a good algorithm for current applications. Only three key sizes are supported: 256, 384, and 521 (sic!) bits. We would recommend always using it with 521 bits, since the keys are still small and probably more secure than the smaller ones (even though they should also be safe). Most SSH clients now support this algorithm.
- `ed25519` - this is a new algorithm added in OpenSSH. Support for it in clients is not yet universal. Thus its use in general purpose applications may not yet be advisable.

The algorithm is selected using the `-t` option and key size using the `-b` option. The following commands illustrate:

```bash
ssh-keygen -t rsa -b 4096
ssh-keygen -t dsa
ssh-keygen -t ecdsa -b 521
ssh-keygen -t ed25519
```

We will use **ecdsa 521** algorithm and create a key file with the default name **id_ecdsa** as below. Windows will only detect and use file **id_ecdsa** to connect ssh, if you use another name, you must specify it manually when connecting. 

```bash
ssh-keygen -t ecdsa -b 521
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2045.png)

In cmd, we can use `type` command to view the content of the key pair file.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2046.png)

We will use the private key to authenticate ssh, but to do that, firstly we have to add our public key to the server. To do this, first ssh to our server using username and password as we set up at installing process in the previous section.

```bash
ssh ns@10.0.5.2
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2047.png)

Now, we have to add the public key to the user’s .ssh folder to allow ssh using the created key pair. Note that we are login by **ns** user, so we are going to add a public key for just only ns user, it means that if you want to log in by root you must do the same process under login with root, we will cover these things later. 

As previously, you must copy the generated public key first, then append that key to ~/.ssh/authorized_keys file and use cat command to validate it. 

> A litter note that you can see the ~/ folder, this is the current user home folder.
> 
> 
> ![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2048.png)
> 

```bash
echo "ecdsa-sha2-nistp521 AAAAE2VjZHNhLXNoYTItbmlzdHA1MjEAAAAIbmlzdHA1MjEAAACFBAFCLhqyxZyj07dunc7gECPOWJhbALWgMICemMoMlevORgApSmUfg/XZU9deGmXmd6EEspQlxaGG1OC8hJMgTJcAsgFWTfhuhHSriamL+xvCo9/bZiz305K8rDYs63LnXcIqq/biAYSfv/p3MicaFvTxLbJekPr39YmBrtfQmcS2Dnrefw== ns@DESKTOP-O1J54PG" >> ~/.ssh/authorized_keys
cat ~/.ssh/authorized_keys
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2049.png)

Now we can test by ssh into ns@10.0.5.2 without a password.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2050.png)

Everything is going as it is. Because we need ssh to root to install OpenStack, so we need to get root access and then add the public key to the root account.

The process is really simple, we can switch to the root account by using the command `sudo -i` and then repeat the entire old process.

```bash
sudo -i
echo "ecdsa-sha2-nistp521 AAAAE2VjZHNhLXNoYTItbmlzdHA1MjEAAAAIbmlzdHA1MjEAAACFBAFCLhqyxZyj07dunc7gECPOWJhbALWgMICemMoMlevORgApSmUfg/XZU9deGmXmd6EEspQlxaGG1OC8hJMgTJcAsgFWTfhuhHSriamL+xvCo9/bZiz305K8rDYs63LnXcIqq/biAYSfv/p3MicaFvTxLbJekPr39YmBrtfQmcS2Dnrefw== ns@DESKTOP-O1J54PG" >> ~/.ssh/authorized_keys
cat ~/.ssh/authorized_keys
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2051.png)

Test for connection:

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2052.png)

No password asking anymore, windows auto uses id_ecdsa private key we generated to establish a connection with our server.

### 3.5. Some more things…

We should run `apt update` to fetch the latest version of the package list and `apt upgrade` to download and install the updates for each outdated package and dependency.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2053.png)

Now it is ready to deploy OpenStack on our OpenStack Node.

### 3.6. Create deploy node

We do not need to do the time-consuming process above again. Just use the clone function of VMWare to quickly create a deploy node to install kolla-ansible on.

Firstly, power off the current OpenStack node.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2054.png)

Now, use the clone function to create our deploy node.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2055.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2056.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2057.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2058.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2059.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2060.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2061.png)

Done! But it is too waste to use 8GB RAM, 40GB Disk, and ton of CPU cores for our deploy node, so we will modify settings of deploy node.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2062.png)

First, remove the Host-only Network Adapter because we are not using it in deploy node.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2063.png)

Then, decrease RAM and Processors, around 2GB RAM is enough.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2064.png)

Now start our deploy node.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2065.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2066.png)

We have a problem here, our deploy node has the same public NAT IP as OpenStack node, so we need to assign a new one as below.

```bash
sudo -i #switch to root
ip a #list all network interfaces
ip addr del 10.0.1.3/24 dev ens34
dhclient ens34
ip a
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2067.png)

Now, we have a new NAT IP 10.0.1.5 for our deploy node and we can connect to using ssh.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2068.png)

Done. We have a deploy node in two minutes!

# 4. Install OpenStack using Kolla-Ansible

We will install the latest supported stable version at writing time is 2023.3. You can feel free to use other versions but the configuration can be different.

> Official document:
> 
> 
> [https://docs.openstack.org/kolla-ansible/2023.2/](https://docs.openstack.org/kolla-ansible/2023.2/)
> 
> [https://docs.openstack.org/kolla-ansible/2023.2/user/quickstart.html](https://docs.openstack.org/kolla-ansible/2023.2/user/quickstart.html)
> 

### 4.1. Install dependencies

While we are using root account, there is no need for `sudo` with some commands.

1. Update the package index
    
    `apt update`
    
2. Install Python build dependencies:
    
    
    ```bash
    apt install git python3-dev libffi-dev gcc libssl-dev
    ```
    

### 4.2. **Install dependencies for the virtual environment**

We will use python virtualenv to create a virtual environment.

1. First, we need to install **pipx.**
    
    ```bash
    apt install pipx
    pipx ensurepath
    ```
    
2. Then, install python **virtualenv** via pipx.
    
    ```bash
    pipx install virtualenv
    ```
    
3. We need to log out and ssh to root again for PATH to update after `pipx ensurepath` command.
4. Now, create a virtual environment in ~/. With root account ~/ is /root folder.
    
    ```bash
    virtualenv ka #ka is short for kolla-ansible, you can use any name that you want
    source ka/bin/activate #activate the virtual environment
    ```
    
5. Ensure the latest version of pip is installed:
    
    ```bash
    pip install -U pip
    ```
    
    Install [Ansible](http://www.ansible.com/). Kolla Ansible requires at least Ansible **`6`** (or ansible-core **`2.14`**) and supports up to **`7`** (or ansible-core **`2.15`**).
    
    ```bash
    pip install 'ansible-core>=2.14,<2.16'
    ```
    

### 4.3. **Install Kolla-ansible**

1. Install kolla-ansible and its dependencies using **`pip`**.
    
    ```bash
    pip install git+https://opendev.org/openstack/kolla-ansible@stable/2023.2
    ```
    
2. Create the **`/etc/kolla`** directory.
    
    ```bash
    mkdir -p /etc/kolla
    chown $USER:$USER /etc/kolla
    ```
    
3. Copy **`globals.yml`** and **`passwords.yml`** to **`/etc/kolla`** directory.
    
    
    ```bash
    cp -r ~/ka/share/kolla-ansible/etc_examples/kolla/* /etc/kolla
    ```
    
4. Copy **`all-in-one`** inventory file to the current directory.
    
    
    ```bash
    cp ~/ka/share/kolla-ansible/ansible/inventory/all-in-one .
    ```
    

### 4.4. Install Ansible Galaxy requirements

Install Ansible Galaxy dependencies:

```bash
kolla-ansible install-deps
```

### 4.5. Prepare initial configuration

### 4.5.1. Inventory

The next step is to prepare our inventory file. An inventory is an Ansible file where we specify hosts and the groups that they belong to. We can use this to define node roles and access credentials.

Kolla Ansible comes with `**all-in-one**` and `**multinode`** example inventory files. The difference between them is that the former is ready for deploying single-node OpenStack on localhost. In this guide, we will show the `**all-in-one**` installation.

We will use `**all-in-one**` since we are installing on only one OpenStack node.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2069.png)

We are deploying OpenStack on the 10.0.5.2 host, so we need to modify the inventory file like this.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2070.png)

Here we have a key file for ssh connection **controller_key.pem**, now we need to create this key file using our previous key pair.

```bash
echo "-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAArAAAABNlY2RzYS
1zaGEyLW5pc3RwNTIxAAAACG5pc3RwNTIxAAAAhQQBQi4assWco9O3bp3O4BAjzliYWwC1
oDCAnpjKDJXrzkYAKUplH4P12VPXXhpl5nehBLKUJcWhhtTgvISTIEyXALIBVk34boR0q4
mpi/sbwqPf22Ys99OSvKw2LOty513CKqv24gGEn7/6dzInGhb08S2yXpD69/WJga7X0JnE
tg563n8AAAEQsFVk5rBVZOYAAAATZWNkc2Etc2hhMi1uaXN0cDUyMQAAAAhuaXN0cDUyMQ
AAAIUEAUIuGrLFnKPTt26dzuAQI85YmFsAtaAwgJ6YygyV685GAClKZR+D9dlT114aZeZ3
oQSylCXFoYbU4LyEkyBMlwCyAVZN+G6EdKuJqYv7G8Kj39tmLPfTkrysNizrcuddwiqr9u
IBhJ+/+ncyJxoW9PEtsl6Q+vf1iYGu19CZxLYOet5/AAAAQgFuMcr/C3nRsrT2CKy+ewQw
OwF7KhEVkG2OCu8mKMvS3cfUxn6OpZ6GpI/4twEpprrewhoteieGfD//OeLngFrMZQAAAB
Juc0BERVNLVE9QLU8xSjU0UEc=
-----END OPENSSH PRIVATE KEY-----" >> controller_key.pem
chmod 400 controller_key.pem
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2071.png)

We must chmod **400** the key file for security reasons, without this permission limit, kolla will show ssh error because of the key file.

We can test our inventory by using the ansible ping module to ping our OpenStack node.

```bash
ansible -i all-in-one all -m ping
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2072.png)

Now everything is ready for install OpenStack.

### 4.5.2. Kolla passwords

Passwords used in our deployment are stored in **`/etc/kolla/passwords.yml`** file. All passwords are blank in this file and have to be filled either manually or by running random password generator:

```bash
kolla-genpwd
```

### 4.5.3. Kolla globals.yml

**`globals.yml`** is the main configuration file for Kolla Ansible and by default stored in /etc/kolla/globals.yml file. There are a few options that are required to deploy Kolla Ansible:

```bash
vim /etc/kolla/globals.yml
```

We need to config some parameters:

```bash
kolla_base_distro: "ubuntu"
kolla_internal_vip_address: "10.0.5.2" #same as internal interface ip
kolla_container_engine: docker
network_interface: "ens33" #the internal interface name
neutron_external_interface: "ens34" #the external interface name
enable_haproxy: "no"
enable_ironic: "no" #since we are not using bare material service
enable_skyline: "yes" #new generation dashboard
nova_compute_virt_type: "qemu"
nova_console: "nonvc"
```

### 4.5.4. Deployment

After the configuration is set, we can proceed to the deployment phase. First, we need to set up basic host-level dependencies, like docker.

Kolla Ansible provides a playbook that will install all required services in the correct versions.

```bash
kolla-ansible -i ./all-in-one bootstrap-servers -v
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2073.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2074.png)

```bash
kolla-ansible -i ./all-in-one prechecks -v
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2075.png)

At this time we can run deploy command, but it is recommended to run pull command first to download all needed materials before actually running deploy command.

```bash
kolla-ansible -i ./all-in-one pull -v
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2076.png)

The last thing is to deploy, just run the deploy command.

```bash
kolla-ansible -i all-in-one deploy -v
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2077.png)

### 4.5.5. Create **clouds.yaml,** openrc file and copy to OpenStack node

Run this command on deploy node to create clouds.yaml and admin openrc file, these files will be created on deploy node although we specified inventory. 

```bash
kolla-ansible -i all-in-one post-deploy
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2078.png)

Now we need to copy these files to our OpenStack node.

```bash
scp -i controller_key.pem /etc/kolla/admin-openrc.sh root@10.0.5.2:/etc/kolla
scp -i controller_key.pem /etc/kolla/clouds.yaml root@10.0.5.2:/etc/kolla
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2079.png)

### 4.5.6. Preparing init-runonce script

```bash
vim ~/ka/share/kolla-ansible/init-runonce
```

We need to change some config to match our network interfaces.

```bash
DEMO_NET_CIDR='10.0.5.0/24'
DEMO_NET_GATEWAY='10.0.5.1'
DEMO_NET_DNS='8.8.8.8'

# This EXT_NET_CIDR is your public network,that you want to connect to the internet via.
ENABLE_EXT_NET=1
EXT_NET_CIDR='10.0.1.0/24'
EXT_NET_RANGE='start=10.0.1.100,end=10.0.1.254'
EXT_NET_GATEWAY='10.0.1.1'
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2080.png)

Now, save the file and copy it to the OpenStack node.

```bash
scp -i controller_key.pem ~/ka/share/kolla-ansible/init-runonce root@10.0.5.2:/root
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2081.png)

### 4.5.6. Install OpenStack client, config, and run init-runonce script

Now we need to ssh to our OpenStack node and then run these commands to install OpenStack client.

At this time, maybe we can lose the internet connection by some configuration of Kolla-ansible, we can check this by running a ping command.

```bash
ping 8.8.8.8
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2082.png)

We can fix this by running `dhclient` on **br-ex** network interface.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2083.png)

After having internet connection again, we can continue.

1. Install the OpenStack CLI client:
    
    
    ```bash
    pip install python-openstackclient -c https://releases.openstack.org/constraints/upper/2023.2
    ```
    
2. Run [admin-openrc.sh](http://admin-openrc.sh) script
    
    ```bash
    . /etc/kolla/admin-openrc.sh
    ```
    
3. Run init-runonce script for testing purpose
    
    ```bash
    ./init-runonce
    ```
    

## 5. Access the OpenStack Horizon dashboard

First, go back to deploy node, and get the horizon admin password.

```bash
cat /etc/kolla/passwords.yml | grep keystone_admin_password
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2084.png)

We can list all endpoint of OpenStack by using OpenStack node.

```bash
openstack endpoint list
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2085.png)

Using the external interface to access the horizon dashboard.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2086.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2087.png)

We also can access new-generation dashboard Skyline by accessing port 9999 with default username ‘skyline’. To get the password, run below command:

```bash
cat /etc/kolla/passwords.yml | grep skyline_keystone_password
```

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2088.png)

Now, go to port 9999 to access the dashboard.

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2089.png)

![Untitled](images/Install%20OpenStack%20on%20VM%20using%20Kolla-ansible%20ef2b2eb585234389af61f80598a52f03/Untitled%2090.png)
