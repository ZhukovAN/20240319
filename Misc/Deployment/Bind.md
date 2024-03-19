---
tags:
  - deployment
  - bind
---
# Развертывание локального DNS-сервера Bind
ПО Bind входит в состав стандартной поставки ОС Linux и, таким образом, может быть развернуто с использованием соответствующего пакетного менеджера:
``` bash
apt install bind9 dnsutils -y
```
После установки необходимо сконфигурировать базовые настройки, находящиеся в файле `/etc/bind/named.conf.options`:
```
options {
	forwarders {
		192.168.0.1;
	};
	directory "/var/cache/bind";
	listen-on { any; };
	recursion yes;
	allow-recursion { 127.0.0.1/32; 192.168.0.0/24; 172.17.0.0/16; 172.18.0.0/16; };
	dnssec-validation no;
};

zone "ptdemo.local." {
        type master;
        file "/var/lib/bind/db.ptdemo.local";
        notify explicit;
};

zone "8.168.192.in-addr.arpa" {
        type master;
        file "/var/lib/bind/db.0.168.192.in-addr.arpa";
};
```
Кроме того, в каталоге `/var/lib/bind` необходимо создать файлы, описывающие соответствующие DNS-зоны. Например, файл зоны  `db.ptdemo.local` может иметь следующий вид: 
```
$TTL 86400

@ IN SOA infra-aio admin.ptdemo.local (
    2024021601
    3600
    900
    604800
    86400
)

                IN NS       infra-aio

infra-aio       IN A        192.168.0.55
nexus           IN CNAME    infra-aio
registry        IN CNAME    infra-aio
dockerhub       IN CNAME    infra-aio
```
Файл `db.0.168.192.in-addr.arpa`, используемый для противоположной задачи, т.е., для преобразования IP-адреса в имя хоста, может выглядеть следующим образом:
```
$TTL 86400

@ IN SOA infra-aio admin.ptdemo.local. (
    202311161
    3600
    900
    604800
    86400
)

               IN NS       192.168.0.55.
101            IN PTR      k8s-1.net173.org.
```
После внесения изменений в конфигурационные файлы необходимо перезагрузить конфигурацию:
``` bash
rndc reload
```
Кроме того, в сетевых настройках хоста необходимо изменить IP-адрес DNS-сервера, указанный в файле `/etc/resolv.conf`:
```
nameserver 192.168.0.55
```
И в завершение необходимо убедиться в том, что развернутый DNS-сервер успешно обрабатывает запросы к внешним и внутренним ресурсам:
``` bash
nslookup ya.ru 192.168.0.55
nslookup nexus.ptdemo.local 192.168.0.55
```