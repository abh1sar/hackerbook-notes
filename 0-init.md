# HB ‘0-init’

- Cloudstack project develops open source software for deploying public and private Infrastructure-as-a-Service (IaaS) clouds.
- Java Project providing management server and agents for hypervisor hosts
- Features
    - Works with multiple hypervisors : XenServer, KVM, Hyoer-V, VMWare ESXi with vSphere
    - Intuitive web-ui
    - native API
    - Amazon S3/EC2 compatible API
    - Manages storage for instances running on the hypervisors (primary storage) as well as templates, snapshots and ISO images (secondary storage)
    - Orchestrates n/w services from the data link layer(l2) to some application later (l7) services such as DHCP, NAT, firewall, VPN and so on.
    - Accounting of n/w, storage and compute resources
    - Multi-tenancy/ account separation
    - User Management
- [Youtube](https://www.notion.so/Youtube-10469aef13514264981206054b55a1a1?pvs=21) [Building IaaS with Apache CloudStack](https://www.youtube.com/watch?v=bxEL06BPGNw)
- [Youtube](https://www.notion.so/Youtube-10469aef13514264981206054b55a1a1?pvs=21) [Building Clouds with Apache CloudStack](https://www.youtube.com/watch?v=4qFFwyK9hos)

# Core Concepts

IaaS platform provides means to partition and primarily consume compute, storage and n/w resources usually by means of virtualization

Management server is a monolith that provides orchestration and control plane accessible via query based http APIs through its UI and CLI.

- Control Plane
    
    The control plane in cloud orchestration refers to the component that sits above the data plane and is responsible for enforcing rules and policies for the data plane. It controls how data packets are forwarded and manages the orchestration and control of resources in the cloud infrastructure.
    

Authentication mechanisms used : pbkdf2, ldap, saml2, api/secret key based

- PBKDF2
    
    (Password-Based Key Derivation Function 2) is a key derivation function that is commonly used for password hashing. It takes a password and a salt as input, and iteratively applies a pseudorandom function to generate a derived key. The number of iterations can be configured to make the function computationally expensive, thereby increasing the time required to perform a brute-force attack on the password. PBKDF2 is designed to be slow and resistant to various types of attacks, including dictionary attacks and rainbow table attacks. It is widely used in systems that require secure storage of passwords, such as authentication systems. 
    
- LDAP
    
    stands for Lightweight Directory Access Protocol. It is an open and cross-platform protocol used for accessing and maintaining distributed directory information services over a network. LDAP is commonly used for centralized authentication, authorization, and directory services in various applications and systems. It provides a hierarchical structure for organizing and accessing directory information, such as user accounts, groups, and other resources. LDAP enables users and applications to query and modify directory information stored in a directory server. It is widely used in enterprise environments for managing user identities and permissions.
    
- SAML2
    
    (Security Assertion Markup Language 2.0) is an XML-based open standard for exchanging authentication and authorization data between parties, specifically between an identity provider (IdP) and a service provider (SP). It enables single sign-on (SSO) across different systems and applications by allowing the exchange of security assertions that contain information about a user's identity and their authenticated session. SAML2 provides a framework for secure communication and trust establishment between the IdP and SP, allowing users to access multiple systems without the need for separate logins. It is widely used in federated identity management systems and is supported by many identity providers and service providers in various industries. 
    
- API/secret key-based authentication
    
    is a method of authentication where users or applications are provided with an API key (also known as a secret key or access key) and a corresponding secret key. These keys are used to authenticate and authorize API requests.
    When making an API request, the user or application includes the API key and secret key in the request headers or parameters. The server then verifies the authenticity of the request by validating the provided keys. If the keys match and are valid, the user or application is granted access to the requested resources or actions.
    API/secret key-based authentication is commonly used in web APIs and services that require secure access and control over user permissions. It provides a way to identify and track individual users or applications making API requests while ensuring the security and integrity of the data being transmitted.
    

Authorization is supported through **Roles.**

For user management cloudstack has **Domains** that have **Accounts** that have **Users,** and all **Accounts** have some **Role**. It has **Projects** that allow users across **Domains** to participate in a team.

Infrastructure management has organisational units like directory and files

- Region
    - Zone - datacenter
        - Pod - racks
            - Cluster - homogenous hosts
                - Host - single compute node; often a hypervisor
- Primary storage - zone or cluster wide
- Secondary storage - zone wide

## Terminologies

[https://docs.cloudstack.apache.org/en/latest/conceptsandterminology/index.html](https://docs.cloudstack.apache.org/en/latest/conceptsandterminology/index.html)

With cloudstack you can 

Set up an on-demand elastic cloud computing service

Allow end-users to provision resources

### Management Server

Runs on a dedicated virtual machine

Runs in an Apache Tomcat container and requires MySQL database for persistence

Single point of configuration

### Regions

Largest organizational unit within a Cloudstack deployment

Group of zones in the same geographic region

Usage records can be tracked at the region level.

User accounts can span regions


### Zones

Typically corresponds to a single data center.

Zones can be public or private ( only visible to a specific domain and its subdomain of users )

Hosts in the same zone are directly accessible to each other without having to go through a firewall.

Hosts in different zones can access each other through statically configured VPN tunnels.


### Pods

Represents a single rack.

Hosts in the same pod are in the same subnet.

Pods are not visible to the end user.


### Clusters

A way to group hosts.

Hosts in a cluster have identical hardware, run the same hyoervisor, are on the same subnet, and access the same shared primary storage.

Instances can be live migrated from one host to another within the same cluster without any downtime.

Even if local storage is used exclusively, clusters are still required organizationally, even if there is just one host per cluster.


### Hosts

Has a hypervisor software to manage guest instances.

Additional hosts can be added any time to provide more capacity for guest instances.

Cloudstack automatically detects the amount of CPU and memory resources provided by the hosts

Hosts are not visible to the end user. End user cannot determine which host their guest has been assigned to.

### Primary Storage

Associated with a cluster and it stores virtual disks for all the instances running on hosts in that cluster. On KVM and VMWare, you can provision primary storage on a per-zone basis.

Can add multiple primary storage servers to a cluster or zone.

For Hyper-V, SMB/CIFS storage is supported. Zone-wide Primary Storagge is not supported in Hyper-V.

Ceph/RBD storage is only supported by KVM. Can be used as Zone-wide Primary Storage.

- Ceph/RBD
    
    **Ceph/RBD** is a distributed storage system designed to provide scalable and reliable storage for cloud computing environments. Ceph uses the RADOS (Reliable Autonomic Distributed Object Store) technology, which allows for the storage of data across multiple servers in a cluster. RBD (RADOS Block Device) is a component of Ceph that provides a virtual disk block device interface.
    
    Ceph/RBD allows for the creation of block devices that can be used as primary storage for virtual machines or other applications. It provides features such as data replication, fault tolerance, and high availability. Data is distributed across the cluster, ensuring that it is protected against hardware failures. Ceph/RBD is widely used in cloud infrastructure deployments for its scalability, flexibility, and robustness.
    
    Overall, Ceph/RBD offers a distributed and reliable storage solution that can adapt to the needs of modern cloud environments, making it a popular choice for many organizations.
    

PowerFlex/ScaleIO only supported by KVM. an be used cluster-wide or zone-wide.

- PowerFlex
    
    PowerFlex, formerly known as ScaleIO, is a software-defined storage solution developed by Dell EMC. It provides a scalable and flexible storage infrastructure that can be deployed across multiple servers and aggregated into a single pool of storage resources. PowerFlex utilizes distributed block storage architecture, where each server contributes its local storage capacity to create a shared storage pool. This distributed approach improves performance, scalability, and resilience, as data is distributed across multiple storage nodes.
    
    PowerFlex offers features such as data redundancy, data protection, and high availability. It allows for dynamic scaling of storage resources, enabling organizations to easily add or remove storage capacity as needed. With its software-defined nature, PowerFlex provides flexibility and agility in managing storage resources, making it suitable for various use cases, including virtualization, databases, and cloud environments.
    

Cloudstack works with all standards-compliant iSCSI and NFS servers that are supported by the underlying hypervisor

Can use the local disk for installation and skip adding a separate Primary Storage.

### Secondary Storage

Stores the following

- Templates - OS images to boot instances. Can include additional configuration information.
- ISO - disc images containing data or bootable media for OS
- Disk Volume Snapshots - saved copies of Instance data which can be used for data recovery.

Secondary storage scope could be a zone or a region

Can add object storage in addition to the zone-based NFS Secondary Storage Store, for it to be available to all hosts throughout the cloud.

Hyper-V supports SMB/CIFS storage

Cloudstack provides plugins to enable OpenStack Object Storage (Swift) and AMazon S3. NFS store in each zone acts as a staging area through which the data is forwarded to the Object Store.

### Physical Networks

**Basic Zone Network Traffic Types**

There can be only one physical n/w in the zone. Two kinds of traffic :

- Guest. Traffic generated by Instances run by end users. Guest instances communicate with each other over the guest n/w. Each Pod in a basic zone is a broadcast domain, so each has a different IP range for the guest network. Admin configures the IP range for each Pod.
    - Broadcast domain
        
        A broadcast domain is a logical division of a computer network in which all devices can directly communicate with each other at the data link layer without the need for a router or gateway. In a broadcast domain, when a device sends a broadcast packet, such as an Address Resolution Protocol (ARP) request, all other devices within the same broadcast domain receive and process the packet. This allows for efficient communication and discovery of devices within a specific network segment.
        
        Broadcast domains are typically defined by network switches or bridges. Each switch port or bridge interface represents a separate broadcast domain. Devices connected to the same switch or bridge are part of the same broadcast domain, while devices connected to different switches or bridges are in separate broadcast domains.
        
        By limiting the size of broadcast domains, network administrators can control the scope of broadcast traffic and prevent it from overwhelming the network. This helps to improve network performance and security by reducing unnecessary traffic and potential broadcast storms.
        
        It's important to note that routers or layer 3 switches can be used to connect multiple broadcast domains together, allowing devices in different domains to communicate with each other through routing.
        
- Management. Communication between hosts, system VMs. and any other component that comuunicates directly with the CloudStack Management Server. One must configure the IP range for the system VMs to use.

It is recommended to use separate NCs for management and guest traffic (*Will the management traffic by that big compared to the guest traffic to warrant a separate NIC?*)

- Public. Doesn’t exist in the Basic Zone. Guest n/w can be assigned publicly routable IP space in case you want tyour Instance to be directly exposed to the Internet.
- Storage. Secondary Storage Traffic only. CloudStack uses a separate NIC named storage NIC for this that always operates on a high bandwidth network to allow fast Template and Snapshot copying. One must configure IP range to use to the Storage Network

Cloudstack will assign IP addresses in the CIDR of the pod to the guests in that pod. The admin must add a DIrect IP range on the pod fot this purpose. Thse IPs are in the same VLAn as the hosts.

- CIDR
    
    CIDR stands for Classless Inter-Domain Routing. It is a method used to allocate and manage IP addresses more efficiently. With CIDR, IP addresses are represented using a prefix notation, which specifies the number of leading bits that make up the network address.
    
    For example, in CIDR notation, the IP address 192.168.0.0/24 represents a network address with a prefix length of 24 bits, indicating that the first 24 bits (192.168.0) are used to identify the network, while the remaining 8 bits (0) are available for host addresses.
    
    CIDR allows for more flexible allocation of IP addresses and enables the creation of smaller subnets, reducing the waste of IP address space. It also simplifies the routing process by aggregating multiple smaller networks into larger blocks.
    
    CIDR notation is commonly used in networking to specify IP address ranges and subnet masks, making it easier to manage and allocate IP addresses in large-scale networks.
    

**Advanced Zone Network Traffic Types**

TBD

# Software Setup

```bash
apt install cpu-checker
kvm-ok

sudo add-apt-repository universe
sudo apt-get update
sudo apt-get dist-upgrade
# general packages
sudo apt-get install vim git subversion mercurial patch rsync curl wget sed openssh-client gpg gnupg2 build-essential gzip bzip2 zip unzip p7zip-full p7zip-rar
# cloudstack related development
sudo apt-get install openjdk-11-jdk maven mysql-client mysql-server nfs-kernel-server quota genisoimage qemu-kvm qemu-utils libvirt-daemon virt-manager ipmitool jq uuid uuid-runtime python2 python2-dev python-setuptools python3-openssl python3-dev libffi-dev build-essential libssl-dev dpkg-dev libffi-dev rpm rpm2cpio bridge-utils iproute2 iptables ebtables ethtool vlan ipset tcpdump telnet fakeroot
# security
sudo apt-get install microcode.ctl intel-microcode amd64-microcode ca-certificates
# opinionated development env (optional)
sudo apt-get install zsh guake kazam ipython3 pv sshpass htop tmux tig vlc mutt bc cmake cmus cowsay gcc g++ wireshark openvpn network-manager-openvpn clisp
```

Virt Manager setup

```bash
sudo systemctl enable --now libvirtd
sudo systemctl start libvirtd
sudo systemctl status libvirtd
sudo usermod -aG kvm $USER
sudo usermod -aG libvirt $USER

# Logout and Logback so that changes are applied

sudo virt-manager
```

Other installs

```bash
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py
sudo python2 get-pip.py
sudo pip2 install --upgrade mysql-connector-python

sudo snap install slack --classic
sudo snap install intellij-idea-community --classic
sudo snap install vscode --classic
sudo snap install pycharm-community --classic
```
