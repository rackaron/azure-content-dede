<properties
   pageTitle="Systemanforderungen für StorSimple Virtual Array"
   description="Erfahren Sie mehr über die Software- und Netzwerkanforderungen für StorSimple Virtual Array."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/14/2016"
   ms.author="alkohli"/>

# Systemanforderungen für StorSimple Virtual Array

## Übersicht

In diesem Artikel werden wichtige Systemanforderungen für Microsoft Azure StorSimple Virtual Array (auch als lokales virtuelles StorSimple-Gerät oder virtuelles StorSimple-Gerät bezeichnet) und für die Speicherclients beschrieben, die auf das Array zugreifen. Sie sollten die Informationen sorgfältig überprüfen, bevor Sie Ihr StorSimple-System bereitstellen. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Systemanforderungen:

-   **Softwareanforderungen für Speicherclients:** Hier werden die unterstützten Virtualisierungsplattformen, Webbrowser, iSCSI-Initiatoren, SMB-Clients, Mindestanforderungen für virtuelle Geräte und alle sonstigen Anforderungen für diese Betriebssysteme beschrieben.

-   **Netzwerkanforderungen für das StorSimple-Gerät:** bietet Informationen zu den Ports, die in der Firewall für Datenverkehr von iSCSI, Cloud oder Verwaltung geöffnet sein müssen.

Die in diesem Artikel veröffentlichten Informationen zu den StorSimple-Systemanforderungen gelten nur für StorSimple Virtual Arrays.

- Informationen zu Geräten der 8000-Serie finden Sie unter [Systemanforderungen für StorSimple-Geräte der 8000-Serie](storsimple-system-requirements.md).
 
- Informationen zu Geräten der 7000-Serie finden Sie unter [Systemanforderungen für StorSimple-Geräte der 5000-7000-Serie](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).


## Softwareanforderungen

Die Softwareanforderungen umfassen Informationen zu den unterstützten Webbrowsern, SMB-Versionen, Virtualisierungsplattformen und die Mindestanforderungen für virtuelle Geräte.

### Unterstützte Virtualisierungsplattformen


| **Hypervisor** | **Version** |
|----------------|--------------------------------------|
| Hyper-V | Windows Server 2008 R2 SP1 und höher |
| VMware ESXi | 5\.5 und höher |

### Anforderungen für virtuelle Geräte

| **Komponente** | **Anforderung** |
|----------------------------------------------|----------------------------|
| Mindestanzahl virtueller Prozessoren (Kerne) | 4 |
| Minimaler Arbeitsspeicher (RAM) | 8 GB |
| Festplattenspeicher<sup>1</sup> | Betriebssystemdatenträger: 80 GB <br></br>Datendatenträger: 500 GB bis 8 TB|
| Mindestanzahl von Netzwerkschnittstellen | 1 |
| Minimale Internetbandbreite<sup>2</sup> | 5 MBit/s |

<sup>1</sup>: Bereitgestelltes Thin-Gerät

<sup>2</sup>: Die Netzwerkanforderungen können abhängig von der täglichen Datenänderungsrate variieren. Beispiel: Wenn von einem Gerät Änderungen mit einem Umfang von mindestens 10 GB pro Tag gesichert werden müssen, kann die tägliche Sicherung bei einer Verbindung mit 5 MBit/s bis zu 4,25 Stunden dauern (falls die Daten nicht komprimiert oder dedupliziert werden konnten).

### Unterstützte Webbrowser

| **Komponente** | **Version** | **Weitere Anforderungen/Hinweise** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge | Aktuelle Version | |
| Internet Explorer | Aktuelle Version | Getestet mit Internet Explorer 11 |
| Google Chrome | Aktuelle Version | Getestet mit Chrome 46 |

### Unterstützte Speicherclients 

Die folgenden Softwareanforderungen gelten für die iSCSI-Initiatoren, die auf StorSimple Virtual Array zugreifen (konfiguriert als iSCSI-Server).

| **Unterstützte Betriebssysteme** | **Erforderliche Version** | **Weitere Anforderungen/Hinweise** |
| --------------------------- | ---------------- | ------------- |
| Windows Server | 2008 R2 SP1, 2012, 2012 R2 |Mit StorSimple können Sie Volumes mit schlanker Speicherzuweisung und vollständig bereitgestellte Volumes erstellen. Sie können keine teilweise bereitgestellten Volumes erstellen. StorSimple iSCSI-Volumes werden nur unterstützt für: <ul><li>Einfache Volumes auf Windows-Basisfestplatten.</li><li>Windows NTFS zum Formatieren eines Volumes.</li>|

Die folgenden Softwareanforderungen gelten für die SMB-Clients, die auf StorSimple Virtual Array zugreifen (konfiguriert als Dateiserver).

| **SMB-Version** |
|-------------|
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |
 
## Netzwerkanforderungen 

In der folgenden Tabelle sind die Ports aufgeführt, die in der Firewall für iSCSI-, SMB-, Cloud- oder Verwaltungsdatenverkehr geöffnet werden müssen. In dieser Tabelle bezieht sich *ein* oder *eingehend* auf die Richtung, aus der eingehende Clientanforderungen auf das Gerät zugreifen. Entsprechend bezieht sich *aus* oder *ausgehend* auf die Richtung, in der das StorSimple-Gerät Daten über die Bereitstellung hinaus an externe Ziele sendet: z. B. ausgehende Verbindungen mit dem Internet.

| **Portnummer<sup>1</sup>** | **ein oder aus** | **Portbereich** | **Erforderlich** | **Hinweise** |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP) | aus | WAN | Nein | Der ausgehende Port wird für den Internetzugriff zum Abrufen von Updates verwendet. <br></br>Der ausgehende Webproxy kann vom Benutzer konfiguriert werden. |
| TCP 443 (HTTPS) | aus | WAN | Ja | Der ausgehende Port wird für den Zugriff auf Daten in der Cloud verwendet. <br></br>Der ausgehende Webproxy kann vom Benutzer konfiguriert werden. |
| UDP 53 (DNS) | aus | WAN | In einigen Fällen; siehe Hinweise. | Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten DNS-Server verwenden. <br></br> **Hinweis:** Bei der Bereitstellung eines Dateiservers wird die Verwendung eines lokalen DNS-Servers empfohlen.|
| UDP 123 (NTP) | aus | WAN | In einigen Fällen; siehe Hinweise. | Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten NTP-Server verwenden.<br></br> **Hinweis:** Bei der Bereitstellung eines Dateiservers wird empfohlen, die Zeit mit Ihren Active Directory-Domänencontrollern zu synchronisieren. |
| TCP 80 (HTTP) | Geben Sie in | LAN | Ja | Dies ist der eingehende Port für die lokale Benutzeroberfläche auf dem StorSimple-Gerät für die lokale Verwaltung. <br></br> **Hinweis**: Beim Zugriff auf die lokale Benutzeroberfläche über HTTP erfolgt automatisch eine Umleitung auf HTTPS.|
| TCP 443 (HTTPS) | Geben Sie in | LAN | Ja | Dies ist der eingehende Port für die lokale Benutzeroberfläche auf dem StorSimple-Gerät für die lokale Verwaltung.|
| TCP 3260 (iSCSI) | Geben Sie in | LAN | Nein | Dieser Port wird für den Datenzugriff über iSCSI verwendet.|

<sup>1</sup> Es müssen keine eingehenden Ports für das öffentliche Internet geöffnet werden.

> [AZURE.IMPORTANT] Achten Sie darauf, dass SSL-Datenverkehr zwischen dem StorSimple-Gerät und Azure nicht durch die Firewall geändert oder entschlüsselt wird.


### URL-Muster für Firewallregeln 

Netzwerkadministratoren können häufig erweiterte, auf den URL-Mustern basierende Firewallregeln konfigurieren, die zum Filtern des eingehenden und ausgehenden Verkehrs verwendet werden. Ihr virtuelles Array und der StorSimple Manager-Dienst sind von anderen Microsoft-Anwendungen abhängig, z.B. Azure Service Bus, Azure Active Directory Access Control, Speicherkonten und Microsoft Update-Server. Die URL-Muster, die diesen Anwendungen zugeordnet sind, können verwendet werden, um Firewallregeln zu konfigurieren. Es ist wichtig, zu verstehen, dass sich diese den Anwendungen zugeordneten URL-Muster ändern können. Das bedeutet, dass der Netzwerkadministrator die Firewallregeln für Ihren StorSimple nach Bedarf überwachen und aktualisieren muss.

Es empfiehlt sich, die Firewallregeln für den ausgehenden Verkehr basierend auf den festen StorSimple IP-Adressen in den meisten Fällen recht locker festzulegen. Sie können jedoch die folgenden Informationen verwenden, um erweiterte Firewallregeln festzulegen, die erforderlich sind, um sichere Umgebungen zu erstellen.

> [AZURE.NOTE] 
> 
> - Die Geräte-IPs (Quell-IPs) sollten immer für alle cloudaktivierten Netzwerkschnittstellen eingerichtet sein.
> - Die Ziel-IPs sollten auf die [IP-Bereiche des Azure-Rechenzentrums](https://www.microsoft.com/de-DE/download/confirmation.aspx?id=41653) festgelegt werden.


| URL-Muster | Komponente/Funktionalität |
|------------------------------------------------------------------|---------------------------------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` | StorSimple Manager-Dienst<br>Access Control Service<br>Azure Service Bus|
|`http://*.backup.windowsazure.com`|Geräteregistrierung|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Zertifikatswiderruf |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Azure-Speicherkonten und Überwachung |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Microsoft Update-Server<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` | Supportpaket |
| `http://*.data.microsoft.com ` | Telemetriedienst in Windows, Informationen finden Sie im [Update für Kundenzufriedenheit und Diagnosetelemetrie](https://support.microsoft.com/de-DE/kb/3068708). |

## Nächster Schritt

-   [Vorbereiten des Portals zum Bereitstellen von StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md)

<!---HONumber=AcomDC_0720_2016-->