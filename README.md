# ansible-challenge

# Files to edit:

1. roles/lamp/defaults/main.yml:

packages:
  - apache2
  - mysql-server
  - mysql-common
  - mysql-client
  - libapache2-mod-wsgi
services:
  - apache2
  - mysql

2. roles/lamp/tasks/main.yml:

- name: Install our packages
  apt:
    name: "{{ packages }}"
    state: present
    update_cache: true

- name: Confirm services are running
  service:
    name: "{{ item }}"
    state: started
  with_items: "{{ services }}"

- name: Enable Apache2 modssl
  shell: a2enmod ssl

- name: Enable Apache2 Default HTTPS site
  shell: a2ensite default-ssl
  notify: Restart Apache

3. roles/webapplication/defaults/main.yml:

---
app_download_dest: /tmp/webapplication
app_dest: /var/www/webapplication
app_repo: https://github.com/cloudacademy/ansible_demo.git

4. roles/webapplication/tasks/site.yml

- apt: name=libapache2-mod-wsgi state=present
- name: Copy the apache configuration file
  template:
    src: apache.conf
    dest: /etc/apache2/sites-available/000-default.conf
  notify: Restart Apache

5. app.yml in root directory:

---
- hosts: localhost
  gather_facts: false
  connection: local
  become: yes

  roles:
    - lamp
    - webapplication