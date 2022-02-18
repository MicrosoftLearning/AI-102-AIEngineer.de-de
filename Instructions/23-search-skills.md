---
lab:
  title: Erstellen eines benutzerdefinierten Skills für Azure Cognitive Search
  module: Module 12 - Creating a Knowledge Mining Solution
ms.openlocfilehash: e09dbbc4fb72ae51e911f1440fd29d4917e50a6a
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2022
ms.locfileid: "135801367"
---
# <a name="create-a-custom-skill-for-azure-cognitive-search"></a>Erstellen eines benutzerdefinierten Skills für Azure Cognitive Search

Azure Cognitive Search verwendet eine Anreicherungspipeline kognitiver Skills, um KI-generierte Felder aus Dokumenten zu extrahieren und in einen Suchindex einzufügen. Es gibt einen umfassenden Satz integrierter Skills, die Sie verwenden können, aber wenn Sie eine bestimmte Anforderung haben, die von diesen Skills nicht erfüllt wird, können Sie einen benutzerdefinierten Skill erstellen.

In dieser Übung erstellen Sie einen benutzerdefinierten Skill, der die Häufigkeit einzelner Wörter in einem Dokument tabelliert, um eine Liste der fünf am häufigsten verwendeten Wörter zu generieren und einer Suchlösung für Margie‘s Travel – einem fiktiven Reisebüro – hinzuzufügen.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** bereits in die Umgebung geklont haben, in der Sie an dieser Übung arbeiten, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es jetzt zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

> **Hinweis**: Wenn Sie die Übung zum **[Erstellen einer Azure Cognitive Search-Lösung](22-azure-search.md)** bereits abgeschlossen haben und diese Azure-Ressourcen noch in Ihrem Abonnement verfügbar sind, können Sie diesen Abschnitt überspringen und mit dem Abschnitt **Erstellen einer Suchlösung** beginnen. Führen Sie andernfalls die folgenden Schritte aus, um die erforderlichen Azure-Ressourcen bereitzustellen.

1. Öffnen Sie in einem Webbrowser das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Zeigen Sie die **Ressourcengruppen** in Ihrem Abonnement an.
3. Wenn Sie ein eingeschränktes Abonnement verwenden, in dem eine Ressourcengruppe für Sie bereitgestellt wurde, wählen Sie die Ressourcengruppe aus, um deren Eigenschaften anzuzeigen. Erstellen Sie andernfalls eine neue Ressourcengruppe mit einem Namen Ihrer Wahl, und wechseln Sie zu dieser, nachdem sie erstellt wurde.
4. Notieren Sie sich auf der Seite mit der **Übersicht** (Overview) zu Ihrer Ressourcengruppe die Abonnement-ID (**Subscription ID**) und den Standort (**Location**) an. Sie benötigen diese Werte zusammen mit dem Namen der Ressourcengruppe in den nachfolgenden Schritten.
5. Erweitern Sie in Visual Studio Code den Ordner **23-custom-search-skill**, und wählen Sie **setup.cmd** aus. Sie verwenden dieses Batchskript, um die Befehle der Azure-Befehlszeilenschnittstellen (CLI) auszuführen, die zum Erstellen der benötigten Azure-Ressourcen erforderlich sind.
6. Klicken Sie mit der rechten Maustaste auf den Ordner **23-custom-search-skill**, und wählen Sie die Option **Open in Integrated Terminal** (In integriertem Terminal öffnen) aus.
7. Geben Sie im Terminalfenster den folgenden Befehl ein, um eine authentifizierte Verbindung mit Ihrem Azure-Abonnement herzustellen.

    ```
    az login --output none
    ```

8. Melden Sie sich bei Ihrem Azure-Abonnement an, wenn Sie dazu aufgefordert werden. Kehren Sie dann zu Visual Studio Code zurück, und warten Sie, bis der Anmeldevorgang abgeschlossen ist.
9. Führen Sie den folgenden Befehl aus, um Azure-Speicherorte auflisten.

    ```
    az account list-locations -o table
    ```

10. Suchen Sie in der Ausgabe nach dem Wert **Name**, der dem Standort Ihrer Ressourcengruppe entspricht (für *East US* ist der entsprechende Name beispielsweise *eastus).*
11. Ändern Sie im Skript **setup.cmd** die Deklarationen der Variablen **subscription_id**, **resource_group** und **location** mit den entsprechenden Werten für Ihre Abonnement-ID, den Namen der Ressourcengruppe und den Standortnamen. Speichern Sie anschließend die Änderungen.
12. Geben Sie im Terminal für den Ordner **23-custom-search-skill** den folgenden Befehl ein, um das Skript auszuführen:

    ```
    setup
    ```

    > **Hinweis**: Das Search CLI-Modul befindet sich in der Vorschau und bleibt möglicherweise bei *- Running ..* hängen. kann ich den Rsync-Vorgang starten. Wenn dies länger als zwei Minuten der Fall ist, drücken Sie STRG+C, um den zeitintensiven Vorgang abzubrechen, und wählen Sie dann **N** aus, wenn Sie gefragt werden, ob Sie das Skript beenden möchten. Es sollte dann erfolgreich abgeschlossen werden.
    >
    > Wenn das Skript fehlschlägt, stellen Sie sicher, dass Sie es mit den richtigen Variablennamen gespeichert haben, und versuchen Sie es erneut.

13. Wenn das Skript abgeschlossen ist, überprüfen Sie die angezeigte Ausgabe, und notieren Sie sich die folgenden Informationen zu Ihren Azure-Ressourcen (diese Werte werden Sie später benötigen):
    - Speicherkontoname
    - Speicherverbindungszeichenfolge
    - Konto für Cognitive Services
    - Schlüssel für Cognitive Services
    - Suchdienstendpunkt
    - Administratorschlüssel für Suchdienst
    - Abfrageschlüssel für Suchdienst

14. Aktualisieren Sie im Azure-Portal die Ressourcengruppe, und überprüfen Sie, ob sie das Azure Storage-Konto, die Azure Cognitive Services-Ressource und die Azure Cognitive Search-Ressource enthält.

## <a name="create-a-search-solution"></a>Erstellen einer Suchlösung

Nachdem Sie nun über die erforderlichen Azure-Ressourcen verfügen, können Sie eine Suchlösung erstellen, die aus den folgenden Komponenten besteht:

- Eine **Datenquelle**, die auf die Dokumente in Ihrem Azure-Speichercontainer verweist.
- Ein **Skillset**, das eine Anreicherungspipeline von Qualifikationen definiert, um KI-generierte Felder aus den Dokumenten zu extrahieren.
- Ein **Index**, der einen durchsuchbaren Satz von Dokumentdatensätzen definiert.
- Ein **Indexer**, der die Dokumente aus der Datenquelle extrahiert, das Skillset anwendet und den Index auffüllt.

In dieser Übung verwenden Sie die REST-Schnittstelle von Azure Cognitive Search, um diese Komponenten durch Übermitteln von JSON-Anforderungen zu erstellen.

1. Erweitern Sie in Visual Studio Code im Ordner **23-custom-search-skill** den Ordner **create-search**, und wählen Sie **data_source.json** aus. Diese Datei enthält eine JSON-Definition für eine Datenquelle namens **margies-custom-data**.
2. Ersetzen Sie den Platzhalter **YOUR_CONNECTION_STRING** durch die Verbindungszeichenfolge für Ihr Azure-Speicherkonto, die etwa wie folgt aussehen sollte:

    ```
    DefaultEndpointsProtocol=https;AccountName=ai102str123;AccountKey=12345abcdefg...==;EndpointSuffix=core.windows.net
    ```

    *Sie finden die Verbindungszeichenfolge auf der Seite mit den **Zugriffsschlüsseln** für Ihr Speicherkonto im Azure-Portal.*

3. Speichern und schließen Sie die aktualisierte JSON-Datei.
4. Öffnen Sie im Ordner **create-search** die Datei **skillset.json**. Diese Datei enthält eine JSON-Definition für ein Skillset namens **margies-custom-skillset**.
5. Ersetzen Sie oben in der Skillsetdefinition im **cognitiveServices**-Element den Platzhalter **YOUR_COGNITIVE_SERVICES_KEY** durch einen der Schlüssel für Ihre Cognitive Services-Ressourcen.

    *Sie finden diese Schlüssel auf der Seite **Keys and Endpoint** für Ihre Cognitive Services-Ressource im Azure-Portal.*

6. Speichern und schließen Sie die aktualisierte JSON-Datei.
7. Öffnen Sie im Ordner **create-search** die Datei **index.json**. Diese Datei enthält eine JSON-Definition für einen Index namens **margies-custom-index**.
8. Überprüfen Sie den JSON-Code für den Index, und schließen Sie die Datei, ohne Änderungen vorzunehmen.
9. Öffnen Sie im Ordner **create-search** die Datei **indexer.json**. Diese Datei enthält eine JSON-Definition für einen Indexer namens **margies-custom-indexer**.
10. Überprüfen Sie den JSON-Code für den Indexer, und schließen Sie die Datei, ohne Änderungen vorzunehmen.
11. Öffnen Sie im Ordner **create-search** die Datei **create-search.cmd**. Dieses Batchskript verwendet das Hilfsprogramm cURL, um die JSON-Definitionen an die REST-Schnittstelle für Ihre Azure Cognitive Search-Ressource zu übermitteln.
12. Ersetzen Sie die Platzhalter für die Variablen **YOUR_SEARCH_URL** und **YOUR_ADMIN_KEY** durch die **URL** und einen der **Administratorschlüssel** für Ihre Azure Cognitive Search-Ressource.

    *Sie finden diese Werte auf der Seite mit der **Übersicht** (Overview) und der Seite mit den **Schlüsseln** (Keys) für Ihre Azure Cognitive Search-Ressource im Azure-Portal.*

13. Speichern Sie die aktualisierte Batchdatei.
14. Klicken Sie mit der rechten Maustaste auf den Ordner **create-search**, und wählen Sie die Option **Open in Integrated Terminal** (In integriertem Terminal öffnen) aus.
15. Geben Sie im Terminalbereich für den Ordner **create-search** den folgenden Befehl ein, um das Batchskript auszuführen.

    ```
    create-search
    ```

16. Wählen Sie nach Abschluss des Skripts im Azure-Portal auf der Seite für Ihre Azure Cognitive Search-Ressource die Seite **Indexer** aus, und warten Sie, bis der Indizierungsprozess abgeschlossen ist.

    *Sie können **Aktualisieren** auswählen, um den Fortschritt des Indizierungsvorgang zu verfolgen. Es kann eine Minute dauern, bis der Vorgang abgeschlossen ist.*

## <a name="search-the-index"></a>Durchsuchen des Index

Nachdem Sie nun über einen Index verfügen, können Sie ihn durchsuchen.

1. Wählen Sie oben auf dem Blatt für Ihre Azure Cognitive Search-Ressource die Option **Search explorer** (Suchexplorer) aus.
2. Geben Sie im Suchexplorer im Feld **Abfragezeichenfolge** die folgende Zeichenfolge ein, und wählen Sie dann **Suchen** aus.

    ```
    search=London&$select=url,sentiment,keyphrases&$filter=metadata_author eq 'Reviewer' and sentiment eq 'positive'
    ```

    Diese Abfrage ruft die Elemente **url**, **sentiment** und **keyphrases** für alle Dokumente ab, in denen *London* erwähnt wird, die von *Reviewer* erstellt wurden und deren **sentiment**-Bezeichnung positiv ist (also positive Rezensionen, die London erwähnen).

## <a name="create-an-azure-function-for-a-custom-skill"></a>Erstellen einer Azure-Funktion für einen benutzerdefinierten Skill

Die Suchlösung enthält eine Reihe integrierter kognitiver Skills, die den Index mit Informationen aus den Dokumenten anreichern, z. B. die Stimmungsbewertungen und Listen von Schlüsselbegriffen aus der vorherigen Aufgabe.

Sie können den Index weiter verbessern, indem Sie benutzerdefinierte Skills erstellen. Beispielsweise kann es hilfreich sein, die Wörter, die in jedem Dokument am häufigsten verwendet werden, zu identifizieren, aber es gibt keinen integrierten Skill, der diese Funktionalität bietet.

Um die Wortzählungsfunktion als benutzerdefinierten Skill zu implementieren, erstellen Sie eine Azure-Funktion in Ihrer bevorzugten Sprache.

1. Zeigen Sie in Visual Studio Code die Registerkarte Azure-Erweiterungen (**&boxplus;**) an, und überprüfen Sie, ob die Erweiterung **Azure Functions** installiert ist. Mit dieser Erweiterung können Sie Azure-Funktionen in Visual Studio Code erstellen und bereitstellen.
2. Erstellen Sie auf der Registerkarte „Azure“ ( **&Delta;** ) im Bereich **Azure Functions** ein neues Azure-Projekt (&#128194;) mit den folgenden Einstellungen, je nach Ihrer bevorzugten Sprache:

    ### <a name="c"></a>**C#**

    - **Ordner**: Navigieren Sie zu **23-custom-search-skill/C-Sharp/wordcount**.
    - **Programmiersprache:** C#
    - **Vorlage:** HTTP-Trigger
    - **Funktionsname:** wordcount
    - **Namespace:** margies.search
    - **Autorisierungsstufe:** Funktion

    ### <a name="python"></a>**Python**

    - **Ordner**: Navigieren Sie zu **23-custom-search-skill/Python/wordcount**.
    - **Programmiersprache:** Python
    - **Virtuelle Umgebung:** Überspringen Sie die virtuelle Umgebung.
    - **Vorlage:** HTTP-Trigger
    - **Funktionsname:** wordcount
    - **Autorisierungsstufe:** Funktion

    *Überschreiben Sie **launch.json**, wenn Sie dazu aufgefordert werden.*

3. Wechseln Sie zurück zur Registerkarte **Explorer** ( **&#128461;** ), und vergewissern Sie sich, dass der Ordner **wordcount** nun die Codedateien für Ihre Azure-Funktion enthält.

    *Wenn Sie Python ausgewählt haben, befinden sich die Codedateien möglicherweise in einem Unterordner, der ebenfalls **wordcount** heißt.*

4. Die Hauptcodedatei für Ihre Funktion sollte automatisch geöffnet worden sein. Falls nicht, öffnen Sie die entsprechende Datei für die gewählte Sprache:
    - **C#** : wordcount.cs
    - **Python**: \_\_init\_\_&#46;py

5. Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code für Ihre ausgewählte Sprache:

### <a name="c"></a>**C#**

```C#
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Newtonsoft.Json;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;
using System.Text.RegularExpressions;
using System.Linq;

namespace margies.search
{
    public static class wordcount
    {

        //define classes for responses
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }

        //function for custom skill
        [FunctionName("wordcount")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req, ILogger log)
        {
            log.LogInformation("Function initiated.");

            string recordId = null;
            string originalText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                return new BadRequestObjectResult("Could not find valid records in values array");
            }

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            foreach (var record in data?.values)
            {
                recordId = record.recordId?.Value as string;
                originalText = record.data?.text?.Value as string;

                if (recordId == null)
                {
                    return new BadRequestObjectResult("recordId cannot be null");
                }

                // Put together response.
                WebApiResponseRecord responseRecord = new WebApiResponseRecord();
                responseRecord.data = new Dictionary<string, object>();
                responseRecord.recordId = recordId;
                responseRecord.data.Add("text", Count(originalText));

                response.values.Add(responseRecord);
            }

            return (ActionResult)new OkObjectResult(response); 
        }


            public static string RemoveHtmlTags(string html)
        {
            string htmlRemoved = Regex.Replace(html, @"<script[^>]*>[\s\S]*?</script>|<[^>]+>| ", " ").Trim();
            string normalised = Regex.Replace(htmlRemoved, @"\s{2,}", " ");
            return normalised;
        }

        public static List<string> Count(string text)
        {
            
            //remove html elements
            text=text.ToLowerInvariant();
            string html = RemoveHtmlTags(text);
            
            //split into list of words
            List<string> list = html.Split(" ").ToList();
            
            //remove any non alphabet characters
            var onlyAlphabetRegEx = new Regex(@"^[A-z]+$");
            list = list.Where(f => onlyAlphabetRegEx.IsMatch(f)).ToList();

            //remove stop words
            string[] stopwords = { "", "i", "me", "my", "myself", "we", "our", "ours", "ourselves", "you", 
                    "you're", "you've", "you'll", "you'd", "your", "yours", "yourself", 
                    "yourselves", "he", "him", "his", "himself", "she", "she's", "her", 
                    "hers", "herself", "it", "it's", "its", "itself", "they", "them", 
                    "their", "theirs", "themselves", "what", "which", "who", "whom", 
                    "this", "that", "that'll", "these", "those", "am", "is", "are", "was",
                    "were", "be", "been", "being", "have", "has", "had", "having", "do", 
                    "does", "did", "doing", "a", "an", "the", "and", "but", "if", "or", 
                    "because", "as", "until", "while", "of", "at", "by", "for", "with", 
                    "about", "against", "between", "into", "through", "during", "before", 
                    "after", "above", "below", "to", "from", "up", "down", "in", "out", 
                    "on", "off", "over", "under", "again", "further", "then", "once", "here", 
                    "there", "when", "where", "why", "how", "all", "any", "both", "each", 
                    "few", "more", "most", "other", "some", "such", "no", "nor", "not", 
                    "only", "own", "same", "so", "than", "too", "very", "s", "t", "can", 
                    "will", "just", "don", "don't", "should", "should've", "now", "d", "ll",
                    "m", "o", "re", "ve", "y", "ain", "aren", "aren't", "couldn", "couldn't", 
                    "didn", "didn't", "doesn", "doesn't", "hadn", "hadn't", "hasn", "hasn't", 
                    "haven", "haven't", "isn", "isn't", "ma", "mightn", "mightn't", "mustn", 
                    "mustn't", "needn", "needn't", "shan", "shan't", "shouldn", "shouldn't", "wasn", 
                    "wasn't", "weren", "weren't", "won", "won't", "wouldn", "wouldn't"}; 
            list = list.Where(x => x.Length > 2).Where(x => !stopwords.Contains(x)).ToList();
            
            //get distict words by key and count, and then order by count.
            var keywords = list.GroupBy(x => x).OrderByDescending(x => x.Count());
            var klist = keywords.ToList();

            // return the top 10 words
            var numofWords = 10;
            if(klist.Count<10)
                numofWords=klist.Count;
            List<string> resList = new List<string>();
            for (int i = 0; i < numofWords; i++)
            {
                resList.Add(klist[i].Key);
            }
            return resList;
        }
    }
}
```

## <a name="python"></a>**Python**

```Python
import logging
import os
import sys
import json
from string import punctuation
from collections import Counter
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Wordcount function initiated.')

    # The result will be a "values" bag
    result = {
        "values": []
    }
    statuscode = 200

    # We're going to exclude words from this list in the word counts
    stopwords = ['', 'i', 'me', 'my', 'myself', 'we', 'our', 'ours', 'ourselves', 'you', 
                "you're", "you've", "you'll", "you'd", 'your', 'yours', 'yourself', 
                'yourselves', 'he', 'him', 'his', 'himself', 'she', "she's", 'her', 
                'hers', 'herself', 'it', "it's", 'its', 'itself', 'they', 'them', 
                'their', 'theirs', 'themselves', 'what', 'which', 'who', 'whom', 
                'this', 'that', "that'll", 'these', 'those', 'am', 'is', 'are', 'was',
                'were', 'be', 'been', 'being', 'have', 'has', 'had', 'having', 'do', 
                'does', 'did', 'doing', 'a', 'an', 'the', 'and', 'but', 'if', 'or', 
                'because', 'as', 'until', 'while', 'of', 'at', 'by', 'for', 'with', 
                'about', 'against', 'between', 'into', 'through', 'during', 'before', 
                'after', 'above', 'below', 'to', 'from', 'up', 'down', 'in', 'out', 
                'on', 'off', 'over', 'under', 'again', 'further', 'then', 'once', 'here', 
                'there', 'when', 'where', 'why', 'how', 'all', 'any', 'both', 'each', 
                'few', 'more', 'most', 'other', 'some', 'such', 'no', 'nor', 'not', 
                'only', 'own', 'same', 'so', 'than', 'too', 'very', 's', 't', 'can', 
                'will', 'just', 'don', "don't", 'should', "should've", 'now', 'd', 'll',
                'm', 'o', 're', 've', 'y', 'ain', 'aren', "aren't", 'couldn', "couldn't", 
                'didn', "didn't", 'doesn', "doesn't", 'hadn', "hadn't", 'hasn', "hasn't", 
                'haven', "haven't", 'isn', "isn't", 'ma', 'mightn', "mightn't", 'mustn', 
                "mustn't", 'needn', "needn't", 'shan', "shan't", 'shouldn', "shouldn't", 'wasn', 
                "wasn't", 'weren', "weren't", 'won', "won't", 'wouldn', "wouldn't"]

    try:
        values = req.get_json().get('values')
        logging.info(values)

        for rec in values:
            # Construct the basic JSON response for this record
            val = {
                    "recordId": rec['recordId'],
                    "data": {
                        "text":None
                    },
                    "errors": None,
                    "warnings": None
                }
            try:
                # get the text to be processed from the input record
                txt = rec['data']['text']
                # remove numeric digits
                txt = ''.join(c for c in txt if not c.isdigit())
                # remove punctuation and make lower case
                txt = ''.join(c for c in txt if c not in punctuation).lower()
                # remove stopwords
                txt = ' '.join(w for w in txt.split() if w not in stopwords)
                # Count the words and get the most common 10
                wordcount = Counter(txt.split()).most_common(10)
                words = [w[0] for w in wordcount]
                # Add the top 10 words to the output for this text record
                val["data"]["text"] = words
            except:
                # An error occured for this text record, so add lists of errors and warning
                val["errors"] =[{"message": "An error occurred processing the text."}]
                val["warnings"] = [{"message": "One or more inputs failed to process."}]
            finally:
                # Add the value for this record to the response
                result["values"].append(val)
    except Exception as ex:
        statuscode = 500
        # A global error occurred, so return an error response
        val = {
                "recordId": None,
                "data": {
                    "text":None
                },
                "errors": [{"message": ex.args}],
                "warnings": [{"message": "The request failed to process."}]
            }
        result["values"].append(val)
    finally:
        # Return the response
        return func.HttpResponse(body=json.dumps(result), mimetype="application/json", status_code=statuscode)
```
    
6. Speichern Sie die aktualisierte Datei.
7. Klicken Sie mit der rechten Maustaste auf den Ordner **wordcount**, der Ihre Codedateien enthält, und wählen Sie **Deploy to Function App** (In Funktions-App bereitstellen) aus. Stellen Sie die Funktion dann mit den folgenden sprachspezifischen Einstellungen bereit (melden Sie sich bei Azure an, wenn Sie dazu aufgefordert werden):

    ### <a name="c"></a>**C#**

    - **Abonnement** (bei Aufforderung): Wählen Sie Ihr Azure-Abonnement aus.
    - **Funktion:** Erstellen Sie eine neue Funktions-App in Azure (Erweitert).
    - **Name der Funktions-App:** Geben Sie einen global eindeutigen Namen ein.
    - **Runtime:** .NET Core 3.1
    - **Betriebssystem:** Linux
    - **Hostingplan**: Verbrauch
    - **Ressourcengruppe**: Die Ressourcengruppe, die Ihre Azure Cognitive Search-Ressource enthält.
        - Hinweis: Wenn diese Ressourcengruppe bereits eine Windows-basierte Web-App enthält, können Sie dort keine Linux-basierte Funktion bereitstellen. Löschen Sie entweder die vorhandene Web-App, oder stellen Sie die Funktion in einer anderen Ressourcengruppe bereit.
    - **Speicherkonto**: Die Speicheranzahl, in der die Reisedokumente von Margie gespeichert werden.
    - **Application Insights:** Überspringen Sie diese Einstellung zunächst.

    *Visual Studio Code stellt die kompilierte Version der Funktion (im Unterordner **bin**) basierend auf den Konfigurationseinstellungen im Ordner **.vscode** bereit, die beim Erstellen des Funktionsprojekts gespeichert wurden.*

    ### <a name="python"></a>**Python**

    - **Abonnement** (bei Aufforderung): Wählen Sie Ihr Azure-Abonnement aus.
    - **Funktion:** Erstellen Sie eine neue Funktions-App in Azure (Erweitert).
    - **Name der Funktions-App:** Geben Sie einen global eindeutigen Namen ein.
    - **Runtime**: Python 3.8
    - **Hostingplan**: Verbrauch
    - **Ressourcengruppe**: Die Ressourcengruppe, die Ihre Azure Cognitive Search-Ressource enthält.
        - Hinweis: Wenn diese Ressourcengruppe bereits eine Windows-basierte Web-App enthält, können Sie dort keine Linux-basierte Funktion bereitstellen. Löschen Sie entweder die vorhandene Web-App, oder stellen Sie die Funktion in einer anderen Ressourcengruppe bereit.
    - **Speicherkonto**: Die Speicheranzahl, in der die Reisedokumente von Margie gespeichert werden.
    - **Application Insights:** Überspringen Sie diese Einstellung zunächst.

8. Warten Sie, bis Visual Studio Code die Funktion bereitgestellt hat. Nach Abschluss der Bereitstellung wird eine Benachrichtigung angezeigt.

## <a name="test-the-function"></a>Testen der Funktion

Nachdem Sie die Funktion nun in Azure bereitgestellt haben, können Sie sie im Azure-Portal testen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zu der Ressourcengruppe, in der Sie die Funktions-App erstellt haben. Öffnen Sie dann den App-Dienst für Ihre Funktions-App.
2. Öffnen Sie auf dem Blatt für Ihren App-Dienst auf der Seite **Funktionen** die **wordcount**-Funktion.
3. Zeigen Sie auf dem Blatt der **wordcount**-Funktion die Seite **Programmieren und testen** an, und öffnen Sie den Bereich **Testen/Ausführen**.
4. Ersetzen Sie im Bereich **Testen/Ausführen** den vorhandenen **Text** durch den folgenden JSON-Code. Dieser entspricht dem Schema, das ein Azure Cognitive Search-Skill erwartet, mit dem Datensätze mit Daten für mindestens ein Dokument zur Verarbeitung übermittelt werden:

    ```
    {
        "values": [
            {
                "recordId": "a1",
                "data":
                {
                "text":  "Tiger, tiger burning bright in the darkness of the night.",
                "language": "en"
                }
            },
            {
                "recordId": "a2",
                "data":
                {
                "text":  "The rain in spain stays mainly in the plains! That's where you'll find the rain!",
                "language": "en"
                }
            }
        ]
    }
    ```
    
5. Klicken Sie auf **Ausführen**, und sehen Sie sich den Inhalt der von der Funktion zurückgegebenen HTTP-Antwort an. Er entspricht dem Schema, das Azure Cognitive Search erwartet, wenn ein Skill angewandt wird, mit dem eine Antwort für jedes Dokument zurückgegeben wird. In diesem Fall besteht die Antwort aus maximal 10 Begriffen in jedem Dokument in absteigender Reihenfolge ihrer Häufigkeit:

    ```
    {
    "values": [
        {
        "recordId": "a1",
        "data": {
            "text": [
            "tiger",
            "burning",
            "bright",
            "darkness",
            "night"
            ]
        },
        "errors": null,
        "warnings": null
        },
        {
        "recordId": "a2",
        "data": {
            "text": [
            "rain",
            "spain",
            "stays",
            "mainly",
            "plains",
            "thats",
            "youll",
            "find"
            ]
        },
        "errors": null,
        "warnings": null
        }
    ]
    }
    ```

6. Schließen Sie den Bereich **Testen/Ausführen**, und klicken Sie auf dem Blatt der **wordcount**-Funktion auf **Funktions-URL abrufen**. Kopieren Sie dann die URL für den Standardschlüssel in die Zwischenablage. Sie wird in der nächsten Prozedur benötigt.

## <a name="add-the-custom-skill-to-the-search-solution"></a>Hinzufügen des benutzerdefinierten Skills zur Suchlösung

Nun müssen Sie Ihre Funktion als benutzerdefinierten Skill in das Skillset der Suchlösung einfügen und die erzeugten Ergebnisse einem Feld im Index zuordnen. 

1. Öffnen Sie die Datei **update-skillset.json** im Ordner **23-custom-search-skill/update-search** in Visual Studio Code. Sie enthält die JSON-Definition eines Skillsets.
2. Überprüfen Sie die Skillsetdefinition. Sie enthält dieselben Skills wie zuvor und dazu noch einen neuen **WebApiSkill**-Skill namens **get-top-words**.
3. Bearbeiten Sie die Definition des Skills **get-top-words**, um den **uri**-Wert auf die URL für Ihre Azure-Funktion festzulegen (die Sie im vorherigen Verfahren in die Zwischenablage kopiert haben), und ersetzen Sie damit **YOUR-FUNCTION-APP-URL**.
4. Ersetzen Sie oben in der Skillsetdefinition im **cognitiveServices**-Element den Platzhalter **YOUR_COGNITIVE_SERVICES_KEY** durch einen der Schlüssel für Ihre Cognitive Services-Ressourcen.

    *Sie finden diese Schlüssel auf der Seite **Keys and Endpoint** für Ihre Cognitive Services-Ressource im Azure-Portal.*

5. Speichern und schließen Sie die aktualisierte JSON-Datei.
6. Öffnen Sie die Datei **update-index.json** im Ordner **update-search**. Diese Datei enthält die JSON-Definition für den Index **margies-custom-index** mit einem zusätzlichen Feld namens **top_words** am unteren Rand der Indexdefinition.
7. Überprüfen Sie den JSON-Code für den Index, und schließen Sie die Datei, ohne Änderungen vorzunehmen.
8. Öffnen Sie die Datei **update-indexer.json** im Ordner **update-search**. Diese Datei enthält eine JSON-Definition für **margies-custom-indexer** mit einer zusätzlichen Zuordnung für das Feld **top_words**.
9. Überprüfen Sie den JSON-Code für den Indexer, und schließen Sie die Datei, ohne Änderungen vorzunehmen.
10. Öffnen Sie die Datei **update-search.cmd** im Ordner **update-search**. Dieses Batchskript verwendet das Hilfsprogramm cURL, um die aktualisierten JSON-Definitionen an die REST-Schnittstelle für Ihre Azure Cognitive Search Ressource zu übermitteln.
11. Ersetzen Sie die Platzhalter für die Variablen **YOUR_SEARCH_URL** und **YOUR_ADMIN_KEY** durch die **URL** und einen der **Administratorschlüssel** für Ihre Azure Cognitive Search Ressource.

    *Sie finden diese Werte auf der Seite mit der **Übersicht** (Overview) und der Seite mit den **Schlüsseln** (Keys) für Ihre Azure Cognitive Search-Ressource im Azure-Portal.*

12. Speichern Sie die aktualisierte Batchdatei.
13. Klicken Sie mit der rechten Maustaste auf den Ordner **update-search**, und wählen Sie die Option **Open in Integrated Terminal** (In integriertem Terminal öffnen) aus.
14. Geben Sie im Terminalbereich für den Ordner **update-search** den folgenden Befehl ein, um das Batchskript auszuführen.

    ```
    update-search
    ```

15. Wählen Sie nach Abschluss des Skripts im Azure-Portal auf der Seite für Ihre Azure Cognitive Search-Ressource die Seite **Indexer** aus, und warten Sie, bis der Indizierungsprozess abgeschlossen ist.

    *Sie können **Aktualisieren** auswählen, um den Fortschritt des Indizierungsvorgang zu verfolgen. Es kann eine Minute dauern, bis der Vorgang abgeschlossen ist.*

## <a name="search-the-index"></a>Durchsuchen des Index

Nachdem Sie nun über einen Index verfügen, können Sie ihn durchsuchen.

1. Wählen Sie oben auf dem Blatt für Ihre Azure Cognitive Search-Ressource die Option **Search explorer** (Suchexplorer) aus.
2. Geben Sie im Suchexplorer im Feld **Query string** (Abfragezeichenfolge) die folgende Zeichenfolge ein, und wählen Sie dann **Search** (Suchen) aus.

    ```
    search=Las Vegas&$select=url,top_words
    ```

    Diese Abfrage ruft die Felder **url** und **top_words** für alle Dokumente ab, in denen *Las Vegas* erwähnt wird.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zum Erstellen benutzerdefinierter Skills für Azure Cognitive Search finden Sie in der [Azure Cognitive Search-Dokumentation](https://docs.microsoft.com/azure/search/cognitive-search-custom-skill-interface).
