---
- name: Config VM with Docker
  hosts: webservers
  become: true
  tasks:

  - name: Install docker.io
    apt:
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present

  - name: Install python3-pip
    apt:
      force_apt_get: yes
      name: python3-pip
      state: present

  - name: Install Python Docker module
    pip:
      name: docker
      state: present

  - name: Download and Launch a Docker Web Container
    docker_container:
      name: dvwa
      image: cyberxsecurity/dvwa
      state: started
      restart_policy: always
      published_ports: 80:80

  - name: Enable Docker service
    systemd:
      name: docker
      enabled: yes