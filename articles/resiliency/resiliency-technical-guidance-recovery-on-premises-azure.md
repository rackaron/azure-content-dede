<properties
   pageTitle="Technischer Leitfaden: Wiederherstellung eines lokalen Rechenzentrums in Azure | Microsoft Azure"
   description="Artikel über die Grundlagen und den Entwurf von Systemen zur Wiederherstellung der lokalen Infrastruktur in Azure"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Technischer Leitfaden zur Resilienz in Azure: Wiederherstellung eines lokalen Rechenzentrums in Azure

Azure bietet eine Reihe von Diensten, mit denen sich ein lokales Datencenter auf Azure ausweiten lässt, um hohe Verfügbarkeit und Notfallwiederherstellung zu ermöglichen:

* __Netzwerk__: Mit einem virtuellen privaten Netzwerk können Sie Ihr lokales Netzwerk sicher in die Cloud erweitern.
* __Compute__: Kunden, die Hyper-V lokal verwenden, können vorhandene virtuelle Computer ganz einfach per „Lift&Shift“ nach Azure verlagern.
* __Speicher__: StorSimple erweitert Ihr Dateisystem in Azure Storage. Der Azure Backup-Dienst ermöglicht die Sicherung von Dateien und SQL-Datenbanken in Azure Storage.
* __Datenbankreplikation__: Mit Verfügbarkeitsgruppen in SQL Server 2014 (oder höher) können Sie eine hohe Verfügbarkeit und Notfallwiederherstellung für Ihre lokalen Daten implementieren.

##Netzwerk

Azure Virtual Network ermöglicht die Erstellung eines logisch isolierten Bereichs in Azure, den Sie über eine IPsec-Verbindung sicher mit Ihrem lokalen Datencenter oder mit einem einzelnen Clientcomputer verbinden können. Mit Virtual Network profitieren Sie von den Vorteilen der skalierbaren bedarfsgesteuerten Infrastruktur in Azure und können gleichzeitig eine Verbindung mit lokalen Daten und Anwendungen bereitstellen – einschließlich Systemen, die unter Windows Server, Mainframes und UNIX ausgeführt werden. Weitere Informationen finden Sie in der [Azure-Netzwerkdokumentation](../virtual-network/virtual-networks-overview.md).

##Compute

Wenn Sie Hyper-V lokal verwenden, können Sie vorhandene virtuelle Computer per „Lift&Shift“ nach Azure sowie an Dienstanbieter unter Windows Server 2012 (oder höher) verlagern, ohne Änderungen am virtuellen Computer vornehmen oder Formate für virtuelle Computer konvertieren zu müssen. Weitere Informationen finden Sie unter [Informationen zu Festplatten und VHDs für virtuelle Azure-Computer](../virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##Azure Site Recovery

Wenn Sie DRaaS (Disaster Recovery as a Service) einrichten möchten, nutzen Sie [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/). Azure Site Recovery bietet umfassenden Schutz für VMware-Server, Hyper-V-Server und physische Server. Mit Azure Site Recovery können Sie einen anderen lokalen Server oder Azure als Wiederherstellungsstandort nutzen. Weitere Informationen zu Azure Site Recovery finden Sie in der [Dokumentation zu Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery/).

##Speicher

Es gibt verschiedene Möglichkeiten, Azure als Sicherungsstandort für lokale Daten zu nutzen.

###StorSimple

StorSimple integriert Cloudspeicher für lokale Anwendungen sicher und transparent. Darüber hinaus bietet die Lösung eine einzelne Appliance mit hochleistungsfähigem, gestaffeltem Speicher (lokal und cloudbasiert), Livearchivierung, cloudbasiertem Datenschutz cloudbasierter Notfallwiederherstellung. Weitere Informationen finden Sie auf der [StorSimple-Produktseite](https://azure.microsoft.com/services/storsimple/).

###Azure Backup

Azure Backup ermöglicht Cloudsicherungen mithilfe der vertrauten Tools in Windows Server 2012 (oder höher), Windows Server 2012 Essentials (oder höher) und System Center 2012 Data Protection Manager (oder höher). Diese Tools stellen einen Workflow für die Sicherungsverwaltung bereit, der unabhängig vom Speicherort der Sicherungen ist – egal, ob es sich um einen lokalen Datenträger oder Azure Storage handelt. Nachdem die Daten in der Cloud gesichert wurden, können autorisierte Benutzer die Sicherungen ohne weiteres auf jedem beliebigen Server wiederherstellen.

Bei inkrementellen Sicherungen werden nur Änderungen an Dateien in die Cloud übertragen. Dies trägt zu einer effizienten Speichernutzung bei, reduziert die Bandbreitenauslastung und unterstützt die Point-in-Time-Wiederherstellung mehrerer Datenversionen. Sie können auch zusätzliche Funktionen nutzen, wie etwa Datenaufbewahrungsrichtlinien, Datenkomprimierung und Drosselung von Datenübertragungen. Die Verwendung von Azure als Speicherort für Sicherungen bietet den offensichtlichen Vorteil, dass die Sicherungen automatisch außerhalb des Standorts gespeichert werden. Damit entfallen zusätzliche Anforderungen zum Sichern und Schützen der lokalen Sicherungsmedien.

Weitere Informationen finden Sie unter [Was ist Azure Backup](../backup/backup-introduction-to-azure-backup.md) sowie unter [Konfigurieren von Azure Backup für DPM-Daten](https://technet.microsoft.com/library/jj728752.aspx).

##Datenbank

Sie können eine Notfallwiederherstellungslösung für Ihre SQL Server-Datenbanken in einer IT-Hybridumgebung mit AlwaysOn-Verfügbarkeitsgruppen, Datenbankspiegelung, Protokollversand und Sicherung/Wiederherstellung mit Azure-Blobspeicher nutzen. All diese Lösungen verwenden SQL Server auf Azure Virtual Machines.

AlwaysOn-Verfügbarkeitsgruppen können in hybriden IT-Umgebungen verwenden werden, in denen Datenbankreplikate sowohl lokal als auch in der Cloud existieren. Dies wird im folgenden Diagramm veranschaulicht:

![SQL Server – AlwaysOn-Verfügbarkeitsgruppen in einer hybriden Cloudarchitektur](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

Die Datenbankspiegelung kann in einer zertifikatbasierten Konfiguration auch lokale Server und die Cloud umfassen. Das folgende Diagramm veranschaulicht dieses Konzept.

![SQL Server – Datenbankspiegelung in einer hybriden Cloudarchitektur](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

Der Protokollversand kann verwendet werden, um eine lokale Datenbank mit einer SQL-Server-Datenbank auf einem virtuellen Azure-Computer zu synchronisieren.

![SQL Server – Protokollversand in einer hybriden Cloudarchitektur](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

Und schließlich können Sie eine lokale Datenbank direkt in einem Azure-Blobspeicher sichern.

![Sichern von SQL Server in einem Azure-Blobspeicher in einer hybriden Cloudarchitektur](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

Weitere Informationen finden Sie unter [Hohe Verfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) und [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).

##Prüflisten für die lokale Wiederherstellung in Microsoft Azure

###Netzwerk

  1. Lesen Sie den Abschnitt [Netzwerk](#networking) in diesem Dokument.
  2. Verwenden Sie Virtual Network, um eine sichere Verbindung zwischen dem lokalen System und der Cloud herzustellen.

###Compute

  1. Lesen Sie den Abschnitt [Compute](#compute) in diesem Dokument.
  2. Verschieben Sie virtuelle Computer zwischen Hyper-V und Azure.

###Speicher
  1. Lesen Sie den Abschnitt [Speicher](#storage) in diesem Dokument.
  2. Nutzen Sie StorSimple-Dienste für Cloudspeicher.
  3. Verwenden Sie den Azure Backup-Dienst.

###Datenbank

  1. Lesen Sie den Abschnitt [Datenbank](#database) in diesem Dokument.
  2. Verwenden Sie ggf. SQL Server auf virtuellen Azure-Computern als Sicherung.
  3. Richten Sie AlwaysOn-Verfügbarkeitsgruppen ein.
  4. Konfigurieren Sie die zertifikatbasierte Datenbankspiegelung.
  5. Nutzen Sie den Protokollversand.
  6. Sichern Sie die lokale Datenbank in einem Azure-Blobspeicher.

##Nächste Schritte

Dieser Artikel gehört zu einer Reihe von Artikeln, die als [Technischer Leitfaden zur Resilienz in Azure](./resiliency-technical-guidance.md) fungieren. Im nächsten Artikel dieser Reihe geht es um die [Wiederherstellung nach Datenbeschädigung oder unbeabsichtigtem Löschen](./resiliency-technical-guidance-recovery-data-corruption.md).

<!---HONumber=AcomDC_0615_2016-->