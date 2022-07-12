---
lab:
  title: Erkennen, Analysieren und Identifizieren von Gesichtern
  module: Module 10 - Detecting, Analyzing, and Recognizing Faces
ms.openlocfilehash: 29b0544e4f31f6e85eeba5cd8fb42951ca1334a9
ms.sourcegitcommit: 7191e53bc33cda92e710d957dde4478ee2496660
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2022
ms.locfileid: "147041656"
---
# <a name="detect-analyze-and-recognize-faces"></a>Erkennen, Analysieren und Identifizieren von Gesichtern

Die Fähigkeit, menschliche Gesichter zu erkennen, zu analysieren und zu identifizieren, ist eine zentrale KI-Funktion. In dieser Übung untersuchen Sie zwei Azure Cognitive Services, die Sie zum Arbeiten mit Gesichtern in Bildern verwenden können: den Dienst für **maschinelles Sehen** und den Dienst für die **Gesichtserkennung**.

> **Hinweis:** Ab dem 21. Juni 2022 sind die Funktionen kognitiver Dienste, die personenbezogene Informationen zurückgeben, auf Kunden beschränkt, die [eingeschränkten Zugriff](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-limited-access) gewährt haben. Darüber hinaus sind Funktionen, die Rückschlüsse auf den emotionalen Zustand zulassen, nicht mehr verfügbar. Diese Einschränkungen können sich auf dieses Lab auswirken. Wir arbeiten daran, dies zu beheben, aber in der Zwischenzeit treten möglicherweise einige Fehler auf, wenn Sie die folgenden Schritte ausführen; dafür möchten wir uns entschuldigen. Weitere Informationen zu den von Microsoft vorgenommenen Änderungen und den Gründen hierfür finden Sie unter [Responsible AI investments and safeguards for facial recognition](https://azure.microsoft.com/blog/responsible-ai-investments-and-safeguards-for-facial-recognition/) (Verantwortungsvolle KI-Investitionen und Vorsichtsmaßnahmen für die Gesichtserkennung).

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
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0
3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
5. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. Sie benötigen den Endpunkt und einen der Schlüssel von dieser Seite im nächsten Verfahren.

## <a name="prepare-to-use-the-computer-vision-sdk"></a>Vorbereiten der Verwendung des SDK für maschinelles Sehen

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die das SDK für maschinelles Sehen zum Analysieren von Gesichtern in einem Bild verwendet.

> **Hinweis**: Sie können auswählen, ob Sie das SDK für **C#** oder **Python** verwenden möchten. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **19-face**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **computer-vision**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das SDK-Paket für maschinelles Sehen, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-vision-computervision==0.7.0
    ```
    
3. Zeigen Sie den Inhalt des Ordners **computer-vision** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

4. Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass sie dem **Endpunkt** und einem Authentifizierungs **schlüssel** für Ihre Cognitive Services-Ressource entsprechen. Speichern Sie die Änderungen.

5. Beachten Sie, dass der Ordner **computer-vision** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: detect-faces.py

6. Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das SDK für maschinelles Sehen verwenden zu können:

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

## <a name="view-the-image-you-will-analyze"></a>Anzeigen des zu analysierenden Bilds

In dieser Übung verwenden Sie den Dienst für maschinelles Sehen, um ein Bild von Personen zu analysieren.

1. Erweitern Sie in Visual Studio Code den Ordner **computer-vision** und den darin enthaltenen Ordner **images**.
2. Wählen Sie das Bild **people.jpg** aus, um es anzuzeigen.

## <a name="detect-faces-in-an-image"></a>Gesichtserkennung in einem Bild

Nun können Sie das SDK verwenden, um den Dienst für maschinelles Sehen aufzurufen und Gesichter in einem Bild zu erkennen.

1. Beachten Sie in der Codedatei für Ihre Clientanwendung (**Program.cs** oder **detect-faces.py**) in der Funktion **Main**, dass der Code zum Laden der Konfigurationseinstellungen bereitgestellt wurde. Suchen Sie dann den Kommentar **Authenticate Computer Vision client** (Client für maschinelles Sehen authentifizieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um ein Clientobjekt für maschinelles Sehen zu erstellen und zu authentifizieren:

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

2. Beachten Sie in der Funktion **Main** unter dem soeben hinzugefügten Code, dass der Code den Pfad zu einer Bilddatei angibt und den Bildpfad dann an eine Funktion namens **AnalyzeFaces** weitergibt. Diese Funktion ist noch nicht vollständig implementiert.

3. Fügen Sie in der Funktion **AnalyzeFaces** unter dem Kommentar **Specify features to be retrieved (faces)** (Abzurufende Features angeben (Gesichter)) den folgenden Code ein:

    **C#**

    ```C#
    // Specify features to be retrieved (faces)
    List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>()
    {
        VisualFeatureTypes.Faces
    };
    ```

    **Python**

    ```Python
    # Specify features to be retrieved (faces)
    features = [VisualFeatureTypes.faces]
    ```

4. Fügen Sie in der Funktion **AnalyzeFaces** unter dem Kommentar **Get image analysis** (Bildanalyse abrufen) den folgenden Code hinzu:

**C#**

```C
// Get image analysis
using (var imageData = File.OpenRead(imageFile))
{    
    var analysis = await cvClient.AnalyzeImageInStreamAsync(imageData, features);

    // Get faces
    if (analysis.Faces.Count > 0)
    {
        Console.WriteLine($"{analysis.Faces.Count} faces detected.");

        // Prepare image for drawing
        Image image = Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen pen = new Pen(Color.LightGreen, 3);
        Font font = new Font("Arial", 3);
        SolidBrush brush = new SolidBrush(Color.LightGreen);

        // Draw and annotate each face
        foreach (var face in analysis.Faces)
        {
            var r = face.FaceRectangle;
            Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
            graphics.DrawRectangle(pen, rect);
            string annotation = $"Person at approximately {face.Left}, {face.Top}";
            graphics.DrawString(annotation,font,brush,r.Left, r.Top);
        }

        // Save annotated image
        String output_file = "detected_faces.jpg";
        image.Save(output_file);
        Console.WriteLine(" Results saved in " + output_file);   
    }
}        
```

**Python**

```Python
# Get image analysis
with open(image_file, mode="rb") as image_data:
    analysis = cv_client.analyze_image_in_stream(image_data , features)

    # Get faces
    if analysis.faces:
        print(len(analysis.faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'

        # Draw and annotate each face
        for face in analysis.faces:
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline=color, width=5)
            annotation = 'Person at approximately {}, {}'.format(r.left, r.top)
            plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)

        # Save annotated image
        plt.imshow(image)
        outputfile = 'detected_faces.jpg'
        fig.savefig(outputfile)

        print('Results saved in', outputfile)
```

5. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **computer-vision** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python detect-faces.py
    ```

6. Beobachten Sie die Ausgabe, die die Anzahl der erkannten Gesichter anzeigen sollte.
7. Zeigen Sie die Datei **detected_faces.jpg** an, die im selben Ordner wie Ihre Codedatei generiert wird, um die kommentierten Gesichter zu sehen. In diesem Fall hat Ihr Code die Attribute des Gesichts verwendet, um die Position der linken oberen Ecke des Rahmens zu kennzeichnen, und die Koordinaten des Begrenzungsrahmens, um ein Rechteck um jedes Gesicht zu zeichnen.

## <a name="prepare-to-use-the-face-sdk"></a>Vorbereiten der Verwendung des SDK für die Gesichtserkennung

Während der Dienst für **maschinelles Sehen** eine grundlegende Gesichtserkennung bietet (zusammen mit vielen anderen Bildanalysefunktionen), bietet der Dienst **Gesichtserkennung** eine umfassendere Funktionalität für die Gesichtsanalyse und -erkennung.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **19-face**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **face-api**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das SDK-Paket für die Gesichtserkennung, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-vision-face==0.4.1
    ```
    
3. Zeigen Sie den Inhalt des Ordners **face-api** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

4. Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass sie dem **Endpunkt** und einem Authentifizierungs **schlüssel** für Ihre Cognitive Services-Ressource entsprechen. Speichern Sie die Änderungen.

5. Beachten Sie, dass der Ordner **face-api** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: analyze-faces.py

6. Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das SDK für maschinelles Sehen verwenden zu können:

    **C#**

    ```C#
    // Import namespaces
    using Microsoft.Azure.CognitiveServices.Vision.Face;
    using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
    ```

    **Python**

    ```Python
    # Import namespaces
    from azure.cognitiveservices.vision.face import FaceClient
    from azure.cognitiveservices.vision.face.models import FaceAttributeType
    from msrest.authentication import CognitiveServicesCredentials
    ```

7. Beachten Sie in der Funktion **Main**, dass der Code zum Laden der Konfigurationseinstellungen bereitgestellt wurde. Suchen Sie dann den Kommentar **Authenticate Face client** (Gesichtserkennungsclient authentifizieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um ein **FaceClient**-Objekt zu erstellen und zu authentifizieren:

    **C#**

    ```C#
    // Authenticate Face client
    ApiKeyServiceClientCredentials credentials = new ApiKeyServiceClientCredentials(cogSvcKey);
    faceClient = new FaceClient(credentials)
    {
        Endpoint = cogSvcEndpoint
    };
    ```

    **Python**

    ```Python
    # Authenticate Face client
    credentials = CognitiveServicesCredentials(cog_key)
    face_client = FaceClient(cog_endpoint, credentials)
    ```

8. Beachten Sie in der Funktion **Main** unter dem Code, den Sie soeben hinzugefügt haben, dass der Code ein Menü anzeigt, mit dem Sie Funktionen in Ihrem Code aufrufen können, um die Möglichkeiten des Diensts für die Gesichtserkennung zu erkunden. Sie werden diese Funktionen im weiteren Verlauf dieser Übung implementieren.

## <a name="detect-and-analyze-faces"></a>Erkennen und Analysieren von Gesichtern

Eine der grundlegendsten Fähigkeiten des Gesichtsdiensts ist die Erkennung von Gesichtern in einem Bild und die Bestimmung ihrer Attribute, wie Kopfhaltung, Verschwommenheitsgrad, Vorhandensein einer Brille usw.

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **1** auswählt. Dieser Code ruft die Funktion **DetectFaces** auf und übergibt den Pfad zu einer Bilddatei.
2. Suchen Sie die Funktion **DetectFaces** in der Codedatei, und fügen Sie unter dem Kommentar **Specify facial features to be retrieved** (Abzurufende Gesichtsmerkmale angeben) den folgenden Code ein:

    **C#**

    ```C#
    // Specify facial features to be retrieved
    List<FaceAttributeType?> features = new List<FaceAttributeType?>
    {
        FaceAttributeType.Occlusion,
        FaceAttributeType.Blur,
        FaceAttributeType.Glasses
    };
    ```

    **Python**

    ```Python
    # Specify facial features to be retrieved
    features = [FaceAttributeType.occlusion,
                FaceAttributeType.blur,
                FaceAttributeType.glasses]
    ```

3. Suchen Sie in der Funktion **DetectFaces** unter dem soeben hinzugefügten Code den Kommentar **Get faces** (Gesichter abrufen), und fügen Sie den folgenden Code hinzu:

**C#**

```C
// Get faces
using (var imageData = File.OpenRead(imageFile))
{    
    var detected_faces = await faceClient.Face.DetectWithStreamAsync(imageData, returnFaceAttributes: features);

    if (detected_faces.Count > 0)
    {
        Console.WriteLine($"{detected_faces.Count} faces detected.");

        // Prepare image for drawing
        Image image = Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen pen = new Pen(Color.LightGreen, 3);
        Font font = new Font("Arial", 4);
        SolidBrush brush = new SolidBrush(Color.Black);

        // Draw and annotate each face
        foreach (var face in detected_faces)
        {
            // Get face properties
            Console.WriteLine($"\nFace ID: {face.FaceId}");
            Console.WriteLine($" - Mouth Occluded: {face.FaceAttributes.Occlusion.MouthOccluded}");
            Console.WriteLine($" - Eye Occluded: {face.FaceAttributes.Occlusion.EyeOccluded}");
            Console.WriteLine($" - Blur: {face.FaceAttributes.Blur.BlurLevel}");
            Console.WriteLine($" - Glasses: {face.FaceAttributes.Glasses}");
            
            // Draw and annotate face
            var r = face.FaceRectangle;
            Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
            graphics.DrawRectangle(pen, rect);
            string annotation = $"Face ID: {face.FaceId}";
            graphics.DrawString(annotation,font,brush,r.Left, r.Top);
        }

        // Save annotated image
        String output_file = "detected_faces.jpg";
        image.Save(output_file);
        Console.WriteLine(" Results saved in " + output_file);   
    }
}
```

**Python**

```Python
# Get faces
with open(image_file, mode="rb") as image_data:
    detected_faces = face_client.face.detect_with_stream(image=image_data,
                                                            return_face_attributes=features)

    if len(detected_faces) > 0:
        print(len(detected_faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'

        # Draw and annotate each face
        for face in detected_faces:

            # Get face properties
            print('\nFace ID: {}'.format(face.face_id))
            detected_attributes = face.face_attributes.as_dict()
            if 'blur' in detected_attributes:
                print(' - Blur:')
                for blur_name in detected_attributes['blur']:
                    print('   - {}: {}'.format(blur_name, detected_attributes['blur'][blur_name]))
                    
            if 'occlusion' in detected_attributes:
                print(' - Occlusion:')
                for occlusion_name in detected_attributes['occlusion']:
                    print('   - {}: {}'.format(occlusion_name, detected_attributes['occlusion'][occlusion_name]))

            if 'glasses' in detected_attributes:
                print(' - Glasses:{}'.format(detected_attributes['glasses']))

            # Draw and annotate face
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline=color, width=5)
            annotation = 'Face ID: {}'.format(face.face_id)
            plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)

        # Save annotated image
        plt.imshow(image)
        outputfile = 'detected_faces.jpg'
        fig.savefig(outputfile)

        print('\nResults saved in', outputfile)
```

4. Untersuchen Sie den Code, den Sie der **DetectFaces**-Funktion hinzugefügt haben. Er analysiert eine Bilddatei und erkennt alle darin enthaltenen Gesichter, einschließlich der Attribute für Alter, Emotionen und das Vorhandensein einer Brille. Die Details der einzelnen Gesichter werden angezeigt, einschließlich eines eindeutigen Gesichtsbezeichners, der jedem Gesicht zugewiesen wird, und die Position der Gesichter wird auf dem Bild mithilfe eines Begrenzungsrahmens angezeigt.
5. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **face-api** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    *Die C#-Ausgabe kann Warnungen zu asynchronen Funktionen anzeigen, die jetzt den Operator **await** verwenden. Sie können diese ignorieren.*

    **Python**

    ```
    python analyze-faces.py
    ```

6. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die die ID und die Attribute jedes erkannten Gesichts enthalten sollte.
7. Zeigen Sie die Datei **detected_faces.jpg** an, die im selben Ordner wie Ihre Codedatei generiert wird, um die kommentierten Gesichter zu sehen.

## <a name="compare-faces"></a>Vergleichen von Gesichtern

Eine häufige Aufgabe besteht darin, Gesichter zu vergleichen und Gesichter zu finden, die ähnlich sind. In diesem Szenario müssen Sie die Person auf den Bildern nicht *identifizieren*, sondern nur feststellen, ob mehrere Bilder die *gleiche* Person zeigen (oder zumindest jemanden, der ähnlich aussieht). 

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **2** auswählt. Dieser Code ruft die Funktion **CompareFaces** auf und übergibt den Pfad zu zwei Bilddateien (**person1.jpg** und **people.jpg**).
2. Suchen Sie die Funktion **CompareFaces** in der Codedatei, und fügen Sie unter dem vorhandenen Code, der eine Meldung auf der Konsole ausgibt, den folgenden Code ein:

**C#**

```C
// Determine if the face in image 1 is also in image 2
DetectedFace image_i_face;
using (var image1Data = File.OpenRead(image1))
{    
    // Get the first face in image 1
    var image1_faces = await faceClient.Face.DetectWithStreamAsync(image1Data);
    if (image1_faces.Count > 0)
    {
        image_i_face = image1_faces[0];
        Image img1 = Image.FromFile(image1);
        Graphics graphics = Graphics.FromImage(img1);
        Pen pen = new Pen(Color.LightGreen, 3);
        var r = image_i_face.FaceRectangle;
        Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
        graphics.DrawRectangle(pen, rect);
        String output_file = "face_to_match.jpg";
        img1.Save(output_file);
        Console.WriteLine(" Results saved in " + output_file); 

        //Get all the faces in image 2
        using (var image2Data = File.OpenRead(image2))
        {    
            var image2Faces = await faceClient.Face.DetectWithStreamAsync(image2Data);

            // Get faces
            if (image2Faces.Count > 0)
            {

                var image2FaceIds = image2Faces.Select(f => f.FaceId).ToList<Guid?>();
                var similarFaces = await faceClient.Face.FindSimilarAsync((Guid)image_i_face.FaceId,faceIds:image2FaceIds);
                var similarFaceIds = similarFaces.Select(f => f.FaceId).ToList<Guid?>();

                // Prepare image for drawing
                Image img2 = Image.FromFile(image2);
                Graphics graphics2 = Graphics.FromImage(img2);
                Pen pen2 = new Pen(Color.LightGreen, 3);
                Font font2 = new Font("Arial", 4);
                SolidBrush brush2 = new SolidBrush(Color.Black);

                // Draw and annotate each face
                foreach (var face in image2Faces)
                {
                    if (similarFaceIds.Contains(face.FaceId))
                    {
                        // Draw and annotate face
                        var r2 = face.FaceRectangle;
                        Rectangle rect2 = new Rectangle(r2.Left, r2.Top, r2.Width, r2.Height);
                        graphics2.DrawRectangle(pen2, rect2);
                        string annotation = "Match!";
                        graphics2.DrawString(annotation,font2,brush2,r2.Left, r2.Top);
                    }
                }

                // Save annotated image
                String output_file2 = "matched_faces.jpg";
                img2.Save(output_file2);
                Console.WriteLine(" Results saved in " + output_file2);   
            }
        }

    }
}
```

**Python**

```Python
# Determine if the face in image 1 is also in image 2
with open(image_1, mode="rb") as image_data:
    # Get the first face in image 1
    image_1_faces = face_client.face.detect_with_stream(image=image_data)
    image_1_face = image_1_faces[0]

    # Highlight the face in the image
    fig = plt.figure(figsize=(8, 6))
    plt.axis('off')
    image = Image.open(image_1)
    draw = ImageDraw.Draw(image)
    color = 'lightgreen'
    r = image_1_face.face_rectangle
    bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
    draw = ImageDraw.Draw(image)
    draw.rectangle(bounding_box, outline=color, width=5)
    plt.imshow(image)
    outputfile = 'face_to_match.jpg'
    fig.savefig(outputfile)

# Get all the faces in image 2
with open(image_2, mode="rb") as image_data:
    image_2_faces = face_client.face.detect_with_stream(image=image_data)
    image_2_face_ids = list(map(lambda face: face.face_id, image_2_faces))

    # Find faces in image 2 that are similar to the one in image 1
    similar_faces = face_client.face.find_similar(face_id=image_1_face.face_id, face_ids=image_2_face_ids)
    similar_face_ids = list(map(lambda face: face.face_id, similar_faces))

    # Prepare image for drawing
    fig = plt.figure(figsize=(8, 6))
    plt.axis('off')
    image = Image.open(image_2)
    draw = ImageDraw.Draw(image)

    # Draw and annotate matching faces
    for face in image_2_faces:
        if face.face_id in similar_face_ids:
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline='lightgreen', width=10)
            plt.annotate('Match!',(r.left, r.top + r.height + 15), backgroundcolor='white')

    # Save annotated image
    plt.imshow(image)
    outputfile = 'matched_faces.jpg'
    fig.savefig(outputfile)
```

3. Untersuchen Sie den Code, den Sie der **CompareFaces**-Funktion hinzugefügt haben. Er findet das erste Gesicht in Bild 1 und vermerkt es in einer neuen Bilddatei namens **face_to_match.jpg**. Dann findet der Code alle Gesichter in Bild 2 und verwendet deren Gesichtsbezeichner (IDs), um die Gesichter zu finden, die Bild 1 ähnlich sind. Die ähnlichen Gesichter werden mit Anmerkungen versehen und in einem neuen Bild namens **matched_faces.jpg** gespeichert.
4. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **face-api** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    *Die C#-Ausgabe kann Warnungen zu asynchronen Funktionen anzeigen, die jetzt den Operator **await** verwenden. Sie können diese ignorieren.*

    **Python**

    ```
    python analyze-faces.py
    ```
    
5. Wenn Sie dazu aufgefordert werden, geben Sie **2** ein und beobachten Sie die Ausgabe. Zeigen Sie dann die Dateien **face_to_match.jpg** und **matched_faces.jpg** an, die im selben Ordner wie Ihre Codedatei generiert werden, um die übereinstimmenden Gesichter zu sehen.
6. Bearbeiten Sie den Code in der Funktion **Main** für die Menüoption **2**, um **person2.jpg** mit **people.jpg** zu vergleichen, und führen Sie das Programm dann erneut aus, um die Ergebnisse anzuzeigen.

## <a name="train-a-facial-recognition-model"></a>Trainieren eines Gesichtserkennungsmodells

Es kann Szenarien geben, in denen Sie ein Modell von bestimmten Personen verwalten müssen, deren Gesichter von einer KI-Anwendung erkannt werden können. Um z. B. ein biometrisches Sicherheitssystem zu ermöglichen, das die Identität von Mitarbeitern in einem gesicherten Gebäude per Gesichtserkennung überprüft.

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **3** auswählt. Dieser Code ruft die Funktion **TrainModel** auf und übergibt den Namen und die ID für eine neue **PersonGroup** (Personengruppe), die in Ihrer Cognitive Services-Ressource registriert wird, sowie eine Liste von Mitarbeiternamen.
2. Beachten Sie, dass es im Ordner **face-api/images** Ordner mit denselben Namen wie die Mitarbeiter gibt. Jeder Ordner enthält mehrere Bilder des genannten Mitarbeiters.
3. Suchen Sie die Funktion **TrainModel** in der Codedatei, und fügen Sie unter dem vorhandenen Code, der eine Meldung auf der Konsole ausgibt, den folgenden Code ein:

**C#**

```C
// Delete group if it already exists
var groups = await faceClient.PersonGroup.ListAsync();
foreach(var group in groups)
{
    if (group.PersonGroupId == groupId)
    {
        await faceClient.PersonGroup.DeleteAsync(groupId);
    }
}

// Create the group
await faceClient.PersonGroup.CreateAsync(groupId, groupName);
Console.WriteLine("Group created!");

// Add each person to the group
Console.Write("Adding people to the group...");
foreach(var personName in imageFolders)
{
    // Add the person
    var person = await faceClient.PersonGroupPerson.CreateAsync(groupId, personName);

    // Add multiple photo's of the person
    string[] images = Directory.GetFiles("images/" + personName);
    foreach(var image in images)
    {
        using (var imageData = File.OpenRead(image))
        { 
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(groupId, person.PersonId, imageData);
        }
    }

}

    // Train the model
Console.WriteLine("Training model...");
await faceClient.PersonGroup.TrainAsync(groupId);

// Get the list of people in the group
Console.WriteLine("Facial recognition model trained with the following people:");
var people = await faceClient.PersonGroupPerson.ListAsync(groupId);
foreach(var person in people)
{
    Console.WriteLine($"-{person.Name}");
}
```

**Python**

```Python
# Delete group if it already exists
groups = face_client.person_group.list()
for group in groups:
    if group.person_group_id == group_id:
        face_client.person_group.delete(group_id)

# Create the group
face_client.person_group.create(group_id, group_name)
print ('Group created!')

# Add each person to the group
print('Adding people to the group...')
for person_name in image_folders:
    # Add the person
    person = face_client.person_group_person.create(group_id, person_name)

    # Add multiple photo's of the person
    folder = os.path.join('images', person_name)
    person_pics = os.listdir(folder)
    for pic in person_pics:
        img_path = os.path.join(folder, pic)
        img_stream = open(img_path, "rb")
        face_client.person_group_person.add_face_from_stream(group_id, person.person_id, img_stream)

# Train the model
print('Training model...')
face_client.person_group.train(group_id)

# Get the list of people in the group
print('Facial recognition model trained with the following people:')
people = face_client.person_group_person.list(group_id)
for person in people:
    print('-', person.name)
```

4. Untersuchen Sie den Code, den Sie der **TrainModel**-Funktion hinzugefügt haben. Er führt die folgenden Aufgaben aus:
    - Ruft eine Liste der **Personengruppen** ab, die im Dienst registriert sind, und löscht die angegebene Gruppe, falls sie vorhanden ist.
    - Erstellt eine Gruppe mit der angegebenen ID und dem Namen.
    - Fügt der Gruppe für jeden angegebenen Namen eine Person hinzu und fügt mehrere Bilder von jeder Person hinzu.
    - Trainiert ein Gesichtserkennungsmodell auf der Grundlage der benannten Personen in der Gruppe und ihrer Gesichtsbilder.
    - Ruft eine Liste der benannten Personen in der Gruppe ab und zeigt sie an.
5. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **face-api** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    *Die C#-Ausgabe kann Warnungen zu asynchronen Funktionen anzeigen, die jetzt den Operator **await** verwenden. Sie können diese ignorieren.*

    **Python**

    ```
    python analyze-faces.py
    ```

6. Wenn Sie dazu aufgefordert werden, geben Sie **3** ein und beobachten Sie die Ausgabe. Sie werden feststellen, dass die erstellte **Personengruppe** zwei Personen umfasst.

## <a name="recognize-faces"></a>Erkennen von Gesichtern

Nachdem Sie nun eine **Personengruppe** definiert und ein Gesichtserkennungsmodell trainiert haben, können Sie es verwenden, um benannte Einzelpersonen in einem Bild zu erkennen.

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **4** auswählt. Dieser Code ruft die Funktion **RecognizeFaces** auf und übergibt den Pfad zu einer Bilddatei (**people.jpg**) sowie die ID der **PeopleGroup** (Personengruppe), die für die Gesichtserkennung verwendet werden soll.
2. Suchen Sie die Funktion **RecognizeFaces** in der Codedatei, und fügen Sie unter dem vorhandenen Code, der eine Meldung auf der Konsole ausgibt, den folgenden Code ein:

**C#**

```C
// Detect faces in the image
using (var imageData = File.OpenRead(imageFile))
{    
    var detectedFaces = await faceClient.Face.DetectWithStreamAsync(imageData);

    // Get faces
    if (detectedFaces.Count > 0)
    {
        
        // Get a list of face IDs
        var faceIds = detectedFaces.Select(f => f.FaceId).ToList<Guid?>();

        // Identify the faces in the people group
        var recognizedFaces = await faceClient.Face.IdentifyAsync(faceIds, groupId);

        // Get names for recognized faces
        var faceNames = new Dictionary<Guid?, string>();
        if (recognizedFaces.Count> 0)
        {
            foreach(var face in recognizedFaces)
            {
                var person = await faceClient.PersonGroupPerson.GetAsync(groupId, face.Candidates[0].PersonId);
                Console.WriteLine($"-{person.Name}");
                faceNames.Add(face.FaceId, person.Name);

            }
        }

        
        // Annotate faces in image
        Image image = Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen penYes = new Pen(Color.LightGreen, 3);
        Pen penNo = new Pen(Color.Magenta, 3);
        Font font = new Font("Arial", 4);
        SolidBrush brush = new SolidBrush(Color.Cyan);
        foreach (var face in detectedFaces)
        {
            var r = face.FaceRectangle;
            Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
            if (faceNames.ContainsKey(face.FaceId))
            {
                // If the face is recognized, annotate in green with the name
                graphics.DrawRectangle(penYes, rect);
                string personName = faceNames[face.FaceId];
                graphics.DrawString(personName,font,brush,r.Left, r.Top);
            }
            else
            {
                // Otherwise, just annotate the unrecognized face in magenta
                graphics.DrawRectangle(penNo, rect);
            }
        }

        // Save annotated image
        String output_file = "recognized_faces.jpg";
        image.Save(output_file);
        Console.WriteLine("Results saved in " + output_file);   
    }
}
```

**Python**

```Python
# Detect faces in the image
with open(image_file, mode="rb") as image_data:

    # Get faces
    detected_faces = face_client.face.detect_with_stream(image=image_data)

    # Get a list of face IDs
    face_ids = list(map(lambda face: face.face_id, detected_faces))

    # Identify the faces in the people group
    recognized_faces = face_client.face.identify(face_ids, group_id)

    # Get names for recognized faces
    face_names = {}
    if len(recognized_faces) > 0:
        print(len(recognized_faces), 'faces recognized.')
        for face in recognized_faces:
            person_name = face_client.person_group_person.get(group_id, face.candidates[0].person_id).name
            print('-', person_name)
            face_names[face.face_id] = person_name

    # Annotate faces in image
    fig = plt.figure(figsize=(8, 6))
    plt.axis('off')
    image = Image.open(image_file)
    draw = ImageDraw.Draw(image)
    for face in detected_faces:
        r = face.face_rectangle
        bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
        draw = ImageDraw.Draw(image)
        if face.face_id in face_names:
            # If the face is recognized, annotate in green with the name
            draw.rectangle(bounding_box, outline='lightgreen', width=3)
            plt.annotate(face_names[face.face_id],
                        (r.left, r.top + r.height + 15), backgroundcolor='white')
        else:
            # Otherwise, just annotate the unrecognized face in magenta
            draw.rectangle(bounding_box, outline='magenta', width=3)

    # Save annotated image
    plt.imshow(image)
    outputfile = 'recognized_faces.jpg'
    fig.savefig(outputfile)

    print('\nResults saved in', outputfile)
```
    
3. Untersuchen Sie den Code, den Sie der **RecognizeFaces**-Funktion hinzugefügt haben. Er findet die Gesichter im Bild und erstellt eine Liste mit deren IDs. Dann verwendet der Code die Personengruppe, um zu versuchen, die Gesichter in der Liste der Gesichtsbezeichner (IDs) zu identifizieren. Die erkannten Gesichter werden mit dem Namen der identifizierten Person beschriftet und die Ergebnisse werden in **recognized_faces.jpg** gespeichert.
4. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **face-api** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    *Die C#-Ausgabe kann Warnungen zu asynchronen Funktionen anzeigen, die jetzt den Operator **await** verwenden. Sie können diese ignorieren.*

    **Python**

    ```
    python analyze-faces.py
    ```

5. Wenn Sie dazu aufgefordert werden, geben Sie **4** ein und beobachten Sie die Ausgabe. Betrachten Sie dann die Datei **recognized_faces.jpg**, die im selben Ordner wie Ihre Codedatei generiert wird, um die identifizierten Personen anzuzeigen.
6. Bearbeiten Sie den Code in der Funktion **Main** für die Menüoption **4**, um Gesichter in **people2.jpg** zu erkennen, und führen Sie das Programm dann erneut aus, um die Ergebnisse anzuzeigen. Es sollten dieselben Personen erkannt werden.

## <a name="verify-a-face"></a>Überprüfen eines Gesichts

Die Gesichtserkennung wird häufig zur Identitätsüberprüfung eingesetzt. Mit dem Gesichtserkennungsdienst können Sie ein Gesicht in einem Bild überprüfen, indem Sie es mit einem anderen Gesicht oder mit einer in einer **Personengruppe** (PersonGroup) registrierten Person vergleichen.

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **5** auswählt. Dieser Code ruft die Funktion **VerifyFace** auf und übergibt den Pfad zu einer Bilddatei (**person1.jpg**), einen Namen sowie die ID der **PeopleGroup** (Personengruppe), die für die Gesichtserkennung verwendet werden soll.
2. Suchen Sie die Funktion **VerifyFace** in der Codedatei, und fügen Sie unter dem Kommentar **Get the ID of the person from the people group** (ID der Person aus der Personengruppe abrufen) (oberhalb des Codes, der das Ergebnis ausgibt) den folgenden Code hinzu:

**C#**

```C
// Get the ID of the person from the people group
var people = await faceClient.PersonGroupPerson.ListAsync(groupId);
foreach(var person in people)
{
    if (person.Name == personName)
    {
        Guid personId = person.PersonId;

        // Get the first face in the image
        using (var imageData = File.OpenRead(personImage))
        {    
            var faces = await faceClient.Face.DetectWithStreamAsync(imageData);
            if (faces.Count > 0)
            {
                Guid faceId = (Guid)faces[0].FaceId;

                //We have a face and an ID. Do they match?
                var verification = await faceClient.Face.VerifyFaceToPersonAsync(faceId, personId, groupId);
                if (verification.IsIdentical)
                {
                    result = "Verified";
                }
            }
        }
    }
}
```

**Python**

```Python
# Get the ID of the person from the people group
people = face_client.person_group_person.list(group_id)
for person in people:
    if person.name == person_name:
        person_id = person.person_id

        # Get the first face in the image
        with open(person_image, mode="rb") as image_data:
            faces = face_client.face.detect_with_stream(image=image_data)
            if len(faces) > 0:
                face_id = faces[0].face_id

                # We have a face and an ID. Do they match?
                verification = face_client.face.verify_face_to_person(face_id, person_id, group_id)
                if verification.is_identical:
                    result = 'Verified'
```

3. Untersuchen Sie den Code, den Sie der **VerifyFace**-Funktion hinzugefügt haben. Er sucht die ID der Person in der Gruppe mit dem angegebenen Namen. Wenn die Person vorhanden ist, ruft der Code die Gesichts-ID des ersten Gesichts auf dem Bild ab. Wenn schließlich ein Gesicht auf dem Bild zu sehen ist, vergleicht der Code es mit der ID der angegebenen Person.
4. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **face-api** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python analyze-faces.py
    ```

5. Wenn Sie dazu aufgefordert werden, geben Sie **5** ein und beobachten Sie das Ergebnis.
6. Bearbeiten Sie den Code in der Funktion **Main** für die Menüoption **5**, um mit verschiedenen Kombinationen von Namen und den Bildern **person1.jpg** und **person2.jpg** zu experimentieren.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur Verwendung des Diensts für **maschinelles Sehen** für die Gesichtserkennung finden Sie in der [Dokumentation für maschinelles Sehen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-detecting-faces).

Weitere Informationen zum Dienst für die **Gesichtserkennung** finden Sie in der [Dokumentation zur Gesichtserkennung](https://docs.microsoft.com/azure/cognitive-services/face/).
