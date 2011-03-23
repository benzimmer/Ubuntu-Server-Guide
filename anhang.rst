.. _findaddress:

Netzwerk-Adresse herausfinden
=============================

Linux
-----

Terminal öffnen und dort eingeben:::

  $ ifconfig

Darauf sollte eine Ausgabe ähnlich der folgenden erscheinen:

::

    eth0      Link encap:Ethernet  Hardware Adresse 08:00:27:61:3f:9c  
            inet Adresse:192.168.0.30  Bcast:192.168.103.255  Maske:255.255.255.0
            inet6-Adresse: fe80::a00:27ff:fe61:3f9c/64 Gültigkeitsbereich:Verbindung
            UP BROADCAST RUNNING MULTICAST  MTU:1500  Metrik:1
            RX packets:3759 errors:0 dropped:0 overruns:0 frame:0
            TX packets:193 errors:0 dropped:0 overruns:0 carrier:0
            Kollisionen:0 Sendewarteschlangenlänge:1000 
            RX bytes:911376 (911.3 KB)  TX bytes:19157 (19.1 KB)

    lo        Link encap:Lokale Schleife
            inet Adresse:127.0.0.1  Maske:255.0.0.0
            inet6-Adresse: ::1/128 Gültigkeitsbereich:Maschine
            UP LOOPBACK RUNNING  MTU:16436  Metrik:1
            RX packets:0 errors:0 dropped:0 overruns:0 frame:0
            TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
            Kollisionen:0 Sendewarteschlangenlänge:0 
            RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

Die Netzwerkadresse findet sich in obiger Ausgabe im Block ``eth0`` in der
zweiten Zeile hinter ``inet``, also ``192.168.0.30``. Falls ihr ein WLAN
benutzt kann das Netzwerkinterface aber auch ``eth1``, ``ath0`` oder auch
``wifi0`` heißen. Hier heißt es Augen auf :-)

Windows
-------

Windows XP
~~~~~~~~~~

Die Netzwerkadapter findet man unter **Start->Systemsteuerung->Netzwerkverbindungen**.
Dort klickt man doppelt auf die gewünschte Netzwerkverbindung (meist
*LAN-Verbindung* oder *Drahtlose Netzwerkverbindung*) und wählt den Karteireiter
*Netzwerkunterstützung*. Dort findet man die Adresse in der Zeile **IP-Adresse**.

Windows 7
~~~~~~~~~~

In der Taskleiste links neben der Uhr klickt man einmal auf das Symbol für die
Netzwerkverbindung (entweder ein Monitor mit Kabel davor, oder eine Reihe
größer werdender Balken) und wählt Netzwerk- und Freigabecenter öffnen. Dort
findet man in der zweiten Zeile einen Link mit dem Namen *LAN-Verbindung*. Nach
einem Klick darauf, gelangt man über *Details* zum gewünschten Dialog in dem
man in der Zeile **IPv4 Adresse** seine Netzwerkadresse findet.

MacOS X
-------

Terminal öffnen und dort eingeben:::

  $ ifconfig

Darauf sollte eine Ausgabe ähnlich der folgenden erscheinen:

::

  lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> mtu 16384
      inet 127.0.0.1 netmask 0xff000000 
      inet6 ::1 prefixlen 128 
      inet6 fe80::1%lo0 prefixlen 64 scopeid 0x1 
  en1: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
      ether 90:27:e4:fe:26:38 
      inet6 fe80::9227:e4ff:fefe:2638%en1 prefixlen 64 scopeid 0x4 
      inet 192.168.0.30 netmask 0xffffff00 broadcast 192.168.103.255
      media: autoselect
      status: active
  en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
      ether c4:2c:03:1e:03:47 
      media: autoselect
      status: inactive

``en1`` bezeichnet dabei in der Regel die WLAN-Karte, ``en0`` das
Kabel-Inferface. Die Adresse findet sich in obiger Ausgabe im Block ``en1`` in
Zeile 3 hinter ``inet``, also ``192.168.0.30``.
