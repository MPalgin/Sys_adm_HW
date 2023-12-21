Код терраформ:
https://github.com/MPalgin/Sys_adm_HW/blob/main/Terraform_diplom.md

Ansible playbook:

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
![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/53495121-ec3b-4480-b92b-4b1523153ada)

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/44f7f9f6-95e6-4ba1-b1cc-81b202d998c4)

elastic_server:

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/e6209dc8-f91e-4665-a2ea-04b3c067a1b8)

Расписание создания snapshot дисков:

![image](https://github.com/MPalgin/Sys_adm_HW/assets/121052923/4d809fc2-3aa4-4748-ab26-90622aba1027)




