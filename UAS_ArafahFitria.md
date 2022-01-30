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
![2](https://user-images.githubusercontent.com/92453574/151706324-62b4279e-76b2-4f40-950e-913862a6a6db.png)

2.	File /app/handlers/main.yml
![3](https://user-images.githubusercontent.com/92453574/151706326-7f0b4fce-acf0-44ba-aa4c-1851ade29f4e.png)

3.	File /app/tasks/main.yml
![4](https://user-images.githubusercontent.com/92453574/151706327-33435f5d-9751-43a7-845a-d119d774a365.png)


4.	File app/templates/app.conf
![5](https://user-images.githubusercontent.com/92453574/151706318-f8d83dd3-3dc6-4a83-9f4a-803a7a4f7c5a.png)


5.	Run ansible deploy-app
6.	Hasil CI
![6](https://user-images.githubusercontent.com/92453574/151706321-fcd6696b-c0d2-40f5-8c9c-a7c488c117a9.png)



**LARAVEL**
1.	Set domain Laravel
![7](https://user-images.githubusercontent.com/92453574/151706553-bc5a209b-f827-4d98-892a-d9f5afa47ad1.png)


2.	role/php/tasks/main.yml
![8](https://user-images.githubusercontent.com/92453574/151706555-22c310d0-28e7-4568-a630-a55c57f6bec6.png)


3.	role/php/handlres/main.yml
![9](https://user-images.githubusercontent.com/92453574/151706556-3942baad-af31-4152-8ece-15c27a21d7a9.png)


4.	role/lv/handlers/main.yml
![10](https://user-images.githubusercontent.com/92453574/151706558-73f98f77-7eb4-4c85-9a2e-1bd322236887.png)


5.	role/lv/tasks/main.yml
![11](https://user-images.githubusercontent.com/92453574/151706559-b6d44318-13cc-4745-af13-04cc97db6834.png)


6.	role/lv/templates/env.templates
![12](https://user-images.githubusercontent.com/92453574/151706562-32fe8f41-1cb6-4d63-a052-10fe6b999c82.png)


7.	role/lv/templates/lv.conf
![13](https://user-images.githubusercontent.com/92453574/151706564-dea6daf8-56c5-45d9-8943-308023bc37e8.png)


8.	hasil Laravel
![14](https://user-images.githubusercontent.com/92453574/151706565-99b0d4a2-a914-4c04-bafd-442cdee53a1f.png)




