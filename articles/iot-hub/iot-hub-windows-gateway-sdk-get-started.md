<properties
	pageTitle="Erste Schritte mit dem IoT Hub Gateway SDK | Microsoft Azure"
	description="Exemplarische Vorgehensweise für das Azure IoT Hub Gateway SDK unter Verwendung von Windows zum Veranschaulichen der grundlegenden Konzepte, die Sie beim Verwenden des Azure IoT Hub Gateway SDK kennen sollten."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/20/2016"
     ms.author="cstreet"/>


# IoT-Gateway SDK (Beta) – erste Schritte mit Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## Erstellen des Beispiels

Bevor Sie beginnen, müssen Sie die [Entwicklungsumgebung einrichten][lnk-setupdevbox], um mit dem SDK unter Windows arbeiten zu können.

1. Öffnen Sie eine **Developer-Eingabeaufforderung für VS2015**.
2. Navigieren Sie in Ihrer lokalen Kopie des Repositorys **azure-iot-gateway-sdk** zum Stammordner.
3. Führen Sie das Skript **tools\\build.cmd** aus. Dieses Skript erstellt eine Visual Studio-Projektmappendatei, erstellt die Projektmappe und führt die Tests aus. Sie finden die Visual Studio-Projektmappe im **build**-Ordner Ihrer lokalen Kopie des **azure-iot-gateway-sdk**-Repositorys.

## Ausführen des Beispiels

1. Das **build.cmd**-Skript erstellt einen Ordner namens **build** in Ihrer lokalen Kopie des Repositorys. Dieser Ordner enthält die beiden in diesem Beispiel verwendeten Module.

    Das Buildskript platziert **logger\_hl.dll** im Ordner **build\\modules\\logger\\Debug** und **hello\_world\_hl.dl** im Ordner **build\\modules\\hello\_world\\Debug**. Verwenden Sie diese Pfade für den Wert **module path**, wie in der JSON-Einstellungsdatei unten gezeigt.

2. Die Datei **hello\_world\_win.json** im Ordner **samples\\hello\_world\\src** ist eine JSON-Beispieleinstellungsdatei für Windows, die Sie zur Ausführung des Beispiels verwenden können. Bei den unten gezeigten beispielhaften JSON-Einstellungen wird angenommen, dass Sie das Gateway SDK-Repository in den Stammordner Ihres Laufwerks **C:** geklont haben. Wenn Sie das Repository in einen anderen Speicherort heruntergeladen haben, müssen Sie die **module path**-Werte in der Datei **samples\\hello\_world\\src\\hello\_world\_win.json** entsprechend anpassen.

3. Legen Sie im Modul **logger\_hl** im Abschnitt **args** den Wert **filename** auf den Namen und Pfad der Datei fest, in der die Protokolldaten gespeichert werden.

    Dies ist ein Beispiel für eine JSON-Einstellungsdatei für Windows, die in die Datei **log.txt** im Stammordner Ihres Laufwerks **C:** schreibt.

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
          "args" : null
        }
      ]
    }
    ```

3. Navigieren Sie an einer Eingabeaufforderung zum Stammordner in Ihrer lokalen Kopie des **azure-iot-gateway-sdk**-Repositorys.
4. Führen Sie den folgenden Befehl aus:
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

<!---HONumber=AcomDC_0504_2016-->