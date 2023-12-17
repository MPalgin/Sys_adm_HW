Код терраформ:
https://github.com/MPalgin/Sys_adm_HW/blob/main/Terraform_diplom.md

Ansible playbook:

Проверка работы балансировщика:
```
</html>mpalgin@mpalgin-VirtualBox:~/learning/terraform$ curl -v 158.160.137.200:80
*   Trying 158.160.137.200:80...
* Connected to 158.160.137.200 (158.160.137.200) port 80 (#0)
> GET / HTTP/1.1
> Host: 158.160.137.200
> User-Agent: curl/7.81.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< server: ycalb
< date: Sun, 17 Dec 2023 11:42:34 GMT
< content-type: text/html
< content-length: 211
< last-modified: Sun, 17 Dec 2023 10:44:12 GMT
< etag: "657ed0fc-d3"
< accept-ranges: bytes
< 
<html>

<h4>CPU: ['0', 'GenuineIntel', 'Intel Core Processor (Haswell, no TSX)', '1', 'GenuineIntel', 'Intel Core Processor (Haswell, no TSX)']</h4>

<h4>RAM: 1963</h4>

<h4>IP_ADRESS: 172.16.16.16</h4>

* Connection #0 to host 158.160.137.200 left intact
```

```
</html>mpalgin@mpalgin-VirtualBox:~/learning/terraform$ curl -v 158.160.137.200:80
*   Trying 158.160.137.200:80...
* Connected to 158.160.137.200 (158.160.137.200) port 80 (#0)
> GET / HTTP/1.1
> Host: 158.160.137.200
> User-Agent: curl/7.81.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< server: ycalb
< date: Sun, 17 Dec 2023 11:42:33 GMT
< content-type: text/html
< content-length: 211
< last-modified: Sun, 17 Dec 2023 10:42:46 GMT
< etag: "657ed0a6-d3"
< accept-ranges: bytes
< 
<html>

<h4>CPU: ['0', 'GenuineIntel', 'Intel Core Processor (Haswell, no TSX)', '1', 'GenuineIntel', 'Intel Core Processor (Haswell, no TSX)']</h4>

<h4>RAM: 1963</h4>

<h4>IP_ADRESS: 172.16.18.29</h4>

* Connection #0 to host 158.160.137.200 left intact

```



