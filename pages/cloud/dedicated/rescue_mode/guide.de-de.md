---
title: 'Rescue-Modus aktivieren und verwenden'
slug: ovh-rescue
excerpt: 'Erfahren Sie hier, wie Sie den Rescue-Modus für einen Dedicated Server aktivieren und verwenden'
section: 'Diagnose & Rescue Modus'
---

> [!primary]
> Diese Übersetzung wurde durch unseren Partner SYSTRAN automatisch erstellt. In manchen Fällen können ungenaue Formulierungen verwendet worden sein, z.B. bei der Beschriftung von Schaltflächen oder technischen Details. Bitte ziehen Sie beim geringsten Zweifel die englische oder französische Fassung der Anleitung zu Rate. Möchten Sie mithelfen, diese Übersetzung zu verbessern? Dann nutzen Sie dazu bitte den Button «Mitmachen» auf dieser Seite.
>

**Letzte Aktualisierung am 14.01.2021**

## Ziel

Der Rescue-Modus ist ein Tool Ihres dedizierten Servers, mit dem Sie diesen auf einem temporären Betriebssystem starten können, um Probleme zu diagnostizieren und zu beheben.

**Diese Anleitung erklärt, wie Sie Ihren OVHcloud Dedicated Server im Rescue-Modus neu starten.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/UdMZSgXATFU?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

## Voraussetzungen

- Sie haben einen [Dedicated Server](https://www.ovhcloud.com/de/bare-metal/) in Ihrem Kunden-Account.
- Sie haben Zugriff auf Ihr [OVHcloud Kundencenter](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.de/&ovhSubsidiary=de).

## In der praktischen Anwendung

Der Rescue-Modus kann nur über Ihr [OVHcloud Kundencenter](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.de/&ovhSubsidiary=de){.external} aktiviert werden. Wählen Sie Ihren Server aus, indem Sie in den Bereich `Bare Metal Cloud`{.action} wechseln und ihn dann unter `Dedicated Server`{.action} anklicken.

Suchen Sie "Boot" im Bereich **Allgemeine Informationen** und klicken Sie auf `...`{.action} und dann auf `Bearbeiten`{.action}.

![Startmodus ändern](images/rescue-mode-01.png){.thumbnail}

Wählen Sie im folgenden Fenster **Im Rescue-Modus booten**. Wenn Ihr Server über ein Linux-Betriebssystem verfügt, wählen Sie `rescue64-pro`{.action} im Drop-down-Menü aus. Wenn Ihr Server auf Windows läuft, können Sie auch `WinRescue`{.action} wählen ([vgl. Abschnitt unten](#windowsrescue)). Geben Sie eine alternative E-Mail-Adresse an, wenn Sie *nicht* möchten, dass die Login-Daten an die Hauptadresse Ihres OVHcloud Kunden-Accounts gesendet werden.
<br>Klicken Sie auf `Weiter`{.action} und `Bestätigen`{.action}.

![Rescue-Modus](images/rescue-mode-03.png){.thumbnail}

Wenn die Änderung abgeschlossen ist, klicken Sie auf `...`{.action}. rechts neben "Status" im Bereich **Dienstleistungsstatus**.
<br>Klicken Sie auf `Neu starten`{.action} und der Server wird im Rescue-Modus neu gestartet. Die Durchführung dieser Operation kann einige Minuten dauern.
<br>Sie können den Fortschritt im Tab `Tasks`{.action} überprüfen. Es wird automatisch eine E-Mail mit einigen zusätzlichen Informationen und den Zugangsdaten des Root-Benutzers für den Rescue-Modus verschickt.

![Server neu starten](images/rescue-mode-02.png){.thumbnail}

Wenn Sie Ihre Tasks im Rescue-Modus beendet haben, denken Sie daran, den Netboot-Modus wieder auf `Von Festplatte booten`{.action} umzustellen bevor Sie Ihren Server neu starten.

### Linux

#### Verwendung des Rescue-Modus (SSH)

> [!primary]
> 
> Wenn Sie einen SSH-Schlüssel verwenden (der auch in Ihrem OVHcloud-Kundencenter aktiviert ist), wird Ihnen kein Passwort zugesandt. Sobald der Server im Rescue-Modus ist, können Sie sich direkt mit Ihrem SSH-Schlüssel verbinden.
>

Nach dem Neustart Ihres Servers erhalten Sie eine E-Mail mit Ihren Login-Daten für den Rescue-Modus. Diese E-Mail ist auch in Ihrem [OVHcloud Kundencenter] (https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.de/&ovhSubsidiary=de) verfügbar. Klicken Sie in der oberen rechten Ecke Ihres Kundencenters auf den Namen Ihrer Kundenkennung und anschließend auf `E-Mails vom Support`{.action}.

Sie müssen dann über die Befehlszeile oder über ein SSH-Tool auf Ihren Server zugreifen, indem Sie das für den Rescue-Modus generierte Root-Passwort verwenden.

Beispiel:

```sh
ssh root@your_server_IP
root@your_server_password:
```

> [!warning]
> 
> Ihr SSH-Client wird die Verbindung wahrscheinlich zuerst ablehnen, weil der ECDSA-Fingerabdruck nicht kompatibel ist. Dies ist normal, da der Rescue-Modus seinen eigenen temporären SSH-Server verwendet.
>
> Um dieses Problem zu umgehen, können Sie den regulären Fingerprint des Systems auskommentieren, indem Sie in der Datei *known_hosts* ein `#` in der entsprechenden Zeile hinzufügen. Achten Sie darauf, dieses Zeichen zu entfernen, bevor Sie den Server im normalen Modus neu starten.
>

Für die meisten Änderungen Ihres Servers via SSH im Rescue-Modus muss eine Partition gemountet werden. Dieser Modus verfügt über ein eigenes temporäres Daateisystem. Folglich gehen alle im Rescue-Modus vorgenommenen Änderungen am Dateisystem beim Neustart des Servers im normalen Modus verloren.

Die Partitionen werden über SSH per `mount` Befehl gemountet. Zunächst müssen jedoch Ihre Partitionen aufgelistet werden, um den Namen derjenigen Partition zu ermitteln, die Sie mounten möchten. Im Folgenden finden Sie Codebeispiele, an denen Sie sich orientieren können.

```sh
rescue:~# fdisk -l

Disk /dev/hda 40.0 GB, 40020664320 bytes
255 heads, 63 sectors/track, 4865 cylinders, total 41943040 sectors
Units = cylinders of 16065 * 512 = 8225280 bytes

Device Boot Start End Blocks Id System
/dev/hda1 * 1 1305 10482381 83 Linux
/dev/hda2 1306 4800 28073587+ 83 Linux
/dev/hda3 4801 4865 522112+ 82 Linux swap / Solaris

Disk /dev/sda 8254 MB, 8254390272 bytes
16 heads, 32 sectors/track, 31488 cylinders, total 41943040 sectors
Units = cylinders of 512 * 512 = 262144 bytes

Device Boot Start End Blocks Id System
/dev/sda1 1 31488 8060912 c W95 FAT32 (LBA)
```

Wenn Sie den Namen der gewünschten Partition ermittelt haben, verwenden Sie den folgenden Befehl:

```sh
rescue:~# mount /dev/hda1 /mnt/
```

> [!primary]
>
> Ihre Partition wird nun gemountet. Sie können dann Operationen im Dateisystem durchführen.
>
> Wenn Ihr Server über eine Software-RAID-Konfiguration verfügt, muss Ihr RAID-Volume gemountet werden (im Allgemeinen `/dev/mdX`).
>

Um den Rescue-Modus zu verlassen, ändern Sie im [OVHcloud Kundencenter](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.de/&ovhSubsidiary=de) den Bootmodus wieder auf `Von Festplatte Booten`{.action} und starten Sie den Server über die Kommandozeile neu.

### Windows <a name="windowsrescue"></a>

#### Verwendung der WinRescue-Tools

Nach dem Neustart Ihres Servers erhalten Sie eine E-Mail mit den Login-Daten des Rescue-Modus. Diese E-Mail ist auch in Ihrem [OVHcloud Kundencenter] (https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.de/&ovhSubsidiary=de) verfügbar. Klicken Sie in der oberen rechten Ecke Ihres Kundencenters auf den Namen Ihrer Kundenkennung und anschließend auf `E-Mails vom Support`{.action}.

Um die GUI für den Windows-Rescue-Modus zu verwenden, müssen Sie eine VNC-Konsole herunterladen und installieren oder das `IPMI`-Modul in Ihrem [OVHcloud-Kundencenter](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.de/&ovhSubsidiary=de){.external} verwenden.

![WinRescue Windows](images/rescue-mode-06.png){.thumbnail}

Folgende Werkzeuge sind bereits in diesem Modus installiert:

|Tool|Beschreibung|
|---|---|
|Firefox|Ein Webbrowser.|
|Freecommander|Ein Dateimanager mit Standardfunktionen.|
|Avast Virus Cleaner|Eine Antivirus-Anwendung mit Datenscan- und Datenbereinigungsfunktionen.|
|ActivNIC|Ein Werkzeug, mit dem Sie eine Netzwerkschnittstelle reaktivieren können.|
|BootSect|Ein Werkzeug, mit dem Sie den Bootsektor reparieren können.|
|Virtual Clone Drive|Ein Tool zum Mounten von BIN-, CCD- und ISO-Dateien in einem virtuellen CD-Laufwerk.|
|smartctl|Ein Werkzeug, mit dem Sie auf die automatischen Monitoring-Logs der Festplatten zugreifen können.|
|Diskpart|Ein Werkzeug, mit dem Sie die Partitionen des Servers verwalten können.|
|SysInternal|Eine Software-Suite von Microsoft, mit der Sie die Wartung des Netzwerks durchführen und die Prozesse verwalten können.|
|TestDisk|Eine leistungsstarke Anwendung zur Datenwiederherstellung. Mit diesem Tool können Sie beschädigte Partitionen wiederherstellen und bearbeiten, verlorene Partitionen wiederfinden, einen Bootsektor reparieren oder sogar einen fehlerhaften MBR rekonstruieren.|
|FileZilla|Ein Open-Source-FTP-Client. Er unterstützt SSH- und SSL-Protokolle und verfügt über ein intuitives Drag-and-Drop-Interface. Es kann verwendet werden, um Ihre Daten auf einen FTP-Server zu übertragen, zum Beispiel das FTP-Backup, das mit den meisten OVHcloud-Servermodellen bereitgestellt wird.|
|7-ZIP|Ein Datenkomprimierungs- und Datenarchivierungstool, das die folgenden Formate liest: ARJ, CAB, CHM, CPIO, CramFS, DEB, DMG, FAT, HFS, ISO, LZH, LZMA, MBR, MSI, NSIS, NTFS, RAR, RPM, SquashFS, UDF, VHD, WIM, XAR und Z. Außerdem können Sie mit diesem Tool Ihre eigenen Archive in den folgenden Formaten anlegen: BZIP2, GZIP, TAR, WIM, XZ, Z und ZIP.|

## Weiterführende Informationen

[Administratorpasswort auf einem Windows Dedicated Server ändern](../windows-admin-passwort-aendern/)

Für den Austausch mit unserer User Community gehen Sie auf <https://community.ovh.com/en/>.
