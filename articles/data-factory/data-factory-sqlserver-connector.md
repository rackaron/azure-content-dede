<properties
	pageTitle="Verschieben von Daten in und aus SQL Server | Azure Data Factory"
	description="Informationen zum Verschieben von Daten in und aus einer SQL Server-Datenbank, die lokal oder mithilfe von Azure Data Factory in einer Azure-VM gehostet wird."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="spelluru"/>

# Verschieben von Daten in und aus SQL Server in einer lokalen oder IaaS-Umgebung (Azure-VM) mithilfe von Azure Data Factory

Dieser Artikel beschreibt die Verwendung der Kopieraktivität in einer Azure Data Factory, um Daten aus einem anderen Datenspeicher in SQL Server und aus SQL Server in einen anderen Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

## Herstellen der Verbindung

Die Konzepte und Schritte zum Herstellen einer Verbindung mit SQL Server, ob lokal oder in Azure-IaaS-VMs (Infrastructure-as-a-Service) gehostet, sind identisch. In beiden Fällen müssen Sie das Datenverwaltungsgateway zum Herstellen der Verbindung nutzen.

Im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erfahren mehr zum Datenverwaltungsgateway und erhalten eine schrittweise Anleitung zum Einrichten des Gateways. Das Einrichten einer Gatewayinstanz ist eine Voraussetzung für die Verbindung mit SQL Server.

Wenngleich Sie das Gateway auf demselben lokalen Computer bzw. in derselben Cloud-VM-Instanz wie SQL Server installieren können, wird aus Leistungsgründen empfohlen, diese Komponenten auf getrennten Computern oder in getrennten Cloud-VMs zu installieren, um Ressourcenkonflikte zu vermeiden.

In den folgenden Beispielen wird veranschaulicht, wie Sie Daten in und aus SQL Server und Azure-BLOB-Speicher kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** aus beliebigen Quellen in die [hier](data-factory-data-movement-activities.md#supported-data-stores) aufgeführten Senken kopiert werden.

## Beispiel: Kopieren von Daten aus SQL Server in Azure-Blob

Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) und [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) verwendet

Im Beispiel werden Daten, die zu einer Zeitreihe gehören, stündlich aus einer Tabelle in einer SQL Server-Datenbank in ein Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als ersten Schritt richten Sie das Datenverwaltungsgateway gemäß den Anweisungen im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) ein.

**Mit SQL Server verknüpfter Dienst**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Mit Azure-Blobspeicher verknüpfter Dienst**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**SQL Server-Eingabedataset**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in SQL Server erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält. Beachten Sie, dass Sie mehrere Tabellen in derselben Datenbank mit einem Dataset abfragen können, aber eine einzelne Tabelle muss für „tableName“ von „typeProperty“ für das Dataset verwendet werden.

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird dem Azure Data Factory-Dienst angegeben, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	  "name": "SqlServerInput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **SqlSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **SqlReaderQuery**-Eigenschaft angegebene SQL-Abfrage wählt die Daten der letzten Stunde zum Kopieren aus.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "SqlServertoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " SqlServerInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}


Im obigen Beispiel ist **sqlReaderQuery** für SqlSource angegeben. Mit der Kopieraktivität wird diese Abfrage in der SQL Server-Datenbankquelle ausgeführt, um die Daten abzurufen. Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden). Beachten Sie, dass sqlReaderQuery auf mehrere Tabellen innerhalb der Datenbank verweisen kann, die durch das Eingabedataset referenziert wird. sqlReaderQuery ist nicht auf den Tabellensatz beschränkt, der über tableName typeProperty für das Dataset festgelegt ist.


Wenn Sie sqlReaderQuery oder sqlReaderStoredProcedureName nicht angeben, werden die im Strukturabschnitt des Dataset-JSON-Bereichs definierten Spalten verwendet, um eine Abfrage (select column1, column2 from mytable) zur Ausführung für die SQL Server-Datenbank zu erstellen. Falls die DataSet-Definition nicht über die Struktur verfügt, werden alle Spalten der Tabelle ausgewählt.


Eine Liste mit den Eigenschaften, die von SqlSource und BlobSink unterstützt werden, finden Sie im Abschnitt [Sql Source](#sqlsource) und unter [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

## Beispiel: Kopieren von Daten aus einem Azure-Blob in SQL Server

Das nachstehende Beispiel zeigt Folgendes:

1.	Den verknüpften Dienst des Typs [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties)
2.	Den verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit einer Kopieraktivität, die [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) und [SqlSink](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) verwendet

Im Beispiel werden Daten, die zu einer Zeitreihe aus einem Azure-Blob gehören, stündlich in eine Tabelle in einer SQL Server-Datenbank kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit SQL Server verknüpfter Dienst**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Mit Azure-Blobspeicher verknüpfter Dienst**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure-Blob-Eingabedataset**

Daten werden stündlich aus einem neuen Blob übernommen ("frequency": "hour", "interval": 1). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Der Ordnerpfad verwendet die Bestandteile Jahr, Monat und Tag der Startzeit, und der Dateiname verwendet die Stunde der Startzeit. Die Festlegung von "external" auf "true" teilt dem Data Factory-Dienst mit, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**SQL Server-Ausgabedataset**

Das Beispiel kopiert Daten in eine Tabelle namens "MyTable" in SQL Server. Sie sollten die Tabelle in SQL Server mit der gleichen Anzahl von Spalten erstellen, die Sie in der CSV-Datei im Blob erwarten. Neue Zeilen werden der Tabelle stündlich hinzugefügt.

	{
	  "name": "SqlServerOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **BlobSource** und der Typ **sink** auf **SqlSink** festgelegt.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": " SqlServerOutput "
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

## Eigenschaften des mit SQL Server verknüpften Diensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit SQL Server verknüpften Dienst spezifisch sind.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| Typ | Die "type"-Eigenschaft muss auf **OnPremisesSqlServer** festgelegt sein. | Ja |
| connectionString | Geben Sie "connectionString"-Informationen an, die zum Herstellen einer Verbindung mit der lokalen SQL Server-Datenbank mithilfe der SQL Server- oder Windows-Authentifizierung benötigt werden. | Ja |
| gatewayName | Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen SQL Server-Datenbank verwenden soll. | Ja |
| username | Geben Sie den Benutzernamen an, wenn Sie die Windows-Authentifizierung verwenden. Beispiel: **Domänenname\\Benutzername**. | Nein |
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. | Nein |

Sie können Anmeldeinformationen mithilfe des **New-AzureRmDataFactoryEncryptValue**-Cmdlets verschlüsseln und wie im folgenden Beispiel gezeigt in der Verbindungszeichenfolge verwenden (**EncryptedCredential**-Eigenschaft):

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

### Beispiele

**JSON für die SQL Server-Authentifizierung**

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
			"typeProperties": {
	        	"connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
	        	"gatewayName": "<gateway name>"
			}
	    }
	}

**JSON für die Windows-Authentifizierung**

Wenn Benutzername und Kennwort angegeben werden, werden diese Informationen vom Gateway genutzt, um die Identität des angegebenen Benutzerkontos zu übernehmen und eine Verbindung mit der lokalen SQL Server-Datenbank herzustellen. Anderenfalls verbindet sich das Gateway mit SQL Server direkt im Sicherheitskontext des Gateways (mit dessen Startkonto).

	{
	     "Name": " MyOnPremisesSQLDB",
	     "Properties":
	     {
	         "type": "OnPremisesSqlLinkedService",
			 "typeProperties": {
	         	"ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
	         	"username": "<domain\\username>",
	         	"password": "<password>",
	         	"gatewayName": "<gateway name>"
			}
	     }
	}

Ausführliche Informationen zum Festlegen von Anmeldeinformationen für eine SQL Server-Datenquelle finden Sie unter [Festlegen von Anmeldeinformationen und Sicherheit](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

## Eigenschaften des Dataset-Typs „SQL Server“

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (SQL Server, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt **typeProperties** für ein Dataset des Typs **SqlServerTable** hat die folgenden Eigenschaften.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| tableName | Name der Tabelle in der SQL Server-Datenbankinstanz, auf die der verknüpfte Dienst verweist. | Ja |

## Eigenschaften des SQL Server-Kopieraktivitätstyps

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

> [AZURE.NOTE] Die Kopieraktivität verwendet nur eine Eingabe und erzeugt nur eine Ausgabe.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

### SqlSource

Wenn bei der Kopieraktivität „source“ den Typ **SqlSource** hat, sind im Abschnitt **typeProperties** die folgenden Eigenschaften verfügbar:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. | SQL-Abfragezeichenfolge. Beispiel: "select * from MyTable". Kann auf mehrere Tabellen aus der Datenbank verweisen, auf die vom Eingabedataset verwiesen wird. Falls nicht angegeben, wird folgende SQL-Anweisung ausgeführt: "select from MyTable". | Nein |
| sqlReaderStoredProcedureName | Der Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. | Name der gespeicherten Prozedur. | Nein |
| storedProcedureParameters | Parameter für die gespeicherte Prozedur. | Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. | Nein |

Wenn **sqlReaderQuery** für SqlSource angegeben ist, führt die Kopieraktivität diese Abfrage in der SQL Server-Datenbankquelle aus, um die Daten abzurufen.

Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden).

Wenn Sie sqlReaderQuery oder sqlReaderStoredProcedureName nicht angeben, werden die im Strukturabschnitt des Dataset-JSON-Bereichs definierten Spalten verwendet, um eine Abfrage (select column1, column2 from mytable) zur Ausführung für die SQL Server-Datenbank zu erstellen. Falls die DataSet-Definition nicht über die Struktur verfügt, werden alle Spalten der Tabelle ausgewählt.

> [AZURE.NOTE] Bei Verwendung von **sqlReaderStoredProcedureName** müssen Sie trotzdem einen Wert für die **tableName**-Eigenschaft in der Dataset-JSON angeben. Dies ist zurzeit noch eine Produkteinschränkung. Es finden jedoch keine Überprüfungen dieser Tabelle statt.

### SqlSink

**SqlSink** unterstützt die folgenden Eigenschaften:


| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. | Zeitraum<br/><br/> Beispiel: 00:30:00 (30 Minuten) | Nein |
| writeBatchSize | Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht. | Integer (Gesamtanzahl von Zeilen) | Nein (Standard = 10000)
| sqlWriterCleanupScript | Benutzerdefinierte Abfrage, die Kopieraktivität so auszuführen, dass Daten von einem bestimmten Slice bereinigt werden. Im Abschnitt zur Wiederholbarkeit unten erfahren Sie weitere Einzelheiten. | Eine Abfrageanweisung. | Nein |
| sliceIdentifierColumnName | Benutzerdefinierter Spaltenname, den die Kopieraktivität mit einem automatisch generierten Slicebezeichner füllen soll, der bei erneuter Ausführung zum Bereinigen von Daten eines bestimmten Slices verwendet wird. Im Abschnitt zur Wiederholbarkeit unten erfahren Sie weitere Einzelheiten. | Spaltenname einer Spalte mit binärem Datentyp (32). | Nein |
| sqlWriterStoredProcedureName | Name der gespeicherten Prozedur, die Daten in die Zieltabelle mit dem Upsert-Vorgang einfügt oder aktualisiert. | Name der gespeicherten Prozedur. | Nein |
| storedProcedureParameters | Parameter für die gespeicherte Prozedur. | Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. | Nein |
| sqlWriterTableType | Benutzerdefinierter Tabellentypname, der in der obigen gespeicherten Prozedur verwendet werden soll. Die Kopieraktivität macht die verschobenen Daten in einer temporären Tabelle mit diesem Tabellentyp verfügbar. Der gespeicherte Prozedurcode kann dann die kopierten Daten mit vorhandenen Daten zusammenführen. | Ein Tabellentypname. | Nein |

## Beheben von Verbindungsproblemen

1. Konfigurieren Sie Ihren SQL-Server, um Remoteverbindungen zu akzeptieren. Starten Sie **SQL Server Management Studio**, klicken Sie mit der rechten Maustaste auf **Server**, und klicken Sie dann auf **Eigenschaften**. Wählen Sie in der Liste den Eintrag **Verbindungen** aus, und aktivieren Sie das Kontrollkästchen **Remoteverbindungen mit diesem Server zulassen**.

	![Aktivieren von Remoteverbindungen](.\media\data-factory-sqlserver-connector\AllowRemoteConnections.png)

	Ausführliche Schritte finden Sie im Abschnitt zum [Konfigurieren der Serverkonfigurationsoption für den Remotezugriff](https://msdn.microsoft.com/library/ms191464.aspx).
2. Starten Sie den **SQL Server-Konfigurations-Manager**. Erweitern Sie **SQL Server-Netzwerkkonfiguration** für die gewünschte Instanz, und wählen Sie **Protokolle für MSSQLSERVER** aus. Im rechten Bereich werden Protokolle angezeigt. Aktivieren Sie TCP/IP, indem Sie mit der rechten Maustaste auf **TCP/IP** klicken und anschließend auf **Aktivieren** klicken.

	![TCP/IP aktivieren](.\media\data-factory-sqlserver-connector\EnableTCPProptocol.png)

	Weitere Methoden zum Aktivieren des TCP/IP-Protokolls finden Sie unter [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](https://msdn.microsoft.com/library/ms191294.aspx).
3. Doppelklicken Sie im gleichen Fenster auf **TCP/IP**, um das Fenster **TCP/IP-Eigenschaften** zu öffnen.
4. Wechseln Sie zur Registerkarte **IP-Adressen**. Scrollen Sie nach unten zum Abschnitt **IPAll**. Notieren Sie sich den **TCP-Port** (Standardwert: **1433**).
5. Erstellen Sie auf dem Computer eine **Regel für die Windows-Firewall**, um eingehenden Datenverkehr über diesen Port zuzulassen.
6. **Verbindung überprüfen:** Verwenden Sie SQL Server Management Studio auf einem anderen Computer, um mit dem vollqualifizierten Namen eine Verbindung mit der SQL Server-Instanz herzustellen. Beispiel: <Computer>.<Domäne>.corp.<Unternehmen>.com,1433.

	> [AZURE.IMPORTANT]
	Ausführliche Informationen finden Sie unter [Ports und Sicherheitsaspekte](data-factory-move-data-between-onprem-and-cloud.md#port-and-security-considerations).
	>   
	> Unter [Problembehandlung bei Gateways](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) finden Sie Tipps zur Behandlung von Verbindungs- bzw. Gatewayproblemen.

## Identitätsspalten in der Zieldatenbank
Dieser Abschnitt enthält ein Beispiel zum Kopieren von Daten aus einer Quelltabelle ohne eine Identitätsspalte in eine Zieltabelle mit einer Identitätsspalte.

**Quelltabelle:**

	create table dbo.SourceTbl
	(
	       name varchar(100),
	       age int
	)

**Zieltabelle:**

	create table dbo.TargetTbl
	(
	       id int identity(1,1),
	       name varchar(100),
	       age int
	)


Beachten Sie, dass die Zieltabelle über eine Identitätsspalte verfügt.

**Definition der Quell-Dataset-JSON**

	{
	    "name": "SampleSource",
	    "properties": {
	        "published": false,
	        "type": " SqlServerTable",
	        "linkedServiceName": "TestIdentitySQL",
	        "typeProperties": {
	            "tableName": "SourceTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}

**Definition der Ziel-Dataset-JSON**

	{
	    "name": "SampleTarget",
	    "properties": {
	        "structure": [
	            { "name": "name" },
	            { "name": "age" }
	        ],
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "TestIdentitySQLSource",
	        "typeProperties": {
	            "tableName": "TargetTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": false,
	        "policy": {}
	    }
	}


Beachten Sie, dass die Quell- und die Zieltabelle unterschiedliche Schemas besitzen (das Ziel verfügt über eine zusätzliche Spalte mit der Identität). In diesem Szenario müssen Sie eine **structure**-Eigenschaft in der Definition des Zieldatasets angeben, die nicht die Identitätsspalte enthält.

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]


[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]


[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]


### Typzuordnung für SQL Server und Azure SQL

Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten in und aus Azure SQL, SQL Server und Sybase werden die folgenden Zuordnungen zwischen SQL-Typ und .NET-Typ und umgekehrt verwendet.

Die Zuordnung ist mit der SQL Server-Datentypzuordnung für ADO.NET identisch.

| Typ "SQL Server-Datenbankmodul" | Typ ".NET Framework" |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte |
| Bit | Boolean |
| char | String, Char |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM-Attribut (varbinary(max)) | Byte |
| Float | Doppelt |
| image | Byte |
| int | Int32 |
| money | Decimal |
| nchar | String, Char |
| ntext | String, Char |
| numeric | Decimal |
| nvarchar | String, Char |
| real | Single |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal |
| sql\_variant | Object * |
| Text | String, Char |
| in | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | GUID |
| varbinary | Byte |
| varchar | String, Char |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Leistung und Optimierung  
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.

<!---HONumber=AcomDC_0713_2016-->