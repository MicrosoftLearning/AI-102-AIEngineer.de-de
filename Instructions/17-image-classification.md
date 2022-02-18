---
lab:
  title: Klassifizieren von Bildern mit Custom Vision
  module: Module 9 - Developing Custom Vision Solutions
ms.openlocfilehash: a8d60adee3e43ce0c613b71c7cba72067fe2b799
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2022
ms.locfileid: "135801379"
---
# <a name="classify-images-with-custom-vision"></a>Klassifizieren von Bildern mit Custom Vision

Mit dem **Custom Vision**-Dienst können Sie Modelle für maschinelles Sehen erstellen, die mit Ihren eigenen Bildern trainiert werden. Sie können ihn verwenden, um *Bildklassifizierungs*- und *Objekterkennungs* modelle zu trainieren, die Sie dann veröffentlichen und aus Anwendungen nutzen können.

In dieser Übung verwenden Sie den Custom Vision-Dienst, um ein Bildklassifizierungsmodell zu trainieren, das drei Klassen von Obst identifizieren kann (Apfel, Banane und Orange).

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** noch nicht in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, führen Sie die folgenden Schritte aus, um dies zu tun. Öffnen Sie andernfalls den geklonten Ordner in Visual Studio Code.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## <a name="create-custom-vision-resources"></a>Erstellen von Custom Vision-Ressourcen

Bevor Sie ein Modell trainieren können, benötigen Sie Azure-Ressourcen für *Training* und *Vorhersage*. Sie können **Custom Vision**-Ressourcen für jede dieser Aufgaben erstellen, oder Sie können eine einzelne **Cognitive Services**-Ressource erstellen und sie entweder für eine davon (oder für beide) verwenden.

In dieser Übung erstellen Sie **Custom Vision**-Ressourcen für Training und Vorhersage, damit Sie den Zugriff und die Kosten für diese Workloads separat verwalten können.

1. Öffnen Sie auf einer neuen Browserregisterkarte das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Wählen Sie die Schaltfläche **&#65291;Ressource erstellen** aus, suchen Sie nach *Custom Vision*, und erstellen Sie eine **Custom Vision**-Ressource mit den folgenden Einstellungen:
    - **Erstellungsoptionen**: Beide
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine Ressourcengruppe (wenn Sie eine gehostete Lab-Umgebung verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen, verwenden Sie dann die bereitgestellte Ressourcengruppe).*
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif für Training**: F0
    - **Tarif für Vorhersage**: F0

    > **Hinweis**: Falls Ihr Abonnement bereits einen Custom Vision-Dienst im Tarif F0 enthält, wählen Sie für diesen **S0** aus.

3. Warten Sie, bis die Ressourcen erstellt wurden, zeigen Sie dann die Bereitstellung an, und beachten Sie, dass zwei Custom Vision-Ressourcen bereitgestellt wurden, eine für das Training und eine für die Vorhersage (erkennbar am Suffix **-Prediction**). Sie können diese Ressourcen anzeigen, indem Sie zur Ressourcengruppe navigieren, in der Sie sie erstellt haben.

> **Wichtig**: Jede Ressource hat ihren eigenen *Endpunkt* und *Schlüssel*, die verwendet werden, um den Zugriff aus Ihrem Code zu verwalten. Um ein Bildklassifizierungsmodell zu trainieren, muss Ihr Code die *Trainings* ressource (mit ihrem Endpunkt und Schlüssel) verwenden, und um das trainierte Modell zum Vorhersagen von Bildklassen zu verwenden, muss Ihr Code die *Vorhersage* ressource (mit ihrem Endpunkt und Schlüssel) verwenden.

## <a name="create-a-custom-vision-project"></a>Erstellen eines Custom Vision-Projekts

Um ein Bildklassifizierungsmodell zu trainieren, müssen Sie ein Custom Vision-Projekt auf Grundlage Ihrer Trainingsressource erstellen. Dazu verwenden Sie das Custom Vision-Portal.

1. Zeigen Sie in Visual Studio Code die Trainingsbilder im Ordner **17-image-classification/training-images** an, in den Sie das Repository geklont haben. Dieser Ordner enthält Unterordner mit Bildern von Äpfeln, Bananen und Organen.
2. Öffnen Sie auf einer neuen Browserregisterkarte das Custom Vision-Portal unter `https://customvision.ai`. Falls Sie dazu aufgefordert werden, melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist, und stimmen Sie den Nutzungsbedingungen zu.
3. Erstellen Sie im Custom Vision-Portal ein neues Projekt mit den folgenden Einstellungen:
    - **Name**: Classify Fruit (Obst klassifizieren)
    - **Beschreibung**: Bildklassifizierung für Obst
    - **Ressource**: *Die Custom Vision-Ressource, die Sie zuvor erstellt haben*
    - **Projekttypen:** Klassifizierung
    - **Klassifizierungstypen**: Multiklassen-Klassifizierung (einzelnes Tag pro Bild)
    - **Domänen**: Lebensmittel
4. Klicken Sie in dem neuen Projekt auf **\[+\]Bilder hinzufügen**, und wählen Sie alle Dateien in dem Ordner **training-images/apple** aus, den Sie zuvor angezeigt haben. Laden Sie anschließend die Bilddateien mit dem Tag *Apfel* hoch:

![Hochladen eines Apfels mit dem Tag „Apfel“](./images/upload_apples.jpg)
   
5. Wiederholen Sie den vorherigen Schritt, um die Bilder im Ordner **banana** mit dem Tag *Banane* und die Bilder im Ordner **orange** mit dem Tag *Orange* hochzuladen.
6. Sehen Sie sich die Bilder an, die Sie in Ihrem Custom Vision-Projekt hochgeladen haben. Sie sollten 15 Bilder pro Klasse haben, wie hier gezeigt:

![Markierte Bilder von Obst: 15 Äpfel, 15 Bananen und 15 Orangen](./images/fruit.jpg)
    
7. Klicken Sie im Custom Vision-Projekt über den Bildern auf **Trainieren**, um ein Klassifizierungsmodell mit den markierten Bildern zu trainieren. Wählen Sie die Option **Schnelltraining** aus, und warten Sie, bis die Trainingsiteration abgeschlossen wurde (dieser Vorgang kann etwa eine Minute dauern).
8. Warten Sie, bis die Modelliteration trainiert wurde, und überprüfen Sie die Leistungsmetriken *Genauigkeit*, *Abruf* und *AP*. Diese Metriken messen die Vorhersagegenauigkeit des Klassifizierungsmodells und sollten jeweils den Wert „Hoch“ haben.

> **Hinweis**: Die Leistungsmetriken basieren auf einem Wahrscheinlichkeitsschwellenwert von 50 % für jede Vorhersage (d. h., wenn das Modell eine Wahrscheinlichkeit von 50 % oder höher berechnet, dass ein Bild einer bestimmten Klasse entspricht, wird diese Klasse vorhergesagt). Sie können dies oben links auf der Seite anpassen.

## <a name="test-the-model"></a>Testen des Modells

Nachdem Sie das Modell nun trainiert haben, können Sie es testen.

1. Klicken Sie über den Leistungsmetriken auf **Schnelltest**.
2. Geben Sie `https://aka.ms/apple-image` im Feld **Bild-URL** ein, und klicken Sie auf „&#10132;“.
3. Sehen Sie sich die von Ihrem Modell zurückgegebenen Vorhersagen an. *Apfel* sollte den höchsten Wahrscheinlichkeitswert haben, wie hier gezeigt:

![Ein Bild mit einer Klassenvorhersage für Apfel](./images/test-apple.jpg)

4. Schließen Sie das Fenster **Schelltest**.

## <a name="view-the-project-settings"></a>Anzeigen der Projekteinstellungen

Dem von Ihnen erstellten Projekt wurde ein eindeutiger Bezeichner zugewiesen, den Sie in jedem Code angeben müssen, der damit interagiert.

1. Klicken Sie auf das Symbol *Einstellungen* (&#9881;) oben rechts auf der Seite **Leistung**, um die Projekteinstellungen anzuzeigen.
2. Notieren Sie unter **Allgemein** (auf der linken Seite) die **Projekt-ID**, die dieses Projekt eindeutig identifiziert.
3. Beachten Sie, dass auf der rechten Seite unter **Ressourcen** der Schlüssel und der Endpunkt angezeigt werden. Dies sind die Details für die *Trainingsressource*. (Sie können diese Informationen auch abrufen, indem Sie die Ressource im Azure-Portal anzeigen.)

## <a name="use-the-training-api"></a>Verwenden der *Trainings*-API

Das Custom Vision-Portal bietet eine praktische Benutzeroberfläche, mit der Sie Bilder hochladen und markieren sowie Modelle trainieren können. In einigen Szenarien kann es jedoch sinnvoll sein, das Modelltraining mithilfe der Custom Vision-Trainings-API zu automatisieren.

> **Hinweis**: In dieser Übung können Sie wahlweise die API aus dem **C#** oder **Python** SDK verwenden. Führen Sie in den folgenden Schritten die Aktionen aus, die für Ihre bevorzugte Sprache geeignet sind.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **17-image-classification**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **train-classifier**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Custom Vision-Trainingspaket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-customvision==3.1.0
```

3. Zeigen Sie den Inhalt des Ordners **train-classifier** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass der Endpunkt und Schlüssel für Ihre Custom Vision-*Trainings* ressource und die Projekt-ID für das zuvor erstellte Klassifizierungsprojekt verwendet werden. Speichern Sie die Änderungen.
4. Beachten Sie, dass der Ordner **train-classifier** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: train-classifier.py

    Öffnen Sie die Codedatei, und überprüfen Sie den darin enthaltenen Code, wobei Sie sich die folgenden Details notieren:
    - Namespaces aus dem von Ihnen installierten Paket werden importiert.
    - Die **Main**-Funktion ruft die Konfigurationseinstellungen ab und verwendet den Schlüssel und Endpunkt, um einen authentifizierten **CustomVisionTrainingClient** zu erstellen, der dann mit der Projekt-ID verwendet wird, um einen **Projekt** verweis auf Ihr Projekt zu erstellen.
    - Die **Upload_Images**-Funktion ruft die im Custom Vision-Projekt definierten Tags ab und lädt dann Bilddateien aus entsprechend benannten Ordnern in das Projekt hoch, wobei die jeweils entsprechende Tag-ID zugewiesen wird.
    - Die **Train_Model**-Funktion erstellt eine neue Trainingsiteration für das Projekt und wartet auf den Abschluss des Trainings.
5. Kehren Sie zum integrierten Terminal in den Ordner **train-classifier** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

**C#**

```
dotnet run
```

**Python**

```
python train-classifier.py
```
    
6. Warten Sie, bis das Programm beendet wird. Kehren Sie dann zu Ihrem Browser zurück, und zeigen Sie die Seite **Trainingsbilder** für Ihr Projekt im Custom Vision-Portal an (aktualisieren Sie ggf. den Browser).
7. Vergewissern Sie sich, dass dem Projekt einige neue markierte Bilder hinzugefügt wurden. Zeigen Sie dann die Seite **Leistung** an, und überprüfen Sie, ob eine neue Iteration erstellt wurde.

## <a name="publish-the-image-classification-model"></a>Veröffentlichen des Bildklassifizierungsmodells

Jetzt können Sie Ihr trainiertes Modell veröffentlichen, damit es in einer Clientanwendung verwendet werden kann.

1. Klicken Sie im Custom Vision-Portal auf der Seite **Leistung** auf **&#128504; Veröffentlichen**, um das trainierte Modell mit den folgenden Einstellungen zu veröffentlichen:
    - **Modellname**: fruit-classifier
    - **Vorhersageressource:** *Dies ist die zuvor erstellte **Vorhersageressource**, die mit „-Prediction“ endet (<u>nicht</u> die Trainingsressource).*
2. Klicken Sie links oben auf der Seite **Projekteinstellungen** auf das Symbol für den *Projektkatalog* (&#128065;), um zur Startseite des Custom Vision-Portals zu gelangen, auf der Ihr Projekt jetzt aufgelistet wird.
3. Klicken Sie auf der Startseite des Custom Vision-Portals oben rechts auf das Symbol *Einstellungen* (&#9881;), um die Einstellungen für Ihren Custom Vision-Dienst anzuzeigen. Suchen Sie dann unter **Ressourcen** Ihre auf „-Prediction“ endende *Vorhersageressource* (<u>nicht</u> die Trainingsressource), um ihre Werte für **Schlüssel** und **Endpunkt** zu ermitteln. (Sie können diese Informationen auch abrufen, indem Sie die Ressource im Azure-Portal anzeigen.)

## <a name="use-the-image-classifier-from-a-client-application"></a>Verwenden der Bildklassifizierung aus einer Clientanwendung

Nachdem Sie nun das Bildklassifizierungsmodell veröffentlicht haben, können Sie es aus einer Clientanwendung heraus verwenden. Auch hier haben Sie wieder die Wahl zwischen der Verwendung von **C#** oder **Python**.

1. Klicken Sie in Visual Studio Code im Ordner **17-image-classification** im Unterordner für Ihre bevorzugte Sprache (**C-Sharp** oder **Python**) mit der rechten Maustaste auf den Ordner **test-classifier**, und öffnen Sie ein integriertes Terminal. Geben Sie dann den folgenden SDK-spezifischen Befehl ein, um das Custom Vision-Vorhersagepaket zu installieren:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-customvision==3.1.0
```

> **Hinweis**: Das Python SDK-Paket enthält sowohl Trainings- als auch Vorhersagepakete und ist möglicherweise bereits installiert.

2. Erweitern Sie den Ordner **test-classifier**, um die darin enthaltenen Dateien anzuzeigen, die zum Implementieren einer Testclientanwendung für Ihr Bildklassifizierungsmodell verwendet werden.
3. Öffnen Sie die Konfigurationsdatei für Ihre Clientanwendung (*appsettings.json* für C# oder *.env* für Python), und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass der Endpunkt und Schlüssel für Ihre Custom Vision-*Vorhersage* ressource, die Projekt-ID für das Klassifizierungsprojekt und der Name Ihres veröffentlichten Modells (dieses sollte *fruit-classifier* sein) verwendet werden. Speichern Sie die Änderungen.
4. Öffnen Sie die Codedatei für Ihre Clientanwendung (*Program.cs* für C#, *test-classification.py* für Python), und überprüfen Sie den darin enthaltenen Code, wobei Sie sich die folgenden Details notieren:
    - Namespaces aus dem von Ihnen installierten Paket werden importiert.
    - Die **Main**-Funktion ruft die Konfigurationseinstellungen ab und verwendet den Schlüssel und Endpunkt zum Erstellen eines authentifizierten **CustomVisionPredictionClient**.
    - Das Vorhersageclientobjekt wird verwendet, um eine Klasse für jedes Bild im Ordner **test-images** vorherzusagen, wobei die Projekt-ID und der Modellname für jede Anforderung angegeben werden. Jede Vorhersage umfasst eine Wahrscheinlichkeit für jede mögliche Klasse, und es werden nur vorhergesagte Tags mit einer Wahrscheinlichkeit von mehr als 50 % angezeigt.
5. Kehren Sie zum integrierten Terminal in den Ordner **test-classifier** zurück, und geben Sie den folgenden SDK-spezifischen Befehl ein, um das Programm auszuführen:

**C#**

```
dotnet run
```

**Python**

```
python test-classifier.py
```

6. Zeigen Sie die Bezeichnung (das Tag) und Wahrscheinlichkeitsbewertungen für jede Vorhersage an. Sie können die Bilder im Ordner **test-images** anzeigen, um zu überprüfen, ob das Modell sie ordnungsgemäß klassifiziert hat.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur Bildklassifizierung mit dem Custom Vision-Dienst finden Sie in der [Custom Vision Dokumentation](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/).
