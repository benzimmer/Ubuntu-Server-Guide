Einleitung
==========

Server sind aus der heutigen IT-Landschaft kaum noch wegzudenken. Fast jede
Firma, die für ihr Tagesgeschäft Computer benutzt, besitzt einen oder gar
mehrere Server. Die Vorteile liegen auf der Hand: Alle Daten sind zentral
verfügbar und liegen nicht mehr verstreut im Netzwerk herum. Das erleichtert
auch die Verwaltung und Sicherung der Datenbestände. Diese Vorteile erkennen
auch immer mehr Kleinunternehmer und neuerdings auch die Heimanwender, deren
Computerlandschaft im Eigenheim, teilweise schon der von kleinen Betrieben,
oder Büros gleicht. Nun kann oder will sich aber nicht gleich jeder einen 
High-End-Stromverbraucher, wie man sie Reihenweise von der Stange kaufen kann,
in den Keller stellen und die Bereitschaft, für Redmonder Serverprodukte und
zugehörige Clientlizenzen, einen Haufen Geld zu investieren, sinkt auch von
Tag zu Tag. Alternativen gibt es mehr als genug, und auf eine davon
konzentriert sich dieser Guide: Den Linux-Server. Als 
`Distribution <http://de.wikipedia.org/wiki/Linux-Distribution>`_ wird die
`Ubuntu Server Edition 
<http://www.ubuntu.com/products/whatisubuntu/serveredition>`_
in der aktuellen :abbr:`LTS (Long-Term-Support)` Version 10.04.2 verwendet.

Kleine Warnung vorweg
---------------------

.. warning::

    Ein Server ist kein Spielzeug, gerade dann nicht, wenn er seine Dienste direkt
    am Internet verrichtet. Ein schlecht konfigurierter oder nicht regelmäßig mit
    Updates versorgter Rechner, kann schnell zum Eigentor werden, wenn jemand
    anderes die Kontrolle darüber erlangt. Bevor man einen Server von außen 
    zugänglich macht, sollte man sich intensiv mit dem System beschäftigen, viel
    Lesen und wissen was zu tun ist, sollte trotz aller Vorsicht mal etwas
    passieren. Weiterhin sollte euer Gehirn immer im Hintergrund mitlaufen, denn
    niemand ist fehlerlos, so auch ich.

Voraussetzungen
---------------

Im Prinzip sind die Voraussetzungen für einen Server auf dem Linux läuft nicht
sehr hoch, zumindest im Vergleich zu Redmonder Qualitätsprodukten. Natürlich
hängt das Ganze auch mit der Beschaffenheit des Netzwerkes zusammen, in dem
der Server seine Dienste anbieten soll.

Natürlich steht es jedem frei seine Hardware so zusammenzustellen wie es ihm
beliebt, deshalb hier nur ein paar kleine Anhaltspunkte aus der offiziellen
Dokumentation:

    +-----------+-------+-----------+------------+
    |           | RAM   | Prozessor | Festplatte |
    +===========+=======+===========+============+
    | Minimal   | 128MB | 200Mhz    | 1GB        |
    +-----------+-------+-----------+------------+
    | Empfohlen | 256MB | 500Mhz    | >10GB      |
    +-----------+-------+-----------+------------+

In diesem Guide werde ich von folgender Beispielkonfiguration ausgehen:

Prozessor
    mind. 500Mhz

RAM
    1 GB

Festplatten
    1 x mind. 10GB für System (evtl. 
    `SSD <http://de.wikipedia.org/wiki/Solid_State_Drive>`_ 
    oder USB-Stick)

..    2 x mind. 500GB für Daten gespiegelt im `RAID <http://de.wikipedia.org/wiki/RAID>`_-1

.. todo: RAID + LVM Konfiguration

Netzwerk
    1 x mind. 100Mbit (besser 1000Mbit) Netzwerkkarte

Des weiteren werden natürlich Monitor und Tastatur (nur zur Installation)
sowie ein CD-Laufwerk (bei Installation von CD) benötigt.

Es sollte dabei jedem klar sein, das diese Empfehlung eher für den
Heimgebrauch oder für kleine Büros oder Betriebe gilt. Grundsätzlich
sollte sich die Hardware-Ausstattung am Einsatzzweck orientieren. Je mehr
Benutzer gleichzeitig auf dem Server arbeiten, sprich auf Daten zugreifen,
Daten schreiben, Mails abrufen oder andere Dienste nutzen, desto
leistungsfähiger sollte die Hardware des Servers dimensioniert sein.

Features
--------

Aufgaben, die der Server nachher im Netz übernehmen wird:

-  Fileserver für verschiedenste Clients (SMB, NFS, WebDAV, FTP)
-  Webserver (inkl. PHP+MySQL)
-  DCHP-/DNS-Server

..
  -  Mailserver
  -  Print-Server (mit Unterstützung für Druck in PDF-Dateien)

Es wird keine grafische Oberfläche auf dem Server laufen und wird
ausschließlich per Konsole im Textmodus administrierbar sein. Das spart
einerseits Ressourcen, andererseits ist die Konfiguration über die Konsole
in der Regel auch wesentlich schneller als es über eine `GUI 
<http://de.wikipedia.org/wiki/Grafische_Benutzeroberfl%C3%A4che>`_ wäre.

Konventionen
------------

In diesem Dokument wird relativ häufig auf der Kommandozeile des Linux-Systems
gearbeitet. Die einzugebenden Textzeilen sind durch eine graue Box
mit einem **$** am Anfang gekennzeichnet:

::

    $ sudo apt-get update

Zu bearbeitende Konfigurationsdateien werden ebenfalls grau hinterlegt und
eventuell mit Zeilennummern zur besseren Orientierung versehen:

.. code-block:: none
    :linenos:

    auto eth0
    iface eth0 inet static
      address 192.168.0.254
      netmask 255.255.255.0
      gateway 192.168.0.1
      dns-nameservers 192.168.0.1

Disclaimer
----------

Wie oben bereits erwähnt ist ein Server kein Spielzeug. Durch unsachgemäße
Handhabung kann unter Umständen enormer Schaden entstehen. Für etwaige
entstehende Schäden, im Zusammenhang eines mit Hilfe dieses Tutorials
entstandenen Servers, kann ich keine Haftung übernehmen. Ich bitte dies zu
beachten.

Lizenz
------

.. image:: images/creativecommons.png
    :align: center

Dieses Werk ist unter einem `Creative Commons <http://creativecommons.org>`_
"Namensnennung-Keine kommerzielle Nutzung-Weitergabe unter gleichen
Bedingungen 3.0 Deutschland" Lizenzvertrag lizenziert. Die komplette Lizenz
finden sich unter http://creativecommons.org/licenses/by-nc-sa/3.0/de/.

Falls Fragen zur Verwendung auftauchen, wenden Sie sich bitte per Mail an
zero@zeroathome.de oder per Jabber an `ben@einfachjabber.de <xmpp:ben@einfachjabber.de>`_.
Zusätzlich gibt es einen Jabber-Chatraum (MUC) unter
`zeroathome.de@conference.einfachjabber.de <xmpp:zeroathome.de@conference.einfachjabber.de>`_.
