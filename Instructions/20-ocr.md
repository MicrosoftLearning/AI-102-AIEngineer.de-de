---
lab:
  title: Lesen von Text in Bildern
  module: Module 11 - Reading Text in Images and Documents
ms.openlocfilehash: 0dc45d60e307769ebfde165201b97c4a3ff49675
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625773"
---
# <a name="read-text-in-images"></a>Lesen von Text in Bildern

Die optische Zeichenerkennung (OCR) ist eine Teilmenge des maschinellen Sehens, die sich mit dem Lesen von Text in Bildern und Dokumenten beschäftigt. Der Dienst für **maschinelles Sehen** bietet zwei APIs zum Lesen von Text, die Sie in dieser Übung kennenlernen werden.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das noch nicht erledigt haben, müssen Sie das Coderepository für diesen Kurs klonen:

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
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (Wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)* .
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0.
3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
5. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. Sie benötigen den Endpunkt und einen der Schlüssel von dieser Seite im nächsten Verfahren.

## <a name="prepare-to-use-the-computer-vision-sdk"></a>Vorbereiten der Verwendung des SDK für maschinelles Sehen

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die das SDK für maschinelles Sehen zum Lesen von Text verwendet.

> **Hinweis**: Sie können auswählen, ob Sie das SDK für **C#** oder **Python** verwenden möchten. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **20-ocr**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **read-text**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das SDK-Paket für maschinelles Sehen, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-computervision==0.7.0
```

3. Zeigen Sie den Inhalt des Ordners **read-text** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass sie dem **Endpunkt** und einem Authentifizierungs **schlüssel** für Ihre Cognitive Services-Ressource entsprechen. Speichern Sie die Änderungen.
4. Beachten Sie, dass der Ordner **read-text** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: read-text.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das SDK für maschinelles Sehen verwenden zu können:

**C#**

```C#
// import namespaces
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
```

**Python**

```Python
# import namespaces
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes
from msrest.authentication import CognitiveServicesCredentials
```

5. Beachten Sie in der Codedatei für Ihre Clientanwendung in der Funktion **Main**, dass der Code zum Laden der Konfigurationseinstellungen bereitgestellt wurde. Suchen Sie dann den Kommentar **Authenticate Computer Vision client** (Client für maschinelles Sehen authentifizieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um ein Clientobjekt für maschinelles Sehen zu erstellen und zu authentifizieren:

**C#**

```C#
// Authenticate Computer Vision client
ApiKeyServiceClientCredentials credentials = new ApiKeyServiceClientCredentials(cogSvcKey);
cvClient = new ComputerVisionClient(credentials)
{
    Endpoint = cogSvcEndpoint
};
```

**Python**

```Python
# Authenticate Computer Vision client
credential = CognitiveServicesCredentials(cog_key) 
cv_client = ComputerVisionClient(cog_endpoint, credential)
```
    
## <a name="use-the-ocr-api"></a>Verwenden der OCR-API

Die **OCR**-API ist eine API zur optischen Zeichenerkennung, die für das Lesen kleiner bis mittlerer Mengen gedruckten Texts in Bildern im Format *JPG*, *PNG*, *GIF* und *BMP* optimiert ist. Sie unterstützt eine Vielzahl von Sprachen und kann nicht nur den Text im Bild lesen, sondern auch die Ausrichtung der einzelnen Textbereiche bestimmen und Informationen über den Drehwinkel des Texts im Verhältnis zum Bild zurückgeben.

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **1** auswählt. Dieser Code ruft die Funktion **GetTextOcr** auf und übergibt den Pfad zu einer Bilddatei.
2. Öffnen Sie im Ordner **read-text/images** das Bild **Lincoln.jpg**, um das Bild anzuzeigen, das Ihr Code verarbeiten wird.
3. Suchen Sie in der Codedatei die Funktion **GetTextOcr**, und fügen Sie unter dem vorhandenen Code, der eine Meldung auf der Konsole ausgibt, den folgenden Code ein:

**C#**

```C#
// Use OCR API to read text in image
using (var imageData = File.OpenRead(imageFile))
{    
    var ocrResults = await cvClient.RecognizePrintedTextInStreamAsync(detectOrientation:false, image:imageData);

    // Prepare image for drawing
    Image image = Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Magenta, 3);

    foreach(var region in ocrResults.Regions)
    {
        foreach(var line in region.Lines)
        {
            // Show the position of the line of text
            int[] dims = line.BoundingBox.Split(",").Select(int.Parse).ToArray();
            Rectangle rect = new Rectangle(dims[0], dims[1], dims[2], dims[3]);
            graphics.DrawRectangle(pen, rect);

            // Read the words in the line of text
            string lineText = "";
            foreach(var word in line.Words)
            {
                lineText += word.Text + " ";
            }
            Console.WriteLine(lineText.Trim());
        }
    }

    // Save the image with the text locations highlighted
    String output_file = "ocr_results.jpg";
    image.Save(output_file);
    Console.WriteLine("Results saved in " + output_file);
}
```

**Python**

```Python
# Use OCR API to read text in image
with open(image_file, mode="rb") as image_data:
    ocr_results = cv_client.recognize_printed_text_in_stream(image_data)

# Prepare image for drawing
fig = plt.figure(figsize=(7, 7))
img = Image.open(image_file)
draw = ImageDraw.Draw(img)

# Process the text line by line
for region in ocr_results.regions:
    for line in region.lines:

        # Show the position of the line of text
        l,t,w,h = list(map(int, line.bounding_box.split(',')))
        draw.rectangle(((l,t), (l+w, t+h)), outline='magenta', width=5)

        # Read the words in the line of text
        line_text = ''
        for word in line.words:
            line_text += word.text + ' '
        print(line_text.rstrip())

# Save the image with the text locations highlighted
plt.axis('off')
plt.imshow(img)
outputfile = 'ocr_results.jpg'
fig.savefig(outputfile)
print('Results saved in', outputfile)
```

4. Untersuchen Sie den Code, den Sie der **GetTextOcr**-Funktion hinzugefügt haben. Er erkennt Bereiche mit gedrucktem Text in einer Bilddatei und extrahiert für jeden Bereich die Textzeilen und hebt deren Position im Bild hervor. Anschließend werden die Wörter in jeder Zeile extrahiert und ausgegeben.
5. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **read-text** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

**C#**

```
dotnet run
```

*Die C#-Ausgabe kann Warnungen zu asynchronen Funktionen anzeigen, die jetzt den Operator **await** verwenden. Sie können diese ignorieren.*

**Python**

```
python read-text.py
```

6. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die der aus dem Bild extrahierte Text ist.
7. Zeigen Sie die Datei **ocr_results.jpg** an, die im selben Ordner wie Ihre Codedatei generiert wird, um die kommentierten Textzeilen im Bild anzuzeigen.

## <a name="use-the-read-api"></a>Verwenden der Lese-API

Die **Lese**-API verwendet ein neueres Texterkennungsmodell als die OCR-API und erzielt bessere Ergebnisse bei größeren Bildern, die viel Text enthalten. Sie unterstützt auch die Textextraktion aus *PDF*-Dateien und kann sowohl gedruckten Text (in mehreren Sprachen) als auch handschriftlichen Text (in Englisch) erkennen.

Die **Lese-API** verwendet ein asynchrones Betriebsmodell, bei dem eine Anforderung zum Starten der Texterkennung übermittelt wird. Die von der Anforderung zurückgegebene Vorgangs-ID kann anschließend verwendet werden, um den Fortschritt zu überprüfen und Ergebnisse abzurufen.

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **2** auswählt. Dieser Code ruft die Funktion **GetTextRead** auf und übergibt den Pfad zu einer PDF-Dokumentendatei.
2. Klicken Sie im Ordner **read-text/images** mit der rechten Maustaste auf **Rom.pdf**, und wählen Sie **Reveal in File Explorer** (Im Datei-Explorer anzeigen) aus. Öffnen Sie dann im Datei-Explorer die PDF-Datei, um sie anzuzeigen.
3. Suchen Sie in der Codedatei in Visual Studio Code die Funktion **GetTextRead**, und fügen Sie unter dem vorhandenen Code, der eine Meldung auf der Konsole ausgibt, den folgenden Code ein:

**C#**

```C#
// Use Read API to read text in image
using (var imageData = File.OpenRead(imageFile))
{    
    var readOp = await cvClient.ReadInStreamAsync(imageData);

    // Get the async operation ID so we can check for the results
    string operationLocation = readOp.OperationLocation;
    string operationId = operationLocation.Substring(operationLocation.Length - 36);

    // Wait for the asynchronous operation to complete
    ReadOperationResult results;
    do
    {
        Thread.Sleep(1000);
        results = await cvClient.GetReadResultAsync(Guid.Parse(operationId));
    }
    while ((results.Status == OperationStatusCodes.Running ||
            results.Status == OperationStatusCodes.NotStarted));

    // If the operation was successfuly, process the text line by line
    if (results.Status == OperationStatusCodes.Succeeded)
    {
        var textUrlFileResults = results.AnalyzeResult.ReadResults;
        foreach (ReadResult page in textUrlFileResults)
        {
            foreach (Line line in page.Lines)
            {
                Console.WriteLine(line.Text);
            }
        }
    }
}  
```

**Python**

```Python
# Use Read API to read text in image
with open(image_file, mode="rb") as image_data:
    read_op = cv_client.read_in_stream(image_data, raw=True)

    # Get the async operation ID so we can check for the results
    operation_location = read_op.headers["Operation-Location"]
    operation_id = operation_location.split("/")[-1]

    # Wait for the asynchronous operation to complete
    while True:
        read_results = cv_client.get_read_result(operation_id)
        if read_results.status not in [OperationStatusCodes.running, OperationStatusCodes.not_started]:
            break
        time.sleep(1)

    # If the operation was successfuly, process the text line by line
    if read_results.status == OperationStatusCodes.succeeded:
        for page in read_results.analyze_result.read_results:
            for line in page.lines:
                print(line.text)
```
    
4. Untersuchen Sie den Code, den Sie der **GetTextRead**-Funktion hinzugefügt haben. Er übermittelt eine Anforderung für einen Lesevorgang und überprüft dann wiederholt den Status, bis der Vorgang abgeschlossen ist. Wenn dies erfolgreich war, verarbeitet der Code die Ergebnisse, indem er jede Seite und dann jede Zeile durchläuft.
5. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **read-text** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

**C#**

```
dotnet run
```

**Python**

```
python read-text.py
```

6. Wenn Sie dazu aufgefordert werden, geben Sie **2** ein, und beobachten Sie die Ausgabe, die der aus dem Dokument extrahierte Text ist.

## <a name="read-handwritten-text"></a>Lesen von handschriftlichem Text

Zusätzlich zum gedruckten Text kann die **Lese-API** handschriftlichen Text in englischer Sprache extrahieren.

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **3** auswählt. Dieser Code ruft die Funktion **GetTextRead** auf und übergibt den Pfad zu einer Bilddatei.
2. Öffnen Sie im Ordner **read-text/images** das Bild **Note.jpg**, um das Bild anzuzeigen, das Ihr Code verarbeiten wird.
3. Geben Sie das integrierte Terminal für den Ordner **read-text** zurück und dann den folgenden Befehl zur Ausführung des Programms ein:

**C#**

```
dotnet run
```

**Python**

```
python read-text.py
```

4. Wenn Sie dazu aufgefordert werden, geben Sie **3** ein, und beobachten Sie die Ausgabe, die der aus dem Dokument extrahierte Text ist.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur Verwendung des Diensts für **maschinelles Sehen** zum Lesen von Text finden Sie in der [Dokumentation zum maschinelles Sehen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text).
