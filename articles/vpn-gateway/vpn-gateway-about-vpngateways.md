<properties 
   pageTitle="Informationen zu VPN Gateway | Microsoft Azure"
   description="Hier erhalten Sie Informationen zu VPN Gateway für Azure Virtual Network."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/20/2016"
   ms.author="cherylmc" />

# Informationen zu VPN Gateway

Das VPN Gateway umfasst eine Sammlung von Einstellungen, die zum Senden von Netzwerkdatenverkehr zwischen virtuellen Netzwerken und lokalen Standorten verwendet werden. In den Abschnitten in diesem Artikel werden die Einstellungen beschrieben, die sich auf das VPN Gateway beziehen. Das VPN Gateway wird für Standort-zu-Standort-, Punkt-zu-Standort- und ExpressRoute-Verbindungen verwendet. Außerdem dient das VPN Gateway zum Senden von Datenverkehr zwischen mehreren virtuellen Netzwerken in Azure (VNET-zu-VNET).

Das VPN Gateway kann einem virtuellen Netzwerk hinzugefügt werden, um eine Verbindung zu erstellen. Jedes virtuelle Netzwerk kann nur über ein VPN Gateway verfügen, und für jede Verbindung gelten bestimmte Konfigurationsschritte. Verbindungsdiagramme finden Sie unter [Azure VPN Gateway-Verbindungstopologien](vpn-gateway-topology.md).

## <a name="gwsku"></a>Gateway-SKUs

Beim Erstellen eines VPN Gateways müssen Sie die gewünschte Gateway-SKU angeben. Gateway-SKUs gelten sowohl für ExpressRoute als auch für das VPN Gateway. Für die einzelnen Gateway-SKUs gelten unterschiedliche Preise. Informationen zu den Preisen finden Sie unter [VPN-Gateway – Preise](https://azure.microsoft.com/pricing/details/vpn-gateway/). Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – Technische Übersicht](../expressroute/expressroute-introduction.md).

Es gibt drei VPN-Gateway-SKUs:

- Basic
- Standard
- HighPerformance

Im Beispiel unten wird `-GatewaySku` als *Standard* angegeben.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

###  <a name="aggthroughput"></a>Geschätzter aggregierter Durchsatz nach SKU und Gatewaytyp


In der folgenden Tabelle sind die Gatewaytypen und der geschätzte zusammengefasste Durchsatz angegeben. Diese Tabelle betrifft sowohl das Resource Manager-Bereitstellungsmodell als auch das klassische Bereitstellungsmodell.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="gwtype"></a>Gatewaytypen

Mit dem Gatewaytyp wird angegeben, wie die Verbindung für das Gateway selbst hergestellt wird. Es ist eine erforderliche Konfigurationseinstellung für das Resource Manager-Bereitstellungsmodell. Verwechseln Sie den Gatewaytyp nicht mit dem VPN-Typ, mit dem der Typ der Weiterleitung für Ihr VPN angegeben wird. Die verfügbaren Werte für `-GatewayType` lauten:

- VPN
- ExpressRoute


Im Beispiel für das Resource Manager-Bereitstellungsmodell wird „-GatewayType“ als *Vpn* angegeben. Beim Erstellen eines Gateways müssen Sie sicherstellen, dass der Gatewaytyp für Ihre Konfiguration korrekt ist.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="connectiontype"></a>Verbindungstypen

Für jede Konfiguration ist ein bestimmter Verbindungstyp erforderlich. Die verfügbaren Resource Manager-PowerShell-Werte für `-ConnectionType` sind:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

Im folgenden Beispiel erstellen wir eine Standort-zu-Standort-Verbindung, für die der Verbindungstyp „IPsec“ festgelegt werden muss.

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="vpntype"></a>VPN-Typen

Für jede Konfiguration ist ein bestimmter VPN-Typ erforderlich, damit sie funktioniert. Wenn Sie zwei Konfigurationen kombinieren, z.B. das Erstellen einer Site-to-Site-Verbindung und einer Point-to-Site-Verbindung mit demselben VNET, müssen Sie einen VPN-Typ verwenden, mit dem beide Verbindungsanforderungen erfüllt werden.

Bei einer Koexistenz von Point-to-Site- und Site-to-Site-Verbindungen müssen Sie einen routenbasierten VPN-Typ nutzen, wenn Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, bzw. ein dynamisches Gateway, wenn Sie das klassische Bereitstellungsmodell verwenden.

Beim Erstellen der Konfiguration wählen Sie den VPN-Typ aus, der für Ihre Verbindung erforderlich ist.

Es gibt zwei VPN-Typen:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Im Beispiel für das Resource Manager-Bereitstellungsmodell wird `-VpnType` als *RouteBased* angegeben. Beim Erstellen eines Gateways müssen Sie sicherstellen, dass -VpnType für Ihre Konfiguration korrekt ist.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Gatewayanforderungen

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Gatewaysubnetz

Sie müssen zuerst ein Gatewaysubnetz für Ihr VNET erstellen, um ein VPN Gateway zu konfigurieren. Das Gatewaysubnetz muss den Namen *GatewaySubnet* haben, damit es einwandfrei funktioniert. Anhand dieses Namens kann Azure erkennen, dass dieses Subnetz für das Gateway verwendet werden soll.<BR>Wenn Sie das klassische Portal verwenden, erhält das Gatewaysubnetz auf der Portaloberfläche automatisch den Namen *Gateway*. Dies gilt nur speziell für die Anzeige des Gatewaysubnetzes im klassischen Portal. In diesem Fall wird das Subnetz in Azure dann als *GatewaySubnet* erstellt und kann auf diese Weise im Azure-Portal und in PowerShell angezeigt werden.

Die Mindestgröße des Gatewaysubnetzes hängt gänzlich von der Konfiguration ab, die Sie erstellen möchten. Obwohl es bei manchen Konfigurationen möglich ist, ein Gatewaysubnetz mit einer Größe von nur /29 zu erstellen, empfehlen wir, ein Gatewaysubnetz von mindestens /28 (/28, /27, /26 usw.) zu erstellen.

Wenn Sie eine höhere Gatewaygröße wählen, wird verhindert, dass Sie die Größenbeschränkung für Gateways erreichen. Wenn Sie beispielsweise ein Gateway mit einem Gatewaysubnetz der Größe /29 erstellt haben und eine Konfiguration zur Koexistenz von Site-to-Site und ExpressRoute durchführen möchten, müssen Sie folgende Schritte ausführen: Sie müssen das Gateway löschen, das Gatewaysubnetz löschen, das Gatewaysubnetz der Größe /28 oder größer erstellen und dann das Gateway neu erstellen.

Indem Sie gleich ein größeres Gatewaysubnetz erstellen, können Sie später Zeit sparen, wenn Sie der Netzwerkumgebung neue Konfigurationsfeatures hinzufügen.

Im Beispiel unten ist ein Gatewaysubnetz mit dem Namen GatewaySubnet zu sehen. Sie erkennen, das mit der CIDR-Notation die Größe /27 angegeben wird. Dies ist für die meisten Konfigurationen groß genug für eine ausreichende Zahl von IP-Adressen, die derzeit üblich sind.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Stellen Sie sicher, dass dem Gatewaysubnetz keine Netzwerksicherheitsgruppe (NSG) zugewiesen ist, da dies Verbindungsfehler verursachen kann.



## <a name="lng"></a>Gateways des lokalen Netzwerks

Mit dem Gateway des lokalen Netzwerks ist normalerweise Ihr lokaler Standort gemeint. Beim klassischen Bereitstellungsmodell wurde das Gateway des lokalen Netzwerks als „Lokaler Standort“ bezeichnet. Sie geben dem Gateway des lokalen Netzwerks einen Namen, stellen die öffentliche IP-Adresse des lokalen VPN-Geräts bereit und geben die Adresspräfixe an, die sich am lokalen Standort befinden. Azure sucht unter den Zieladresspräfixen nach Netzwerkdatenverkehr, sieht in der Konfiguration nach, die Sie für das Gateway des lokalen Netzwerks angegeben haben, und leitet die Pakete entsprechend weiter. Sie können diese Adresspräfixe bei Bedarf ändern.


### Ändern von Adresspräfixen – Resource Manager

Beim Ändern von Adresspräfixen unterscheidet sich das Verfahren in Abhängigkeit davon, ob Sie Ihr VPN Gateway bereits erstellt haben. Weitere Informationen finden Sie im Artikelabschnitt [Ändern von Adresspräfixen für ein Gateway für das lokale Netzwerk](vpn-gateway-create-site-to-site-rm-powershell.md#modify).

Im Beispiel unten sehen Sie, dass ein Gateway des lokalen Netzwerks mit dem Namen MyOnPremiseWest angegeben ist und zwei IP-Adresspräfixe enthält.

	New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')	

### Ändern von Adresspräfixen – Klassische Bereitstellung

Wenn Sie Ihre lokalen Standorte bei Verwendung des klassischen Bereitstellungsmodells ändern, können Sie die Konfigurationsseite „Lokale Netzwerke“ im klassischen Portal verwenden oder die Datei für die Netzwerkkonfiguration (NETCFG.XML) direkt ändern.



## Nächste Schritte

Lesen Sie sich die weiteren Informationen im Artikel [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md) durch, bevor Sie mit der Planung und dem Entwurf Ihrer Konfiguration fortfahren.





 

<!---HONumber=AcomDC_0720_2016-->