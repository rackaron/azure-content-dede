<properties 
	pageTitle="Einführung in DocumentDB, eine JSON-Datenbanken | Microsoft Azure" 
	description="Informationen zu Azure DocumentDB, eine JSON-Datenbank. Dieses Dokumentendatenbank ist für große Datenmengen, flexible Skalierbarkeit und hohe Verfügbarkeit konzipiert." 
	keywords="JSON-Datenbank, Dokumentendatenbank"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="07/01/2016" 
	ms.author="mimig"/>

# Einführung in DocumentDB: JSON-NoSQL-Datenbanken

Bei Azure DocumentDB handelt es sich um einen vollständig verwalteten NoSQL-Datenbankdienst, der für hohe und vorhersagbare Leistung, hohe Verfügbarkeit, automatische Skalierung, globale Verteilung und einfache Bereitstellung entworfen wurde. Dank seines flexiblen Datenmodells, der durchgängig niedrigen Latenz und der umfangreichen Abfragefunktionen eignet sich der Dienst hervorragend für webbasierte und mobile Anwendungen, Spiele und IoT-Anwendungen sowie zahlreiche andere Anwendungen, für die eine nahtlose Skalierung erforderlich ist.

Eine schnelle Möglichkeit, mehr über die JSON-Datenbank zu erfahren und die Datenbank in Aktion zu sehen, bieten die drei folgenden Schritte:

1. Sehen Sie sich das zweiminütige Video[Was ist DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/)an, das die Vorteile von DocumentDB präsentiert.
2. Sehen Sie sich das dreiminütige Video [Erstellen einer DocumentDB in Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) an, das die ersten Schritte mit DocumentDB im Azure-Portal veranschaulicht.
3. Besuchen Sie den [Query Playground](http://www.documentdb.com/sql/demo), in dem Sie die verschiedenen Aktivitäten durchlaufen können, um mehr über die umfassende Abfragefunktionalität in DocumentDB zu erfahren. Wechseln Sie dann zur Registerkarte "Sandbox", um eigene benutzerdefinierten SQL-Abfragen auszuführen und mit DocumentDB zu experimentieren.

Kehren Sie dann zurück zu diesem Artikel, in dem Dinge vertieft werden und Sie die Antworten auf die folgenden Fragen erhalten:

-	[Was ist DocumentDB und welchen Nutzen bietet der Dienst für moderne Anwendungen?](#what-is-azure-documentdb)
-	[Wie werden meine Daten in DocumentDB verwaltet, und wie kann ich auf sie zugreifen?](#data-management)
-	[Wie kann ich mithilfe von DocumentDB Anwendungen entwickeln?](#develop)
-	[Welche sind die nächsten Schritte zum Erstellen einer DocumentDB-Anwendung?](#next-steps)

## Was ist Azure DocumentDB?  

Moderne Anwendungen produzieren, verwenden und reagieren schnell auf sehr große Datenmengen. Diese Anwendungen entwickeln sich rasant – ebenso wie das ihnen zugrunde liegende Datenschema. Um dem zu begegnen, gehen Entwickler zunehmend dazu über, schemafreie NoSQL-Dokumentdatenbanken als einfache, schnelle und skalierbare Lösungen für die Speicherung und Verarbeitung von Daten einzusetzen. Dabei können Anwendungsdatenmodelle und unstrukturierte Datenfeeds weiterhin schnell durchlaufen werden. Viele schemafreie Datenbanken ermöglichen jedoch keine komplexen Abfragen und Transaktionsverarbeitung und erschweren so eine moderne Datenverwaltung. Hier kommt DocumentDB ins Spiel. Microsoft hat DocumentDB entwickelt, um diese Anforderungen beim Verwalten schemafreier Daten zu erfüllen.

DocumentDB ist ein wirklich schemaloser NoSQL-Datenbankdienst, der für moderne mobile und webbasierte Anwendungen, Spiele und IoT-Anwendungen entwickelt wurde. Mit DocumentDB wird sichergestellt, dass 99% Ihrer Lesevorgänge in einem Zeitraum von unter 10 Millisekunden und 99% Ihrer Schreibvorgänge in einem Zeitraum von unter 15 Millisekunden bereitgestellt werden. Außerdem ist für Schemaflexibilität und das bedarfsabhängige einfache zentrale und horizontale Hochskalieren einer Datenbank gesorgt. Es setzt kein Schema für die indizierten JSON-Dokumente voraus. Alle Dokumente in der Datenbank werden automatisch indiziert, ohne dass ein Schema oder die Erstellung sekundärer Indizes erwartet oder angefordert wird. DocumentDB ermöglicht mithilfe einer SQL-Sprache komplexe Ad-hoc-Abfragen, unterstützt wohl definierte Konsistenzebenen und bietet Unterstützung für JavaScript-Transaktionsverarbeitung mehrerer Dokumente mithilfe des vertrauten Programmiermodells gespeicherter Prozeduren, Trigger und benutzerdefinierter Funktionen (UDFs).

Als JSON-Datenbank unterstützt DocumentDB nativ JSON-Dokumente und ermöglicht dadurch eine einfache Iteration des Anwendungsschemas. Darüber hinaus werden Anwendungen unterstützt, für die ein Schlüssel-Wert- oder Dokumentmodell oder ein auf tabellarischen Daten basierendes Modell erforderlich ist. DocumentDB berücksichtigt die Verbreitung von JSON und JavaScript und beseitigt den Konflikt zwischen anwendungsdefinierten Objekten und Datenbankschema. Die enge Integration von JavaScript ermöglicht es den Entwicklern zudem, Anwendungslogik auf effiziente Weise direkt im Datenbankmodul innerhalb einer Datenbanktransaktion auszuführen.

Azure DocumentDB bietet die folgenden wesentlichen Funktionen und Vorteile:

-	**Flexibel skalierbarer Durchsatz und Speicher:** Die DocumentDB-JSON-Datenbank lässt sich problemlos zentral hoch- und herunterskalieren, um Ihren Anwendungsanforderungen gerecht zu werden. Ihre Daten werden für niedrige vorhersagbare Latenzen auf Solid-State-Datenträgern (SSDs) gespeichert. DocumentDB unterstützt Container zum Speichern von JSON-Daten, die als Sammlungen bezeichnet werden. Diese Container können auf nahezu unbegrenzte Speichergrößen und Mengen an bereitgestelltem Durchsatz skaliert werden. Sie können DocumentDB mit vorhersagbarer Leistung flexibel skalieren, wenn Ihre Anwendung wächst.

-	**Replikation in mehreren Regionen:** DocumentDB repliziert Ihre Daten auf transparente Weise in allen Regionen, die Sie Ihrem DocumentDB-Konto zugeordnet haben. Sie können Anwendungen entwickeln, die globalen Zugriff auf Daten benötigen, und dabei für die Balance von Einheitlichkeit, Verfügbarkeit und Leistung sorgen – jeweils mit den entsprechenden Garantien. DocumentDB bietet transparentes regionales Failover mit Multihosting-APIs sowie die Fähigkeit, Durchsatz und Speicher für alle Konten weltweit elastisch zu skalieren. Weitere Informationen finden Sie unter [Globale Verteilung von Daten mit DocumentDB](documentdb-distribute-data-globally.md).

-	**Ad-Hoc-Abfragen mit vertrauter SQL-Syntax**: Heterogene JSON-Dokumente können in DocumentDB gespeichert und mithilfe einer vertrauten SQL-Syntax abgefragt werden. DocumentDB nutzt eine sperrfreie, protokollstrukturierte Indizierungstechnologie für die gleichzeitige Ausführung zahlreicher Vorgänge, um sämtliche Dokumentinhalte automatisch zu indizieren. Dies ermöglicht umfassende Echtzeitabfragen, ohne Schemahinweise, Sekundärindizes oder Sichten angeben zu müssen. Erfahren Sie mehr unter [Abfragen von DocumentDB](documentdb-sql-query.md).

-	**JavaScript-Ausführung innerhalb der Datenbank**: Anwendungslogik kann mithilfe von Standard-JavaScript als gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen ausgedrückt werden. Auf diese Weise kann die Anwendungslogik auf Daten angewendet werden, ohne dass Sie sich Gedanken über den Konflikt zwischen Anwendung und Datenbankschema machen müssen. DocumentDB bietet eine umfassende transaktionale Ausführung von JavaScript-Anwendungslogik direkt im Datenbankmodul. Die enge Integration von JavaScript ermöglicht es, Einfüge-, Ersetzungs-, Lösch- und Auswahlvorgänge über ein JavaScript-Programm als isolierte Transaktion auszuführen. Weitere Einzelheiten finden Sie unter [Serverseitige DocumentDB-Programmierung](documentdb-programming.md).

-	**Optimierbare Konsistenzebenen:** Die Konsistenz kann über vier wohl definierte Ebenen abgestimmt werden, um für ein ausgewogenes Verhältnis von Konsistenz und Leistung zu sorgen. Für Abfragen und Lesevorgänge bietet DocumentDB vier verschiedene Konsistenzebenen – „strong“, „bounded-staleness“, „session“ und „eventual“. Mit diesen granularen, gut abgegrenzten Konsistenzebenen können fundierte Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz geschlossen werden. Weitere Informationen finden Sie unter [Verwenden von Konsistenzebenen zum Maximieren der Verfügbarkeit und Leistung in DocumentDB](documentdb-consistency-levels.md).

-	**Vollständig verwaltet:** Sie müssen sich nicht mehr mit der Verwaltung von Datenbanken und Rechenressourcen befassen. Dank des vollständig verwalteten Microsoft Azure-Diensts müssen Sie sich nicht mit der Verwaltung virtueller Computer, der Bereitstellung und Konfiguration von Software, der Skalierung oder mit komplexen Datenebenenupgrades herumschlagen. Alle Datenbanken werden automatisch gesichert und vor regionalen Ausfällen geschützt. Sie können einfach DocumentDB-Konten hinzufügen und nach Bedarf Kapazitäten bereitstellen. Dies ermöglicht es Ihnen, sich auf Ihre Anwendung zu konzentrieren, ohne sich mit dem Betrieb und der Verwaltung der Datenbank aufhalten zu müssen.

-	**Offen konzipiert:** Mithilfe Ihrer vorhandenen Kenntnisse und Tools finden Sie ganz schnell den Einstieg. Die Programmierung mit DocumentDB ist einfach und verständlich und erfordert keine neuen Tools oder benutzerdefinierten Erweiterungen für JSON oder JavaScript. Sie können auf sämtliche Datenbankfunktionen – einschließlich CRUD-, Abfrage- und JavaScript-Verarbeitung – über eine einfache HTTP-basierte RESTful-Schnittstelle zugreifen. DocumentDB bezieht vorhandene Formate, Sprachen und Standards ein und bietet gleichzeitig wichtige, darauf aufbauende Datenbankfunktionen.

Sie können DocumentDB für die Speicherung flexibler Datasets verwenden, die den Abruf von Abfragen und Transaktionsverarbeitung erfordern. Anwendungsszenarien können Benutzerdaten für interaktive webbasierte und mobile Anwendungen und Spiele sowie Speicherung, Abruf und Verarbeitung von JSON-Daten, die von IoT-Geräten generiert werden, beinhalten. Eine Datenbank kann eine beliebige Menge von JSON-Dokumenten speichern, entsprechend ist DocumentDB bestens für Anwendungen geeignet, die webbasiert ausgeführt werden.

##<a name="data-management"></a>Azure DocumentDB-Ressourcen
Azure DocumentDB verwaltet Daten mithilfe wohl definierter Datenbankressourcen. Diese Ressourcen werden werden repliziert, um Hochverfügbarkeit zu bieten, und sind anhand ihres logischen URI eindeutig adressierbar. DocumentDB bietet ein einfaches HTTP-basiertes RESTful-Programmiermodell für alle Ressourcen.

Ein DocumentDB-Datenbankkonto ist ein eindeutiger Namespace, über den Sie Zugriff auf Azure DocumentDB erhalten. Um ein Datenbankkonto erstellen zu können, müssen Sie über ein Azure-Abonnement verfügen. Dadurch erhalten Sie Zugriff auf eine Vielzahl von Azure-Diensten.

Alle Ressourcen in DocumentDB werden als JSON-Dokumente modelliert und gespeichert. Ressourcen werden als Elemente verwaltet, bei denen es sich um JSON-Dokumente mit Metadaten handelt, und als Feeds, bei denen es sich um Auflistungen von Elementen handelt. Gruppen von Elementen sind in ihren jeweiligen Feeds enthalten.

Die folgende Abbildung veranschaulicht die Beziehung zwischen den DocumentDB-Ressourcen:

![Die hierarchische Beziehung zwischen Ressourcen in DocumentDB, einer JSON-NoSQL-Datenbank][1]

Ein Datenbankkonto besteht aus einer Reihe von Datenbanken, die jeweils mehrere Auflistungen umfassen, die wiederum jeweils gespeicherte Prozeduren, Trigger, UDFs, Dokumente und zugehörige Anhänge enthalten können. Einer Datenbank sind zudem Benutzer zugeordnet, die jeweils über eine Reihe von Berechtigungen für den Zugriff auf verschiedene andere Auflistungen, gespeicherte Prozeduren, Trigger, UDFs, Dokumente oder Anhänge verfügen. Während es sich bei Datenbanken, Benutzern, Berechtigungen und Auflistungen um systemdefinierte Ressourcen mit bekannten Schemas handelt, enthalten Dokumente, gespeicherte Prozeduren, Trigger, UDFs und Anhänge beliebige benutzerdefinierte JSON-Inhalte.

##<a name="develop"></a> Entwickeln mit Azure DocumentDB
DocumentDB stellt Ressourcen über eine REST-API zur Verfügung, die in jeder Sprache aufgerufen werden kann, die HTTP/HTTPS-Anfragen unterstützt. Zusätzlich bietet DocumentDB Programmierbibliotheken für einige gängige Sprachen. Diese Bibliotheken vereinfachen viele Aspekte der Arbeit mit Azure DocumentDB durch Verarbeitung von Details wie Zwischenspeicherung von Adressen, Ausnahmeverwaltung und automatischen Neuversuchen. Bibliotheken sind aktuell für die folgenden Sprachen und Plattformen erhältlich:

Herunterladen | Dokumentation
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [.NET-Bibliothek](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Node.js-Bibliothek](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Java-Bibliothek](http://azure.github.io/azure-documentdb-java/)
[JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) | [JavaScript-Bibliothek](http://azure.github.io/azure-documentdb-js/)
– | [Serverseitiges JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Python-Bibliothek](http://azure.github.io/azure-documentdb-python/)

Abgesehen von den grundlegenden Erstellungs-, Lese-, Aktualisierungs- und Löschvorgängen bietet Azure DocumentDB eine funktionsreiche SQL-Abfrageschnittstelle für den Abruf von JSON-Dokumenten und serverseitige Unterstützung für die transaktionale Ausführung von JavaScript-Anwendungslogik. Die Schnittstellen für die Abfrage- und Skriptausführung sind über alle Plattformbibliotheken verfügbar. Gleiches gilt für die REST-APIs.

### SQL-Abfrage
Azure DocumentDB unterstützt die Abfrage von Dokumenten mithilfe einer SQL-Sprache, die im JavaScript-Typsystem verankert ist, sowie mithilfe von Ausdrücken, die relationale, hierarchische und räumliche Abfragen unterstützen. Die DocumentDB-Abfragesprache ist eine einfache und dennoch effektive Schnittstelle für die Abfrage von JSON-Dokumenten. Die Sprache unterstützt einen Teil der ANSI SQL-Grammatik und sorgt für eine enge Integration von JavaScript-basierten Objekt-, Array-, Objektkonstruktions- und Funktionsaufrufen. DocumentDB stellt sein Abfragemodell ohne explizite Schema- oder Indizierungshinweise vom Entwickler bereit.

Benutzerdefinierte Funktionen (UDFs) können mit DocumentDB registriert und als Teil einer SQL-Abfrage referenziert werden, um so die Grammatik zu erweitern und die benutzerdefinierte Anwendungslogik zu unterstützen. Diese UDFs werden als JavaScript-Programme programmiert und in der Datenbank ausgeführt.

Für .NET-Entwickler bietet DocumentDB auch einen LINQ-Abfrageanbieter als Teil des [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx).

### Transaktionen und JavaScript-Ausführung
DocumentDB ermöglicht es Ihnen, Anwendungslogik als festgelegte Programme zu programmieren, die vollständig in JavaScript verfasst sind. Diese Programme werden für eine Auflistung registriert und unterstützen die Ausführung von Datenbankvorgängen in einer gegebenen Auflistung. JavaScript kann für die Ausführung als Trigger, gespeicherte Prozedur oder UDF registriert werden. Trigger und gespeicherte Prozeduren können Dokumente erstellen, lesen, aktualisieren und löschen. UDFs hingegen werden ohne Schreibzugriff auf die Auflistung als Teil der Abfrageausführung ausgeführt.

Die JavaScript-Ausführung in DocumentDB wurde anhand der gleichen Konzepte modelliert wie relationale Datenbanksysteme. JavaScript dient dabei als moderner Ersatz für Transact-SQL. Sämtliche JavaScript-Logik wird innerhalb einer umgebenden ACI-Transaktion mit Snapshotisolation ausgeführt. Während des Ausführungsverlaufs wird die gesamte Transaktion abgebrochen, wenn JavaScript eine Ausnahme auslöst.

## Nächste Schritte
Wenn Sie bereits ein Azure-Konto haben, können Sie die ersten Schritte mit DocumentDB im [Azure-Portal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) unternehmen, indem Sie [ein DocumentDB-Datenbankkonto erstellen](documentdb-create-account.md).

Wenn Sie nicht über ein Konto verfügen, können Sie:

- Sich für eine [kostenlose Testversion von Azure](https://azure.microsoft.com/free/) registrieren. Sie erhalten ein Guthaben von 200 US-Dollar für 30 Tage, in denen Sie alle Azure-Dienste ausprobieren können.
- Wenn Sie ein MSDN-Abonnement haben, sind Sie für ein [kostenloses Azure-Guthaben von 150 US-Dollar pro Monat](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) berechtigt, das Sie für beliebige Azure-Dienste nutzen können.

Wenn Sie mehr erfahren möchten, besuchen Sie unseren [Lernpfad](https://azure.microsoft.com/documentation/learning-paths/documentdb/), um alle Lernressourcen kennenzulernen, die Ihnen zur Verfügung stehen.


[1]: ./media/documentdb-introduction/json-database-resources1.png
 

<!---HONumber=AcomDC_0706_2016-->