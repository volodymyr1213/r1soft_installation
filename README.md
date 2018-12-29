# This file helps you to automate the Installation of R1Soft Agent into CentOS6, CentOS7, Fedora, Debian and Ubuntu machines.
---
  - name: R1Soft Installation on CentOS6-7, Debian, Ubuntu and Fedora
    hosts: r1soft
    connection: ssh
    tasks:

# Installing python-dnf on Fedora
    - name: Install python-dnf on Fedora
      when: ansible_distribution == "Fedora"
      command: "yum install python-dnf"

# Creating repository on "Redhat" OS
    - name: Create Repo File
      when: ansible_os_family == "RedHat"
      copy:
        src: /etc/yum.repos.d/r1soft.repo
        dest: /etc/yum.repos.d/r1soft.repo
        owner: root
        group: root

# Installing R1Soft Agent on "Redhat" OS
    - name: Install Agent
      when: ansible_os_family == "RedHat"
      package:
        name: serverbackup-enterprise-agent
        state: installed

# Getting the key from R1Soft Server
    - name: Get Key From server
      when: ansible_os_family == "RedHat"
      command: "r1soft-setup --get-key http://ser_vers_IP"  # <-- R1Soft Server (Change it to your server's IP)

# Restarting R1Soft Agent on Fedora
    - name: Restart R1Soft Agent
      when: ansible_distribution == "Fedora"
      service:
        name: cdp-agent
        state: restarted
# Restarting R1Soft Agent on CentOS6
    - name: Restart R1Soft Agent
      when: (ansible_distribution == "CentOS" and ansible_distribution_major_version == "6")
      service:
        name: cdp-agent
        state: restarted

# Restarting R1Soft Agent on CentOS7
    - name: Restart R1Soft Agent
       when: (ansible_distribution == "Centos" and ansible_distribution_major_version == "7")
      systemd:
        name: sbm-agent
        state: restarted

# Installing python-apt on Debian
    - name: Install python-apt
      when: (ansible_distribution == "Debian" or ansible_distribution == "Ubuntu" )
      apt:
        name: "python-apt"
        state: present

# Creating R1Soft repository on Debian and Ubuntu
    - name: Install R1Soft on Debian and Ubuntu
      when: (ansible_distribution == "Debian" or ansible_distribution == "Ubuntu" )
      command: "echo deb http://repo.r1soft.com/apt stable main >> /etc/apt/sources.list"

    - name: Next Command
      when: (ansible_distribution == "Debian" or ansible_distribution == "Ubuntu" )
      get_url:
        url: http://repo.r1soft.com/r1soft.asc
        dest: /root/r1soft.asc

    - name: Next Command
      when: (ansible_distribution == "Debian" or ansible_distribution == "Ubuntu" )
      command: "apt-key add r1soft.asc"

    - name: Next Command
      when: (ansible_distribution == "Debian" or ansible_distribution == "Ubuntu" )
      command: "apt-get update"

# Installing R1Soft Agent on Debian and Ubuntu
    - name: Install Agent
      when: (ansible_distribution == "Debian" or ansible_distribution == "Ubuntu" )
      apt:
        name: "serverbackup-enterprise-agent"
        state: present
# Getting Key from R1Soft Server
    - name: Get Key From Server
      when: (ansible_distribution == "Debian" or ansible_distribution == "Ubuntu" )
      command: "r1soft-setup --get-key http://ser_vers_IP:8080"  #<--- R1Soft Server (Change to your server's IP)
