---
title: "Hasła do wifi"
date: 2020-01-05T23:42:30+01:00
description: "Sprawdzenie zapisanych haseł do sieci WiFi"
tags:
- Windows
series:
-
categories:
-
featured_image: "ikony_tematow/wave.png"
---

Zdarza się, że potrzebujemy hasło do sieci wifi, do której się wcześniej logowaliśmy, by móc zalogować się np. w telefonie, ale nie pamiętamy hasła.
Rozwiązanie pod Windowsa jest bardzo proste. Jedyne czego potrzebujemy to `cmd`.

W terminalu wpisujemy poniższe polecenie:
```
netsh wlan show profiles
```

Otrzymamy listę zapisanych sieci:
![Lista sieci](/images/scrsht/netlist.PNG) 

Następnie wpisujemy poniższe polecenie, gdzie zamiast NAZWA_SIECI podajemy interesującą nas sieć:
```
netsh wlan show profiles name=NAZWA_SIECI key=clear
```


Nasze hasło zapisane jest pod `Key Content`:

![Lista sieci](/images/scrsht/wifi_passwd.PNG) 