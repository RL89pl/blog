---
title: "IP mojego RaspberryPi"
date: 2020-01-05T00:34:30+01:00
description: "Moje Raspberry automatycznie po uruchomieniu łączy się z VPN. Potrzebuję tylko IP. Jednym z wielu rozwiązań, będzie automat wysyłający go SMSem"
tags:
- Raspberry
- Python
series:
-
categories:
- Skrypty
featured_image: "ikony_tematow/raspberry.png"
---
Moje Raspberry automatycznie po uruchomieniu łączy się z VPN. Bez statycznego IP, ciężko jest wyszukać adres urządzenia, gdy nieoczekiwanie nastąpi restart, albo chwilowe rozłączenie z siecią. 
Jednym z wielu rozwiązań, będzie automat wysyłający SMSem swój adres IP. 
Dzięki takiemu rozwiązaniu mogę z każdego miejsca na ziemi połączyć się z moim Raspberry, nie zastanawiając się jakie ma IP.

### Potrzebne biblioteki

By sprawdzić IP potrzebna jest biblioteka [netifaces](https://pypi.org/project/netifaces/), a do wysyłania SMSów najlepszą opcją będzie `Nexmo`

### Skrypt Python

``` python
import netifaces as ni
import nexmo
client = nexmo.Client(key='klucz', secret='kod')
a = ni.ifaddresses('tap0')
for c, d in a.items():
    for z in d:
        adres = z['addr']
        if '.' in adres:
                client.send_message({'from': 'Raspberry', 'to': 48123456789, 'text': adres, 'type': 'unicode'})
```

Następnie dodaję do crona odpowiednie zadania. Dzięki pierwszemu wpisowi, po restarcie łączy się z odpowiednim VPNem, a drugi uruchamia minutę po restarcie powyższy skrypt.

### Zadania cron

``` bash
@reboot sudo openvpn --config /etc/default/plik_vpn.ovpn
@reboot sleep 60 && /usr/bin/python3 /root/Desktop/Skrypty/send_ip.py
```
---
