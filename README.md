# Kubernetes-Cluster-Master-and-Node on Centos - 8
# Describe Kubernetes Cluster Master and Worker Node, First off all we create a Three Virtual Machine One for Master Node and two [2] Worker Node.

**My site Master ip and host = 192.168.2.121 and Hostname [k8master.pc]
and Worker Node ip and host = 192.168.2.122 and Hostname [k8snode01.pc] && 192.168.2.123 and Hostname [k8snode02.pc]**

**Step [1] First Off your swap mamery**

    vi /etc/fstab

**disable swap Copy and paste the line if already swap memory inclue your machine you Just Change comment [#]**

#/dev/mapper/cl-swap swap swap defaults 0 0

    swapoff -a
    
**Check It's Disable or not**

    free -m
    
**Step [2] Stop firewalld & disable firewalld service**

    systemctl stop firewalld && systemctl disable firewalld
    
**Check Status**

    systemctl status firewalld
    
**Step [3] Disable selinux service**
    
    vi /etc/selinux/config
    
**Line Number 7 Change the comment [disabled]**
  SELINUX=disabled
  
**This module is required to enable transparent masquerading and to facilitate Virtual Extensible LAN (VxLAN) traffic for communication between Kubernetes pods across the cluster.**
      
      modprobe br_netfilter
      
 **Step [4] Change your Hostname**

      hostnamectl set-hostname k8master.pc
      
**Config your Hostname [192.168.2.212 k8master.pc]**

    vi /etc/hosts

# Now reboot your Host PC


# Step [5] Install Docker Engine on Your Host Machine

 
**Step1: IF install old Docker engine first Uninstall old versions**

    sudo yum remove docker \

              docker-client \
                   
              docker-client-latest \
                   
              docker-common \
                   
              docker-latest \
                   
              docker-latest-logrotate \
                   
              docker-logrotate \
                   
              docker-engine

**Step2: Installation methods**

   **1. Install using the repository**

    sudo yum install -y yum-utils
    
    sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

**Install Docker Engine**
**Install the latest version of Docker Engine and containerd**

    sudo yum install -y docker-ce docker-ce-cli containerd.io

**Start Docker Service**

    systemctl start docker && systemctl enable docker
   
**Check Docker Service**

    service docker status

**Check Docker Version**

    docker version

**Check Docker Container running**

    docker ps  
    
**Check Docker Container all** 
          
    docker ps -a

**Create a Directory**

    mkdir /etc/docker
    
**Copy and Paste the Commande / file**

cat > /etc/docker/daemon.json <<EOF
    {
      "exec-opts": ["native.cgroupdriver=systemd"],
      "log-driver": "json-file",
      "log-opts": {
      "max-size": "100m"
    },
      "storage-driver": "overlay2",
      "storage-opts": [
      "overlay2.override_kernel_check=true"
        ]
       }
EOF


**Now Again create a Directory**

    mkdir -p /etc/systemd/system/docker.service.d

**Reload daemon**

    systemctl daemon-reload

#Step [6] Install kubernetes on linux machine
**Copy and Paste the Commande / file**
                                    
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
	[kubernetes]
	name=Kubernetes
	baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
	enabled=1
	gpgcheck=1
	repo_gpgcheck=1
	gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

    sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
    
**Install Command on Kubernetes**
    
    yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
	
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
    
**Chweck sysctl System**
	
	sysctl --system

**Enable Kubelet**
	
	systemctl enable --now kubelet






