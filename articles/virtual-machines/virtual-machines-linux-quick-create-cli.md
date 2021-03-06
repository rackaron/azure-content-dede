<properties
   pageTitle="Erstellen einer Linux-VM in Azure mithilfe der Befehlszeilenschnittstelle (CLI) | Microsoft Azure"
   description="Erstellen Sie einen virtuellen Linux-Computer in Azure mithilfe der Befehlszeilenschnittstelle."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/03/2016"
   ms.author="v-livech"/>


# Erstellen eines virtuellen Linux-Computers in Azure mithilfe der Befehlszeilenschnittstelle

Dieser Artikel zeigt, wie Sie mit dem Befehl `azure vm quick-create` der Azure-Befehlszeilenschnittstelle schnell einen virtuellen Linux-Computer bereitstellen können. Der Befehl `quick-create` stellt einen virtuellen Computer mit einer umgebenden Basisinfrastruktur bereit, mit dem Sie rasch einen Prototyp erstellen oder ein Konzept testen können. (Dies ist der schnellste Weg zu einer Linux-Bash-Shell.) Für die Schritte in diesem Artikel ist ein Azure-Konto ([kostenlose Testversion herunterladen](https://azure.microsoft.com/pricing/free-trial/)) und eine Anmeldung bei der [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) (`azure login`) im Resource Manager-Modus (`azure config mode arm`) erforderlich. Sie können mithilfe des [Azure-Portals](virtual-machines-linux-quick-create-portal.md) auch schnell einen virtuellen Linux-Computer bereitstellen.

## Kurze Zusammenfassung der Befehle

Befehl zum Bereitstellen eines virtuellen CoreOS-Computers und zum Anfügen Ihres SSH-Schlüssels:

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## Bereitstellen eines virtuellen Linux-Computers

Verwendung des gleichen Befehls wie oben. Sie sehen im Folgenden jede Eingabeaufforderung zusammen mit der Ausgabe, die angezeigt werden sollte. Hier wird jedoch der RHEL-Alias zum Erstellen eines virtuellen RedHat Enterprise Linux 7.2-Computers verwendet.

## Verwenden eines ImageURN-Alias

Beim Azure-CLI-Befehl `quick-create` sind den wichtigsten Betriebssystemdistributionen Aliase zugeordnet. Die folgende Tabelle listet die Distributionsaliase auf (ab Azure-CLI Version 0.10). Alle Bereitstellungen mit `quick-create` verwenden standardmäßig virtuelle SSD-Speicher-Back-End-Computer, was eine hohe Leistung ermöglicht.

| Alias | Herausgeber | Angebot | SKU | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7,2 | neueste |
| CoreOS | CoreOS | CoreOS | Stable | neueste |
| Debian | credativ | Debian | 8 | neueste |
| openSUSE | SUSE | openSUSE | 13\.2 | neueste |
| RHEL | Redhat | RHEL | 7,2 | neueste |
| SLES | SLES | SLES | 12-SP1 | neueste |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | neueste |



Für die Option **ImageURN** (`-Q`) verwenden wir `RHEL`, um einen virtuellen RedHat Enterprise Linux 7.2-Computer bereitzustellen. (Diese sieben Aliase repräsentieren nur einen kleinen Teil der auf Azure verfügbaren Betriebssysteme. Weitere Images finden Sie im Marketplace, indem Sie [ein Image suchen](virtual-machines-linux-cli-ps-findimage.md). Sie können aber auch [ein benutzerdefiniertes Image hochladen](virtual-machines-linux-create-upload-generic.md).)

Ersetzen Sie in der folgenden exemplarischen Vorgehensweise für einen Befehl die Aufforderungen durch die Werte aus Ihrer eigenen Umgebung. Wir verwenden hier Beispielwerte.

Befolgen Sie die Anweisungen und geben Sie Ihre Namen ein.

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

Die Ausgabe sollte dem folgenden Ausgabeblock ähneln.

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Sie können jetzt eine SSH-Verbindung mit Ihrem virtuellen Computer auf dem SSH-Standardport 22 und dem vollqualifizierten Domänennamen (FQDN) (s. Ausgabe oben) herstellen. (Sie können auch die aufgeführten IP-Adresse verwenden.)

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
Der Anmeldeprozesses sollte etwa folgendermaßen aussehen:

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## Nächste Schritte

`azure vm quick-create` ist eine Möglichkeit, schnell einen virtuellen Computer bereitzustellen, damit Sie sich bei einer Bash-Shell anmelden und arbeiten können. Mit `vm quick-create` stehen Ihnen die zusätzlichen Vorteile einer komplexen Umgebung nicht zur Verfügung. Lesen Sie zum Bereitstellen eines virtuellen Linux-Computers, der an Ihre Infrastruktur angepasst ist, einen der folgenden Artikel:

- [Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und der Azure-CLI](virtual-machines-linux-cli-deploy-templates.md)
- [Direktes Erstellen einer benutzerdefinierten Umgebung für einen virtuellen Linux-Computer über Azure-CLI-Befehle](virtual-machines-linux-create-cli-complete.md)
- [Erstellen einer geschützten Linux-VM mit einer Azure-Vorlage](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Diese Artikel beschreiben die ersten Schritte beim Erstellen einer Azure-Infrastruktur sowie einer beliebigen Anzahl von proprietären und Open Source-Tools zur Infrastrukturbereitstellung, Konfiguration und Orchestrierung.

<!---HONumber=AcomDC_0706_2016-->