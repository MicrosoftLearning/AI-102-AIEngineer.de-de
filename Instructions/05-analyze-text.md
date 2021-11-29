---
lab:
  title: Analysieren von Text
  module: Module 3 - Getting Started with Natural Language Processing
ms.openlocfilehash: eb4e413e64c322f182cd5eadaff56bb880636e2f
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625874"
---
# <a name="analyze-text"></a>Analysieren von Text

Die **Textanalyse-API** ist ein Cognitive Service, der die Analyse von Text unterstützt, einschließlich Sprachenerkennung, Standpunktanalyse, Schlüsselbegriffserkennung und Entitätserkennung.

Angenommen, ein Reiseunternehmen möchte Hotelbewertungen verarbeiten, die auf der Website des Unternehmens abgegeben wurden. Mithilfe der Textanalyse-API können sie die Sprache bestimmen, in der jede der Bewertungen geschrieben wurde, den Standpunkt (positiv, neutral oder negativ) der Bewertungen, Schlüsselbegriffe, die auf die in der Bewertung behandelten Hauptthemen hinweisen können, sowie benannte Entitäten wie Orte, Sehenswürdigkeiten oder Personen, die in den Bewertungen erwähnt werden.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** noch nicht in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, führen Sie die folgenden Schritte aus, um dies zu tun. Öffnen Sie andernfalls den geklonten Ordner in Visual Studio Code.

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
5. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. Sie benötigen den Endpunkt und einen der Schlüssel von dieser Seite im nächsten Verfahren.

## <a name="prepare-to-use-the-text-analytics-sdk"></a>Vorbereiten der Verwendung des Textanalyse-SDK

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die das Textanalyse-SDK zum Analysieren von Hotelbewertungen verwendet.

> **Hinweis**: Sie können auswählen, das SDK für **C#** oder **Python** zu verwenden. Führen Sie in den folgenden Schritten die Aktionen aus, die für Ihre bevorzugte Sprache geeignet sind.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **05-analyze-text**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **text-analysis**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Textanalyse-SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:
    
    **C#**
    
    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.0.0
    ```
    
    **Python**
    
    ```
    pip install azure-ai-textanalytics==5.0.0
    ```
    
3. Zeigen Sie den Inhalt des Ordners **text-analysis** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass sie dem **Endpunkt** und einem Authentifizierungs **schlüssel** für Ihre Cognitive Services-Ressource entsprechen. Speichern Sie die Änderungen.

4. Beachten Sie, dass der Ordner **text-analysis** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: text-analysis.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Textanalyse-SDK verwenden zu können:

    **C#**
    
    ```C#
    // import namespaces
    using Azure;
    using Azure.AI.TextAnalytics;
    ```

    **Python**

    ```Python
    # import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.textanalytics import TextAnalyticsClient
    ```

5. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden des Cognitive Services-Endpunkts und -Schlüssels aus der Konfigurationsdatei bereitgestellt wurde. Suchen Sie dann den Kommentar **Create client using endpoint and key** (Client mit Endpunkt und Schlüssel erstellen), und fügen Sie den folgenden Code hinzu, um einen Client für die Textanalyse-API zu erstellen:

    **C#**

    ```C#
    // Create client using endpoint and key
    AzureKeyCredential credentials = new AzureKeyCredential(cogSvcKey);
    Uri endpoint = new Uri(cogSvcEndpoint);
    TextAnalyticsClient CogClient = new TextAnalyticsClient(endpoint, credentials);
    ```

    **Python**

    ```Python
    # Create client using endpoint and key
    credential = AzureKeyCredential(cog_key)
    cog_client = TextAnalyticsClient(endpoint=cog_endpoint, credential=credential)
    ```

6. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **text-analysis** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python text-analysis.py
    ```

6. Beobachten Sie die Ausgabe, da der Code ohne Fehler ausgeführt werden sollte, wobei der Inhalt jeder Bewertungstextdatei im Ordner **reviews** angezeigt wird. Die Anwendung erstellt erfolgreich einen Client für die Textanalyse-API, nutzt ihn aber nicht. Dies wird im nächsten Verfahren behoben.

## <a name="detect-language"></a>Sprache erkennen

Nachdem Sie nun einen Client für die Textanalyse-API erstellt haben, verwenden wir ihn, um die Sprache zu erkennen, in der die jeweilige Bewertung geschrieben ist.

1. Suchen Sie in der **Main**-Funktion für Ihr Programm den Kommentar **Get language** (Sprache erhalten). Fügen Sie dann unter diesem Kommentar den Code hinzu, der zum Erkennen der Sprache im jeweiligen Bewertungsdokument erforderlich ist:

    **C#**
    
    ```C
    // Get language
    DetectedLanguage detectedLanguage = CogClient.DetectLanguage(text);
    Console.WriteLine($"\nLanguage: {detectedLanguage.Name}");
    ```

    **Python**
    
    ```Python
    # Get language
    detectedLanguage = cog_client.detect_language(documents=[text])[0]
    print('\nLanguage: {}'.format(detectedLanguage.primary_language.name))
    ```

    > **Hinweis**: *In diesem Beispiel wird jede Bewertung einzeln analysiert, was zu einem separaten Aufruf des Diensts für jede Datei führt. Ein alternativer Ansatz besteht in der Erstellung einer Sammlung von Dokumenten und deren Übergeben an den Dienst in einem einzigen Aufruf. Bei beiden Ansätzen besteht die Antwort des Diensts aus einer Sammlung von Dokumenten. Dies ist der Grund, warum im obigen Python-Code der Index des ersten (und einzigen) Dokuments in der Antwort ([0]) angegeben ist.*

6. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **text-analysis** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python text-analysis.py
    ```

7. Sehen Sie sich die Ausgabe an, und beachten Sie, dass dieses Mal die Sprache für jede Bewertung identifiziert wird.

## <a name="evaluate-sentiment"></a>Auswerten des Standpunkts

*Standpunktanalyse* ist eine häufig verwendete Methode zur Klassifizierung von Text als *positiv* oder *negativ* (oder möglicherweise als *neutral* oder *gemischt*). Sie wird häufig verwendet, um Beiträge in sozialen Medien, Produktbewertungen und andere Elemente zu analysieren, bei denen der Standpunkt des Texts nützliche Erkenntnisse liefern kann.

1. Suchen Sie in der **Main**-Funktion für Ihr Programm den Kommentar **Get sentiment** (Standpunkt erhalten). Fügen Sie dann unter diesem Kommentar den Code hinzu, der zum Erkennen des Standpunkts im jeweiligen Bewertungsdokument erforderlich ist:

    **C#**
    
    ```C
    // Get sentiment
    DocumentSentiment sentimentAnalysis = CogClient.AnalyzeSentiment(text);
    Console.WriteLine($"\nSentiment: {sentimentAnalysis.Sentiment}");
    ```

    **Python**
    
    ```Python
    # Get sentiment
    sentimentAnalysis = cog_client.analyze_sentiment(documents=[text])[0]
    print("\nSentiment: {}".format(sentimentAnalysis.sentiment))
    ```

2. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **text-analysis** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```

    **Python**

    ```
    python text-analysis.py
    ```

3. Beobachten Sie die Ausgabe, und beachten Sie, dass der Standpunkt der Bewertungen erkannt wird.

## <a name="identify-key-phrases"></a>Identifizieren von Schlüsselbegriffen

Es kann hilfreich sein, Schlüsselbegriffe in einer Textsammlung zu identifizieren, um die wichtigsten Themen zu bestimmen, die behandelt werden.

1. Suchen Sie in der **Main**-Funktion für Ihr Programm den Kommentar **Get key phrases** (Schlüsselbegriffe erhalten). Fügen Sie dann unter diesem Kommentar den Code hinzu, der zum Erkennen der Schlüsselbegriffe im jeweiligen Bewertungsdokument erforderlich ist:

    **C#**

    ```C
    // Get key phrases
    KeyPhraseCollection phrases = CogClient.ExtractKeyPhrases(text);
    if (phrases.Count > 0)
    {
        Console.WriteLine("\nKey Phrases:");
        foreach(string phrase in phrases)
        {
            Console.WriteLine($"\t{phrase}");
        }
    }
    ```
    
    **Python**
    
    ```Python
    # Get key phrases
    phrases = cog_client.extract_key_phrases(documents=[text])[0].key_phrases
    if len(phrases) > 0:
        print("\nKey Phrases:")
        for phrase in phrases:
            print('\t{}'.format(phrase))
    ```

2. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **text-analysis** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-analysis.py
    ```

3. Sehen Sie sich die Ausgabe an, und beachten Sie, dass jedes Dokument Schlüsselbegriffe enthält, die Einblicke in die Thematik der jeweiligen Bewertung geben.

## <a name="extract-entities"></a>Extrahieren von Entitäten

Häufig werden in Dokumenten oder anderen Textsammlungen Personen, Orte, Zeiträume oder andere Entitäten erwähnt. Die Textanalyse-API kann mehrere Kategorien (und Unterkategorien) einer Entität in Ihrem Text erkennen.

1. Suchen Sie in der **Main**-Funktion für Ihr Programm den Kommentar **Get entities** (Entitäten erhalten). Fügen Sie dann unter diesem Kommentar den Code hinzu, der zum Identifizieren von Entitäten erforderlich ist, die in der jeweiligen Bewertung erwähnt werden:

    **C#**
    
    ```C
    // Get entities
    CategorizedEntityCollection entities = CogClient.RecognizeEntities(text);
    if (entities.Count > 0)
    {
        Console.WriteLine("\nEntities:");
        foreach(CategorizedEntity entity in entities)
        {
            Console.WriteLine($"\t{entity.Text} ({entity.Category})");
        }
    }
    ```

    **Python**
    
    ```Python
    # Get entities
    entities = cog_client.recognize_entities(documents=[text])[0].entities
    if len(entities) > 0:
        print("\nEntities")
        for entity in entities:
            print('\t{} ({})'.format(entity.text, entity.category))
    ```

2. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **text-analysis** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-analysis.py
    ```

3. Beobachten Sie die Ausgabe, und beachten Sie die Entitäten, die im Text erkannt wurden.

## <a name="extract-linked-entities"></a>Extrahieren verknüpfter Entitäten

Zusätzlich zu kategorisierten Entitäten kann die Textanalyse-API Entitäten erkennen, für die bekannte Links zu Datenquellen wie Wikipedia verfügbar sind.

1. Suchen Sie in der **Main**-Funktion für Ihr Programm den Kommentar **Get linked entities** (Verknüpfte Entitäten erhalten). Fügen Sie dann unter diesem Kommentar den Code hinzu, der zum Identifizieren von verknüpften Entitäten erforderlich ist, die in der jeweiligen Bewertung erwähnt werden:

    **C#**
    
    ```C
    // Get linked entities
    LinkedEntityCollection linkedEntities = CogClient.RecognizeLinkedEntities(text);
    if (linkedEntities.Count > 0)
    {
        Console.WriteLine("\nLinks:");
        foreach(LinkedEntity linkedEntity in linkedEntities)
        {
            Console.WriteLine($"\t{linkedEntity.Name} ({linkedEntity.Url})");
        }
    }
    ```

    **Python**
    
    ```Python
    # Get linked entities
    entities = cog_client.recognize_linked_entities(documents=[text])[0].entities
    if len(entities) > 0:
        print("\nLinks")
        for linked_entity in entities:
            print('\t{} ({})'.format(linked_entity.name, linked_entity.url))
    ```

2. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **text-analysis** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-analysis.py
    ```

3. Sehen Sie sich die Ausgabe an, und beachten Sie die verknüpften Entitäten, die identifiziert werden.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur Verwendung des **Textanalyse**-Diensts finden Sie in der [Dokumentation zur Textanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).
