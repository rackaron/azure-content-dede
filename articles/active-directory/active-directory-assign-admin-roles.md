<properties
	pageTitle="Zuweisen von Administratorrollen in Azure Active Directory | Microsoft Azure"
	description="Erläutert, welche Administratorrollen in Azure Active Directory verfügbar sind und wie sie zugewiesen werden."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="curtand"/>

# Zuweisen von Administratorrollen in Azure Active Directory

Mithilfe von Azure Active Directory (Azure AD) können Sie verschiedene Administratoren bestimmen, um unterschiedliche Funktionen zu erfüllen. Diese Administratoren haben Zugriff auf verschiedene Funktionen im Azure-Portal oder im klassischen Azure-Portal und können abhängig von ihrer Rolle unter anderem Benutzer erstellen oder bearbeiten, anderen administrative Rollen zuweisen, Benutzerkennwörter zurücksetzen, Benutzerlizenzen verwalten und Domänen verwalten. Ein Benutzer mit einer Administratorrolle besitzt die gleichen Berechtigungen für alle Clouddienste, die Ihre Organisation abonniert hat, unabhängig davon, ob Sie die Rolle im Office 365-Portal, im klassischen Azure-Portal oder mithilfe des Azure AD-Moduls für Windows PowerShell zuweisen.

Die folgenden Administratorrollen sind verfügbar:

- **Rechnungsadministrator**: erledigt Käufe, verwaltet Abonnements, verwaltet Support-Tickets und überwacht die Integrität des Dienstes.

- **Globaler Administrator**: hat Zugriff auf alle administrativen Funktionen. Die Person, die die Anmeldung für das Azure-Konto vornimmt, wird ein globaler Administrator. Nur globale Administratoren können weitere Administratorrollen zuweisen. In Ihrem Unternehmen können mehrere globale Administratoren vorhanden sein.

	> [AZURE.NOTE] In der Microsoft Graph-API, der Azure AD Graph-API und Azure AD PowerShell wird diese Rolle als „Unternehmensadministrator“ identifiziert.

- **Kennwortadministrator**: setzt Kennwörter zurück, verwaltet Dienstanforderungen und überwacht die Integrität des Diensts. Kennwortadministratoren können Kennwörter nur für Benutzer und andere Kennwortadministratoren zurücksetzen.

	> [AZURE.NOTE] In der Microsoft Graph-API, der Azure AD Graph-API und Azure AD PowerShell wird diese Rolle als „Helpdeskadministrator“ identifiziert.

- **Dienstadministrator**: verwaltet Dienstanforderungen und überwacht die Integrität des Dienstes.

	> [AZURE.NOTE] Um einem Benutzer die Dienstadministratorrolle zuzuweisen, muss der globale Administrator zunächst dem Benutzer Administratorberechtigungen im Dienst (z. B. Exchange Online) zuweisen, und anschließend die Dienstadministratorrolle im klassischen Azure-Portal.

- **Benutzeradministrator**: setzte Kennwörter zurück, überwacht die Dienstintegrität und verwaltet Benutzerkonten, Benutzergruppen und Dienstanforderungen. Für die Berechtigungen eines Benutzerverwaltungsadministrators gelten einige Einschränkungen. Sie können z. B. keinen globalen Administrator löschen oder andere Administratoren erstellen. Sie können außerdem keine Kennwörter für Abrechnungs-, globale und Dienstadministratoren zurücksetzen.

- **Sicherheit lesen**: schreibgeschützter Zugriff auf eine Reihe von Sicherheitsfunktionen von Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health und Office 365 Protection Center.

- **Sicherheitsadministrator**: alle Leseberechtigungen der Rolle **Sicherheit lesen** sowie eine Reihe von zusätzlichen administrativen Berechtigungen für die gleichen Dienste: Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health und Office 365 Protection Center.

## Administratorberechtigungen

### Abrechnungsadministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens- und Benutzerinformationen</p><p>Verwalten von Office-Support-Tickets</p><p>Ausführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p> | <p>Zurücksetzen von Benutzerkennwörtern</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere</p><p>Verwenden von Verzeichnissynchronisierung</p>

### Globaler Administrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens-und Benutzerinformationen</p><p>Verwalten von Office-Support-Tickets</p><p>Ausführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p> <p>Zurücksetzen von Benutzerkennwörtern</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere</p><p>Verwenden von Verzeichnissynchronisierung</p><p>Aktivieren oder Deaktivieren der Multi-Factor Authentication</p> | N/V

### Kennwortadministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens-und Benutzerinformationen</p><p>Verwalten von Office-Support-Tickets</p><p>Zurücksetzen von Kennwörtern</p> | <p>Ausführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere</p><p>Verwenden der Verzeichnissynchronisierung</p>

### Dienstadministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens-und Benutzerinformationen</p><p>Verwalten von Office-Support-Tickets</p> | <p>Zurücksetzen von Benutzerkennwörtern</p><p>Ausführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere</p><p>Verwenden der Verzeichnissynchronisierung</p>

### Benutzeradministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens-und Benutzerinformationen</p><p>Verwalten von Office-Support-Tickets</p><p>Zurücksetzen von Benutzerkennwörtern (mit Einschränkungen). Er kann keine Kennwörter für Abrechnungs-, globale und Dienstadministratoren zurücksetzen.</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, Bearbeiten und Löschen von Benutzern und Gruppen, Verwalten von Benutzerlizenzen (mit Einschränkungen). Er oder Sie kann keinen globalen Administrator löschen oder andere Administratoren erstellen.</p> | <p>Ausführen von Abrechnungs- und Kaufvorgängen für Office-Produkte</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere</p><p>Verwenden der Verzeichnissynchronisierung</p><p>Aktivieren oder Deaktivieren der Multi-Factor Authentication</p>

### Sicherheit lesen

Geben Sie in | Möglich
------------- | -------------
Identity Protection Center | Lesen von allen Sicherheitsberichten und Einstellungsinformationen für die Sicherheitsfunktionen<ul><li>Antispam<li>Verschlüsselung<li>Verhinderung von Datenverlust<li>Antischadsoftware<li>Advanced Threat Protection<li>Antiphishing<li>Nachrichtenflussregeln
Privileged Identity Management | <p>Verfügt über schreibgeschützten Zugriff auf alle eingeblendeten Informationen in Azure AD PIM: Richtlinien und Berichte für Azure AD-Rollenzuweisungen, Sicherheitsüberprüfungen und in Zukunft Lesezugriff auf Richtliniendaten und Berichte für Szenarien zusätzlich zu der Azure AD-Rollenzuweisung.<p>**Kann sich nicht** für Azure AD PIM registrieren oder Änderungen durchführen. Im PIM-Portal oder über PowerShell können Personen mit dieser Rolle zusätzliche Rollen (z.B. globaler Administrator oder Administrator für privilegierte Rollen) aktivieren, wenn der Benutzer für sie geeignet ist.
<p>Monitor Office 365 Service Health</p><p>Office 365 Protection Center</p> | <ul><li>Lesen und Verwalten von Warnungen<li>Lesen von Sicherheitsrichtlinien<li>Lesen von Informationen zu Bedrohungen, Cloud App Discovery und Quarantäne in „Search and Investigate“<li>Lesen aller Berichte

### Sicherheitsadministrator

Geben Sie in | Möglich
------------- | -------------
Identity Protection Center | <ul><li>Alle Berechtigungen der Rolle „Sicherheit lesen“.<li>Darüber hinaus die Möglichkeit, alle IPC-Vorgänge außer des Zurücksetzens von Kennwörtern auszuführen.
Privileged Identity Management | <ul><li>Alle Berechtigungen der Rolle „Sicherheit lesen“.<li>Kann Rollenmitgliedschaften oder -einstellungen in Azure AD **nicht** verwalten.
<p>Monitor Office 365 Service Health</p><p>Office 365 Protection | <ul><li>Alle Berechtigungen der Rolle „Sicherheit lesen“.<li>Kann alle Einstellungen im Feature „Advanced Threat Protection“ (Schutz vor Malware und Viren, schadhafte URL-Konfiguration, URL-Ablaufverfolgung usw.) konfigurieren.

## Details zur globalen Administratorrolle

Der globale Administrator hat Zugriff auf alle administrativen Funktionen. Standardmäßig wird der Person, die sich für ein Azure-Abonnement registriert, die globale Administratorrolle für das Verzeichnis zugewiesen. Nur globale Administratoren können weitere Administratorrollen zuweisen.

## Zuweisen oder Entfernen von Administratorrollen

1. Klicken Sie im klassischen Azure-Portal auf **Active Directory**, und klicken Sie dann auf den Namen des Verzeichnisses Ihrer Organisation.

2. Auf der Seite **Benutzer** klicken Sie auf den Anzeigenamen des Benutzers, den Sie bearbeiten möchten.

3. In der Liste **Organisationsrolle** wählen Sie die Administratorrolle, die diesem Benutzer zugewiesen werden soll, oder wählen Sie **Benutzer**, wenn Sie eine vorhandene Administratorrolle entfernen möchten.

4. Im Feld **Alternative E-Mail-Adresse** geben Sie eine E-Mail-Adresse ein. Diese E-Mail-Adresse wird für wichtige Benachrichtigungen, einschließlich des automatischen Zurücksetzens des Kennworts verwendet, so dass der Benutzer Zugriff auf das E-Mail-Konto benötigt, und zwar unabhängig davon, ob er auf Azure zugreifen kann oder nicht.

5. Wählen Sie **Zulassen** oder **Blockieren**, um anzugeben, ob der Benutzer sich anmelden kann und Zugriff auf die Dienste bekommt.

6. Geben Sie einen Speicherort aus der Dropdown-Liste **Nutzungsspeicherort** an.

7. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

## Nächste Schritte

- Weitere Informationen zum Ändern von Administratoren für ein Azure-Abonnement finden Sie unter [Hinzufügen oder Ändern von Azure-Administratorrollen](../billing-add-change-azure-subscription-administrator.md)

- Informationen dazu, wie der Zugriff auf Ressourcen in Microsoft Azure gesteuert wird, finden Sie unter [Grundlegendes zum Zugriff auf Ressourcen in Azure](active-directory-understanding-resource-access.md)

- Weitere Informationen zur Beziehung zwischen Azure Active Directory und Ihrem Azure-Abonnement finden Sie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Verwalten von Benutzern](active-directory-create-users.md)

- [Verwalten von Kennwörtern](active-directory-manage-passwords.md)

- [Gruppen verwalten](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0706_2016-->