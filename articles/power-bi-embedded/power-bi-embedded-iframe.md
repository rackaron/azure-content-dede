<properties
   pageTitle="Microsoft Power BI Embedded – Einbetten eines Power BI-Berichts mit einem IFrame"
   description="Microsoft Power BI Embedded – Grundlegender Code zum Einbetten eines Berichts in Ihre App, Authentifizieren mit Power BI Embedded-App-Token, Abrufen von Berichten"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/19/2016"
   ms.author="owend"/>

# Einbetten eines Power BI-Berichts mit einem IFrame
Dieser Artikel enthält grundlegenden Code, um einen Bericht mithilfe der REST-API **Power BI Embedded**, App-Token, einem IFrame und JavaScript-Code in Ihre App zu integrieren (einzubetten).

Unter [Erste Schritte mit Microsoft Power BI Embedded](power-bi-embedded-get-started.md) erfahren Sie, wie Sie eine Arbeitsbereichssammlung mit mindestens einem Arbeitsbereich für Ihren Berichtsinhalt konfigurieren. Unter [Erste Schritte mit dem Beispiel zu Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md) importieren Sie dann einen Bericht in einen Arbeitsbereich.

In diesem Artikel werden die Schritte zum Einbetten eines Berichts in Ihre App beschrieben. Laden Sie für diesen Artikel am besten das Beispiel [Integrate a report with an IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) (Integrieren eines Berichts mit einem IFrame) von GitHub herunter. Bei diesem Beispiel handelt es sich um eine einfache ASP.NET-Webformular-App zur Veranschaulichung von grundlegendem C#- und JavaScript-Code für die Berichtsintegration. Ein etwas komplexeres Beispiel, bei dem der Bericht mithilfe des Model-View-Controller (MVC)-Entwurfsmusters integriert wird, finden Sie auf GitHub unter [Sample dashboard web app](http://go.microsoft.com/fwlink/?LinkId=761493) (Beispiel für eine Dashboard-Web-App).

Dies sind die Schritte zum Integrieren eines Berichts:

- Schritt 1: [Abrufen eines Berichts in einem Arbeitsbereich](#GetReport). In diesem Schritt rufen Sie mithilfe eines App-Tokenflusses ein Zugriffstoken ab, um den REST-Vorgang [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) (Berichte abrufen) aufzurufen. Nach dem Abrufen eines Berichts aus der Liste **Get Reports** (Berichte abrufen) betten Sie ihn mithilfe eines Elements vom Typ **IFrame** in eine App ein.
- Schritt 2: [Einbetten eines Berichts in eine App](#EmbedReport). In diesem Schritt verwenden Sie ein Einbettungstoken für einen Bericht, JavaScript-Code und einen IFrame, um einen Bericht in eine Web-App zu integrieren (einzubetten).

Wenn Sie das Beispiel ausführen möchten, laden Sie das Beispiel [Integrate a report with an IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) (Integrieren eines Berichts mit einem IFrame) von GitHub herunter und konfigurieren drei Web.Config-Einstellungen:

- **AccessKey**: Ein Element vom Typ **AccessKey** wird verwendet, um ein JSON Web Token (JWT) zum Abrufen von Berichten und zum Einbetten eines Berichts zu generieren.
- **Name der Arbeitsbereichsammlung**: Identifiziert den Arbeitsbereich.
- **Arbeitsbereich-ID**: Eine eindeutige ID für den Arbeitsbereich.

Informationen dazu, wie Sie einen Zugriffsschlüssel, den Namen einer Arbeitsbereichsammlung und die Arbeitsbereich-ID über das Azure-Portal abrufen, finden Sie unter [Erste Schritte mit Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

<a name="GetReport"/>
## Abrufen eines Berichts in einem Arbeitsbereich

Wenn Sie einen Bericht in eine App integrieren möchten, benötigen Sie eine Berichts-ID (**ID**) und eine Einbettungs-URL (**embedUrl**). Zum Abrufen rufen Sie den REST-Vorgang [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) (Berichte abrufen) auf und wählen in der JSON-Liste einen Bericht aus.

### JSON-Antwort für „Get Reports“ (Berichte abrufen)
```
{
  "@odata.context":"https://api.powerbi.com/v1.0/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

Der REST-Vorgang [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) (Berichte abrufen) wird mithilfe eines App-Tokens aufgerufen. Weitere Informationen zum App-Tokenfluss finden Sie unter [Authentifizieren und Autorisieren mit Power BI Embedded](power-bi-embedded-app-token-flow.md). Der folgende Code dient zum Abrufen einer JSON-Liste mit Berichten.

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/v1.0/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppKey {0}", accessKey));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}

```

<a name="EmbedReport"/>
## Einbetten eines Berichts in eine App

Damit Sie einen Bericht in Ihre App einbetten können, benötigen Sie ein Einbettungskoken für einen Bericht. Dieses Token ähnelt einem App-Token zum Aufrufen von REST-Vorgängen des Typs Power BI Embedded, wird aber nicht für eine REST-Ressource, sondern für eine Berichtsressource generiert. Im Anschluss finden Sie den Code zum Abrufen eines App-Tokens für einen Bericht.

<a name="EmbedReportToken"/>
### Abrufen eines App-Tokens für einen Bericht

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### Einbetten des Berichts in Ihre App

Um einen Bericht vom Typ **Power BI** in Ihre App einzubetten, verwenden Sie einen IFrame und JavaScript-Code. Im Anschluss finden Sie einen Beispiel-IFrame und JavaScript-Code zum Einbetten eines Berichts. Den gesamten Beispielcode zum Einbetten eines Berichts finden Sie auf GitHub unter [Integrate a report with an IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) (Integrieren eines Berichts mit einem IFrame).

![IFrame](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
Nachdem Sie einen Bericht in Ihre App eingebettet haben, können Sie den Bericht filtern. Im nächsten Abschnitt erfahren Sie, wie Sie einen Bericht mit einer URL-Syntax filtern.

## Filtern eines Berichts

Sie können einen eingebetteten Bericht mit einer URL-Syntax filtern. Fügen Sie hierfür der iFrame-SRC-URL einen Abfragezeichenfolgenparameter mit angegebenem Filter hinzu. Sie können **nach einem Wert filtern** und den **Filterbereich ausblenden**.


**Filtern nach einem Wert**

Verwenden Sie zum Filtern nach einem Wert eine **$filter**-Abfragesyntax mit einem **eq**-Operator:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

Sie können beispielsweise nach der Ladenkette „Lindseys“ filtern. Der Filterteil der URL sieht in dem Fall wie folgt aus:

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {tableName/fieldName} darf keine Leerzeichen oder Sonderzeichen enthalten. Für {fieldValue} wird ein einzelner Kategoriewert akzeptiert.

**Ausblenden des Filterbereichs**

Wenn Sie den **Filterbereich** ausblenden möchten, fügen Sie der Berichtabfragezeichenfolge **filterPaneEnabled** hinzu:

```
&filterPaneEnabled=false
```

## Zusätzliche Ressourcen

In diesem Artikel haben Sie den Code kennengelernt, mit dem Sie einen **Power BI**-Bericht in Ihre App integrieren können. Sehen Sie sich auch diese weiteren Beispiele auf GitHub an:

- [Beispiel zum Integrieren eines Berichts mit einem IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [Beispiel für eine Dashboard-Web-App](http://go.microsoft.com/fwlink/?LinkId=761493)

## Siehe auch
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)

<!---HONumber=AcomDC_0720_2016-->