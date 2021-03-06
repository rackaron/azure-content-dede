
Die wichtigsten Merkmale dieser Instanzen lauten wie folgt:

* **Hochleistungshardware**: Die Azure-Rechenzentrumshardware, auf der diese Instanzen ausgeführt werden, wurde für rechenintensive Anwendungen mit hoher Netzwerkauslastung konzipiert und optimiert. Hierzu zählen beispielsweise High Performance Computing (HPC)- und Stapelanwendungen, Modellierung und umfangreiche Simulationen.

* **RDMA-Netzwerkverbindung für MPI-Anwendungen**: Richten Sie die A8- und A9-Instanzen so ein, dass sie über ein Azure-Netzwerk mit niedriger Latenz und hohem Durchsatz, das auf RDMA-Technologie (Remote Direct Memory Access) basiert, mit anderen A8- und A9-Instanzen kommunizieren können. Dieses Feature kann die Leistung von bestimmten Linux- und Windows-MPI-Anwendungen (Message Passing Interface) steigern.

* **Unterstützung für HPC-Cluster**: Stellen Sie in den A8-, A9-, A10- und A11-Instanzen in Azure Clusterverwaltungs- und Auftragsplanungssoftware bereit, um einen eigenständigen HPC-Cluster zu erstellen oder die Kapazität eines lokalen Clusters zu erweitern.

>[AZURE.NOTE]Die A10- und A11-Instanzen verfügen über die gleichen Leistungsoptimierungen und Spezifikationen wie die A8- und A9-Instanzen. Allerdings bieten sie keinen Zugriff auf das RDMA-Netzwerk in Azure. Sie wurden für HPC-Anwendungen entworfen, die keine konstante Kommunikation mit niedriger Latenz zwischen den Knoten erfordern und auch als parametrische oder hochgradig parallele Anwendungen bezeichnet werden.


## Spezifikationen

### CPU und Arbeitsspeicher

Die rechenintensiven Azure-Instanzen A8 und A9, A10 und A11 verfügen über schnelle Mehrkern-CPUs und große Mengen an Arbeitsspeicher, wie in der folgenden Tabelle dargestellt.

Größe | CPU | Arbeitsspeicher
------------- | ----------- | ----------------
A8 und A10 | Intel Xeon E5-2670<br/>8 Kerne, 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 und A11 | Intel Xeon E5-2670<br/>16 Kerne, 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Weitere Prozessordetails sowie die unterstützten Befehlssatzerweiterungen finden Sie auf der Website „Intel.com“.

### Netzwerkadapter

Die A8- und A9-Instanzen besitzen zwei Netzwerkadapter, die mit den beiden folgenden Azure-Back-End-Netzwerken verbunden sind:


Netzwerk | Beschreibung
-------- | -----------
Ethernet mit 10 GBit/s | Verbindung mit Azure-Diensten (z. B. Azure Storage und Azure Virtual Network) und mit dem Internet
Back-End mit 32 GBit/s, RDMA-fähig | Ermöglicht Anwendungskommunikation mit geringer Latenz und hohem Durchsatz zwischen Instanzen in einem einzelnen Clouddienst oder in einer einzelnen Verfügbarkeitsgruppe Für MPI-Verkehr reserviert.


>[AZURE.IMPORTANT]Informationen zu zusätzlichen Anforderungen von MPI-Anwendungen beim Zugriff auf das RDMA-Netzwerk finden Sie in diesem Artikel unter [Zugreifen auf das RDMA-Netzwerk](#access-to-the-rdma-network).

A10- und A11-Instanzen besitzen einen einzelnen 10-GBit/s-Ethernet-Netzwerkadapter, der mit den Azure-Diensten und dem Internet verbunden ist.

## Überlegungen zur Bereitstellung

* **Azure-Abonnement**: Wenn Sie eine größere Anzahl von rechenintensiven Instanzen bereitstellen möchten, sollten Sie ein nutzungsbasiertes Abonnement oder andere Kaufoptionen in Erwägung ziehen. Bei Verwendung eines [kostenlosen Azure-Kontos](https://azure.microsoft.com/free/) können Sie nur eine begrenzte Anzahl von Azure-Compute-Kernen nutzen.

* **Kernkontingent**: Unter Umständen müssen Sie das Kontingent an CPU-Kernen in Ihrem Azure-Abonnement erhöhen. Standardmäßig stehen 20 Kerne pro Abonnement (im klassischen Bereitstellungsmodell) oder 20 Kerne pro Region (im Resource Manager-Bereitstellungsmodell) zur Verfügung. Zum Anfordern einer Kontingenterhöhung erstellen Sie ein kostenloses Supportticket, wie in [Grundlegendes zu Azure-Einschränkungen und -Steigerungen](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) beschrieben.

    >[AZURE.NOTE]Wenn Sie einen umfangreichen Kapazitätsbedarf haben, wenden Sie sich an den Azure-Support. Azure-Kontingente sind Angebotsbeschränkungen, keine Kapazitätsgarantien. Unabhängig vom Kontingent werden nur verwendete Kerne in Rechnung gestellt.

* **Virtuelles Netzwerk**: Ein [virtuelles Azure-Netzwerk](https://azure.microsoft.com/documentation/services/virtual-network/) ist nicht erforderlich, um die rechenintensiven Instanzen zu verwenden. Sie benötigen möglicherweise jedoch für viele Szenarios mindestens ein cloudbasiertes virtuelles Azure-Netzwerk oder für den Zugriff auf lokale Ressourcen, z. B. einen Anwendungslizenzserver, eine Site-to-Site-Verbindung. Sie müssen ein neues virtuelles Netzwerk erstellen, um die Instanzen bereitzustellen. Das Hinzufügen von A8-, A9-, A10- oder A11-VMs zu einem virtuellen Netzwerk in einer Affinitätsgruppe wird nicht unterstützt.

* **Clouddienst oder Verfügbarkeitsgruppe**: Um eine Verbindung über das RDMA-Netzwerk herzustellen, müssen die virtuellen A8- oder A9-Computer im gleichen Clouddienst (klassisches Bereitstellungsmodell) oder in der gleichen Verfügbarkeitsgruppe (Azure Resource Manager-Bereitstellungsmodell) bereitgestellt werden.

* **Preise**: Die Größen A8 bis A11 für virtuelle Computer sind nur im Tarif "Standard" verfügbar.

* **Größenänderung** – Sie können einer rechenintensiven Instanz (A8-A11) nur die Größe A8 bis A11 zuweisen, und Sie können eine rechenintensive Instanz nicht in eine nicht rechenintensive Instanz ändern. Dies liegt an der speziellen Hardware und den Leistungsoptimierungen, die für rechenintensive Instanzen typisch sind.

* **RDMA-Netzwerkadressbereich**: Für das RDMA-Netzwerk in Azure wird der Adressbereich 172.16.0.0/16 reserviert. Wenn Sie beabsichtigen, MPI-Anwendungen auf A8- und A9-Instanzen in einem virtuellen Azure-Netzwerk auszuführen, stellen Sie sicher, dass der Adressbereich des virtuellen Netzwerks sich nicht mit dem RDMA-Netzwerk überschneidet.

<!---HONumber=AcomDC_0622_2016-->