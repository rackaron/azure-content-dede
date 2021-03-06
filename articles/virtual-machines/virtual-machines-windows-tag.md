<properties
   pageTitle="Kennzeichnen eines virtuellen Computers | Microsoft Azure"
   description="Informieren Sie sich über das Markieren eines virtuellen Windows-Computers, der in Azure mithilfe des Resource Manager-Bereitstellungsmodells erstellt wurde."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# So markieren Sie einen virtuellen Windows-Computer in Azure


In diesem Artikel werden verschiedene Methoden zum Markieren eines virtuellen Windows-Computers in Azure mithilfe des Resource Manager-Bereitstellungsmodells beschrieben. Tags sind benutzerdefinierte Schlüssel-Wert-Paare, die direkt auf einer Ressource oder einer Ressourcengruppe platziert werden können. Azure unterstützt derzeit bis zu 15 Tags pro Ressource und Ressourcengruppe. Tags können zum Zeitpunkt der Erstellung auf einer Ressource platziert werden oder zu einer vorhandenen Ressource hinzugefügt werden. Beachten Sie, dass Tags nur für Ressourcen unterstützt werden, die über das Resource Manager-Bereitstellungsmodell erstellt wurden. Wenn Sie einen virtuellen Linux-Computer markieren möchten, finden Sie weitere Informationen unter [How to tag a Linux virtual machine in Azure](virtual-machines-linux-tag.md) (So markieren Sie einen virtuellen Linux-Computer in Azure).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Markieren mit PowerShell

Zum Erstellen, Hinzufügen und Löschen von Tags über PowerShell müssen Sie zunächst Ihre [PowerShell-Umgebung mit dem Azure-Ressourcen-Manager][] einrichten. Sobald Sie die Einrichtung abgeschlossen haben, können Sie die Tags auf Compute-, Netzwerk- und Speicherressourcen platzieren, entweder während der Erstellung oder nachdem die Ressource mithilfe von PowerShell erstellt wurde. Dieser Artikel konzentriert sich auf das Anzeigen und Bearbeiten von Tags, die auf virtuellen Computern platziert sind.

Navigieren Sie zuerst über das `Get-AzureRmVM`-Cmdlet zu einem virtuellen Computer.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Wenn der virtuelle Computer bereits Tags enthält, werden auf der Ressource anschließend alle Tags angezeigt:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Wenn Sie Tags über PowerShell hinzufügen möchten, können Sie den Befehl `Set-AzureRmResource` verwenden. Beachten Sie, dass beim Aktualisieren von Tags über PowerShell die Tags als Ganzes aktualisiert werden. Wenn Sie ein Tag auf eine Ressource hinzufügen, die bereits über Tags verfügt, müssen Sie alle Tags einschließen, die auf der Ressource platziert werden sollen. Es folgt ein Beispiel für das Hinzufügen zusätzlicher Tags zu einer Ressource über PowerShell-Cmdlets.

Das erste Cmdlet legt alle Tags, die auf *MyTestVM* platziert sind, mithilfe der Funktionen `Get-AzureRmResource` und `Tags` auf die *tags*-Variable fest.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Der zweite Befehl zeigt die Tags für die angegebene Variable an.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

Der dritte Befehl fügt ein zusätzliches Tag für die *Tags*-Variable hinzu. Beachten Sie, wie **+=** verwendet wird, um das neue Schlüssel-Wert-Paar an die *Tags*-Liste anzufügen.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

Der vierte Befehl legt alle Tags, die gemäß der *Tags*-Variable definiert sind, auf die angegebene Ressource fest. In diesem Fall ist dies „MyTestVM“.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Der fünfte Befehl zeigt alle Tags auf der Ressource an. Wie Sie sehen können, wird der *Speicherort* jetzt als Tag mit *MeinStandort* als Wert definiert.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

Weitere Informationen zum Festlegen von Tags über PowerShell finden Sie in den [Azure Resource-Cmdlets][].

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## Nächste Schritte

* Weitere Informationen zum Markieren Ihrer Azure-Ressourcen finden Sie unter [Übersicht über den Azure Resource Manager][] und [Verwenden von Tags zum Organisieren von Azure-Ressourcen][].
* Informationen dazu, wie Tags Ihnen helfen können, die Verwendung Ihrer Azure-Ressourcen zu verwalten, finden Sie unter [Informationen zu Ihrer Rechnung für Microsoft Azure][] und [Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure][].

[PowerShell-Umgebung mit dem Azure-Ressourcen-Manager]: ../powershell-azure-resource-manager.md
[Azure Resource-Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Übersicht über den Azure Resource Manager]: ../resource-group-overview.md
[Verwenden von Tags zum Organisieren von Azure-Ressourcen]: ../resource-group-using-tags.md
[Informationen zu Ihrer Rechnung für Microsoft Azure]: ../billing-understand-your-bill.md
[Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0706_2016-->