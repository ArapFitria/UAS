TUGAS BESAR 
---
Annisa Aulia Arafah    (1202190038)

Fitria Rahma Wulandari (1202190045)

---

•	Install LXC atau containers sesuai kebutuhan. Setting IP sehingga tidak ada yang sama
```
sudo lxc-create -n lxc_php7_1 -t download -- --dist ubuntu --release focal --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org
```
![1](https://user-images.githubusercontent.com/92453574/151706235-a32fe8e7-57bc-4490-843d-248ee3d5dd49.png)


•	Code Igniter
1.	File deploy-app.yml
```
- hosts: ci
  vars:
    git_url: 'https://github.com/aldonesia/sas-ci'
    destdir: '/var/www/html/ci'
    domain: 'lxc_php5_1.dev'
    domain: 'lxc_php5_2.dev'
  roles:
    - app
```

![2](https://user-images.githubusercontent.com/92453574/151706324-62b4279e-76b2-4f40-950e-913862a6a6db.png)

2.	File /app/handlers/main.yml
![3](https://user-images.githubusercontent.com/92453574/151706326-7f0b4fce-acf0-44ba-aa4c-1851ade29f4e.png)

3.	File /app/tasks/main.yml
![4](https://user-images.githubusercontent.com/92453574/151706327-33435f5d-9751-43a7-845a-d119d774a365.png)


4.	File app/templates/app.conf
```
server {
  listen 80;
  server_name {{servername}};
  root {{ destdir }};
  index index.php;
  location / {
     try_files $uri $uri/ /index.php?$query_string;
  }
  location ~ \.php$ {
     fastcgi_pass unix:/run/php/php5.6-fpm.sock;  #Sesuaikan dengan versi PHP
     fastcgi_index index.php;
     fastcgi_param SCRIPT_FILENAME {{ destdir }}$fastcgi_script_name;
     include fastcgi_params;
  }
}
```

![5](https://user-images.githubusercontent.com/92453574/151706318-f8d83dd3-3dc6-4a83-9f4a-803a7a4f7c5a.png)


5.	Run ansible deploy-app
6.	Hasil CI
![6](https://user-images.githubusercontent.com/92453574/151706321-fcd6696b-c0d2-40f5-8c9c-a7c488c117a9.png)



**LARAVEL**
1.	Set domain Laravel
```
- hosts: laravel
  vars:
    username: 'arafah'
    password: '1234'
    domain: "lxc_php7_2.dev"
    domain: "lxc_php7_3.dev"
    domain: "lxc_php7_4.dev"
    domain: "lxc_php7_5.dev"
  roles:
    - php
    - lv
```
![7](https://user-images.githubusercontent.com/92453574/151706553-bc5a209b-f827-4d98-892a-d9f5afa47ad1.png)


2.	role/php/tasks/main.yml
```
---
- name: delete apt chache
  become: yes
  become_user: root
  become_method: su
  command: rm -vf /var/lib/apt/lists/*

- name: install nginx phpmyadmin
  become: yes
  become_user: root
  become_method: su
  apt: name={{ item }} state=latest update_cache=true
  with_items:
    - gtkhash
    - crack-md5
    - git
    - curl
    - nginx
    - nginx-extras
    - php7.4
    - php7.4-fpm
    - php7.4-curl
    - php7.4-xml
    - php7.4-gd
    - php7.4-opcache
    - php7.4-mbstring
    - php7.4-zip
    - php7.4-json
    - php7.4-cli

- name: enable module php mbstring
  command: phpenmod mbstring
  notify:
    - restart php
```
![8](https://user-images.githubusercontent.com/92453574/151706555-22c310d0-28e7-4568-a630-a55c57f6bec6.png)


3.	role/php/handlres/main.yml
```
---
- name: restart php
  become: yes
  become_user: root
  become_method: su
  action: service name=php7.4-fpm state=restarted
- name: restart nginx
  become: yes
  become_user: root
  become_method: su
  action: service name=php7.4-fpm state=restarted
```
![9](https://user-images.githubusercontent.com/92453574/151706556-3942baad-af31-4152-8ece-15c27a21d7a9.png)


4.	role/lv/handlers/main.yml
![10](https://user-images.githubusercontent.com/92453574/151706558-73f98f77-7eb4-4c85-9a2e-1bd322236887.png)


5.	role/lv/tasks/main.yml
```
---
- name: delete apt chache
  become: yes
  become_user: root
  become_method: su
  command: rm -vf /var/lib/apt/lists/*

- name: Download and install Composer
  shell: curl -sS https://getcomposer.org/installer | php
  args:
    chdir: /usr/src/
    creates: /usr/local/bin/composer
    warn: false
  become: yes

- name: Add Composer to global path
  copy:
    dest: /usr/local/bin/composer
    group: root
    mode: '0755'
    owner: root
    src: /usr/src/composer.phar
    remote_src: yes
  become: yes

- name: Ansible delete file create-project
  file:
    path: /var/www/html/lara
    state: absent

- name: composer create-project
  shell: /usr/local/bin/composer create-project laravel/laravel /var/www/html/lara --prefer-dist --no-interaction

- name: Copy .env.template
  template:
    src=templates/env.template
    dest=/var/www/html/lara/.env

- name: composer
  shell: cd /var/www/html/lara; /usr/local/bin/composer install  --no-interaction

- name: key
  shell: /usr/bin/php7.4 /var/www/html/lara/artisan key:generate

- name: chmod
  become: yes
  become_user: root
  become_method: su
  command: chmod 777 -R /var/www/html/lara/storage

- name: Copy lv.conf
  template:
    src=templates/lv.conf
    dest=/etc/nginx/sites-available/{{ domain }}
  vars:
    servername: '{{ domain }}'

- name: copy php7.conf
  template:
    src=templates/php7.conf
    dest=/etc/php/7.4/fpm/pool.d/www.conf

- name: Symlink lv.conf
  command: ln -sfn /etc/nginx/sites-available/{{ domain }} /etc/nginx/sites-enabled/{{ domain }}
  notify:
    - restart nginx

- name: Write {{ domain }} to /etc/hosts
  lineinfile:
    dest: /etc/hosts
    regexp: '.*{{ domain }}$'
    line: "127.0.0.1 {{ domain }}"
    state: present

```
![11](https://user-images.githubusercontent.com/92453574/151706559-b6d44318-13cc-4745-af13-04cc97db6834.png)


6.	role/lv/templates/env.templates
```
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://kelompok11.fpsas

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=10.0.3.50
DB_PORT=3306
DB_DATABASE=lara_db
DB_USERNAME=iqbal
DB_PASSWORD=iqbal

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DRIVER=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=null
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_APP_CLUSTER=mt1

MIX_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
MIX_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
```
![12](https://user-images.githubusercontent.com/92453574/151706562-32fe8f41-1cb6-4d63-a052-10fe6b999c82.png)


7.	role/lv/templates/lv.conf
```
server {
  listen 80;
  listen [::]:80;
  
  access_log /var/log/nginx/vhostlaravel-access.log;
  error_log /var.log.nginx/vhostlaravel-error.log;
  
  root/var/html/laravel/public;
  index index.php index.html index.htm;
  
  server_name lxc_PHP_2.dev lxc_PHP7_3.dev lxc_PHP7_4.dev lxc_PHP7_5.dev;
  
  location / {
    try_files $uri $uri/ /index.php?$query_string;
  }
  
  location ~ \.php5 {
    try_files $uri =404;
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass unix:/ru/php/php7.4-fpm.sock;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_param;
```
![13](https://user-images.githubusercontent.com/92453574/151706564-dea6daf8-56c5-45d9-8943-308023bc37e8.png)


8.	hasil Laravel
![14](https://user-images.githubusercontent.com/92453574/151706565-99b0d4a2-a914-4c04-bafd-442cdee53a1f.png)


**WORDPRESS**
1.	Install wp
![15](https://user-images.githubusercontent.com/92453574/151706709-b9cc0b53-ed1f-4370-a95e-2ae06a82aa8d.png)


3.	Wp.conf
![16](https://user-images.githubusercontent.com/92453574/151706711-940b8cbb-26d7-4b34-9fa6-16932ef09896.png)


3.	Wordpress.conf
![17](https://user-images.githubusercontent.com/92453574/151706712-930cb440-d615-4af0-a51b-7d12c47efb2b.png)


4.	Hasil WordPress
![18](https://user-images.githubusercontent.com/92453574/151706713-4dd0f276-9364-4af5-814a-a6807e183525.png)



**LXC DATABASE**
1.	Database
![19](https://user-images.githubusercontent.com/92453574/151706880-d84aebb0-065c-485d-927b-b33ba113b4e7.png)


3.	Role/pma/tasks/main.yml
![20](https://user-images.githubusercontent.com/92453574/151706881-72ee056c-9524-4fd8-ab3b-0c505b58234e.png)


5.	Role/pma/handlers/main.yml
![21](https://user-images.githubusercontent.com/92453574/151706884-85326f7d-add9-43d6-90cc-e8aa9d2ce634.png)


7.	Role/pma/templates/pm.local
![22](https://user-images.githubusercontent.com/92453574/151706885-dc434773-afa4-4e06-b3e5-89105f7a1efd.png)


5.	Hasil Server
![23](https://user-images.githubusercontent.com/92453574/151706886-913bd378-0c71-446d-95d1-a0ac56c80bfe.png)

