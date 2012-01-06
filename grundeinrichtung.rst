.. todo: Kapitel Sicherheit

Grundeinrichtung
================

Grundbefehle
------------

Wer noch nie Linux auf der Kommandozeile bedient hat, sollte sich zunächst
einmal mit den Grundbefehlen auseinandersetzen. Im `ubuntuusers.de - Wiki
<http://wiki.ubuntuusers.de/Shell/Befehls%C3%BCbersicht>`_
findet sich eine kleine Übersicht, die man gut zum gelegentlichen Nachschauen
nutzen kann.

.. _editor:

Wahl eines Editors
------------------

Es bietet sich an entweder `nano <http://wiki.ubuntuusers.de/Nano>`_ oder
`VIM <http://wiki.ubuntuusers.de/Vim>`_ zu verwenden. Nano ist für
Anfänger sicher die bessere Wahl, Vim dafür wesentlich mächtiger aber
dafür auch mit einer steilen Lernkurve versehen. (Ein kleiner Tipp: Um Vim zu beenden drückt man um sicher zu gehen das man im richtigen Modus ist die **Esc**-Taste und anschließend ``:wq<Enter>``).

Um eine Konfigurationsdatei zu öffnen reicht es den Namen des Editors
gefolgt von der zu editierenden Datei einzutippen und mit Enter zu
bestätigen.
Also entweder

::

    $ vim /etc/network/interfaces

für Vim oder

::

    $ nano /etc/network/interfaces

für Nano.

Update des Systems
------------------

Manuelle Upgrades
~~~~~~~~~~~~~~~~~

Nachdem die Verbindung ins Netz steht, sollte im nächsten Schritt das System
auf den aktuellen Stand gebracht werden. Ubuntu bringt dafür das Werkzeug
``apt-get`` mit:

``sudo apt-get update``
    Aktualisiert die Paketdatenbank vom Update-Server

``sudo apt-get upgrade``
    Lädt aktualisierbare Pakete vom Server herunter und installiert diese.

``sudo apt-get dist-upgrade``
    Wenn ein Update es erfordert, dass ein Paket zusätzlich installiert
    (upgraden != installieren) werden muss, geht dies nur über
    ``apt-get dist-upgrade``. Dazu gehören zum Beispiel `Kernel
    <http://de.wikipedia.org/wiki/Betriebssystemkern>`_-Updates, da hier nicht
    der aktuelle Kernel überschrieben (geupgradet) wird, sondern der neue
    zusätzlich *installiert* wird. ``apt-get upgrade`` weist darauf hin, wenn
    solche Pakete verfügbar sind.

``sudo apt-get autoremove``
    Viele Pakete besitzen sogenannte Abhängigkeiten. Das sind andere Pakete, ohne die
    das Paket das wir gerade installieren wollen nicht funktioniert. Bei der De-installation
    bleiben solche Pakete gelegentlich ungenutzt im System liegen. Um diese loszuwerden, lohnt
    es sich regelmäßig den Befehl ``apt-get autoremove`` auszuführen.

Automatische Upgrades
~~~~~~~~~~~~~~~~~~~~~

Alternativ gibt es die Möglichkeit Updates automatisch installieren zu lassen.
Dazu muss zunächst das Paket ``unattended-upgrades`` installiert werden:::

  $ sudo apt-get install unattended-upgrades

Um die Automatik auch zu aktivieren sind noch ein paar kleine Einstellungen von
nöten. Dazu schaut man sich zunächst die Konfigurationsdatei
``/etc/apt/apt.conf.d/50unattended-upgrades`` an:::

  $ sudo nano /etc/apt/apt.conf.d/50unattended-upgrades

Hier die Abschnitte der Datei im Detail:

::

  // Automatically upgrade packages from these (origin, archive) pairs
  Unattended-Upgrade::Allowed-Origins {
          "Ubuntu lucid-security";
  //      "Ubuntu lucid-updates";
  };

Dieser Teil führt dazu, dass lediglich Sicherheits-Updates automatisch installiert werden.
Möchte man alle Arten von Upgrades aktivieren, entfernt man einfach die beiden ``//`` vor der Zeile
``"Ubuntu lucid-updates";``.

::

  // List of packages to not update
  Unattended-Upgrade::Package-Blacklist {
  //      "vim";
  //      "libc6";
  //      "libc6-dev";
  //      "libc6-i686";
  };

Hier kann man das automatische Update daran hindern, bestimmte Pakete auf den neuesten Stand zu bringen.
Gelegentlich ist das nötig wenn man auf eine bestimmte Version eines Programmes angewiesen ist. Dazu trägt
man den Paketnamen in die Liste ein, natürlich ohne die ``//``.

::

  //Unattended-Upgrade::Remove-Unused-Dependencies "false";

In dem man hier die ``//`` entfernt und ``false`` in ``true`` umschreibt, erlaubt man es, das nicht mehr benötigte Pakete
automatisch entfernt werden.

Die restlichen Einstellungen sollen an dieser Stelle unbetrachtet bleiben, da sie für den Grundbetrieb nicht notwendig
sind, bzw. zusätzliche Software benötigen.

Um das periodische Aktualisieren zu aktivieren muss noch die Datei ``/etc/apt/apt.conf.d/10periodic`` bearbeitet
werden, so dass sie folgenden Inhalt enthält:::

  APT::Periodic::Update-Package-Lists "1";
  APT::Periodic::Download-Upgradeable-Packages "1";
  APT::Periodic::AutocleanInterval "7";
  APT::Periodic::Unattended-Upgrade "1";

Zeile 1 und 2 aktiviert das Aktualisieren der Paketlisten und das Herunterladen der zu aktualisierenden Pakete.
Zeile 3 stellt das Intervall ein, in dem nicht mehr benötigte Pakete vom System entfernt werden.
Zeile 4 schließlich erledigt die eigentliche Aktivierung des automatischen Aktualisierung.

.. todo: Sicherheit verlinken

.. warning::

    Das System sollte immer auf dem aktuellen Stand gehalten werden.
    Informationen über neue Sicherheitsupdates bieten zum Beispiel
    die `Ubuntu Security Notices <http://www.ubuntu.com/usn/>`_. Diese
    werden sowohl über eine `Mailingliste
    <https://lists.ubuntu.com/mailman/listinfo/ubuntu-security-announce>`_
    als auch per `RSS <http://www.ubuntu.com/usn/rss.xml>`_ zur Verfügung
    gestellt und gehören für jeden Ubuntu-Server-Betreiber zur
    absoluten Pflicht-Lektüre.


Die richtige Zeit vom Zeitserver
--------------------------------

Ein nicht zu unterschätzender Faktor auf einem Server ist die Zeit. Nicht die
Zeit bis die Hardware veraltet ist, sondern die Systemzeit. Zum Beispiel beim
Auswerten von Logfiles, ist es hilfreich wenn man sich sicher sein kann, dass
die Uhr des Servers richtig geht. Um das zu gewährleisten lässt man ihn seine
Zeit mit einem der vielen Zeit-Server über das Internet abgleichen. Dazu
installiert man zunächst das Paket **ntp** und konfiguriert den Dienst::

    $ sudo apt-get install ntp

Die Konfiguration findet sich in der Datei **/etc/ntp.conf**. Dort fügt man,
der Redundanz wegen, in der Zeile *server* noch einen zweiten Time-Server
hinzu, so dass die Zeile am Ende so aussieht::

    server ntp.ubuntu.com de.pool.ntp.org

Der Server von dem die Zeit letztlich bezogen wird ist beliebig wählbar,
*de.pool.ntp.org* ist hier als Beispiel genannt, weitere Server finden sich
unter http://www.pool.ntp.org/.

Falls der Zeitserver von anderen Rechnern im Netz zu Zeit-Synchronisation
benutzt werden soll, muss die Zeile

::

  restrict 127.0.0.1

zu

::

  #restrict 127.0.0.1

umgeschrieben werden.

Nach der Konfigurationsänderung muss der Dienst noch einmal neu gestartet
werden und holt dann in regelmäßigen Abständen die richtige Zeit aus dem Netz.

::

    $ sudo service ntp restart

.. note::

    **ntp** hat gegenüber dem standardmäßig installierten **ntpdate** den
    Vorteil, dass es die Zeit nicht *in einem Schlag* umstellt, sondern diese
    über einen längeren Zeitraum Schritt für Schritt anpasst. Das
    verhindert, dass Dienste, die mit einem größeren Sprung in der Zeit nicht
    zu Recht kommen, nicht aus dem Tritt geraten. 

.. _usergroups:

Benutzer und Gruppen
--------------------

Da die meisten Dienste die im System hinterlegten Benutzer dazu verwenden, um die
Zugriffsrechte auf Dateifreigaben oder Lese- und Schreibrechte auf Dateien zu
verwalten, muss jeder Benutzer, der Zugriff zu Daten auf dem Server erhalten
soll, zunächst im System ein Konto erhalten.

Um Dateien und Ordern mehreren Benutzern zugänglich zu machen, gibt es zudem
Gruppen. Wenn man einer Gruppe Rechte an, zum Beispiel, einem Ordner gibt,
erbt automatisch jedes Mitglied dieser Gruppe die Rechte.

Linux stellt für die Verwaltung von Benutzern und Gruppen zwei Werkzeuge bereit.
``adduser`` und ``addgroup``.

addgroup
~~~~~~~~

Um eine neue Gruppe mit dem Namen *gruppe1* zu erstellen genügt der
einfache Befehl

::

    $ addgroup gruppe1

adduser
~~~~~~~

Einen Benutzer anlegen, der *benutzer1* heißt, ein Verzeichnis mit gleichem
Namen unterhalb von ``/home`` bekommt, Mitglied der Gruppe *users* ist, und
sich nicht per SSH einloggen kann. Zusätzlich wird eine neue Gruppe namens
*benutzer1* erstellt und der neue Benutzer als Mitglied hinzugefügt::

    $ sudo adduser --shell /bin/false benutzer1

Wie oben, allerdings erhält der Benutzer Shell-Zugriff, zum Beispiel per SSH::

    $ sudo adduser benutzer1

.. _hostsfile:

Die Datei /etc/hosts
--------------------

Wenn man in seinem Netzwerk keinen konfigurierbaren `DNS
<http://de.wikipedia.org/wiki/Domain_Name_System>`_-Server hat, zum
Beispiel auf dem Router und auch nicht plant einen solchen auf dem Server
einzurichten (Anleitung siehe :ref:`dns-dhcp`), kann man sich einer anderen
Methode bedienen. In der Datei **/etc/hosts** können IP-Adressen Rechnernamen
zugeordnet werden. Diese fungiert dann, als eine Art *lokaler DNS-Server*,
die Einträge sind also nur auf dem lokalen Rechner benutzbar. Dazu bearbeitet
man die Datei mit einem Texteditor und fügt Zeilen im folgenden Format ein::

    <IP-Adresse> <Rechnername.Domain> <Rechnername>

oder alternativ:::

    <IP-Adresse> <Rechnername>

Also zum Beispiel

::

    192.168.0.210 rechner1.daheim.lan rechner1
    192.168.0.211 rechner2.daheim.lan rechner2
    192.168.0.212 rechner3

Nach dem Editieren kann mit dem Befehl ``host`` überprüft werden ob alles
wunschgemäß funktioniert:: 

    $ host rechner1
    rechner1 has address 192.168.0.210

Umgekehrt geht das natürlich auch:

::

    $ host 192.168.101.211
    211.101.168.192.in-addr.arpa domain name pointer rechner2.
