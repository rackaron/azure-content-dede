<properties
   pageTitle="Azure Automation-Sicherheit"
   description="Dieser Artikel enthält eine Übersicht über die Automation-Sicherheit und die unterschiedlichen Authentifizierungsmethoden, die für Automation-Konten in Azure Automation verfügbar sind."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="Automation-Sicherheit, sicher Automation" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="magoedte" />

# Azure Automation-Sicherheit
Mit Azure Automation können Sie Aufgaben für Ressourcen in Azure, lokal und mit anderen Cloudanbietern, z.B. Amazon Web Services (AWS), automatisieren. Damit ein Runbook seine erforderlichen Aktionen durchführen kann, muss es über die Berechtigungen zum sicheren Zugreifen auf die Ressourcen verfügen. Hierbei sollten die minimalen Rechte gewährt werden, die im Rahmen des Abonnements erforderlich sind. In diesem Artikel werden die unterschiedlichen Authentifizierungsszenarien beschrieben, die von Azure Automation unterstützt werden. Außerdem wird gezeigt, wie Sie für die Umgebung bzw. Umgebungen, die Sie verwalten müssen, die ersten Schritte durchführen.

## Automation-Konto – Übersicht
Wenn Sie das erste Mal mit Azure Automation arbeiten, müssen Sie zunächst mindestens ein Automation-Konto erstellen. Mithilfe von Automation-Konten können Sie Ihre Automation-Ressourcen (Runbooks, Objekte, Konfigurationen) von den Ressourcen in anderen Automation-Konten isolieren. Sie können Automation-Konten dazu verwenden, um Ressourcen in separate logische Umgebungen zu trennen. Beispielsweise können Sie ein Konto für die Entwicklung, ein Konto für die Produktion und ein Konto für Ihre lokale Umgebung nutzen. Ein Azure Automation-Konto unterscheidet sich von einem Microsoft-Konto, das unter Ihrem Azure-Abonnement erstellt wird.

Die Automation-Ressourcen für jedes Automation-Konto sind mit einer einzelnen Azure-Region verknüpft, aber die Automation-Konten können Ressourcen in beliebigen Regionen verwalten. Der Hauptgrund für das Erstellen von Automation-Konten in unterschiedlichen Regionen ist der, dass Richtlinien eine Isolierung von Daten und Ressourcen innerhalb einer spezifischen Region vorsehen.

>[AZURE.NOTE]Auf Automation-Konten und die darin enthaltenen Ressourcen, die mit dem Azure-Portal erstellt werden, kann im klassischen Azure-Portal nicht zugegriffen werden. Wenn Sie diese Konten oder die enthaltenen Ressourcen mit Windows PowerShell verwalten möchten, müssen Sie die Azure-Ressourcen-Manager-Module verwenden.

Alle Aufgaben, die Sie für Ressourcen mit dem Azure Resource Manager (ARM) und den Azure-Cmdlets in Azure Automation durchführen, müssen gegenüber Azure mit der Azure Active Directory basierend auf den Anmeldeinformationen für die Organisationsidentität authentifiziert werden. Ursprünglich wurde die zertifikatbasierte Authentifizierungsmethode mit dem Azure-Dienstverwaltungsmodus (ASM) verwendet, aber die Einrichtung hierfür war kompliziert. Die Authentifizierung für Azure per Azure AD-Benutzer wurde 2014 nicht nur eingeführt, um den Konfigurationsprozess für ein Authentifizierungskonto zu vereinfachen. Es sollte auch die Möglichkeit der nicht interaktiven Authentifizierung für Azure mit einem einzelnen Benutzerkonto unterstützt werden, das sowohl für den ASM-Modus als auch für den ARM-Modus funktioniert.

Wir haben vor Kurzem ein weiteres Update veröffentlicht, bei dem bei der Erstellung des Automation-Kontos jetzt automatisch ein Azure AD-Dienstprinzipalobjekt erstellt wird. Dies wird als „ausführendes Konto“ von Azure bezeichnet und ist das standardmäßige Authentifizierungverfahren für die Runbookautomatisierung mit Azure Resource Manager.

Die rollenbasierte Zugriffssteuerung ist im ARM-Modus verfügbar, um einem Azure AD-Benutzerkonto und -Dienstprinzipal zulässige Aktionen zu gewähren und den Dienstprinzipal zu authentifizieren. Weitere Informationen zur Entwicklung Ihres Modells zum Verwalten von Automation-Berechtigungen finden Sie im Artikel [Rollenbasierte Zugriffssteuerung in Azure Automation](../automation/automation-role-based-access-control.md).

Für Runbooks, die in Ihrem Rechenzentrum über einen Hybrid Runbook Worker oder für Computing-Dienste in AWS ausgeführt werden, kann nicht das gleiche Verfahren verwendet werden, das normalerweise für die Authentifizierung von Runbooks bei Azure-Ressourcen genutzt wird. Dies liegt daran, dass diese Ressourcen außerhalb von Azure ausgeführt werden und es daher erforderlich ist, dass deren eigenen in Automation definierten Sicherheitsanmeldeinformationen für die Authentifizierung gegenüber Ressourcen verwendet werden, auf die lokal zugegriffen wird.

## Authentifizierungsmethoden

In der folgenden Tabelle sind die unterschiedlichen Authentifizierungsmethoden für die einzelnen Umgebungen zusammengefasst, die von Azure Automation unterstützt werden. Außerdem ist der Artikel angegeben, in dem beschrieben wird, wie Sie die Authentifizierung für Ihre Runbooks einrichten.

Methode | Environment | Artikel
----------|----------|----------
Azure AD-Benutzerkonto | Azure-Ressourcen-Manager und Azure Service Management | [Authentifizieren von Runbooks mit dem Azure AD-Benutzerkonto](../automation/automation-sec-configure-aduser-account.md)
Azure AD-Dienstprinzipalobjekt | Azure-Ressourcen-Manager | [Authentifizieren von Runbooks mit der Azure-Option „Ausführendes Konto“](../automation/automation-sec-configure-azure-runas-account.md)
Windows-Authentifizierung | Lokales Rechenzentrum | [Authentifizieren von Runbooks für Hybrid Runbook Workers](../automation/automation-hybrid-runbook-worker.md)
AWS-Anmeldeinformationen | Amazon Web Services | [Authentifizieren von Runbooks mit Amazon Web Services (AWS)](../automation/automation-sec-configure-aws-account.md)

<!---HONumber=AcomDC_0713_2016-->