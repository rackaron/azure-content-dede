<properties
 pageTitle="Linux RDMA-Cluster zum Ausführen von MPI-Anwendungen | Microsoft Azure"
 description="Erstellen Sie einen Linux-Cluster mit virtuellen Computern der Größe A8 oder A9, um RDMA für die Ausführung von MPI-Apps zu verwenden."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="05/09/2016"
 ms.author="danlep"/>

# Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Hier erfahren Sie, wie Sie einen Linux RDMA-Cluster mit [virtuellen Computern der Größe A8 und A9](virtual-machines-linux-a8-a9-a10-a11-specs.md) in Azure einrichten, um parallele MPI (Message Passing Interface)-Anwendungen auszuführen. Wenn Sie einen Cluster mit virtuellen Computern der Größe A8 und A9 zur Ausführung einer unterstützten Linux-HPC-Implementierung und einer unterstützten MPI-Implementierung konfigurieren, wird die Kommunikation der MPI-Anwendungen in Azure effizient über ein auf RDMA-Technologie (Remote Direct Memory Access) basierendes Netzwerk mit geringer Latenz und hohem Durchsatz abgewickelt.

>[AZURE.NOTE] Azure Linux RDMA wird derzeit über Intel MPI Library 5 auf virtuellen Computern der Größe A8 oder A9 unterstützt, die auf der Grundlage eines HPC-Images für SUSE Linux Enterprise Server (SLES) 12 oder eines CentOS-basierten HPC-Images (Version 6.5 oder 7.1) aus dem Azure Marketplace erstellt wurden. Die CentOS-basierten HPC-Marketplace-Images installieren Version 5.1.3.181 von Intel MPI.



## Optionen für die Clusterbereitstellung

Mit den folgenden Methoden können Sie einen Linux RDMA-Cluster mit oder ohne eine Auftragsplanung erstellen.

* **HPC Pack** – Erstellen Sie einen Microsoft HPC Pack-Cluster in Azure, und fügen Sie Computeknoten der Größe A8 oder A9 hinzu, auf denen unterstützte Linux-Distributionen für den Zugriff auf das RDMA-Netzwerk ausgeführt werden. Informationen finden Sie unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

* **Azure-CLI-Skripts** – Verwenden Sie die [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) (wie in den anderen Schritten in diesem Artikel gezeigt), um Skripts für die Bereitstellung eines virtuellen Netzwerks und anderer Komponenten zu erstellen, die für die Erstellung eines Clusters mit virtuellen Linux-Computern der Größe A8 oder A9 benötigt werden. Da Clusterknoten im Rahmen des klassischen Bereitstellungsmodells im Dienstverwaltungsmodus der Befehlszeilenschnittstelle seriell erstellt werden, kann die Bereitstellung einer großen Anzahl von Computeknoten mehrere Minuten dauern.

* **Azure Resource Manager-Vorlagen** – Verwenden Sie das Resource Manager-Bereitstellungsmodell, um mehrere virtuelle Linux-Computer der Größe A8 und A9 bereitzustellen sowie virtuelle Netzwerke, statische IP-Adressen, DNS-Einstellungen und andere Ressourcen für einen Computecluster zu definieren, der das RDMA-Netzwerk zum Ausführen von MPI-Workloads nutzen kann. Sie können [eine eigene Vorlage erstellen](../resource-group-authoring-templates.md) oder in den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/) nach Vorlagen von Microsoft oder der Community suchen, um die gewünschte Lösung bereitzustellen. Ressourcen-Manager-Vorlagen stellen eine schnelle und zuverlässige Möglichkeit zum Bereitstellen eines Linux-Clusters dar.

## Beispielbereitstellung im klassischen Modell

Die folgenden Schritte helfen Ihnen beim Bereitstellen eines SUSE Linux Enterprise Server 12-HPC-VMs aus dem Azure Marketplace, beim Installieren von Intel MPI Library und anderen Anpassungen, beim Erstellen eines benutzerdefinierten VM-Images und beim anschließenden Erstellen eines Skripts zum Bereitstellen eines Clusters mit virtuellen A8- oder A9-Computern unter Verwendung der Azure-Befehlszeilenschnittstelle.

>[AZURE.TIP]  Ein Cluster mit virtuellen A8- oder A9-Computern, die auf dem CentOS-basierten HPC-Image (Version 6.5 oder 7.1) aus dem Azure Marketplace basieren, lässt sich auf ganz ähnliche Weise bereitstellen. Unterschiede sind in den jeweiligen Schritten angegeben. Da die CentOS-basierten HPC-Images Intel MPI enthalten, muss Intel MPI beispielsweise nicht separat auf virtuellen Computern installiert werden, die auf der Grundlage dieser Images erstellt wurden.

### Voraussetzungen

*   **Clientcomputer** – Sie benötigen einen Mac-, Linux- oder Windows-basierten Clientcomputer für die Kommunikation mit Azure. Bei diesen Schritten wird davon ausgegangen, dass Sie einen Linux-Client verwenden.

*   **Azure-Abonnement** – Falls Sie über kein Abonnement verfügen, können Sie in wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/free/) einrichten. Bei größeren Clustern empfiehlt sich die Verwendung eines Abonnements mit nutzungsbasierter Bezahlung oder einer anderen Kaufoption.

*   **Kernnutzungskontingent** – Sie müssen möglicherweise das Kernnutzungskontingent erhöhen, um einen Cluster mit virtuellen A8- oder A9-Computern bereitzustellen. Beispielsweise benötigen Sie mindestens 128 Kerne, wenn Sie, wie in diesem Artikel dargestellt, acht A9-VMs bereitstellen möchten. Um ein Kontingent zu erhöhen, können Sie kostenlos [eine Anfrage an den Onlinekundensupport richten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

*   **Azure-CLI** – [Installieren](../xplat-cli-install.md) Sie die Azure-Befehlszeilenschnittstelle, und stellen Sie auf dem Clientcomputer eine [Verbindung mit dem Azure-Abonnement](../xplat-cli-connect.md) her.

*   **Intel MPI** – Wenn Sie ein SLES 12-HPC-VM-Image für Ihren Cluster anpassen möchten (wie weiter unten in diesem Artikel beschrieben), müssen Sie die aktuelle Intel MPI Library 5-Laufzeit von der Website [Intel.com](https://software.intel.com/de-DE/intel-mpi-library/) herunterladen und installieren. Folgen Sie als Vorbereitung dazu nach der Registrierung bei Intel dem Link in der Bestätigungs-E-Mail auf die entsprechende Webseite, und kopieren Sie den Downloadlink der TGZ-Datei für die entsprechende Version von Intel MPI. Dieser Artikel basiert auf Intel MPI-Version 5.0.3.048.

    >[AZURE.NOTE] Wenn Sie Ihre Clusterknoten auf der Grundlage des HPC-Images für CentOS 6.5 oder CentOS 7.1 aus dem Azure Marketplace erstellen, ist die Intel MPI-Version 5.1.3.181 auf dem virtuellen Computer bereits vorinstalliert.

### Bereitstellen einer SLES 12-VM

Führen Sie nach der Anmeldung bei Azure über die Azure-Befehlszeilenschnittstelle den Befehl `azure config list` aus, um sicherzustellen, dass der Dienstverwaltungsmodus verwendet wird. Wenn dies nicht der Fall ist, aktivieren Sie den Modus durch Ausführen dieses Befehls:


    azure config mode asm


Geben Sie Folgendes ein, um alle Abonnements aufzulisten, zu deren Verwendung Sie berechtigt sind:


    azure account list

Das aktuelle aktive Abonnement erkennen Sie daran, dass für `Current` der Wert `true` angegeben ist. Ist dies nicht das Abonnement, das Sie zum Erstellen des Clusters verwenden möchten, legen Sie die entsprechenden Abonnementnummer als aktives Abonnement fest:

    azure account set <subscription-number>

Um die öffentlich verfügbaren SLES 12 HPC-Images in Azure anzuzeigen, führen Sie einen Befehl aus, der etwa folgendermaßen aussieht (vorausgesetzt, Ihre Shellumgebung unterstützt **grep**):


    azure vm image list | grep "suse.*hpc"

Stellen Sie nun einen virtuellen Computer der Größe A9 mit einem verfügbaren SLES 12 HPC-Image bereit, indem Sie einen Befehl ausführen, der etwa folgendermaßen aussieht:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708

Hierbei gilt:

* Die Größe (in diesem Beispiel A9) kann A8 oder A9 sein.

* Die externe SSH-Portnummer (in diesem Beispiel der SSH-Standardwert 22) ist eine beliebige gültige Portnummer. Die interne SSH-Portnummer wird auf 22 festgelegt.

* Ein neuer Clouddienst wird in der durch den Standort festgelegten Azure-Region erstellt. Geben Sie einen Standort, z. B. "West US" ein, an dem die A8- und A9-Instanzen verfügbar sind.

* Der SLES 12-Imagename kann derzeit `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` oder `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` (für SUSE Priority Support; kostenpflichtig) lauten.

    >[AZURE.NOTE]Bei Verwendung eines CentOS-basierten HPC-Images lauten die aktuellen Imagenamen `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-65-HPC-20160408` und `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-71-HPC-20160408`.

### Anpassen des virtuellen Computers

Nach Abschluss der VM-Bereitstellung stellen Sie mithilfe der externen IP-Adresse des virtuellen Computers (oder des DNS-Namens) und der externen Portnummer, die Sie konfiguriert haben, eine SSH-Verbindung zum virtuellen Computer her, und nehmen Sie die Anpassungen vor. Weitere Informationen zur Verbindung finden Sie unter [Anmelden bei einem mit Linux betriebenen virtuellen Computer](virtual-machines-linux-classic-log-on.md). Sofern zum Ausführen eines Schritts kein Stammzugriff erforderlich ist, sollten Sie Befehle als der Benutzer ausführen, den Sie für den virtuellen Computer konfiguriert haben.

>[AZURE.IMPORTANT]Microsoft Azure bietet keinen Stammzugriff auf Linux-VMs. Wenn Sie beim virtuellen Computer als Benutzer angemeldet sind, können Sie Befehle mit `sudo` ausführen, um Administratorzugriff zu erhalten.

* **Updates** – Installieren Sie Updates mit **zypper**. Sie können auch NFS-Dienstprogramme installieren.

    >[AZURE.IMPORTANT]Wenn Sie einen SLES 12-HPC-VM bereitgestellt haben, raten wir derzeit davon ab, Kernel-Updates zu installieren, da dies zu Problemen mit den Linux RDMA-Treibern führen kann.
    >
    >Für die CentOS-basierten HPC-Images aus dem Marketplace sind Kernel-Updates in der **yum**-Konfigurationsdatei deaktiviert. Der Grund: Die Linux RDMA-Treiber werden als RPM-Paket verteilt, und Treiberupdates funktionieren möglicherweise nicht, wenn der Kernel aktualisiert wird.

* **Linux RDMA-Treiberupdates** – Wenn Sie einen SLES 12-HPC-VM bereitgestellt haben, müssen die RDMA-Treiber aktualisiert werden. Ausführlichere Informationen finden Sie unter [Informationen zu den rechenintensiven A8-, A9, A10- und A11-Instanzen](virtual-machines-linux-a8-a9-a10-a11.md#Linux-RDMA-driver-updates-for-SLES-12).

* **Intel MPI** – Wenn Sie einen SLES 12-HPC-VM bereitgestellt haben, laden Sie die Intel MPI Library 5-Laufzeit von der Website „Intel.com“ herunter, und installieren Sie sie. Entsprechende Beispielbefehle finden Sie weiter unten. Dieser Schritt ist nicht erforderlich, wenn Sie einen CentOS-basierten HPC-VM (Version 6.5 oder 7.1) bereitgestellt haben.

        sudo wget <download link for your registration>

        sudo tar xvzf <tar-file>

        cd <mpi-directory>

        sudo ./install.sh

    Akzeptieren Sie die Standardeinstellungen, um Intel MPI auf dem virtuellen Computer zu installieren.

* **Sperren von Speicher** – Zum Sperren des für RDMA verfügbaren Arbeitsspeichers mit MPI-Codes müssen Sie die folgenden Einstellungen in der Datei „/etc/security/limits.conf“ hinzufügen oder ändern. (Sie benötigen Stammzugriff zum Bearbeiten dieser Datei.) Wenn Sie einen CentOS-basierten HPC-VM (Version 6.5 oder 7.1) bereitgestellt haben, sind die Einstellungen bereits in der Datei enthalten.

        <User or group name> hard    memlock <memory required for your application in KB>

        <User or group name> soft    memlock <memory required for your application in KB>

    >[AZURE.NOTE]Zu Testzwecken können Sie "memlock" auch auf "unbegrenzt" festlegen. Beispiel: „<Benutzer- oder Gruppenname> hard memlock unlimited“

* **SSH-Schlüssel für SLES 12-VMs** – Generieren Sie SSH-Schlüssel, um bei der Ausführung von MPI-Aufträgen zwischen allen Computeknoten im SLES 12-HPC-Cluster eine Vertrauensstellung für Ihr Benutzerkonto einzurichten. (Überspringen Sie diesen Schritt, wenn Sie einen CentOS-basierten HPC-VM bereitgestellt haben. Anweisungen zum Einrichten einer kennwortlosen SSH-Vertrauensstellung zwischen den Clusterknoten nach Erfassung des Images und Bereitstellung des Clusters finden Sie weiter unten in diesem Artikel.)

    Führen Sie den folgenden Befehl aus, um SSH-Schlüssel zu erstellen: Drücken Sie einfach die EINGABETASTE, um die Schlüssel am Standardspeicherort zu erstellen, ohne eine Passphrase festzulegen.

        ssh-keygen

    Fügen Sie den öffentlichen Schlüssel der Datei „authorized\_keys“ für bekannte öffentliche Schlüssel an.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Bearbeiten oder erstellen Sie im „~/.ssh“-Verzeichnis die Datei „ssh\_config“. Geben Sie den IP-Adressbereich des privaten Netzwerks an, das Sie in Azure verwenden (in diesem Beispiel: 10.32.0.0/16):

        host 10.32.0.*
        StrictHostKeyChecking no

    Führen Sie alternativ die VPN-IP-Adressen der einzelnen virtuellen Computer in Ihrem Cluster wie folgt auf:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]Das Konfigurieren von `StrictHostKeyChecking no` kann ein potenzielles Sicherheitsrisiko darstellen, wenn eine bestimmte IP-Adresse oder ein bestimmter Adressbereich nicht wie in diesen Beispielen dargestellt angegeben wird.

* **Anwendungen** – Installieren Sie alle benötigten Anwendungen auf diesem virtuellen Computer, oder führen Sie andere Anpassungen aus, bevor Sie das Image erfassen.

### Erfassen des Images

Zum Erfassen des Images führen Sie zuerst den folgenden Befehl auf dem virtuellen Linux-Computer aus: Dadurch wird die Bereitstellung des virtuellen Computers aufgehoben, eingerichtete Benutzerkonten und SSH-Schlüssel werden jedoch beibehalten.

```
sudo waagent -deprovision
```

Führen Sie dann auf Ihrem Clientcomputer die folgenden Azure-CLI-Befehle zum Erfassen des Images aus. Weitere Informationen dazu finden Sie unter [Erfassen eines klassischen virtuellen Linux-Computers als Image](virtual-machines-linux-classic-capture-image.md).

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Nachdem Sie diese Befehle ausgeführt haben, wird das Image des virtuellen Computers für Ihre Verwendung erfasst, und der virtuelle Computer wird gelöscht. Jetzt ist Ihr benutzerdefiniertes Image zur Bereitstellung eines Clusters fertig.

### Bereitstellen eines Clusters mit dem Image

Ändern Sie das folgende Bash-Skript mit den entsprechenden Werten für Ihre Umgebung, und führen Sie es auf Ihrem Clientcomputer aus. Da die virtuellen Computer im klassischen Bereitstellungsmodell von Azure seriell bereitgestellt werden, dauert es einige Minuten, bis die acht im Skript vorgeschlagenen virtuellen A9-Computer bereitgestellt wurden.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Select a region where A8 and A9 VMs are available, such as West US
# See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## Kennwortlose SSH-Vertrauensstellung für einen CentOS-Cluster

Wenn Sie einen Cluster mit einem CentOS-basierten HPC-Image bereitgestellt haben, kann die Vertrauensstellung zwischen den Computeknoten auf zwei Arten eingerichtet werden: mit einer hostbasierten Authentifizierung oder mit einer benutzerbasierten Authentifizierung. Die hostbasierte Authentifizierung wird in diesem Artikel nicht behandelt und muss grundsätzlich mithilfe eines Erweiterungsskripts während der Bereitstellung erfolgen. Mit der benutzerbasierten Authentifizierung können Sie nach der Bereitstellung komfortabel eine Vertrauensstellung einrichten. Zu diesem Zweck müssen SSH-Schlüssel generiert und von den Computeknoten im Cluster genutzt werden. Dies wird häufig als SSH-Anmeldung ohne Kennwort bezeichnet und ist zum Ausführen von MPI-Aufträgen erforderlich.

Auf [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) steht ein Beispielskript aus der Community zur Verfügung, das eine einfache Benutzerauthentifizierung in einem CentOS-basierten HPC-Cluster ermöglicht. Die Vorgehensweise zum Herunterladen und Verwenden dieses Skripts wird weiter unten beschrieben. Sie können das Skript auch bearbeiten oder eine andere Methode verwenden, um eine kennwortlose SSH-Authentifizierung zwischen den Computeknoten des Clusters einzurichten.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Für die Skriptausführung wird das Präfix Ihrer Subnetz-IP-Adressen benötigt. Führen Sie zum Ermitteln dieser Informationen auf einem der Clusterknoten den folgenden Befehl aus. Die Ausgabe sollte in etwa wie folgt aussehen: 10.1.3.5. In diesem Fall wäre „10.1.3“ das Präfix.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Führen Sie nun das Skript aus, und geben Sie dabei drei Parameter an: den gemeinsamen Benutzernamen auf den Computeknoten, das gemeinsame Kennwort für diesen Benutzer auf den Computeknoten und das zurückgegebene Subnetzpräfix aus dem vorherigen Befehl.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Das Skript bewirkt Folgendes:

* Es erstellt auf dem Hostknoten ein Verzeichnis namens „.ssh“ (wird für die Anmeldung ohne Kennwort benötigt).
* Es erstellt im Verzeichnis „.ssh“ eine Konfigurationsdatei, mit der die kennwortlose Anmeldung angewiesen wird, Anmeldungen von beliebigen Knoten im Cluster zuzulassen.
* Es erstellt Dateien mit den Namen und IP-Adressen aller Knoten im Cluster. Diese Dateien bleiben nach Ausführung des Skripts zu Referenzzwecken erhalten.
* Es erstellt ein Schlüsselpaar mit privatem und öffentlichem Schlüssel für jeden Clusterknoten (auch für den Hostknoten), gibt die Informationen zum Schlüsselpaar weiter und erstellt einen Eintrag in der Datei „authorized\_keys“.

>[AZURE.WARNING]Die Ausführung dieses Skripts stellt ein potenzielles Sicherheitsrisiko dar. Stellen Sie sicher, dass die Informationen des öffentlichen Schlüssels in „~/.ssh“ nicht weitergegeben werden.


## Konfigurieren und Ausführen von Intel MPI

Zum Ausführen von MPI-Anwendungen auf Azure Linux RDMA müssen Sie bestimmte, für Intel MPI spezifische Umgebungsvariablen konfigurieren. Hier finden Sie ein Bash-Beispielskript zum Konfigurieren der Variablen und Ausführen einer Anwendung. Passen Sie ggf. den Pfad zu „mpivars.sh“ für Ihre Installation von Intel MPI an.

```
#!/bin/bash -x

# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh


export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Das Format der Hostdatei sieht wie folgt aus. Fügen Sie eine Zeile für jeden Knoten im Cluster hinzu. Geben Sie private IP-Adressen aus dem zuvor definierten VNet an, keine DNS-Namen. Für zwei Hosts mit den IP-Adressen 10.32.0.1 und 10.32.0.2 enthält die Datei beispielsweise Folgendes:

```
10.32.0.1:16
10.32.0.2:16
```

## Verifizieren eines Basisclusters mit zwei Knoten nach der Installation von Intel MPI

Richten Sie zunächst die Umgebung für Intel MPI ein, sofern noch nicht geschehen.

```
# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### Ausführen eines einfachen MPI-Befehls

Führen Sie einen einfachen MPI-Befehl auf einem der Computeknoten aus, um zu überprüfen, ob MPI ordnungsgemäß installiert ist und die Kommunikation zwischen mindestens zwei Computerknoten möglich ist. Mit dem folgenden **mpirun**-Befehl wird der **hostname**-Befehl auf zwei Knoten ausgeführt.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
In Ihrer Ausgabe sollten die Namen aller Knoten aufgeführt sein, die Sie als Eingabe für `-hosts` übergeben haben. Ein **mpirun**-Befehl mit zwei Knoten gibt beispielsweise eine Ausgabe zurück, die etwa wie folgt aussieht:

```
cluster11
cluster12
```

### Ausführen eines MPI-Benchmarks

Mit dem folgenden Intel MPI-Befehl werden die Clusterkonfiguration und die Verbindung mit dem RDMA-Netzwerk unter Verwendung des Pingpongbenchmarks überprüft.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

In einem funktionierenden Cluster mit zwei Knoten wird eine Ausgabe angezeigt, die etwa wie folgt aussieht: Im Azure RDMA-Netzwerk wird eine Latenz von maximal 3 Mikrosekunden für Nachrichten mit bis zu 512 Byte erwartet.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## Nächste Schritte

* Versuchen Sie, Ihre Linux-MPI-Anwendungen im Linux-Cluster bereitzustellen und auszuführen.

* Anleitungen zu Intel MPI finden Sie in der [Dokumentation zu Intel MPI Library](https://software.intel.com/de-DE/articles/intel-mpi-library-documentation/).

* Verwenden Sie eine [Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos), um einen Intel Lustre-Cluster unter Verwendung eines CentOS-basierten HPC-Images zu erstellen.

<!---HONumber=AcomDC_0629_2016-->