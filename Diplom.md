Код терраформ:

https://github.com/MPalgin/Sys_adm_HW/blob/main/main.tf

Ansible playbook:

https://github.com/MPalgin/Sys_adm_HW/tree/main/service_install

Ansible заходил на хосты с nginx с помощью прокси настроенной в inventory файле и fqdn:

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/7cf23e74-4716-4228-afdf-a65f14fc43b2)

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

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/cf0892c3-a4dc-4d9d-b3a6-d842db189f56)


elastic_server:

http://158.160.113.33:5601/app/discover#/?_g=(filters:!(),refreshInterval:(pause:!t,value:60000),time:(from:now-15m,to:now))&_a=(columns:!(),filters:!(),index:'463eaf74-f060-4100-9ff1-9d8d2b63afc4',interval:auto,query:(language:kuery,query:''),sort:!(!('@timestamp',desc)))

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/e6209dc8-f91e-4665-a2ea-04b3c067a1b8)

Расписание создания snapshot дисков:

```
resource "yandex_compute_snapshot_schedule" "vm-sceduler" {
  name           = "vm-sceduler"

  schedule_policy {
	expression = "0 0 ? * *"
  }

  snapshot_count = 7

  disk_ids = ["yandex_compute_instance.secvm.boot_disk.id", "yandex_compute_instance.webvm1.boot_disk.id", "yandex_compute_instance.elastic-vm.boot_disk.id", "yandex_compute_instance.kibana-vm.boot_disk.id", "yandex_compute_instance.zabbix-vm.boot_disk.id"]

```



