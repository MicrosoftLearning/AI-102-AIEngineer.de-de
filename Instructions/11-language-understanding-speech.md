---
lab:
  title: Verwenden der Speech- und Language Understanding-Dienste
  module: Module 5 - Creating Language Understanding Solutions
ms.openlocfilehash: 31b88612bce38780f828859f8e2b166dbe8df34d
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625868"
---
# <a name="use-the-speech-and-language-understanding-services"></a>Verwenden der Speech- und Language Understanding-Dienste

Sie können den Speech-Dienst in den Language Understanding-Dienst integrieren, um Anwendungen zu erstellen, die Benutzerabsichten auf intelligente Weise aus gesprochenen Eingaben bestimmen können.

> **Hinweis**: Diese Übung funktioniert am besten, wenn Sie über ein Mikrofon verfügen. Einige gehostete virtuelle Umgebungen können möglicherweise Audiodaten von Ihrem lokalen Mikrofon erfassen, aber wenn dies nicht funktioniert (oder Sie überhaupt kein Mikrofon haben), können Sie eine bereitgestellte Audiodatei für die Spracheingabe verwenden. Befolgen Sie die Anweisungen sorgfältig, da Sie verschiedene Optionen auswählen müssen, je nachdem, ob Sie ein Mikrofon oder die Audiodatei verwenden.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** bereits in die Umgebung geklont haben, in der Sie an dieser Übung arbeiten, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es jetzt zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

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

## <a name="prepare-a-language-understanding-app"></a>Vorbereiten einer Language Understanding-App

Wenn Sie bereits über eine **Clock**-App (Uhr) aus einer vorherigen Übung verfügen, öffnen Sie sie im Language Understanding-Portal unter `https://www.luis.ai`. Befolgen Sie andernfalls diese Anleitungen, um sie zu erstellen.

1. Öffnen Sie auf einer neuen Browserregisterkarte das Language Understanding-Portal unter `https://www.luis.ai`.
2. Melden Sie sich mit Ihrem Microsoft-Konto an, das mit Ihrem Azure-Abonnement verknüpft ist. Wenn Sie sich zum ersten Mal beim Language Understanding-Portal anmelden, müssen Sie der App möglicherweise einige Berechtigungen für den Zugriff auf Ihre Kontodaten erteilen. Führen Sie dann die Schritte unter *Welcome* (Willkommen) aus, indem Sie Ihr Abonnement und die Erstellungsressource auswählen, die Sie gerade erstellt haben.
3. Öffnen Sie die Seite **Konversations-Apps** neben **&#65291;Neue App**, zeigen Sie die Dropdownliste an, und wählen Sie **Als LU importieren** aus.
Navigieren Sie im Projektordner mit den Labdateien für diese Übung zum Unterordner **11-luis-speech**, und wählen Sie **Clock.lu** aus. Geben Sie dann einen eindeutigen Namen für die Clock-App an.
4. Wenn ein Fenster mit Tipps zur Erstellung einer effektiven Language Understanding-App angezeigt wird, schließen Sie es.

## <a name="train-and-publish-the-app-with-speech-priming"></a>Trainieren und Veröffentlichen der App mit *Spracherkennungsvorbereitung*

1. Wählen Sie oben im Language Understanding-Portal **Trainieren** aus, um die App zu trainieren, wenn sie noch nicht trainiert wurde.
2. Wählen Sie oben rechts im Language Understanding-Portal **Veröffentlichen** aus. Wählen Sie dann den **Produktionsslot** aus, und ändern Sie die Einstellungen, um die **Spracherkennungsvorbereitung** zu aktivieren (dies führt zu einer besseren Leistung bei der Spracherkennung).
3. Wählen Sie nach Abschluss der Veröffentlichung oben im Language Understanding-Portal **Verwalten** aus.
4. Notieren Sie die **App-ID** von der Seite **Einstellungen**. Clientanwendungen benötigen dies, um Ihre App zu verwenden.
5. Fügen Sie auf der Seite **Azure-Ressourcen** unter **Vorhersageressourcen** die Vorhersageressource in Ihrem Azure-Abonnement hinzu, wenn keine Vorhersageressource aufgeführt ist.
6. Notieren Sie den **Primärschlüssel**, den **Sekundärschlüssel** und den **Standort** (<u>nicht</u> den Endpunkt) für die Vorhersageressource. Speech SDK-Clientanwendungen benötigen den Standort und einen der Schlüssel, um eine Verbindung mit der Vorhersageressource herzustellen und authentifiziert zu werden.

## <a name="configure-a-client-application-for-language-understanding"></a>Konfigurieren einer Clientanwendung für Language Understanding

In dieser Übung erstellen Sie eine Clientanwendung, die gesprochene Eingaben akzeptiert und Ihre Language Understanding-App verwendet, um die Absicht des Benutzers vorherzusagen.

> **Hinweis**: Sie können auswählen, das SDK für **C#** oder **Python** in dieser Übung zu verwenden. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **11-luis-speech**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Zeigen Sie den Inhalt des Ordners **speaking-clock-client** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die Konfigurationswerte, die sie enthält, um die **App-ID** für Ihre Language Understanding-App, den **Standort** (<u>nicht</u> den vollständigen Endpunkt, z. B. *eastus*) und einen der **Schlüssel** für die Vorhersageressource (von der Seite **Verwalten** für Ihre App im Language Understanding-Portal) einzuschließen.

## <a name="install-sdk-packages"></a>Installieren von SDK-Paketen

Um das Speech SDK mit dem Language Understanding-Dienst verwenden zu können, müssen Sie das Speech SDK-Paket für Ihre Programmiersprache installieren.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf den Ordner **speaking-clock-client**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Language Understanding SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.14.0
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-speech==1.14.0
    ```

2. Wenn Ihr System <u>nicht</u> über ein funktionierendes Mikrofon verfügt, müssen Sie außerdem eine Audiodatei verwenden, um Ihrer Anwendung gesprochene Eingaben bereitzustellen. Verwenden Sie in diesem Fall die folgenden Befehle, um ein zusätzliches Paket zu installieren, damit Ihr Programm die Audiodatei wiedergeben kann (Sie können dies überspringen, wenn Sie ein Mikrofon verwenden möchten):

    **C#**

    ```
    dotnet add package System.Windows.Extensions --version 4.6.0 
    ```

    **Python**

    ```
    pip install playsound==1.2.2
    ```

3. Beachten Sie, dass der Ordner **speaking-clock-client** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: speaking-clock-client.py

4. Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Speech SDK verwenden zu können:

    **C#**

    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Intent;
    ```

    **Python**

    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. Wenn Ihr System <u>nicht</u> über ein funktionierendes Mikrofon verfügt, fügen Sie außerdem unter den vorhandenen Namespaceimporten den folgenden Code hinzu, um die Bibliothek zu importieren, die Sie für die Wiedergabe einer Audiodatei verwenden werden:

    **C#**

    ```C#
    using System.Media;
    ```

    **Python**

    ```Python
    from playsound import playsound
    ```

## <a name="create-an-intentrecognizer"></a>Erstellen eines *IntentRecognizer*-Objekts

Die **IntentRecognizer**-Klasse bietet ein Clientobjekt, mit dem Sie Language Understanding-Vorhersagen aus gesprochenen Eingaben erhalten können.

1. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden der App-ID, der Vorhersageregion und des Schlüssels aus der Konfigurationsdatei bereitgestellt wurde. Suchen Sie dann den Kommentar **Configure speech service and get intent recognizer** (Speech-Dienst konfigurieren und Absichtserkennung abrufen), und fügen Sie den folgenden Code hinzu, je nachdem, ob Sie ein Mikrofon oder eine Audiodatei für die Spracheingabe verwenden werden:

    ### <a name="if-you-have-a-working-microphone"></a>**Wenn Sie über ein funktionierendes Mikrofon verfügen:**

    **C#**

    ```C#
    // Configure speech service and get intent recognizer
    SpeechConfig speechConfig = SpeechConfig.FromSubscription(predictionKey, predictionRegion);
    AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    IntentRecognizer recognizer = new IntentRecognizer(speechConfig, audioConfig);
    ```

    **Python**

    ```Python
    # Configure speech service and get intent recognizer
    speech_config = speech_sdk.SpeechConfig(subscription=lu_prediction_key, region=lu_prediction_region)
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    recognizer = speech_sdk.intent.IntentRecognizer(speech_config, audio_config)
    ```

    ### <a name="if-you-need-to-use-an-audio-file"></a>**Wenn Sie eine Audiodatei verwenden müssen:**

    **C#**

    ```C#
    // Configure speech service and get intent recognizer
    string audioFile = "time-in-london.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    System.Threading.Thread.Sleep(2000);
    SpeechConfig speechConfig = SpeechConfig.FromSubscription(predictionKey, predictionRegion);
    AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    IntentRecognizer recognizer = new IntentRecognizer(speechConfig, audioConfig);
    ```

    **Python**

    ```Python
    # Configure speech service and get intent recognizer
    audioFile = 'time-in-london.wav'
    playsound(audioFile)
    speech_config = speech_sdk.SpeechConfig(subscription=lu_prediction_key, region=lu_prediction_region)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    recognizer = speech_sdk.intent.IntentRecognizer(speech_config, audio_config)
    ```

## <a name="get-a-predicted-intent-from-spoken-input"></a>Erhalten einer vorhergesagten Absicht aus gesprochener Eingabe

Nun können Sie Code implementieren, der das Speech SDK verwendet, um eine vorhergesagte Absicht aus gesprochenen Eingaben zu erhalten.

1. Suchen Sie in der **Main**-Funktion direkt unter dem Code, den Sie gerade hinzugefügt haben, den Kommentar **Get the model from the AppID and add the intents we want to use** (Modell von der AppID abrufen und die zu verwendenden Absichten hinzufügen), und fügen Sie den folgenden Code hinzu, um Ihr Language Understanding-Modell (basierend auf seiner App-ID) abzurufen und die Absichten anzugeben, die die Erkennung identifizieren soll.

    **C#**

    ```C#
    // Get the model from the AppID and add the intents we want to use
    var model = LanguageUnderstandingModel.FromAppId(luAppId);
    recognizer.AddIntent(model, "GetTime", "time");
    recognizer.AddIntent(model, "GetDate", "date");
    recognizer.AddIntent(model, "GetDay", "day");
    recognizer.AddIntent(model, "None", "none");
    ```

    *Beachten Sie, dass Sie für jede Absicht eine zeichenfolgenbasierte ID angeben können.*

    **Python**

    ```Python
    # Get the model from the AppID and add the intents we want to use
    model = speech_sdk.intent.LanguageUnderstandingModel(app_id=lu_app_id)
    intents = [
        (model, "GetTime"),
        (model, "GetDate"),
        (model, "GetDay"),
        (model, "None")
    ]
    recognizer.add_intents(intents)
    ```

2. Fügen Sie unter dem Kommentar **Process speech input** (Spracheingabe verarbeiten) den folgenden Code hinzu, der die Erkennen verwendet, um den Language Understanding-Dienst mit gesprochener Eingabe asynchron aufzurufen und die Antwort abzurufen. Wenn die Antwort eine vorhergesagte Absicht enthält, werden die gesprochene Abfrage, die vorhergesagte Absicht und die vollständige JSON-Antwort angezeigt. Andernfalls verarbeitet der Code die Antwort auf Grundlage des zurückgegebenen Grunds.

**C#**

```C
// Process speech input
string intent = "";
var result = await recognizer.RecognizeOnceAsync().ConfigureAwait(false);
if (result.Reason == ResultReason.RecognizedIntent)
{
    // Intent was identified
    intent = result.IntentId;
    Console.WriteLine($"Query: {result.Text}");
    Console.WriteLine($"Intent Id: {intent}.");
    string jsonResponse = result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult);
    Console.WriteLine($"JSON Response:\n{jsonResponse}\n");
    
    // Get the first entity (if any)

    // Apply the appropriate action
    
}
else if (result.Reason == ResultReason.RecognizedSpeech)
{
    // Speech was recognized, but no intent was identified.
    intent = result.Text;
    Console.Write($"I don't know what {intent} means.");
}
else if (result.Reason == ResultReason.NoMatch)
{
    // Speech wasn't recognized
    Console.WriteLine($"Sorry. I didn't understand that.");
}
else if (result.Reason == ResultReason.Canceled)
{
    // Something went wrong
    var cancellation = CancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
    }
}
```

**Python**

```Python
# Process speech input
intent = ''
result = recognizer.recognize_once_async().get()
if result.reason == speech_sdk.ResultReason.RecognizedIntent:
    intent = result.intent_id
    print("Query: {}".format(result.text))
    print("Intent: {}".format(intent))
    json_response = json.loads(result.intent_json)
    print("JSON Response:\n{}\n".format(json.dumps(json_response, indent=2)))
    
    # Get the first entity (if any)
    
    # Apply the appropriate action
    
elif result.reason == speech_sdk.ResultReason.RecognizedSpeech:
    # Speech was recognized, but no intent was identified.
    intent = result.text
    print("I don't know what {} means.".format(intent))
elif result.reason == speech_sdk.ResultReason.NoMatch:
    # Speech wasn't recognized
    print("Sorry. I didn't understand that.")
elif result.reason == speech_sdk.ResultReason.Canceled:
    # Something went wrong
    print("Intent recognition canceled: {}".format(result.cancellation_details.reason))
    if result.cancellation_details.reason == speech_sdk.CancellationReason.Error:
        print("Error details: {}".format(result.cancellation_details.error_details))
```

Der Code, den Sie bisher hinzugefügt haben, identifiziert die *Absicht*, aber einige Absichten können auf *Entitäten* verweisen, weshalb Sie Code hinzufügen müssen, um die Entitätsinformationen aus dem vom Dienst zurückgegebenen JSON-Code zu extrahieren.

3. Suchen Sie in dem soeben hinzugefügten Code den Kommentar **Get the first entity (if any)** (Erste Entität abrufen (falls vorhanden)), und fügen Sie darunter den folgenden Code hinzu:

**C#**

```C
// Get the first entity (if any)
JObject jsonResults = JObject.Parse(jsonResponse);
string entityType = "";
string entityValue = "";
if (jsonResults["entities"].HasValues)
{
    JArray entities = new JArray(jsonResults["entities"][0]);
    entityType = entities[0]["type"].ToString();
    entityValue = entities[0]["entity"].ToString();
    Console.WriteLine(entityType + ": " + entityValue);
}
```

**Python**

```Python
# Get the first entity (if any)
entity_type = ''
entity_value = ''
if len(json_response["entities"]) > 0:
    entity_type = json_response["entities"][0]["type"]
    entity_value = json_response["entities"][0]["entity"]
    print(entity_type + ': ' + entity_value)
```
    
Ihr Code verwendet jetzt die Language Understanding-App, um eine Absicht sowie alle Entitäten vorherzusagen, die in der Eingabeäußerung erkannt wurden. Ihre Clientanwendung muss diese Vorhersage jetzt verwenden, um die entsprechende Aktion zu bestimmen und durchzuführen.

4. Suchen Sie unter dem gerade hinzugefügten Code nach dem Kommentar **Apply the appropriate action** (Die entsprechende Aktion anwenden), und fügen Sie den folgenden Code hinzu, der nach Absichten sucht, die von der Anwendung unterstützt werden (**GetTime**, **GetDate** und **GetDay**), und bestimmt, ob relevante Entitäten erkannt wurden, bevor eine vorhandene Funktion zum Erzeugen einer entsprechenden Antwort aufgerufen wird.

**C#**

```C#
// Apply the appropriate action
switch (intent)
{
    case "time":
        var location = "local";
        // Check for entities
        if (entityType == "Location")
        {
            location = entityValue;
        }
        // Get the time for the specified location
        var getTimeTask = Task.Run(() => GetTime(location));
        string timeResponse = await getTimeTask;
        Console.WriteLine(timeResponse);
        break;
    case "day":
        var date = DateTime.Today.ToShortDateString();
        // Check for entities
        if (entityType == "Date")
        {
            date = entityValue;
        }
        // Get the day for the specified date
        var getDayTask = Task.Run(() => GetDay(date));
        string dayResponse = await getDayTask;
        Console.WriteLine(dayResponse);
        break;
    case "date":
        var day = DateTime.Today.DayOfWeek.ToString();
        // Check for entities
        if (entityType == "Weekday")
        {
            day = entityValue;
        }

        var getDateTask = Task.Run(() => GetDate(day));
        string dateResponse = await getDateTask;
        Console.WriteLine(dateResponse);
        break;
    default:
        // Some other intent (for example, "None") was predicted
        Console.WriteLine("You said " + result.Text.ToLower());
        if (result.Text.ToLower().Replace(".", "") == "stop")
        {
            intent = result.Text;
        }
        else
        {
            Console.WriteLine("Try asking me for the time, the day, or the date.");
        }
        break;
}
```

**Python**

```Python
# Apply the appropriate action
if intent == 'GetTime':
    location = 'local'
    # Check for entities
    if entity_type == 'Location':
        location = entity_value
    # Get the time for the specified location
    print(GetTime(location))

elif intent == 'GetDay':
    date_string = date.today().strftime("%m/%d/%Y")
    # Check for entities
    if entity_type == 'Date':
        date_string = entity_value
    # Get the day for the specified date
    print(GetDay(date_string))

elif intent == 'GetDate':
    day = 'today'
    # Check for entities
    if entity_type == 'Weekday':
        # List entities are lists
        day = entity_value
    # Get the date for the specified day
    print(GetDate(day))

else:
    # Some other intent (for example, "None") was predicted
    print('You said {}'.format(result.text))
    if result.text.lower().replace('.', '') == 'stop':
        intent = result.text
    else:
        print('Try asking me for the time, the day, or the date.')
```

## <a name="run-the-client-application"></a>Ausführen der Clientanwendung

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock-client** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python speaking-clock-client.py
    ```

2. Wenn Sie ein Mikrofon verwenden, sprechen Sie Äußerungen laut aus, um die Anwendung zu testen. Versuchen Sie beispielsweise Folgendes (führen Sie das Programm jedes Mal erneut aus):

    *Wie viel Uhr ist es?*
    
    *Wie spät ist es?*

    *Welcher Tag ist heute?*

    *Wie spät ist es in London?*

    *Welches Datum haben wir?*

    *Welches Datum ist am Sonntag?*

> **Hinweis**: Die Logik in der Anwendung ist absichtlich einfach und weist eine Reihe von Einschränkungen auf, sollte jedoch den Zweck erfüllen, die Fähigkeit des Language Understanding-Modells zum Vorhersagen von Absichten aus gesprochenen Eingaben mit dem Speech SDK zu testen. Möglicherweise haben Sie Probleme beim Erkennen der **GetDay**-Absicht mit einer bestimmten Datumsentität, da es schwierig ist, ein Datum im *MM/TT/YYYY*-Format zu verbalisieren.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur Speech- und Language Understanding-Integration finden Sie in der [Speech-Dokumentation](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition).
