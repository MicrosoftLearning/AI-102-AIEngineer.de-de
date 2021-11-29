---
lab:
  title: Übersetzen von Text
  module: Module 3 - Getting Started with Natural Language Processing
ms.openlocfilehash: 4ca4394ce5d9456abeabbdded1ee219f271f284e
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625765"
---
# <a name="translate-text"></a>Übersetzen von Text

Der **Textübersetzungs**-Dienst ist ein Cognitive Service, mit dem Sie Text zwischen Sprachen übersetzen können.

Angenommen, ein Reiseunternehmen möchte Hotelbewertungen untersuchen, die auf der Website des Unternehmens abgegeben wurden. Dabei wird Englisch als Sprache für die Analyse standardisiert. Mithilfe des Textübersetzungs-Diensts können Sie die Sprache ermitteln, in der die jeweilige Bewertung geschrieben ist, und wenn sie nicht bereits in Englisch ist, sie aus jeder Ausgangssprache, in der sie geschrieben wurde, ins Englische übersetzen.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** bereits in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es jetzt zu klonen.

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

## <a name="prepare-to-use-the-translator-service"></a>Vorbereiten der Verwendung des Textübersetzungs-Diensts

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die die Textübersetzungs-REST-API zum Übersetzen von Hotelbewertungen verwendet.

> **Hinweis**: Sie können auswählen, die API von **C#** oder **Python** zu verwenden. Führen Sie in den folgenden Schritten die Aktionen aus, die für Ihre bevorzugte Sprache geeignet sind.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **06-translate-text**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Zeigen Sie den Inhalt des Ordners **text-translation** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die Konfigurationswerte, die sie enthält, um einen Authentifizierungs **schlüssel** für Ihre Cognitive Services-Ressource sowie den **Standort** der Bereitstellung (<u>nicht</u> den Endpunkt) einzuschließen. Sie sollten beide Werte von der Seite **Schlüssel und Endpunkt** für Ihre Cognitive Services-Ressource kopieren. Speichern Sie die Änderungen.
3. Beachten Sie, dass der Ordner **text-translation** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: text-translation.py

    Öffnen Sie die Codedatei, und untersuchen Sie den darin enthaltenen Code.

4. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden des Cognitive Services-Schlüssels und der Region aus der Konfigurationsdatei bereitgestellt wurde. Der Endpunkt für den Übersetzungsdienst wird ebenfalls in Ihrem Code angegeben.
5. Klicken Sie mit der rechten Maustaste auf den Ordner **text-translation**, öffnen Sie ein integriertes Terminal, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-translation.py
    ```

6. Beobachten Sie die Ausgabe, da der Code ohne Fehler ausgeführt werden sollte, wobei der Inhalt jeder Bewertungstextdatei im Ordner **reviews** angezeigt wird. Die Anwendung nutzt derzeit nicht den Textübersetzungs-Dienst. Dies wird im nächsten Verfahren behoben.

## <a name="detect-language"></a>Sprache erkennen

Der Textübersetzungs-Dienst kann die Ausgangssprache von zu übersetzendem Text automatisch erkennen, ermöglicht es Ihnen aber auch, die Sprache, in der Text geschrieben ist, explizit zu erkennen.

1. Suchen Sie in Ihrer Codedatei nach der **GetLanguage**-Funktion, die derzeit für alle Textwerte „en“ zurückgibt.
2. Fügen Sie in der **GetLanguage**-Funktion unter dem Kommentar **Use the Translator detect function** (Textübersetzungsfunktion detect verwenden) den folgenden Code hinzu, um die REST-API der Textübersetzung zu verwenden, um die Sprache des angegebenen Texts zu erkennen. Achten Sie dabei darauf, den Code am Ende der Funktion, die die Sprache zurückgibt, nicht zu ersetzen:

**C#**

```C#
// Use the Translator detect function
object[] body = new object[] { new { Text = text } };
var requestBody = JsonConvert.SerializeObject(body);
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Build the request
        string path = "/detect?api-version=3.0";
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(translatorEndpoint + path);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", cogSvcKey);
        request.Headers.Add("Ocp-Apim-Subscription-Region", cogSvcRegion);

        // Send the request and get response
        HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
        // Read response as a string
        string responseContent = await response.Content.ReadAsStringAsync();

        // Parse JSON array and get language
        JArray jsonResponse = JArray.Parse(responseContent);
        language = (string)jsonResponse[0]["language"]; 
    }
}
```

**Python**

```Python
# Use the Translator detect function
path = '/detect'
url = translator_endpoint + path

# Build the request
params = {
    'api-version': '3.0'
}

headers = {
'Ocp-Apim-Subscription-Key': cog_key,
'Ocp-Apim-Subscription-Region': cog_region,
'Content-type': 'application/json'
}

body = [{
    'text': text
}]

# Send the request and get response
request = requests.post(url, params=params, headers=headers, json=body)
response = request.json()

# Parse JSON array and get language
language = response[0]["language"]
```

3. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **text-translation** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-translation.py
    ```

4. Sehen Sie sich die Ausgabe an, und beachten Sie, dass dieses Mal die Sprache für jede Bewertung identifiziert wird.

## <a name="translate-text"></a>Text übersetzen

Nachdem Ihre Anwendung nun die Sprache bestimmen kann, in der Bewertungen geschrieben werden, können Sie den Textübersetzungs-Dienst verwenden, um alle nicht englischsprachigen Bewertungen ins Englische zu übersetzen.

1. Suchen Sie in Ihrer Codedatei nach der **Translate**-Funktion, die derzeit für alle Textwerte eine leere Zeichenfolge zurückgibt.
2. Fügen Sie in der **Translate**-Funktion unter dem Kommentar **Use the Translator translate function** (Textübersetzungsfunktion translate verwenden) den folgenden Code hinzu, um die REST-API der Textübersetzung zu verwenden, um den angegebenen Text aus seiner Quellsprache ins Englische zu übersetzen. Achten Sie dabei darauf, den Code am Ende der Funktion, die die Übersetzung zurückgibt, nicht zu ersetzen:

**C#**

```C#
// Use the Translator translate function
object[] body = new object[] { new { Text = text } };
var requestBody = JsonConvert.SerializeObject(body);
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Build the request
        string path = "/translate?api-version=3.0&from=" + sourceLanguage + "&to=en" ;
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(translatorEndpoint + path);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", cogSvcKey);
        request.Headers.Add("Ocp-Apim-Subscription-Region", cogSvcRegion);

        // Send the request and get response
        HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
        // Read response as a string
        string responseContent = await response.Content.ReadAsStringAsync();

        // Parse JSON array and get translation
        JArray jsonResponse = JArray.Parse(responseContent);
        translation = (string)jsonResponse[0]["translations"][0]["text"];  
    }
}
```

**Python**

```Python
# Use the Translator translate function
path = '/translate'
url = translator_endpoint + path

# Build the request
params = {
    'api-version': '3.0',
    'from': source_language,
    'to': ['en']
}

headers = {
    'Ocp-Apim-Subscription-Key': cog_key,
    'Ocp-Apim-Subscription-Region': cog_region,
    'Content-type': 'application/json'
}

body = [{
    'text': text
}]

# Send the request and get response
request = requests.post(url, params=params, headers=headers, json=body)
response = request.json()

# Parse JSON array and get translation
translation = response[0]["translations"][0]["text"]
```

3. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **text-translation** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-translation.py
    ```

4. Sehen Sie sich die Ausgabe an, und beachten Sie, dass nicht englischsprachige Bewertungen ins Englische übersetzt sind.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zum **Textübersetzungs**-Dienst finden Sie in der [Dokumentation zur Textübersetzung](https://docs.microsoft.com/azure/cognitive-services/translator/).
