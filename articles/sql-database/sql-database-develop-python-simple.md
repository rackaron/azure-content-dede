<properties
	pageTitle="Herstellen von Verbindungen mit SQL-Datenbank mithilfe von Python | Microsoft Azure"
	description="Zeigt ein Python-Codebeispiel zum Herstellen einer Verbindung mit Azure SQL-Datenbank."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="meetb"/>


# Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Python


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


In diesem Thema wird gezeigt, wie Sie mithilfe von Python eine Verbindung mit einer Azure SQL-Datenbank herstellen und Abfragen ausführen. Dieses Beispiel kann auf Windows-, Ubuntu Linux- oder Mac-Plattformen ausgeführt werden.


## Schritt 1: Konfigurieren der Entwicklungsumgebung

[Voraussetzungen für die Verwendung des pymssql Python-Treibers für SQL Server](https://msdn.microsoft.com/library/mt694094.aspx)

## Schritt 2: Erstellen einer SQL-Datenbank

Auf der [Seite für erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank. Sie sollten unbedingt die Anleitung zum Erstellen einer **AdventureWorks-Datenbankvorlage** befolgen. Die unten gezeigten Beispiele funktionieren nur mit dem **AdventureWorks-Schema**.

## Schritt 3: Abrufen der Verbindungsdetails

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Schritt 4: Ausführen von Beispielcode

[Proof of Concept connecting to SQL using Python](http://msdn.microsoft.com/library/mt715796.aspx) (Machbarkeitsstudie für das Herstellen einer Verbindung mit SQL mithilfe von Python)

## Nächste Schritte

* Lesen Sie die [Übersicht über die Entwicklung von SQL-Datenbanken](sql-database-develop-overview.md)
* Weitere Informationen zum [Microsoft Python-Treiber für SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Besuchen Sie das [Python Developer Center](/develop/python/).

## Zusätzliche Ressourcen 

* [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und Azure SQL-Datenbank](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Entdecken Sie alle [Funktionen von SQL-Datenbank](https://azure.microsoft.com/services/sql-database/).

<!---HONumber=AcomDC_0622_2016-->