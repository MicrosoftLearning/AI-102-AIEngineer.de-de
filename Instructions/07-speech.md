---
lab:
  title: Erkennen und Synthetisieren von Sprache
  module: Module 4 - Building Speech-Enabled Applications
ms.openlocfilehash: 9867905b0189fa5932dba02cd0815817171e4928
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2022
ms.locfileid: "135801358"
---
# <a name="recognize-and-synthesize-speech"></a>Erkennen und Synthetisieren von Sprache

Der **Speech-Dienst** ist ein Azure Cognitive Services-Dienst, der sprachbezogene Funktionen bietet, einschließlich der folgenden:

- Eine *Spracherkennungs*-API, mit der Sie die Spracherkennung implementieren können (konvertieren hörbarer gesprochener Wörter in Text).
- Eine *Sprachsynthese*-API mit der Sie eine Sprachsynthese implementieren können (konvertieren von Text in hörbare Sprache).

In dieser Übung werden Sie diese beiden APIs verwenden, um eine Anwendung für eine sprechende Uhr zu implementieren.

**Hinweis**: Für diese Übung ist es erforderlich, dass Sie einen Computer mit Lautsprechern/Kopfhörern verwenden. Für eine optimale Benutzererfahrung ist auch ein Mikrofon erforderlich. Einige gehostete virtuelle Umgebungen können möglicherweise Audiodaten von Ihrem lokalen Mikrofon erfassen, aber wenn dies nicht funktioniert (oder Sie überhaupt kein Mikrofon haben), können Sie eine bereitgestellte Audiodatei für die Spracheingabe verwenden. Befolgen Sie die Anweisungen sorgfältig, da Sie verschiedene Optionen auswählen müssen, je nachdem, ob Sie ein Mikrofon oder die Audiodatei verwenden.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** noch nicht in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, führen Sie die folgenden Schritte aus, um dies zu tun. Öffnen Sie andernfalls den geklonten Ordner in Visual Studio Code.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Jetzt nicht** aus.

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

## <a name="prepare-to-use-the-speech-service"></a>Vorbereiten der Verwendung des Speech-Diensts

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die das Speech SDK zum Erkennen und Synthetisieren von Sprache verwendet.

**Hinweis**: Sie können auswählen, ob Sie das SDK für **C#** oder **Python** verwenden möchten. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **07-speech**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **speaking-clock**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Speech SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.19.0
    ```
    
    **Python**
    
    ```
    pip install azure-cognitiveservices-speech==1.19.0
    ```

3. Zeigen Sie den Inhalt des Ordners **speaking-clock** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass sie einen Authentifizierungs **schlüssel** für Ihre Cognitive Services-Ressource und den **Standort**, an dem sie bereitgestellt ist, umfassen. Speichern Sie die Änderungen.
4. Beachten Sie, dass der Ordner **speaking-clock** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: speaking-clock.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Speech SDK verwenden zu können:

    **C#**
    
    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    ```
    
    **Python**
    
    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden des Cognitive Services-Schlüssels und der Region aus der Konfigurationsdatei bereitgestellt wurde. Sie müssen diese Variablen verwenden, um eine **SpeechConfig** für Ihre Cognitive Services-Ressource zu erstellen. Fügen Sie den folgenden Code unter dem Kommentar **Configure speech service** (Speech-Dienst konfigurieren) hinzu:

    **C#**
    
    ```C#
    // Configure speech service
    speechConfig = SpeechConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    Console.WriteLine("Ready to use speech service in " + speechConfig.Region);
    
    // Configure voice
    speechConfig.SpeechSynthesisVoiceName = "en-US-AriaNeural";
    ```
    
    **Python**
    
    ```Python
    # Configure speech service
    speech_config = speech_sdk.SpeechConfig(cog_key, cog_region)
    print('Ready to use speech service in:', speech_config.region)
    ```

6. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

7. Wenn Sie C# verwenden, können Sie alle Warnungen zur Verwendung des **await**-Operators in asynchronen Methoden ignorieren. Dies wird später behoben. Der Code sollte die Region der Speech-Dienstressource anzeigen, die die Anwendung verwenden wird.

## <a name="recognize-speech"></a>Erkennen von Sprache

Nachdem Sie nun über eine **SpeechConfig** für den Speech-Dienst in Ihrer Cognitive Services-Ressource verfügen, können Sie die **Spracherkennungs**-API verwenden, um Sprache zu erkennen und in Text zu transkribieren.

### <a name="if-you-have-a-working-microphone"></a>Wenn Sie über ein funktionierendes Mikrofon verfügen

1. Beachten Sie in der **Main**-Funktion für Ihr Programm, dass der Code die **TranscribeCommand**-Funktion verwendet, um gesprochene Eingaben zu akzeptieren.
2. Fügen Sie in der Funktion **TranscribeCommand** unter dem Kommentar **Configure speech recognition** (Spracherkennung konfigurieren) den entsprechenden Code unten ein, um einen **SpeechRecognizer**-Client zu erstellen, der zur Erkennung und Transkription von Sprache unter Verwendung des Standardmikrofons des Systems verwendet werden kann:

    **C#**
    
    ```C#
    // Configure speech recognition
    using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    Console.WriteLine("Speak now...");
    ```
    
    **Python**
    
    ```Python
    # Configure speech recognition
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    print('Speak now...')
    ```

3. Wechseln Sie nun zum Abschnitt **Hinzufügen von Code zum Verarbeiten des transkribierten Befehls** weiter unten.

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

3. Beachten Sie in der **Main**-Funktion, dass der Code die **TranscribeCommand**-Funktion verwendet, um gesprochene Eingaben zu akzeptieren. Fügen Sie dann in der Funktion **TranscribeCommand** unter dem Kommentar **Configure speech recognition** (Spracherkennung konfigurieren) den entsprechenden Code unten ein, um einen **SpeechRecognizer**-Client zu erstellen, der zur Erkennung und Transkription von Sprache aus einer Audiodatei verwendet werden kann:

    **C#**

    ```C#
    // Configure speech recognition
    string audioFile = "time.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    ```

    **Python**

    ```Python
    # Configure speech recognition
    audioFile = 'time.wav'
    playsound(audioFile)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    ```

### <a name="add-code-to-process-the-transcribed-command"></a>Hinzufügen von Code zum Verarbeiten des transkribierten Befehls

1. Fügen Sie in der Funktion **TranscribeCommand** unter dem Kommentar **Process speech input** (Spracheingabe verarbeiten) den folgenden Code ein, um auf gesprochene Eingaben zu lauschen. Achten Sie darauf, dass Sie den Code am Ende der Funktion, die den Befehl zurückgibt, nicht ersetzen:

    **C#**
    
    ```C#
    // Process speech input
    SpeechRecognitionResult speech = await speechRecognizer.RecognizeOnceAsync();
    if (speech.Reason == ResultReason.RecognizedSpeech)
    {
        command = speech.Text;
        Console.WriteLine(command);
    }
    else
    {
        Console.WriteLine(speech.Reason);
        if (speech.Reason == ResultReason.Canceled)
        {
            var cancellation = CancellationDetails.FromResult(speech);
            Console.WriteLine(cancellation.Reason);
            Console.WriteLine(cancellation.ErrorDetails);
        }
    }
    ```

    **Python**
    
    ```Python
    # Process speech input
    speech = speech_recognizer.recognize_once_async().get()
    if speech.reason == speech_sdk.ResultReason.RecognizedSpeech:
        command = speech.text
        print(command)
    else:
        print(speech.reason)
        if speech.reason == speech_sdk.ResultReason.Canceled:
            cancellation = speech.cancellation_details
            print(cancellation.reason)
            print(cancellation.error_details)
    ```

2. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. Wenn Sie ein Mikrofon verwenden, sprechen Sie deutlich und sagen Sie „What time is it?“ (Wie spät ist es?). Das Programm sollte Ihre gesprochenen Eingaben transkribieren und die Zeit anzeigen (basierend auf der lokalen Zeit des Computers, auf dem der Code ausgeführt wird, was möglicherweise nicht die richtige Zeit an Ihrem Standort ist).

    Der SpeechRecognizer gibt Ihnen etwa fünf Sekunden Zeit zum Sprechen. Wenn er keine gesprochene Eingabe erkannt, erzeugt er das Ergebnis „No match“ (Keine Übereinstimmung).

    Wenn der SpeechRecognizer auf einen Fehler stößt, gibt er das Ergebnis „Cancelled“ (Abgebrochen) aus. Der Code in der Anwendung zeigt dann die Fehlermeldung an. Die wahrscheinlichste Ursache ist ein falscher Schlüssel oder eine falsche Region in der Konfigurationsdatei.

## <a name="synthesize-speech"></a>Synthetisieren von Sprache

Ihre Anwendung für die sprechende Uhr akzeptiert gesprochene Eingaben, aber sie spricht nicht wirklich! Lassen Sie uns das beheben, indem wir Code zur Sprachsynthese hinzufügen.

1. Beachten Sie in der Funktion **Main** für Ihr Programm, dass der Code die Funktion **TellTime** verwendet, um dem Benutzer die aktuelle Zeit mitzuteilen.
2. Fügen Sie in der Funktion **TellTime** unter dem Kommentar **Configure speech synthesis** (Sprachsynthese konfigurieren) den folgenden Code ein, um einen **SpeechSynthesizer**-Client zu erstellen, der zum Generieren von Sprachausgaben verwendet werden kann:

    **C#**
    
    ```C#
    // Configure speech synthesis
    speechConfig.SpeechSynthesisVoiceName = "en-GB-RyanNeural";
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    ```
    
    **Python**
    
    ```Python
    # Configure speech synthesis
    speech_config.speech_synthesis_voice_name = "en-GB-RyanNeural"
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    ```
    
    > **Hinweis**: *Die Standardaudiokonfiguration verwendet das Standardaudiogerät des Systems für die Ausgabe, sodass Sie nicht explizit eine **AudioConfig** angeben müssen. Wenn Sie die Audioausgabe in eine Datei umleiten müssen, können Sie dazu eine **AudioConfig** mit einem Dateipfad verwenden.*

3. Fügen Sie in der Funktion **TellTime** unter dem Kommentar **Synthesize spoken output** (Gesprochene Ausgabe synthetisieren) den folgenden Code ein, um die gesprochene Ausgabe zu generieren. Achten Sie darauf, dass Sie den Code am Ende der Funktion, die die Antwort ausgibt, nicht ersetzen:

    **C#**
    
    ```C#
    // Synthesize spoken output
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(responseText);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```
    
    **Python**
    
    ```Python
    # Synthesize spoken output
    speak = speech_synthesizer.speak_text_async(response_text).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

4. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

5. Wenn Sie dazu aufgefordert werden, sprechen Sie deutlich in das Mikrofon und sagen Sie „What time is it?“ (Wie spät ist es?). Das Programm sollte eine Sprachausgabe erzeugen und Ihnen die Uhrzeit mitteilen.

## <a name="use-a-different-voice"></a>Verwenden einer anderen Stimme

Ihre Anwendung für die sprechende Uhr verwendet eine Standardstimme, die Sie ändern können. Der Speech-Dienst unterstützt eine Reihe von *Standardstimmen* sowie eher menschenähnliche *neuronale* Stimmen. Sie können auch *benutzerdefinierte* Stimmen erstellen.

> **Hinweis**: Eine Liste der neuronalen und Standardstimmen finden Sie unter [Unterstützung von Sprachen und Stimmen](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech) in der Dokumentation des Speech-Diensts.

1. Ändern Sie in der Funktion **TellTime** unter dem Kommentar **Configure speech synthesis** (Sprachsynthese konfigurieren) den Code wie folgt, um eine alternative Stimme anzugeben, bevor Sie den **SpeechSynthesizer**-Client erstellen:

   **C#**

    ```C#
    // Configure speech synthesis
    speechConfig.SpeechSynthesisVoiceName = "en-GB-LibbyNeural"; // add this
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    ```
    
    **Python**
    
    ```Python
    # Configure speech synthesis
    speech_config.speech_synthesis_voice_name = 'en-GB-LibbyNeural' # add this
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    ```

2. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. Wenn Sie dazu aufgefordert werden, sprechen Sie deutlich in das Mikrofon und sagen Sie „What time is it?“ (Wie spät ist es?). Das Programm sollte mit der angegebenen Stimme sprechen und Ihnen die Uhrzeit mitteilen.

## <a name="use-speech-synthesis-markup-language"></a>Verwenden von Sprachsynthese-Markupsprache

Mit der Speech Synthesis Markup Language (SSML) können Sie die Art und Weise, wie Ihre Sprache synthetisiert wird, mithilfe eines XML-basierten Formats anpassen.

1. Ersetzen Sie in der Funktion **TellTime** den gesamten aktuellen Code unter dem Kommentar **Synthetize spoken output** (Gesprochene Ausgabe synthetisieren) durch den folgenden Code (belassen Sie den Code unter dem Kommentar **Print the response** (Antwort ausgeben)):

   **C#**

    ```C#
    // Synthesize spoken output
    string responseSsml = $@"
        <speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
            <voice name='en-GB-LibbyNeural'>
                {responseText}
                <break strength='weak'/>
                Time to end this lab!
            </voice>
        </speak>";
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakSsmlAsync(responseSsml);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```

    **Python**
    
    ```Python
    # Synthesize spoken output
    responseSsml = " \
        <speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'> \
            <voice name='en-GB-LibbyNeural'> \
                {} \
                <break strength='weak'/> \
                Time to end this lab! \
            </voice> \
        </speak>".format(response_text)
    speak = speech_synthesizer.speak_ssml_async(responseSsml).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

2. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. Wenn Sie dazu aufgefordert werden, sprechen Sie deutlich in das Mikrofon und sagen Sie „What time is it?“ (Wie spät ist es?). Das Programm sollte in der Stimme sprechen, die in der SSML angegeben ist (und damit die in der SpeechConfig angegebene Stimme außer Kraft setzen), und Ihnen die Uhrzeit mitteilen und nach einer Pause sagen, dass es Zeit ist, dieses Lab zu beenden – was auch der Fall ist!

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur Verwendung der APIs für **Spracherkennung** und **Sprachsynthese** finden Sie in der [Dokumentation zur Spracherkennung](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) und der [Dokumentation zur Sprachsynthese](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-text-to-speech).
