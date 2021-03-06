<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure"
	description="Ein Thema, in dem erläutert wird, was Azure AD Privileged Identity Management ist und wie Sie damit die Sicherheit in Ihrer Cloud erhöhen."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2016"
	ms.author="kgremban"/>

# Azure AD Privileged Identity Management

Mithilfe von Azure Active Directory (AD) Privileged Identity Management (PIM) können Sie Zugriff auf Ressourcen in Azure AD und anderen Microsoft Online Services wie Office 365 oder Microsoft Intune verwalten, steuern und überwachen.

Organisationen möchten die Anzahl der Personen minimieren, die auf sichere Informationen oder Ressourcen zugreifen können, da dadurch das Risiko reduziert wird, dass ein böswilligen Benutzers Zugriff hat. Benutzer müssen jedoch immer noch in der Lage sein, in Azure-, Office 365- oder SaaS-Apps privilegierte Vorgänge auszuführen. Schließlich muss die Arbeit erledigt werden. Daher gewähren Organisationen Benutzern privilegierten Zugriff in Azure AD, ohne zu überwachen, was Benutzer mit ihren Administratorberechtigungen machen. Mit Azure AD Privileged Identity Management können Sie dieses Risiko in den Griff bekommen.

Azure AD Privileged Identity Management ermöglicht Ihnen Folgendes:

- Ermitteln, welche Benutzer Azure AD-Administratoren sind
- Aktivieren von bedarfsgesteuertem Just-In-Time-Administratorzugriff auf Microsoft Online Services wie z. B. Office 365 und Intune
- Abrufen von Berichten zum Administratorzugriffsverlauf und zu Änderungen bei Administratorzuweisungen
- Aktivieren von Benachrichtigungen zum Zugriff auf eine privilegierte Rolle

Azure AD Privileged Identity Management kann die folgenden integrierten Azure AD-Organisationsrollen verwalten:

- Globaler Administrator
- Abrechnungsadministrator
- Dienstadministrator
- Benutzeradministrator
- Kennwortadministrator

## Bedarfsabhängiger Administratorzugriff

In der Vergangenheit konnten Sie einen Benutzer über das Azure-Verwaltungsportal oder Windows PowerShell einer Administratorrolle zuweisen. Dadurch wird der Benutzer zum **permanenten Administrator**, der in seiner zugewiesenen Rolle stets aktiv bleibt. Mit Azure AD Privileged Identity Management wird das Konzept **berechtigter Administratoren** eingeführt. Berechtigte Administratoren sollten Benutzer sein, die ab und an privilegierten Zugriff benötigen, aber nicht jeden Tag. Die Rolle ist inaktiv, bis der Benutzer Zugriff benötigt. Dann wird eine Aktivierung ausgeführt, und der Benutzer wird für einen zuvor festgelegten Zeitraum zu einem aktiven Administrator.

## Aktivieren von Privileged Identity Management für Ihr Verzeichnis

Im [Azure-Portal](https://portal.azure.com/) können Sie mit der Nutzung von Azure AD Privileged Identity Management beginnen.

>[AZURE.NOTE] Sie müssen ein globaler Administrator mit einem Geschäftskonto (wie @IhreDomäne.com) und keinem Microsoft-Konto (wie @outlook.com) sein, um Azure AD Privileged Identity Management für ein Verzeichnis zu aktivieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Verzeichnisses an.
2. Wenn Ihre Organisation über mehr als ein Verzeichnis verfügt, klicken Sie in der oberen rechten Ecke des Azure-Portals auf Ihren Benutzernamen, und wählen Sie das Verzeichnis aus, in dem Sie Azure AD Privileged Identity Management verwenden möchten.
3. Wählen Sie **Neu** > **Sicherheit und Identität** > **Azure AD Privileged Identity Management** aus.

	![Aktivieren von PIM im Portal][1]

4. Aktivieren Sie das Kontrollkästchen **An Dashboard anheften**, und klicken Sie dann auf die Schaltfläche **Erstellen**. Das Privileged Identity Management-Dashboard wird geöffnet.

Wenn Sie die erste Person sind, die Azure AD Privileged Identity Management in Ihrem Verzeichnis verwendet, führt Sie der [Sicherheits-Assistent](active-directory-privileged-identity-management-security-wizard.md) durch die erste Zuweisung. Danach sind Sie automatisch der erste **Sicherheitsadministrator** und der **Administrator für privilegierte Rollen** des Verzeichnisses.

Nur ein Administrator für privilegierte Rollen kann den Zugriff für andere Administratoren verwalten. Sie können [anderen Benutzern Zugriff auf PIM gewähren, damit diese Verwaltungsaktivitäten ausführen können](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Privileged Identity Management-Dashboard

Azure AD Privileged Identity Management bietet ein Dashboard, das wichtige Informationen enthält, wie z. B.:

- Warnungen, die auf Möglichkeiten zur Erhöhung der Sicherheit hinweisen
- Anzahl der Benutzer, die jeder privilegierten Rolle zugewiesen sind
- Anzahl berechtigter und permanenter Administratoren
- Kontinuierliche Prüfung des Zugriffs

![PIM-Dashboard – Screenshot][2]

## Verwaltung privilegierter Rollen

Mit Azure AD Privileged Identity Management können Sie die Administratoren verwalten, indem Sie jeder Rolle permanente oder berechtigte Administratoren hinzufügen oder diese entfernen.

![Hinzufügen/Entfernen von PIM-Administratoren – Screenshot][3]

## Konfigurieren der Rollenaktivierungseinstellungen

Mithilfe der Rollenaktivierungseinstellungen können Sie die Eigenschaften der Aktivierung der berechtigten Rolle konfigurieren:

- Dauer des Rollenaktivierungszeitraums
- Benachrichtigung zur Rollenaktivierung
- Informationen, die ein Benutzers während des Rollenaktivierungsprozesses bereitstellen muss

![PIM-Einstellungen – Administratoraktivierung – Screenshot][4]

Beachten Sie, dass in der Abbildung die Schaltflächen für **Multi-Factor Authentication** deaktiviert sind. Für bestimmte, sehr privilegierte Rollen ist MFA für erhöhten Schutz erforderlich.

## Rollenaktivierung  

Um eine Rolle zu aktivieren, muss ein berechtigter Administrator eine zeitgebundene „Aktivierung“ für die Rolle anfordern. Die Aktivierung kann über die Option **Meine Rolle aktivieren** in Azure AD Privileged Identity Management angefordert werden.

Ein Administrator, der eine Rolle aktivieren möchte, muss Azure AD Privileged Identity Management im Azure-Portal initialisieren.

Alle Administratortypen können Azure AD Privileged Identity Management zum Aktivieren ihrer Rolle nutzen.

Die Rollenaktivierung ist zeitlich gebunden. In den Rollenaktivierungseinstellungen können Sie die Dauer der Aktivierung und auch die erforderlichen Informationen festlegen, die der Administrator angeben muss, um die Rolle zu aktivieren.

![Anforderung der Rollenaktivierung durch PIM-Administrator – Screenshot][5]

## Verlauf der Rollenaktivierung

Mithilfe von Azure AD Privileged Identity Management können Sie auch Änderungen bei Zuweisungen privilegierter Rollen und den Rollenaktivierungsverlauf nachverfolgen. Dies kann mithilfe der Überwachungsprotokolloptionen erfolgen:

![PIM-Aktivierungsverlauf – Screenshot][6]

## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0706_2016-->