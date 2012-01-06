Netzwerk einrichten
===================

.. admonition:: Netzwerk-Adresse herausfinden

  In den Tutorials werden häufig Netzwerkadressen benötigt. Welchen IP-Bereich
  man dafür wählt ist zunächst egal, so lange man alle beteiligten Rechner
  (Server und Clients) im gleichen Bereich konfiguriert. Die Tutorials werden
  beispielhaft den Bereich ``192.168.0.0/24`` benutzen, in dem der Server die
  IP ``192.168.0.254`` tragen wird um eventuelle Kollisionen mit einem bereits
  vorhandenen Router vorzubeugen.

  Wie sie ihren Adressbereich herausfinden zeigt der Abschnitt
  :ref:`Netzwerk-Adresse herausfinden<findaddress>` im Anhang. Wichtig ist, dass
  sie dem Server nicht die gleiche Adresse geben wie ihrem Rechner. Die Ziffer
  hinter dem letzten Punkt sollte sich unterscheiden und nicht höher sein als 254.

Nach dem Einloggen ins System, sollte zuerst überprüft werden ob das Netzwerk
richtig eingerichtet ist. Der Befehl ``ifconfig`` bringt eine Übersicht über
die vorhandenen Netzwerkschnittstellen und die ihnen zugewiesenen Adressen. Hier
als Beispiel von einem Rechner mit nur einer Netzwerkkarte.

::

    $ ifconfig
    eth0      Link encap:Ethernet  Hardware Adresse 08:00:27:61:3f:9c
            inet Adresse:192.168.0.254  Bcast:192.168.103.255  Maske:255.255.255.0
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

**eth0** bezeichnet hier die erste Netzwerkkarte. Im Beispiel besitzt die
Schnittstelle die Adresse **192.168.0.254**. Wie man die Adresse seinen eigenen
Bedürfnissen anpasst zeigt der folgende Abschnitt.

**lo** ist eine sogenannte `Loopback-Schnittstelle <http://de.wikipedia.org/wiki/Loopback>`_. Sie kann zum Beispiel verwendet werden um bestimmte Serverdienste nur für den Rechner auf dem sie laufen zur Verfügung zu stellen.

Netzwerkadresse festlegen
-------------------------

Um die Netzwerkkonfiguration zu ändern muss, mit einem Editor
(siehe :ref:`Wahl eines Editors <editor>`),
die Datei (**/etc/network/interfaces**) bearbeitet werden. Im Ausgangszustand,
sieht diese so aus:

::

    # This file describes the network interfaces available on your system
    # and how to activate them. For more information, see interfaces(5).

    # The loopback network interface
    auto lo
    iface lo inet loopback

    # The primary network interface
    auto eth0
    iface eth0 inet dhcp

Die erste Netzwerkkarte **eth0** ist so eingerichtet, dass sie die Adresse von
einem `DHCP <http://de.wikipedia.org/wiki/DHCP>`_-Server bezieht. Die Zeile
``auto eth0`` sorgt dafür das die Schnittstelle automatisch gestartet wird.
Das ``dhcp`` in der letzten Zeile sorgt für den automatischen Empfang einer
Netzwerkadresse von einem anderen Server oder einem Router.
Wenn man den DHCP-Server nicht so einstellen kann, dass er dem Server immer die
gleiche Adresse zuweist sollte man eine feste IP-Adresse vergeben. Dazu muss
der Eintrag für **eth0** folgendermaßen abgeändert werden:

.. code-block:: none
    :linenos:

    auto eth0
    iface eth0 inet static
      address 192.168.0.254
      netmask 255.255.255.0
      gateway 192.168.0.1
      dns-nameservers 192.168.0.1

Das ``dhcp`` in Zeile 2 wird durch ``static`` ersetzt. In Zeile 3 die
Adresse zugeteilt: ``address 192.168.0.254``, in Zeile 4 die `Netzmaske
<http://de.wikipedia.org/wiki/Netzmaske>`_: ``netmask 255.255.255.0`` und in
Zeile 5 das Gateway, in der Regel die Adresse des zuständigen Routers im
Netzwerk: ``gateway 192.168.0.1``. Zusätzlich sollte wie in Zeile 6 auch noch
mindestens ein `DNS <http://de.wikipedia.org/wiki/Domain_Name_System>`_-Server
angegeben werden. In der Regel ist auch hier die Adresse des lokalen Routers
einzutragen: ``dns-nameservers 192.168.0.1``. Die Angaben sollten natürlich an
das jeweilige Netzwerk und die eigenen Bedürfnisse angepasst werden.

Nachdem die Einträge gemacht sind, kann die Datei gespeichert und geschlossen
werden. Um die Änderungen zu übernehmen muss das Netzwerk neu gestartet
werden:

.. warning:: Falls man per SSH auf dem Server arbeitet, bricht die Verbindung
    ab, wenn man das Netzwerk neu startet.

::

    $ sudo service networking restart

Zum Abschluss der Konfiguration sollte noch  getestet werden, ob der Zugriff
zum Internet auch funktioniert.

::

    $ ping -c 4 ubuntu.com
    PING ubuntu.com (91.189.94.156) 56(84) bytes of data.
    64 bytes from vostok.canonical.com (91.189.94.156): icmp_seq=1 ttl=45 time=37.6 ms
    64 bytes from vostok.canonical.com (91.189.94.156): icmp_seq=2 ttl=45 time=31.8 ms
    64 bytes from vostok.canonical.com (91.189.94.156): icmp_seq=3 ttl=45 time=32.7 ms
    64 bytes from vostok.canonical.com (91.189.94.156): icmp_seq=4 ttl=45 time=31.5 ms

    --- ubuntu.com ping statistics ---
    4 packets transmitted, 4 received, 0% packet loss, time 3007ms
    rtt min/avg/max/mdev = 31.517/33.464/37.689/2.490 ms

Der Befehl ``ping -c 4 ubuntu.com`` prüft ob der Server hinter ubuntu.com
erreichbar ist. Er sendet dazu 4 (Parameter ``-c 4``) `ICMP 
<Internet_Control_Message_Protocol>`_-"Echo-Request"-Pakete an den Server und
wartet auf eine Antwort. Im oben gezeigten Fall ist die Verbindung erfolgreich.
Wenn die Ausgabe nicht der obigen entspricht sollte noch einmal ein Blick auf
die Netzwerkkonfiguration geworfen werden (Sofern man Hardware Probleme wie
lose oder kaputte Kabel ausschließen kann).

