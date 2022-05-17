---
lab:
  title: Erstellen einer Clientanwendung für Conversational Language Understanding (Vorschau)
ms.openlocfilehash: 486a6716a189156739e9107824e561f7bb8fa95c
ms.sourcegitcommit: 2c92ea1491cac72a4765755cf2bc6d8b5f657a46
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/09/2022
ms.locfileid: "141580025"
---
# <a name="create-a-language-service-client-application"></a>Erstellen einer Clientanwendung für den Sprachdienst

Mit dem Feature Conversational Language Understanding des Azure Cognitive Service für Language können Sie ein Conversational Language-Modell definieren, das Client-Apps verwenden können, um Eingaben in natürlicher Sprache von Benutzern zu interpretieren, die *Absicht* des Benutzers vorherzusagen (was er erreichen möchte) und *Entitäten* zu identifizieren, auf die die Absicht angewendet werden soll. Sie können Clientanwendungen erstellen, die Conversational Language Understanding-Modelle direkt über REST-Schnittstellen oder mithilfe sprachspezifischer Software Development Kits (SDKs) nutzen.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** bereits in die Umgebung geklont haben, in der Sie an dieser Übung arbeiten, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es jetzt zu klonen.

1. Starten Sie Visual Studio Code.

2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).

3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.

4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## <a name="create-language-service-resources"></a>Erstellen von Sprachdienstressourcen

Wenn Sie bereits über eine Sprachdienstressource in Ihrem Azure-Abonnement verfügen, können Sie diese in dieser Übung verwenden. Befolgen Sie andernfalls diese Anweisungen, um eine zu erstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.

2. Wählen Sie die Schaltfläche **&#65291;Ressource erstellen** aus. Suchen Sie nach *Sprachdienst*, und erstellen Sie eine **Sprachdienst**-Ressource mit den folgenden Einstellungen:

    - **Standardfeatures**: All
    - **Benutzerdefinierte Features**: Keine
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *westus2*
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: *S*
    - **Rechtliche Bedingungen**: *Aktivieren Sie das Kontrollkästchen zur Bestätigung*.
    - **Hinweis zur verantwortlichen Verwendung von KI**: *Aktivieren Sie das Kontrollkästchen zur Bestätigung*.

3. Warten Sie, bis die Ressourcen erstellt wurden. Sie können Ihre Ressource anzeigen, indem Sie zu der Ressourcengruppe navigieren, in der Sie sie erstellt haben.

## <a name="import-train-and-publish-a-conversational-language-understanding-model"></a>Importieren, Trainieren und Veröffentlichen eines Conversational Language Understanding-Modells

Wenn Sie bereits über ein **Clock**-Projekt (Uhr) aus einem vorherigen Lab oder einer Übung verfügen, können Sie sie in dieser Übung verwenden. Befolgen Sie andernfalls diese Anleitungen, um sie zu erstellen.

1. Öffnen Sie in einer neuen Browser-Registerkarte das Language Studio-Portal (Vorschau) unter `https://language.cognitive.azure.com`.

2. Melden Sie sich mit Ihrem Microsoft-Konto an, das mit Ihrem Azure-Abonnement verknüpft ist. Wenn Sie sich zum ersten Mal beim Sprachdienstportal anmelden, müssen Sie der App unter Umständen bestimmte Berechtigungen für den Zugriff auf Ihre Kontodetails erteilen. Führen Sie dann die Schritte unter *Welcome* (Willkommen) aus, indem Sie Ihr Abonnement und die Erstellungsressource auswählen, die Sie gerade erstellt haben.

3. Öffnen Sie die Seite **Conversational Language Understanding**.

3. Zeigen Sie neben **&#65291;Neues Projekt erstellen** die Dropdownliste an, und wählen Sie **Importieren** aus. Klicken Sie auf **Datei auswählen**, und navigieren Sie dann zum Unterordner **10b-clu-client-(preview)** im Projektordner, der die Labdateien enthält. Wählen Sie **Clock.json** aus, klicken Sie auf **Öffnen** und dann auf **Fertig**.

4. Falls ein Bereich mit Tipps zum Erstellen einer effektiven Sprachdienst-App angezeigt wird, schließen Sie ihn.

5. Wählen Sie auf der linken Seite des Language Studio-Portals die Option **Modell trainieren** aus, um die App zu trainieren. Klicken Sie auf **Trainingsauftrag starten**, nennen Sie das Modell **Clock** (Uhr), und stellen Sie sicher, dass die Auswertung mit Training aktiviert ist. Das Training kann mehrere Minuten dauern.

    > **Hinweis:** Da der Modellname **Clock** im Code des Clock-Clients (der später im Lab verwendet wird) hartcodiert ist, sollten Sie den Namen genau wie beschrieben schreiben und auf Rechtschreibung sowie Groß-/Kleinschreibung achten. 

6. Wählen Sie auf der linken Seite des Language Studio-Portals das **Bereitstellungsmodell** aus, und verwenden Sie **Bereitstellung hinzufügen**, um die Bereitstellung für das Modell „Clock“ zu erstellen, das mit dem Namen **Production** (Produktion) bezeichnet wird.

    > **Hinweis:** Da der Bereitstellungsname **Production** im Code des Clock-Clients (der später im Lab verwendet wird) hartcodiert ist, sollten Sie den Namen genau wie beschrieben schreiben und auf Rechtschreibung sowie Groß-/Kleinschreibung achten. 

7. Wählen Sie nach Abschluss der Bereitstellung die **Production**-Bereitstellung aus, und klicken Sie dann auf **Vorhersage-URL abrufen**. Clientanwendungen benötigen die Endpunkt-URL, um Ihr bereitgestelltes Modell zu verwenden.

8. Wählen Sie links im Language Studio-Portal **Projekteinstellungen** aus, und notieren Sie den **Primärschlüssel**. Clientanwendungen benötigen den Primärschlüssel, um Ihr bereitgestelltes Modell zu verwenden.

9. Clientanwendungen benötigen Informationen von der Vorhersage-URL und dem Sprachdienstschlüssel, um eine Verbindung mit Ihrem bereitgestellten Modell herzustellen und authentifiziert zu werden.

## <a name="prepare-to-use-the-language-service-sdk"></a>Vorbereiten auf die Verwendung des Sprachdienst-SDK

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die das Clock-Modell (veröffentlichtes Conversational Language Understanding-Modell) verwendet, um Absichten aus Benutzereingaben vorherzusagen und entsprechend zu reagieren.

> **Hinweis:** Sie können auswählen, ob Sie das SDK für **.NET** oder **Python** verwenden möchten. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **10b-clu-client-(preview)** , und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.

2. Klicken Sie mit der rechten Maustaste auf den Ordner **clock-client**, und wählen Sie dann **In integriertem Terminal öffnen** aus. Installieren Sie dann das Conversational Language Service SDK-Paket, indem Sie den entsprechenden Befehl für die von Ihnen bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Azure.AI.Language.Conversations --version 1.0.0-beta.2
    ```

    **Python**

    ```
    pip install azure-ai-language-conversations --pre
    python -m pip install python-dotenv
    python -m pip install python-dateutil
    ```

3. Zeigen Sie den Inhalt des Ordners **clock-client** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:

    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte, um die **Endpunkt-URL** und den **Primärschlüssel** für Ihre Sprachressource einzubeziehen. Sie finden die erforderlichen Werte wie folgt im Azure-Portal oder Language Studio:

    - Azure-Portal: Öffnen Sie Ihre Sprachressource. Wählen Sie unter **Ressourcenverwaltung** die Option **Schlüssel und Endpunkt** aus. Kopieren Sie die **KEY 1**- und **Endpunkt**-Werte in die Datei mit den Konfigurationseinstellungen.
    - Language Studio: Öffnen Sie Ihr **Clock**-Projekt. Den Sprachdienstendpunkt finden Sie auf der Seite **Modell bereitstellen** unter **Vorhersage-URL abrufen**, und der **Primärschlüssel** befindet sich auf der Seite **Projekteinstellungen**. Der Endpunktteil des Sprachdiensts der Vorhersage-URL endet mit **.cognitiveservices.azure.com/** . Beispiel: `https://ai102-langserv.cognitiveservices.azure.com/`.

4. Beachten Sie, dass der Ordner **clock-client** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: clock-client.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Sprachdienst-SDK verwenden zu können:

    **C#**

    ```C#
    // Import namespaces
    using Azure;
    using Azure.AI.Language.Conversations;
    ```

    **Python**

    ```Python
    # Import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.language.conversations import ConversationAnalysisClient
    from azure.ai.language.conversations.models import ConversationAnalysisOptions
    ```

## <a name="get-a-prediction-from-the-conversational-language-model"></a>Abrufen einer Vorhersage vom Conversational Language-Modell

Nun können Sie Code implementieren, der das SDK verwendet, um eine Vorhersage von Ihrem Conversational Language-Modell zu erhalten.

1. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden des Vorhersageendpunkts und des Schlüssels aus der Konfigurationsdatei bereitgestellt wurde. Suchen Sie dann den Kommentar **Create a client for the Language service model** (Client für das Sprachdienstmodell erstellen), und fügen Sie den folgenden Code hinzu, um einen Vorhersageclient für Ihre Sprachdienst-App zu erstellen:

    **C#**

    ```C#
    // Create a client for the Language service model
    Uri lsEndpoint = new Uri(predictionEndpoint);
    AzureKeyCredential lsCredential = new AzureKeyCredential(predictionKey);

    ConversationAnalysisClient lsClient = new ConversationAnalysisClient(lsEndpoint, lsCredential);
    ```

    **Python**

    ```Python
    # Create a client for the Language service model
    client = ConversationAnalysisClient(
        ls_prediction_endpoint, AzureKeyCredential(ls_prediction_key))
    ```

2. Beachten Sie, dass der Code in der **Main**-Funktion zu Benutzereingaben auffordert, bis der Benutzer „quit“ eingibt. Suchen Sie in dieser Schleife nach dem Kommentar **Call the Language service model to get intent and entities** (Sprachdienstmodell aufrufen, um Absicht und Entitäten zu erhalten), und fügen Sie den folgenden Code hinzu:

    **C#**

    ```C#
    // Call the Language service model to get intent and entities
    var lsProject = "Clock";
    var lsSlot = "production";

    ConversationsProject conversationsProject = new ConversationsProject(lsProject, lsSlot);
    Response<AnalyzeConversationResult> response = await lsClient.AnalyzeConversationAsync(userText, conversationsProject);

    ConversationPrediction conversationPrediction = response.Value.Prediction as ConversationPrediction;

    Console.WriteLine(JsonConvert.SerializeObject(conversationPrediction, Formatting.Indented));
    Console.WriteLine("--------------------\n");
    Console.WriteLine(userText);
    var topIntent = "";
    if (conversationPrediction.Intents[0].Confidence > 0.5)
    {
        topIntent = conversationPrediction.TopIntent;
    }

    var entities = conversationPrediction.Entities;
    ```

    **Python**

    ```Python
    # Call the Language service model to get intent and entities
    convInput = ConversationAnalysisOptions(
        query = userText
        )
    
    cls_project = 'Clock'
    deployment_slot = 'production'

    with client:
        result = client.analyze_conversations(
            convInput, 
            project_name=cls_project, 
            deployment_name=deployment_slot
            )

    # list the prediction results
    top_intent = result.prediction.top_intent
    entities = result.prediction.entities

    print("view top intent:")
    print("\ttop intent: {}".format(result.prediction.top_intent))
    print("\tcategory: {}".format(result.prediction.intents[0].category))
    print("\tconfidence score: {}\n".format(result.prediction.intents[0].confidence_score))

    print("view entities:")
    for entity in entities:
        print("\tcategory: {}".format(entity.category))
        print("\ttext: {}".format(entity.text))
        print("\tconfidence score: {}".format(entity.confidence_score))

    print("query: {}".format(result.query))
    ```

    Der Aufruf des Sprachdienstmodells gibt eine Vorhersage (bzw. ein Ergebnis) zurück, die die oberste (wahrscheinlichste) Absicht sowie alle Entitäten enthält, die in der Eingabeäußerung erkannt wurden. Ihre Clientanwendung muss diese Vorhersage jetzt verwenden, um die entsprechende Aktion zu bestimmen und durchzuführen.

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
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Location")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        location = entity.Text;
                    }
                    
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
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Date")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        date = entity.Text;
                    }
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
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Weekday")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        day = entity.Text;
                    }
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
            for entity in entities:
                if 'Location' == entity.category:
                    # ML entities are strings, get the first one
                    location = entity.text
        # Get the time for the specified location
        print(GetTime(location))

    elif top_intent == 'GetDay':
        date_string = date.today().strftime("%m/%d/%Y")
        # Check for entities
        if len(entities) > 0:
            # Check for a Date entity
            for entity in entities:
                if 'Date' == entity.category:
                    # Regex entities are strings, get the first one
                    date_string = entity.text
        # Get the day for the specified date
        print(GetDay(date_string))

    elif top_intent == 'GetDate':
        day = 'today'
        # Check for entities
        if len(entities) > 0:
            # Check for a Weekday entity
            for entity in entities:
                if 'Weekday' == entity.category:
                # List entities are lists
                    day = entity.text
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

> **Hinweis**: Die Logik in der Anwendung ist absichtlich einfach und unterliegt einer Reihe von Einschränkungen. Wenn Sie beispielsweise die Zeit abrufen, wird nur eine eingeschränkte Gruppe von Städten unterstützt, und Sommerzeit wird ignoriert. Ziel ist es, ein Beispiel für ein typisches Muster für die Verwendung des Sprachdiensts zu sehen, in dem Ihre Anwendung Folgendes muss:
>
>   1. Verbinden mit einem Vorhersageendpunkt.
>   2. Übermitteln einer Äußerung, um eine Vorhersage zu erhalten.
>   3. Implementieren von Logik, um angemessen auf die vorhergesagte(n) Absicht und Entitäten zu reagieren.

6. Wenn Sie die Tests abgeschlossen haben, geben Sie *quit* (Beenden) ein.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zum Erstellen eines Sprachdienstclients finden Sie in der [Entwicklerdokumentation](https://docs.microsoft.com/azure/cognitive-services/luis/developer-reference-resource).
