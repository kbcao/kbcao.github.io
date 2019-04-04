---
title: Ubuntu-Apache发布网站
comments: true
categories:
  - server
  - website
tags:
  - null
abbrlink: 8f042342
date: 2019-04-04 11:24:27
---

# Ubuntu-Apache发布网站

1. 把网站文件夹 Project 放到 /var/www/ 下
2. `sudo chmod 777 /var/www/Project`
3. 进入 `/etc/apache2/sites-available`
4. `sudo cp 000-default.conf Project.conf`
5. `sudo vi Project.conf` 修改 VirtualHost 为自定义的端口（例如88）将 DocumentRoot 改成`/var/www/Project`
6. `sudo vi /etc/apache2/apache2.conf`在中间部分增加

    ```json
    <Directory /var/www/Project/>
            Options Indexes FollowSymLinks
            AllowOverride None
            Require all granted
    </Directory>
    ```

7. 进入 `/etc/apache2/sites-enabled`
8. `sudo ln -s ../sites-available/Project.conf Project.conf` 建立软连接
9. 进入`/etc/apache2/`
10. `sudo vi ports.conf` 增加 Listen +自定义端口号（例如88）
11. `sudo service apache2 restart`
12. `http://ip:port`