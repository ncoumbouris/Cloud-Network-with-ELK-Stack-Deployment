---
- name: Config ELK VM with Docker
  hosts: elk
  remote_user: azadmin
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

  - name: Use more memory
    sysctl:
      name: vm.max_map_count
      value: "262144"
      state: present
      reload: yes

  - name: Download and Launch a Docker ELK Container
    docker_container:
      name: elk
      image: sebp/elk:761
      state: started
      restart_policy: always
      published_ports:
        - 5601:5601
        - 9200:9200
        - 5044:5044

  - name: Enable Docker service on boot
    systemd:
      name: docker
      enabled: yes