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


