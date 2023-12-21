Код терраформ:
https://github.com/MPalgin/Sys_adm_HW/blob/main/Terraform_diplom.md

Ansible playbook:

https://github.com/MPalgin/Sys_adm_HW/tree/main/service_install
Сервисы на VM разворачивались на локальной VM с помощью настройки с /etc/hosts на локальной VM и на VM выступающей в качестве бастион хоста:

локальная VM

```
51.250.92.135 secvm.ru-central1.internal
172.16.18.26 webvm2.ru-central1.internal
158.160.125.94 zabbix-vm.ru-central1.internal
158.160.127.250 elastic-vm.ru-central1.internal
158.160.113.33 kibana-vm.ru-central1.internal
172.16.16.16 webvm1.ru-central1.internal

```
Бастион VM:

```
172.16.18.26 webvm2.ru-central1.internal
158.160.125.94 zabbix-vm.ru-central1.internal
158.160.127.250 elastic-vm.ru-central1.internal
158.160.113.33 kibana-vm.ru-central1.internal
172.16.16.16 webvm1.ru-central1.internal

```

Ansible заходил на хосты с nginx с помощью проксси настроенной в inventory файле и fqdn:

```
[proxy]
secvm.ru-central1.internal

[web_hosts:children]
web1
web2

[web1]
webvm1.ru-central1.internal ansible_user=user

[web2]
webvm2.ru-central1.internal ansible_user=user

[zabbix_server:children]
zabbix_host

[zabbix_host]
zabbix-vm.ru-central1.internal ansible_user=user

[elastic_server]
elastic-vm.ru-central1.internal ansible_user=user


[kibana_server]
kibana-vm.ru-central1.internal ansible_user=user

[web_hosts:vars]
ansible_ssh_common_args='-o ProxyCommand="ssh -p 22 -W %h:%p -q user@secvm.ru-central1.internal"'
```
Роли для elastic, filebeat, kibana и zabbix были звяты с ansible-galaxy и установлены через командную строку(zabbix-comunity) и с помощью requirements.yml файла для ролей geerlingguy.kibana, geerlingguy.elasticsearch, geerlingguy.postgresql, geerlingguy.filebeat. Nginx был установлен без ansible-galaxy. Nginx работает:

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/543d65d3-8603-4b99-842b-c50a0b9bdf3c)

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/2752ae8f-c14c-4a1f-b787-a6b7e2fbdd66)

Доступность проверялась с помощью ssh туннелей.


Проверка работы балансировщика:


```
</html>mpalgin@mpalgin-VirtualBox:~$ curl -v 158.160.136.130:80
*   Trying 158.160.136.130:80...
* Connected to 158.160.136.130 (158.160.136.130) port 80 (#0)
> GET / HTTP/1.1
> Host: 158.160.136.130
> User-Agent: curl/7.81.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< server: ycalb
< date: Thu, 21 Dec 2023 09:53:30 GMT
< content-type: text/html
< content-length: 211
< last-modified: Thu, 21 Dec 2023 08:33:35 GMT
< etag: "6583f85f-d3"
< accept-ranges: bytes
< 
<html>

<h4>CPU: ['0', 'GenuineIntel', 'Intel Core Processor (Haswell, no TSX)', '1', 'GenuineIntel', 'Intel Core Processor (Haswell, no TSX)']</h4>

<h4>RAM: 1963</h4>

<h4>IP_ADRESS: 172.16.18.26</h4>

* Connection #0 to host 158.160.136.130 left intact
```

```
</html>^[[Ampalgin@mpalgin-VirtualBox:~$ curl -v 158.160.136.130:80
*   Trying 158.160.136.130:80...
* Connected to 158.160.136.130 (158.160.136.130) port 80 (#0)
> GET / HTTP/1.1
> Host: 158.160.136.130
> User-Agent: curl/7.81.0
> Accept: */*
> 
^[[A* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< server: ycalb
< date: Thu, 21 Dec 2023 09:53:31 GMT
< content-type: text/html
< content-length: 211
< last-modified: Thu, 21 Dec 2023 08:33:35 GMT
< etag: "6583f85f-d3"
< accept-ranges: bytes
< 
<html>

<h4>CPU: ['0', 'GenuineIntel', 'Intel Core Processor (Haswell, no TSX)', '1', 'GenuineIntel', 'Intel Core Processor (Haswell, no TSX)']</h4>

<h4>RAM: 1963</h4>

<h4>IP_ADRESS: 172.16.16.16</h4>

* Connection #0 to host 158.160.136.130 left intact

```
Zabbix сервер с метриками:

http://158.160.125.94/zabbix/

User: Admin
Pass:zabbix

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/53495121-ec3b-4480-b92b-4b1523153ada)

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/44f7f9f6-95e6-4ba1-b1cc-81b202d998c4)

elastic_server:

http://158.160.113.33:5601/app/discover#/?_g=(filters:!(),refreshInterval:(pause:!t,value:60000),time:(from:now-15m,to:now))&_a=(columns:!(),filters:!(),index:'463eaf74-f060-4100-9ff1-9d8d2b63afc4',interval:auto,query:(language:kuery,query:''),sort:!(!('@timestamp',desc)))

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/e6209dc8-f91e-4665-a2ea-04b3c067a1b8)

Расписание создания snapshot дисков:

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/4d809fc2-3aa4-4748-ab26-90622aba1027)




