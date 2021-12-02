---
lab:
  title: Übersetzen von gesprochener Sprache
  module: Module 4 - Building Speech-Enabled Applications
ms.openlocfilehash: 54bc0d9f942455e981437ecf5fe4a9de828c1cfb
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625811"
---
# <a name="translate-speech"></a>Übersetzen von gesprochener Sprache

Der **Sprachübersetzungs**-Dienst umfasst eine **Sprachübersetzungs**-API, mit der Sie gesprochene Sprache übersetzen können. Angenommen, Sie möchten eine Übersetzungsanwendung entwickeln, die Benutzer verwenden können, wenn sie an Orte reisen, wo sie nicht die lokale Sprache sprechen. Sie könnten dann Ausdrücke wie „Where is the station?“ (Wo ist der Bahnhof?) oder „I need to find a pharmacy“ (Ich suche eine Apotheke) in ihrer Muttersprache sagen und diese in die lokale Sprache übersetzen lassen.

**Hinweis**: Für diese Übung ist es erforderlich, dass Sie einen Computer mit Lautsprechern/Kopfhörern verwenden. Für eine optimale Benutzererfahrung ist auch ein Mikrofon erforderlich. Einige gehostete virtuelle Umgebungen können möglicherweise Audiodaten von Ihrem lokalen Mikrofon erfassen, aber wenn dies nicht funktioniert (oder Sie überhaupt kein Mikrofon haben), können Sie eine bereitgestellte Audiodatei für die Spracheingabe verwenden. Befolgen Sie die Anweisungen sorgfältig, da Sie verschiedene Optionen auswählen müssen, je nachdem, ob Sie ein Mikrofon oder die Audiodatei verwenden.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** bereits in die Umgebung geklont haben, in der Sie an dieser Übung arbeiten, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es jetzt zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Jetzt nicht** aus.

## <a name="provision-a-cognitive-services-resource"></a>Bereitstellen einer Cognitive Services-Ressource

Wenn Sie noch keine in Ihrem Abonnement haben, müssen Sie eine **Cognitive Services**-Ressource bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Wählen Sie die Schaltfläche **&#65291;Ressource erstellen**, suchen Sie nach *Cognitive Services*, und erstellen Sie eine **Cognitive Services**-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine Ressourcengruppe (wenn Sie eine gehostete Lab-Umgebung verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen, verwenden Sie dann die bereitgestellte Ressourcengruppe).*
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0
3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
5. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. Im nächsten Verfahren benötigen Sie von dieser Seite einen der Schlüssel und den Standort, an dem der Dienst bereitgestellt wird.

## <a name="prepare-to-use-the-speech-translation-service"></a>Vorbereiten der Verwendung des Sprachübersetzungs-Diensts

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die das Speech SDK zum Erkennen, Übersetzen und Synthetisieren von Sprache verwendet.

> **Hinweis**: Sie können auswählen, das SDK für **C#** oder **Python** zu verwenden. Führen Sie in den folgenden Schritten die Aktionen aus, die für Ihre bevorzugte Sprache geeignet sind.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **08-speech-translation**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **translator**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Speech SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.14.0
    ```
    
    **Python**
    
    ```
    pip install azure-cognitiveservices-speech==1.14.0
    ```

3. Zeigen Sie den Inhalt des Ordners **translator** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass sie einen Authentifizierungs **schlüssel** für Ihre Cognitive Services-Ressource und den **Standort**, an dem sie bereitgestellt ist, umfassen. Speichern Sie die Änderungen.
4. Beachten Sie, dass der Ordner **translator** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: translator.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Speech SDK verwenden zu können:

    **C#**
    
    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Translation;
    ```
    
    **Python**
    
    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden des Cognitive Services-Schlüssels und der Region aus der Konfigurationsdatei bereitgestellt wurde. Sie müssen diese Variablen verwenden, um eine **SpeechTranslationConfig** für Ihre Cognitive Services-Ressource zu erstellen, die Sie zum Übersetzen gesprochener Eingaben verwenden werden. Fügen Sie den folgenden Code unter dem Kommentar **Configure translation** (Übersetzung konfigurieren) hinzu:

    **C#**
    
    ```C#
    // Configure translation
    translationConfig = SpeechTranslationConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    translationConfig.SpeechRecognitionLanguage = "en-US";
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("es");
    translationConfig.AddTargetLanguage("hi");
    Console.WriteLine("Ready to translate from " + translationConfig.SpeechRecognitionLanguage);
    ```
    
    **Python**
    
    ```Python
    # Configure translation
    translation_config = speech_sdk.translation.SpeechTranslationConfig(cog_key, cog_region)
    translation_config.speech_recognition_language = 'en-US'
    translation_config.add_target_language('fr')
    translation_config.add_target_language('es')
    translation_config.add_target_language('hi')
    print('Ready to translate from',translation_config.speech_recognition_language)
    ```

6. Sie verwenden die **SpeechTranslationConfig**, um Sprache in Text zu übersetzen, aber Sie verwenden auch eine **SpeechConfig**, um Übersetzungen in gesprochener Sprache zu synthetisieren. Fügen Sie den folgenden Code unter dem Kommentar **Configure speech** (Sprache konfigurieren) hinzu:

    **C#**
    
    ```C#
    // Configure speech
    speechConfig = SpeechConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    ```
    
    **Python**
    
    ```Python
    # Configure speech
    speech_config = speech_sdk.SpeechConfig(cog_key, cog_region)
    ```

7. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **translator** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

8. Wenn Sie C# verwenden, können Sie alle Warnungen zur Verwendung des **await**-Operators in asynchronen Methoden ignorieren. Dies wird später behoben. Der Code sollte eine Meldung anzeigen, dass er für die Übersetzung aus en-US bereit ist. Drücken Sie die EINGABETASTE, um das Programm zu beenden.

## <a name="implement-speech-translation"></a>Implementieren von Sprachübersetzung

Nachdem Sie nun über eine **SpeechTranslationConfig** für den Speech-Dienst in Ihrer Cognitive Services-Ressource verfügen, können Sie die **Sprachübersetzungs**-API verwenden, um Sprache zu erkennen und zu übersetzen.

### <a name="if-you-have-a-working-microphone"></a>Wenn Sie über ein funktionierendes Mikrofon verfügen

1. Beachten Sie in der **Main**-Funktion für Ihr Programm, dass der Code die **Translate**-Funktion verwendet, um gesprochene Eingaben zu übersetzen.
2. Fügen Sie in der **Translate**-Funktion unter dem Kommentar **Translate speech** (Sprache übersetzen) den folgenden Code hinzu, um einen **TranslationRecognizer**-Client zu erstellen, der zum Erkennen und Übersetzen von Sprache mithilfe des Standardsystemmikrofons für Eingaben verwendet werden kann.

    **C#**
    
    ```C#
    // Translate speech
    using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using TranslationRecognizer translator = new TranslationRecognizer(translationConfig, audioConfig);
    Console.WriteLine("Speak now...");
    TranslationRecognitionResult result = await translator.RecognizeOnceAsync();
    Console.WriteLine($"Translating '{result.Text}'");
    translation = result.Translations[targetLanguage];
    Console.OutputEncoding = Encoding.UTF8;
    Console.WriteLine(translation);
    ```
    
    **Python**
    
    ```Python
    # Translate speech
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config)
    print("Speak now...")
    result = translator.recognize_once_async().get()
    print('Translating "{}"'.format(result.text))
    translation = result.translations[targetLanguage]
    print(translation)
    ```

    > **Hinweis**: Der Code in Ihrer Anwendung übersetzt die Eingabe in einem einzigen Aufruf in alle drei Sprachen. Es wird nur die Übersetzung für die spezifische Sprache angezeigt, aber Sie könnten jede der Übersetzungen abrufen, indem Sie den Zielsprachcode in der **translations**-Sammlung des Ergebnisses angeben.

3. Fahren Sie nun mit dem Abschnitt **Programm ausführen** weiter unten fort.

### <a name="alternatively-use-audio-input-from-a-file"></a>Alternativ können Sie Audioeingaben aus einer Datei verwenden.

1. Geben Sie im Terminalfenster den folgenden Befehl ein, um eine Bibliothek zu installieren, mit der Sie die Audiodatei wiedergeben können:

    **C#**

    ```
    dotnet add package System.Windows.Extensions --version 4.6.0 
    ```

    **Python**

    ```
    pip install playsound==1.2.2
    ```

2. Fügen Sie in der Codedatei für Ihr Programm unter den vorhandenen Namespaceimporten den folgenden Code hinzu, um die soeben installierte Bibliothek zu importieren:

    **C#**

    ```C#
    using System.Media;
    ```

    **Python**

    ```Python
    from playsound import playsound
    ```

3. Beachten Sie in der **Main**-Funktion für Ihr Programm, dass der Code die **Translate**-Funktion verwendet, um gesprochene Eingaben zu übersetzen. Fügen Sie dann in der **Translate**-Funktion unter dem Kommentar **Translate speech** (Sprache übersetzen) den folgenden Code hinzu, um einen **TranslationRecognizer**-Client zu erstellen, der zum Erkennen und Übersetzen von Sprache aus einer Datei verwendet werden kann.

    **C#**
    
    ```C#
    // Translate speech
    string audioFile = "station.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    using TranslationRecognizer translator = new TranslationRecognizer(translationConfig, audioConfig);
    Console.WriteLine("Getting speech from file...");
    TranslationRecognitionResult result = await translator.RecognizeOnceAsync();
    Console.WriteLine($"Translating '{result.Text}'");
    translation = result.Translations[targetLanguage];
    Console.OutputEncoding = Encoding.UTF8;
    Console.WriteLine(translation);
    ```
    
    **Python**
    
    ```Python
    # Translate speech
    audioFile = 'station.wav'
    playsound(audioFile)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config)
    print("Getting speech from file...")
    result = translator.recognize_once_async().get()
    print('Translating "{}"'.format(result.text))
    translation = result.translations[targetLanguage]
    print(translation)
    ```

    > **Hinweis**: Der Code in Ihrer Anwendung übersetzt die Eingabe in einem einzigen Aufruf in alle drei Sprachen. Es wird nur die Übersetzung für die spezifische Sprache angezeigt, aber Sie könnten jede der Übersetzungen abrufen, indem Sie den Zielsprachcode in der **translations**-Sammlung des Ergebnisses angeben.

### <a name="run-the-program"></a>Ausführen des Programms

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **translator** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

2. Wenn Sie dazu aufgefordert werden, geben Sie einen gültigen Sprachcode ein (*fr*, *es* oder *hi*), und sprechen Sie dann bei Verwendung eines Mikrofons mit klarer Stimme, und sagen Sie „Where is the station?“ (Wo ist der Bahnhof?) oder einen anderen Satz, den Sie bei Reisen im Ausland verwenden könnten. Das Programm sollte Ihre gesprochene Eingabe transkribieren und in die von Ihnen angegebene Sprache übersetzen (Französisch, Spanisch oder Hindi). Wiederholen Sie diesen Vorgang, wobei Sie jede von der Anwendung unterstützte Sprache ausprobieren. Wenn Sie fertig sind, drücken Sie die EINGABETASTE, um das Programm zu beenden.

    > **Hinweis**: Der TranslationRecognizer gibt Ihnen etwa 5 Sekunden Zeit, um zu sprechen. Wenn er keine gesprochene Eingabe erkannt, erzeugt er das Ergebnis „No match“ (Keine Übereinstimmung).
    >
    > Die Übersetzung in Hindi wird aufgrund von Zeichencodierungsproblemen möglicherweise nicht immer ordnungsgemäß im Konsolenfenster angezeigt.

## <a name="synthesize-the-translation-to-speech"></a>Synthetisieren der Übersetzung in Sprache

Bisher übersetzt Ihre Anwendung gesprochene Eingaben in Text, was ausreichend sein kann, wenn Sie während einer Reise eine Person um Hilfe bitten müssen. Es wäre jedoch besser, wenn die Übersetzung in einer passenden Stimme laut gesprochen würde.

1. Fügen Sie in der **Translate**-Funktion unter dem Kommentar **Synthesize Translation** (Übersetzung synthetisieren) den folgenden Code hinzu, um einen **SpeechSynthesizer**-Client zu verwenden, um die Übersetzung als Sprache über den Standardlautsprecher zu synthetisieren:

    **C#**
    
    ```C#
    // Synthesize translation
    var voices = new Dictionary<string, string>
                    {
                        ["fr"] = "fr-FR-HenriNeural",
                        ["es"] = "es-ES-ElviraNeural",
                        ["hi"] = "hi-IN-MadhurNeural"
                    };
    speechConfig.SpeechSynthesisVoiceName = voices[targetLanguage];
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(translation);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```
    
    **Python**
    
    ```Python
    # Synthesize translation
    voices = {
            "fr": "fr-FR-HenriNeural",
            "es": "es-ES-ElviraNeural",
            "hi": "hi-IN-MadhurNeural"
    }
    speech_config.speech_synthesis_voice_name = voices.get(targetLanguage)
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    speak = speech_synthesizer.speak_text_async(translation).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

2. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **translator** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

3. Wenn Sie dazu aufgefordert werden, geben Sie einen gültigen Sprachcode ein (*fr*, *es* oder *hi*), und sprechen Sie dann mit klarer Stimme in das Mikrofon, und sagen Sie einen Satz, den Sie bei einer Auslandsreise verwenden würden. Das Programm sollte Ihre gesprochene Eingabe transkribieren und mit einer gesprochenen Übersetzung antworten. Wiederholen Sie diesen Vorgang, wobei Sie jede von der Anwendung unterstützte Sprache ausprobieren. Wenn Sie fertig sind, drücken Sie die EINGABETASTE, um das Programm zu beenden.

    > **Hinweis**: *In diesem Beispiel haben Sie eine **SpeechTranslationConfig** verwendet, um Sprache in Text zu übersetzen, und dann eine **SpeechConfig**, um die Übersetzung als Sprache zu synthetisieren. Tatsächlich können Sie die **SpeechTranslationConfig** verwenden, um die Übersetzung direkt zu synthetisieren, aber dies funktioniert nur bei der Übersetzung in eine einzelne Sprache und resultiert in einem Audiostream, der normalerweise als Datei gespeichert wird, anstatt direkt an einen Lautsprecher gesendet zu werden.*

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur Verwendung der **Sprachübersetzungs**-API finden Sie in der [Dokumentation zur Sprachübersetzung](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-translation).
