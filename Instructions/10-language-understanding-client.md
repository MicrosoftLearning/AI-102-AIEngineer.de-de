---
lab:
  title: Erstellen einer Language Understanding-Clientanwendung
  module: Module 5 - Creating Language Understanding Solutions
ms.openlocfilehash: 36f75e47910707c959495140be43c4196649d471
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625764"
---
# <a name="create-a-language-understanding-client-application"></a>Erstellen einer Language Understanding-Clientanwendung

Mit dem Language Understanding-Dienst können Sie eine App definieren, die ein Sprachmodell kapselt, mit dem Anwendungen Benutzereingaben in natürlicher Sprache interpretieren, die *Absicht* des Benutzers vorhersagen (was sie erreichen möchten) und alle *Entitäten* identifizieren können, auf die die Absicht angewendet werden soll. Sie können Clientanwendungen erstellen, die Language Understanding-Apps direkt über REST-Schnittstellen oder mithilfe sprachspezifischer Software Development Kits (SDKs) nutzen.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** bereits in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es jetzt zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Jetzt nicht** aus.

## <a name="create-language-understanding-resources"></a>Erstellen von Language Understanding-Ressourcen

Wenn Sie in Ihrem Azure-Abonnement bereits über Language Understanding-Erstellungs- und -Vorhersageressourcen verfügen, können Sie diese in dieser Übung verwenden. Befolgen Sie andernfalls diese Anleitungen, um sie zu erstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Wählen Sie die Schaltfläche **&#65291;Ressource erstellen** aus, suchen Sie nach *Language Understanding*, und erstellen Sie eine **Language Understanding**-Ressource mit den folgenden Einstellungen:
    - **Option „Erstellen“**: Beides
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine Ressourcengruppe (wenn Sie eine gehostete Lab-Umgebung verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen, verwenden Sie dann die bereitgestellte Ressourcengruppe).*
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Speicherort für Dokumenterstellung**: *Wählen Sie Ihren bevorzugten Speicherort aus.*
    - **Tarif für die Erstellung**: F0
    - **Speicherort für Vorhersage**: *Wählen Sie den <u>gleichen Speicherort</u> wie für die Dokumenterstellung aus.*
    - **Tarif für die Vorhersage**: F0 (*Wenn F0 nicht verfügbar ist, wählen Sie S0 aus*.)

3. Warten Sie, bis die Ressourcen erstellt wurden, und beachten Sie, dass zwei Language Understanding-Ressourcen bereitgestellt werden, eine für die Dokumenterstellung und eine für die Vorhersage. Sie können beide anzeigen, indem Sie zu der Ressourcengruppe navigieren, in der Sie sie erstellt haben.

## <a name="import-train-and-publish-a-language-understanding-app"></a>Importieren, Trainieren und Veröffentlichen einer Language Understanding App

Wenn Sie bereits über eine **Clock**-App (Uhr) aus einer vorherigen Übung verfügen, können Sie sie in dieser Übung verwenden. Befolgen Sie andernfalls diese Anleitungen, um sie zu erstellen.

1. Öffnen Sie auf einer neuen Browserregisterkarte das Language Understanding-Portal unter `https://www.luis.ai`.
2. Melden Sie sich mit Ihrem Microsoft-Konto an, das mit Ihrem Azure-Abonnement verknüpft ist. Wenn Sie sich zum ersten Mal beim Language Understanding-Portal anmelden, müssen Sie der App möglicherweise einige Berechtigungen für den Zugriff auf Ihre Kontodaten erteilen. Führen Sie dann die Schritte unter *Welcome* (Willkommen) aus, indem Sie Ihr Abonnement und die Erstellungsressource auswählen, die Sie gerade erstellt haben.
3. Öffnen Sie die Seite **Konversations-Apps** neben **&#65291;Neue App**, zeigen Sie die Dropdownliste an, und wählen Sie **Als LU importieren** aus.
Navigieren Sie im Projektordner mit den Labdateien für diese Übung zum Unterordner **10-luis-client**, und wählen Sie **Clock.lu** aus. Geben Sie dann einen eindeutigen Namen für die Clock-App an.
4. Wenn ein Fenster mit Tipps zur Erstellung einer effektiven Language Understanding-App angezeigt wird, schließen Sie es.
5. Wählen Sie oben im Language Understanding-Portal **Trainieren** aus, um die App zu trainieren.
6. Wählen Sie oben rechts im Language Understanding-Portal **Veröffentlichen** aus, und veröffentlichen Sie die App im **Produktionsslot**.
7. Wählen Sie nach Abschluss der Veröffentlichung oben im Language Understanding-Portal **Verwalten** aus.
8. Notieren Sie die **App-ID** von der Seite **Einstellungen**. Clientanwendungen benötigen dies, um Ihre App zu verwenden.
9. Fügen Sie auf der Seite **Azure-Ressourcen** unter **Vorhersageressourcen** die Vorhersageressource in Ihrem Azure-Abonnement hinzu, wenn keine Vorhersageressource aufgeführt ist.
10. Notieren Sie den **Primärschlüssel**, den **Sekundärschlüssel** und die **Endpunkt-URL** für die Vorhersageressource. Clientanwendungen benötigen den Endpunkt und einen der Schlüssel, um eine Verbindung mit der Vorhersageressource herzustellen und authentifiziert zu werden.

## <a name="prepare-to-use-the-language-understanding-sdk"></a>Vorbereiten der Verwendung des Language Understanding SDK

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die die Language Understanding-Clock-App (Uhr) verwendet, um Absichten aus Benutzereingaben vorherzusagen und entsprechend zu reagieren.

> **Hinweis**: Sie können auswählen, das SDK für **C#** oder **Python** zu verwenden. Führen Sie in den folgenden Schritten die Aktionen aus, die für Ihre bevorzugte Sprache geeignet sind.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **10-luis-client**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **clock-client**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Language Understanding SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

*Zusätzlich zum **Laufzeit** paket (Vorhersage) gibt es ein **Erstellung** spaket, das Sie zum Schreiben von Code verwenden können, um Language Understanding-Modelle zu erstellen und zu verwalten.*

**Python**

```
pip install azure-cognitiveservices-language-luis==0.7.0
```
    
*Das Python SDK-Paket umfasst Klassen für sowohl **Vorhersage** als auch **Erstellung**.*

3. Zeigen Sie den Inhalt des Ordners **clock-client** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die Konfigurationswerte, die sie enthält, um die **APP-ID** für Ihre Language Understanding-App sowie die **Endpunkt-URL** und einen der **Schlüssel** für die Vorhersageressource (von der Seite **Verwalten** für Ihre App im Language Understanding-Portal) einzuschließen.

4. Beachten Sie, dass der Ordner **clock-client** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: clock-client.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Language Understanding Vorhersage-SDK verwenden zu können:

**C#**

```C#
// Import namespaces
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

**Python**

```Python
# Import namespaces
from azure.cognitiveservices.language.luis.runtime import LUISRuntimeClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="get-a-prediction-from-the-language-understanding-app"></a>Erhalten einer Vorhersage von der Language Understanding-App

Nun können Sie Code implementieren, der das SDK verwendet, um eine Vorhersage von Ihrer Language Understanding-App zu erhalten.

1. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden der App-ID, des Vorhersageendpunkts und des Schlüssels aus der Konfigurationsdatei bereitgestellt wurde. Suchen Sie dann den Kommentar **Create a client für the LU app** (Client für die LU-App erstellen), und fügen Sie den folgenden Code hinzu, um einen Vorhersageclient für Ihre Language Understanding-App zu erstellen:

**C#**

```C#
// Create a client for the LU app
var credentials = new Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.ApiKeyServiceClientCredentials(predictionKey);
var luClient = new LUISRuntimeClient(credentials) { Endpoint = predictionEndpoint };
```

**Python**

```Python
# Create a client for the LU app
credentials = CognitiveServicesCredentials(lu_prediction_key)
lu_client = LUISRuntimeClient(lu_prediction_endpoint, credentials)
```

2. Beachten Sie, dass der Code in der **Main**-Funktion zu Benutzereingaben auffordert, bis der Benutzer „quit“ eingibt. Suchen Sie in dieser Schleife nach dem Kommentar **Call the LU app to get intent and entities** (LU-App aufrufen, um Absicht und Entitäten zu erhalten), und fügen Sie den folgenden Code hinzu:

**C#**

```C#
// Call the LU app to get intent and entities
var slot = "Production";
var request = new PredictionRequest { Query = userText };
PredictionResponse predictionResponse = await luClient.Prediction.GetSlotPredictionAsync(luAppId, slot, request);
Console.WriteLine(JsonConvert.SerializeObject(predictionResponse, Formatting.Indented));
Console.WriteLine("--------------------\n");
Console.WriteLine(predictionResponse.Query);
var topIntent = predictionResponse.Prediction.TopIntent;
var entities = predictionResponse.Prediction.Entities;
```

**Python**

```Python
# Call the LU app to get intent and entities
request = { "query" : userText }
slot = 'Production'
prediction_response = lu_client.prediction.get_slot_prediction(lu_app_id, slot, request)
top_intent = prediction_response.prediction.top_intent
entities = prediction_response.prediction.entities
print('Top Intent: {}'.format(top_intent))
print('Entities: {}'.format (entities))
print('-----------------\n{}'.format(prediction_response.query))
```

Der Aufruf der Language Understanding-App gibt eine Vorhersage zurück, die die oberste (wahrscheinlichste) Absicht sowie alle Entitäten enthält, die in der Eingabeäußerung erkannt wurden. Ihre Clientanwendung muss diese Vorhersage jetzt verwenden, um die entsprechende Aktion zu bestimmen und durchzuführen.

3. Suchen Sie nach dem Kommentar **Apply the appropriate action** (Die entsprechende Aktion anwenden), und fügen Sie den folgenden Code hinzu, der nach Absichten sucht, die von der Anwendung unterstützt werden (**GetTime**, **GetDate** und **GetDay**), und bestimmt, ob relevante Entitäten erkannt wurden, bevor eine vorhandene Funktion zum Erzeugen einer entsprechenden Antwort aufgerufen wird.

**C#**

```C#
// Apply the appropriate action
switch (topIntent)
{
    case "GetTime":
        var location = "local";
        // Check for entities
        if (entities.Count > 0)
        {
            // Check for a location entity
            if (entities.ContainsKey("Location"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Location"].ToString());
                // ML entities are strings, get the first one
                location = entityJson[0].ToString();
            }
        }

        // Get the time for the specified location
        var getTimeTask = Task.Run(() => GetTime(location));
        string timeResponse = await getTimeTask;
        Console.WriteLine(timeResponse);
        break;

    case "GetDay":
        var date = DateTime.Today.ToShortDateString();
        // Check for entities
        if (entities.Count > 0)
        {
            // Check for a Date entity
            if (entities.ContainsKey("Date"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Date"].ToString());
                // Regex entities are strings, get the first one
                date = entityJson[0].ToString();
            }
        }
        // Get the day for the specified date
        var getDayTask = Task.Run(() => GetDay(date));
        string dayResponse = await getDayTask;
        Console.WriteLine(dayResponse);
        break;

    case "GetDate":
        var day = DateTime.Today.DayOfWeek.ToString();
        // Check for entities
        if (entities.Count > 0)
        {
            // Check for a Weekday entity
            if (entities.ContainsKey("Weekday"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Weekday"].ToString());
                // List entities are lists
                day = entityJson[0][0].ToString();
            }
        }
        // Get the date for the specified day
        var getDateTask = Task.Run(() => GetDate(day));
        string dateResponse = await getDateTask;
        Console.WriteLine(dateResponse);
        break;

    default:
        // Some other intent (for example, "None") was predicted
        Console.WriteLine("Try asking me for the time, the day, or the date.");
        break;
}
```

**Python**

```Python
# Apply the appropriate action
if top_intent == 'GetTime':
    location = 'local'
    # Check for entities
    if len(entities) > 0:
        # Check for a location entity
        if 'Location' in entities:
            # ML entities are strings, get the first one
            location = entities['Location'][0]
    # Get the time for the specified location
    print(GetTime(location))

elif top_intent == 'GetDay':
    date_string = date.today().strftime("%m/%d/%Y")
    # Check for entities
    if len(entities) > 0:
        # Check for a Date entity
        if 'Date' in entities:
            # Regex entities are strings, get the first one
            date_string = entities['Date'][0]
    # Get the day for the specified date
    print(GetDay(date_string))

elif top_intent == 'GetDate':
    day = 'today'
    # Check for entities
    if len(entities) > 0:
        # Check for a Weekday entity
        if 'Weekday' in entities:
            # List entities are lists
            day = entities['Weekday'][0][0]
    # Get the date for the specified day
    print(GetDate(day))

else:
    # Some other intent (for example, "None") was predicted
    print('Try asking me for the time, the day, or the date.')
```
    
4. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **clock-client** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

**C#**

```
dotnet run
```

**Python**

```
python clock-client.py
```

5. Wenn Sie dazu aufgefordert werden, geben Sie Äußerungen ein, um die Anwendung zu testen. Versuchen Sie zum Beispiel:

    *Hello*
    
    *Wie spät ist es?*

    *Wie spät ist es in London?*

    *Welches Datum haben wir?*

    *Welches Datum ist am Sonntag?*

    *Welcher Tag ist heute?*

    *Welcher Tag ist der 01.01.2025?*

> **Hinweis**: Die Logik in der Anwendung ist absichtlich einfach und unterliegt einer Reihe von Einschränkungen. Wenn Sie beispielsweise die Zeit abrufen, wird nur eine eingeschränkte Gruppe von Städten unterstützt, und Sommerzeit wird ignoriert. Ziel ist es, ein Beispiel für ein typisches Muster für die Verwendung von Language Understanding zu sehen, in dem Ihre Anwendung Folgendes muss:
>
>   1. Verbinden mit einem Vorhersageendpunkt.
>   2. Übermitteln einer Äußerung, um eine Vorhersage zu erhalten.
>   3. Implementieren von Logik, um angemessen auf die vorhergesagte(n) Absicht und Entitäten zu reagieren.

6. Wenn Sie die Tests abgeschlossen haben, geben Sie *quit* (Beenden) ein.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zum Erstellen eines Language Understanding-Clients finden Sie in der [Entwicklerdokumentation](https://docs.microsoft.com/azure/cognitive-services/luis/developer-reference-resource).
