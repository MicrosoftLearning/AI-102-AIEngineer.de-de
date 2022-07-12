---
lab:
  title: Analysieren von Bildern mit dem Dienst für maschinelles Sehen
  module: Module 8 - Getting Started with Computer Vision
ms.openlocfilehash: f2ee18ff682d53e9fd554749ed2b9cbaa9b03611
ms.sourcegitcommit: 7191e53bc33cda92e710d957dde4478ee2496660
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2022
ms.locfileid: "147041665"
---
# <a name="analyze-images-with-computer-vision"></a>Analysieren von Bildern mit dem Dienst für maschinelles Sehen

Das maschinelle Sehen ist eine Fähigkeit der künstlichen Intelligenz, die es Softwaresystemen ermöglicht, visuelle Eingaben durch die Analyse von Bildern zu interpretieren. In Microsoft Azure bietet der kognitive Dienst für **maschinelles Sehen** vorgefertigte Modelle für gängige Aufgaben für maschinelles Sehen, darunter die Analyse von Bildern, um Beschriftungen und Tags vorzuschlagen, die Erkennung von gewöhnlichen Objekten, Wahrzeichen, Prominenten, Marken und das Vorhandensein von Inhalten für Erwachsene. Sie können den Dienst für maschinelles Sehen auch nutzen, um Bildfarben und -formate zu analysieren und „intelligent zugeschnittene“ Miniaturbilder zu erstellen.

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
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0
3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
5. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. Sie benötigen den Endpunkt und einen der Schlüssel von dieser Seite im nächsten Verfahren.

## <a name="prepare-to-use-the-computer-vision-sdk"></a>Vorbereiten der Verwendung des SDK für maschinelles Sehen

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die das SDK für maschinelles Sehen zum Analysieren von Bildern verwendet.

> **Hinweis**: Sie können auswählen, ob Sie das SDK für **C#** oder **Python** verwenden möchten. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **15-computer-vision**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **image-analysis**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das SDK-Paket für maschinelles Sehen, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-computervision==0.7.0
```
    
3. Zeigen Sie den Inhalt des Ordners **image-analysis** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass sie dem **Endpunkt** und einem Authentifizierungs **schlüssel** für Ihre Cognitive Services-Ressource entsprechen. Speichern Sie die Änderungen.
4. Beachten Sie, dass der Ordner **image-analysis** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: image-analysis.py

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
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials
```
    
## <a name="view-the-images-you-will-analyze"></a>Anzeigen der zu analysierenden Bilder

In dieser Übung verwenden Sie den Dienst für maschinelles Sehen, um mehrere Bilder zu analysieren.

1. Erweitern Sie in Visual Studio Code den Ordner **image-analysis** und den darin enthaltenen Ordner **images**.
2. Wählen Sie nacheinander die einzelnen Bilddateien aus, um sie in Visual Studio Code anzuzeigen.

## <a name="analyze-an-image-to-suggest-a-caption"></a>Analysieren eines Bilds zum Vorschlagen einer Beschriftung

Nun können Sie das SDK verwenden, um den Dienst für maschinelles Sehen aufzurufen und ein Bild zu analysieren.

1. Beachten Sie in der Codedatei für Ihre Clientanwendung (**Program.cs** oder **image-analysis.py**) in der Funktion **Main**, dass der Code zum Laden der Konfigurationseinstellungen bereitgestellt wurde. Suchen Sie dann den Kommentar **Authenticate Computer Vision client** (Client für maschinelles Sehen authentifizieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um ein Clientobjekt für maschinelles Sehen zu erstellen und zu authentifizieren:

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

2. Beachten Sie in der Funktion **Main** unter dem soeben hinzugefügten Code, dass der Code den Pfad zu einer Bilddatei angibt und dann den Bildpfad an zwei andere Funktionen (**AnalyzeImage** und **GetThumbnail**) weitergibt. Diese Funktionen sind noch nicht vollständig implementiert.

3. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Specify features to be retrieved** (Abzurufende Features angeben) den folgenden Code ein:

**C#**

```C#
// Specify features to be retrieved
List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>()
{
    VisualFeatureTypes.Description,
    VisualFeatureTypes.Tags,
    VisualFeatureTypes.Categories,
    VisualFeatureTypes.Brands,
    VisualFeatureTypes.Objects,
    VisualFeatureTypes.Adult
};
```

**Python**

```Python
# Specify features to be retrieved
features = [VisualFeatureTypes.description,
            VisualFeatureTypes.tags,
            VisualFeatureTypes.categories,
            VisualFeatureTypes.brands,
            VisualFeatureTypes.objects,
            VisualFeatureTypes.adult]
```
    
4. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get image analysis** (Bildanalyse abrufen) den folgenden Code ein (einschließlich der Kommentare, die darauf hinweisen, wo Sie später weiteren Code hinzufügen werden):

**C#**

```C
// Get image analysis
using (var imageData = File.OpenRead(imageFile))
{    
    var analysis = await cvClient.AnalyzeImageInStreamAsync(imageData, features);

    // get image captions
    foreach (var caption in analysis.Description.Captions)
    {
        Console.WriteLine($"Description: {caption.Text} (confidence: {caption.Confidence.ToString("P")})");
    }

    // Get image tags


    // Get image categories


    // Get brands in the image


    // Get objects in the image


    // Get moderation ratings
    

}            
```

**Python**

```Python
# Get image analysis
with open(image_file, mode="rb") as image_data:
    analysis = cv_client.analyze_image_in_stream(image_data , features)

# Get image description
for caption in analysis.description.captions:
    print("Description: '{}' (confidence: {:.2f}%)".format(caption.text, caption.confidence * 100))

# Get image tags


# Get image categories 


# Get brands in the image


# Get objects in the image


# Get moderation ratings

```
    
5. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **image-analysis** zurück, und geben Sie den folgenden Befehl ein, um das Programm mit dem Argument **images/street.jpg** auszuführen:

**C#**

```
dotnet run images/street.jpg
```

**Python**

```
python image-analysis.py images/street.jpg
```
    
6. Beobachten Sie die Ausgabe, die eine vorgeschlagene Beschriftung für das Bild **street.jpg** enthalten sollte.
7. Führen Sie das Programm erneut aus, diesmal mit dem Argument **images/building.jpg**, um die Bildunterschrift anzuzeigen, die für das Bild **building.jpg** generiert wird.
8. Wiederholen Sie den vorherigen Schritt, um eine Beschriftung für die Datei **images/person.jpg** zu erstellen.

## <a name="get-suggested-tags-for-an-image"></a>Abrufen vorgeschlagener Tags für ein Bild

Es kann manchmal hilfreich sein, relevante *Tags* zu identifizieren, die Hinweise auf den Inhalt eines Bilds geben.

1. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get image tags** (Bildtags abrufen) den folgenden Code hinzu:

**C#**

```C
// Get image tags
if (analysis.Tags.Count > 0)
{
    Console.WriteLine("Tags:");
    foreach (var tag in analysis.Tags)
    {
        Console.WriteLine($" -{tag.Name} (confidence: {tag.Confidence.ToString("P")})");
    }
}
```

**Python**

```Python
# Get image tags
if (len(analysis.tags) > 0):
    print("Tags: ")
    for tag in analysis.tags:
        print(" -'{}' (confidence: {:.2f}%)".format(tag.name, tag.confidence * 100))
```

2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Beachten Sie dabei, dass neben der Bildbeschriftung auch eine Liste mit vorgeschlagenen Tags angezeigt wird.

## <a name="get-image-categories"></a>Abrufen von Bildkategorien

Der Dienst für maschinelles Sehen kann *Kategorien* für Bilder vorschlagen, und innerhalb jeder Kategorie kann er bekannte Wahrzeichen identifizieren.

1. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get image categories** (Bildkategorien abrufen) den folgenden Code hinzu:

**C#**

```C
// Get image categories
List<LandmarksModel> landmarks = new List<LandmarksModel> {};
Console.WriteLine("Categories:");
foreach (var category in analysis.Categories)
{
    // Print the category
    Console.WriteLine($" -{category.Name} (confidence: {category.Score.ToString("P")})");

    // Get landmarks in this category
    if (category.Detail?.Landmarks != null)
    {
        foreach (LandmarksModel landmark in category.Detail.Landmarks)
        {
            if (!landmarks.Any(item => item.Name == landmark.Name))
            {
                landmarks.Add(landmark);
            }
        }
    }
}

// If there were landmarks, list them
if (landmarks.Count > 0)
{
    Console.WriteLine("Landmarks:");
    foreach(LandmarksModel landmark in landmarks)
    {
        Console.WriteLine($" -{landmark.Name} (confidence: {landmark.Confidence.ToString("P")})");
    }
}

```

**Python**

```Python
# Get image categories
if (len(analysis.categories) > 0):
    print("Categories:")
    landmarks = []
    for category in analysis.categories:
        # Print the category
        print(" -'{}' (confidence: {:.2f}%)".format(category.name, category.score * 100))
        if category.detail:
            # Get landmarks in this category
            if category.detail.landmarks:
                for landmark in category.detail.landmarks:
                    if landmark not in landmarks:
                        landmarks.append(landmark)

    # If there were landmarks, list them
    if len(landmarks) > 0:
        print("Landmarks:")
        for landmark in landmarks:
            print(" -'{}' (confidence: {:.2f}%)".format(landmark.name, landmark.confidence * 100))

```
    
2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Achten Sie darauf, dass neben der Bildbeschriftung und den Tags auch eine Liste der vorgeschlagenen Kategorien sowie erkannte Wahrzeichen angezeigt werden (insbesondere beim Bild **building.jpg**).

## <a name="get-brands-in-an-image"></a>Abrufen von Marken in einem Image

Einige Marken sind visuell an Logos zu erkennen, auch wenn der Name der Marke nicht angezeigt wird. Der Dienst für maschinelles Sehen ist darauf trainiert, Tausende von bekannten Marken zu identifizieren.

1. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get brands in the image** (Marken im Bild abrufen) den folgenden Code hinzu:

**C#**

```C
// Get brands in the image
if (analysis.Brands.Count > 0)
{
    Console.WriteLine("Brands:");
    foreach (var brand in analysis.Brands)
    {
        Console.WriteLine($" -{brand.Name} (confidence: {brand.Confidence.ToString("P")})");
    }
}
```

**Python**

```Python
# Get brands in the image
if (len(analysis.brands) > 0):
    print("Brands: ")
    for brand in analysis.brands:
        print(" -'{}' (confidence: {:.2f}%)".format(brand.name, brand.confidence * 100))
```
    
2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Beachten Sie dabei alle Marken, die identifiziert werden (insbesondere im Bild **person.jpg**).

## <a name="detect-and-locate-objects-in-an-image"></a>Erkennen und Suchen von Objekten in einem Bild

Die *Objekterkennung* ist eine spezielle Form des maschinellen Sehens, bei der einzelne Objekte innerhalb eines Bilds identifiziert und ihre Position durch einen Begrenzungsrahmen angegeben werden.

1. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get objects in the image** (Objekte im Bild abrufen) den folgenden Code hinzu:

**C#**

```C
// Get objects in the image
if (analysis.Objects.Count > 0)
{
    Console.WriteLine("Objects in image:");

    // Prepare image for drawing
    Image image = Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Cyan, 3);
    Font font = new Font("Arial", 16);
    SolidBrush brush = new SolidBrush(Color.Black);

    foreach (var detectedObject in analysis.Objects)
    {
        // Print object name
        Console.WriteLine($" -{detectedObject.ObjectProperty} (confidence: {detectedObject.Confidence.ToString("P")})");

        // Draw object bounding box
        var r = detectedObject.Rectangle;
        Rectangle rect = new Rectangle(r.X, r.Y, r.W, r.H);
        graphics.DrawRectangle(pen, rect);
        graphics.DrawString(detectedObject.ObjectProperty,font,brush,r.X, r.Y);

    }
    // Save annotated image
    String output_file = "objects.jpg";
    image.Save(output_file);
    Console.WriteLine("  Results saved in " + output_file);   
}
```

**Python**

```Python
# Get objects in the image
if len(analysis.objects) > 0:
    print("Objects in image:")

    # Prepare image for drawing
    fig = plt.figure(figsize=(8, 8))
    plt.axis('off')
    image = Image.open(image_file)
    draw = ImageDraw.Draw(image)
    color = 'cyan'
    for detected_object in analysis.objects:
        # Print object name
        print(" -{} (confidence: {:.2f}%)".format(detected_object.object_property, detected_object.confidence * 100))
        
        # Draw object bounding box
        r = detected_object.rectangle
        bounding_box = ((r.x, r.y), (r.x + r.w, r.y + r.h))
        draw.rectangle(bounding_box, outline=color, width=3)
        plt.annotate(detected_object.object_property,(r.x, r.y), backgroundcolor=color)
    # Save annotated image
    plt.imshow(image)
    outputfile = 'objects.jpg'
    fig.savefig(outputfile)
    print('  Results saved in', outputfile)
```
    
2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Beobachten Sie dabei die erkannten Objekte. Zeigen Sie nach jedem Durchlauf die Datei **objects.jpg** an, die im selben Ordner wie Ihre Codedatei generiert wird, um die kommentierten Objekte zu sehen.

## <a name="get-moderation-ratings-for-an-image"></a>Abrufen von Moderationsbewertungen für ein Bild

Einige Bilder sind möglicherweise nicht für alle Zielgruppen geeignet, und Sie müssen möglicherweise eine gewisse Moderation anwenden, um Bilder zu identifizieren, die nicht jugendfreie oder gewalttätige Inhalte aufweisen.

1. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get moderation ratings** (Moderationsbewertungen abrufen) den folgenden Code hinzu:

**C#**

```C
// Get moderation ratings
string ratings = $"Ratings:\n -Adult: {analysis.Adult.IsAdultContent}\n -Racy: {analysis.Adult.IsRacyContent}\n -Gore: {analysis.Adult.IsGoryContent}";
Console.WriteLine(ratings);
```

**Python**

```Python
# Get moderation ratings
ratings = 'Ratings:\n -Adult: {}\n -Racy: {}\n -Gore: {}'.format(analysis.adult.is_adult_content,
                                                                    analysis.adult.is_racy_content,
                                                                    analysis.adult.is_gory_content)
print(ratings)
```
    
2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Beobachten Sie dabei die Bewertungen für die einzelnen Bilder.

> **Hinweis**: In den vorangegangenen Aufgaben haben Sie eine einzelne Methode verwendet, um das Bild zu analysieren, und dann inkrementell Code hinzugefügt, um die Ergebnisse zu analysieren und anzuzeigen. Das SDK bietet auch individuelle Methoden für das Vorschlagen von Beschriftungen, das Identifizieren von Tags, das Erkennen von Objekten usw. – das bedeutet, dass Sie die am besten geeignete Methode verwenden können, um nur die benötigten Informationen zurückzugeben und so die Menge der zurückzugebenden Nutzdaten zu reduzieren. Weitere Informationen finden Sie in der [.NET SDK-Dokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) oder in der [Python SDK-Dokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/computervision?view=azure-python).

## <a name="generate-a-thumbnail-image"></a>Generieren eines Miniaturbilds

In manchen Fällen müssen Sie eine kleinere Version eines Bilds mit der Bezeichnung *Miniaturbild* erstellen und es so zuschneiden, dass das Hauptmotiv in den neuen Bilddimensionen enthalten ist.

1. Suchen Sie in Ihrer Codedatei die Funktion **GetThumbnail**, und fügen Sie unter dem Kommentar **Generate a thumbnail** (Miniaturbild generieren) den folgenden Code ein:

**C#**

```C
// Generate a thumbnail
using (var imageData = File.OpenRead(imageFile))
{
    // Get thumbnail data
    var thumbnailStream = await cvClient.GenerateThumbnailInStreamAsync(100, 100,imageData, true);

    // Save thumbnail image
    string thumbnailFileName = "thumbnail.png";
    using (Stream thumbnailFile = File.Create(thumbnailFileName))
    {
        thumbnailStream.CopyTo(thumbnailFile);
    }

    Console.WriteLine($"Thumbnail saved in {thumbnailFileName}");
}
```

**Python**

```Python
# Generate a thumbnail
with open(image_file, mode="rb") as image_data:
    # Get thumbnail data
    thumbnail_stream = cv_client.generate_thumbnail_in_stream(100, 100, image_data, True)

# Save thumbnail image
thumbnail_file_name = 'thumbnail.png'
with open(thumbnail_file_name, "wb") as thumbnail_file:
    for chunk in thumbnail_stream:
        thumbnail_file.write(chunk)

print('Thumbnail saved in.', thumbnail_file_name)
```
    
2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Öffnen Sie dabei die Datei **thumbnail.jpg**, die im selben Ordner wie Ihre Codedatei für jedes Bild generiert wird.

## <a name="more-information"></a>Weitere Informationen

In dieser Übung haben Sie einige der Bildanalyse- und -bearbeitungsfunktionen des Diensts für maschinelles Sehen kennengelernt. Der Dienst umfasst auch Funktionen für das Lesen von Text, die Erkennung von Gesichtern und andere Aufgaben für maschinelles Sehen.

Weitere Informationen zur Verwendung des Diensts für **maschinelles Sehen** finden Sie in der [Dokumentation zum maschinellen Sehen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/).
