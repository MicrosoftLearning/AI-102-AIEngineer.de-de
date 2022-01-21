---
lab:
  title: Erstellen eines Wissensspeichers mit Azure Cognitive Search
  module: Module 12 - Creating a Knowledge Mining Solution
ms.openlocfilehash: 8d7008ee592d4a76f24ca26a4b192c7dd5cbe9ea
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625754"
---
# <a name="create-a-knowledge-store-with-azure-cognitive-search"></a>Erstellen eines Wissensspeichers mit Azure Cognitive Search

Azure Cognitive Search verwendet eine Anreicherungspipeline kognitiver Skills, um KI-generierte Felder aus Dokumenten zu extrahieren und in einen Suchindex einzufügen. Während der Index als die primäre Ausgabe eines Indizierungsprozesses betrachtet werden kann, können die angereicherten Daten, die er enthält, auch auf andere Weise nützlich sein. Beispiel:

- Da der Index im Wesentlichen eine Sammlung von JSON-Objekten ist, die jeweils einen indizierten Datensatz repräsentieren, kann es sinnvoll sein, die Objekte als JSON-Dateien zu exportieren, um sie mit Tools wie Azure Data Factory in einen Datenorchestrierungsprozess zu integrieren.
- Möglicherweise möchten Sie die Indexdatensätze mit Tools wie Microsoft Power BI in ein relationales Tabellenschema für Analysen und Berichte normalisieren.
- Nachdem Sie während des Indizierungsvorgangs eingebettete Bilder aus den Dokumenten extrahiert haben, möchten Sie diese Bilder möglicherweise als Dateien speichern.

In dieser Übung implementieren Sie einen Wissensspeicher für *Margie's Travel*, ein fiktives Reisebüro, das Informationen in Broschüren und Hotelbewertungen nutzt, um Kunden bei der Reiseplanung zu helfen.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** bereits in die Umgebung geklont haben, in der Sie an dieser Übung arbeiten, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es jetzt zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Jetzt nicht** aus.

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

> **Hinweis**: Wenn Sie die Übung zum **[Erstellen einer Azure Cognitive Search-Lösung](22-azure-search.md)** bereits abgeschlossen haben und diese Azure-Ressourcen noch in Ihrem Abonnement verfügbar sind, können Sie diesen Abschnitt überspringen und mit dem Abschnitt **Erstellen einer Suchlösung** beginnen. Führen Sie andernfalls die folgenden Schritte aus, um die erforderlichen Azure-Ressourcen bereitzustellen.

1. Öffnen Sie in einem Webbrowser das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Zeigen Sie die **Ressourcengruppen** in Ihrem Abonnement an.
3. Wenn Sie ein eingeschränktes Abonnement verwenden, in dem eine Ressourcengruppe für Sie bereitgestellt wurde, wählen Sie die Ressourcengruppe aus, um deren Eigenschaften anzuzeigen. Erstellen Sie andernfalls eine neue Ressourcengruppe mit einem Namen Ihrer Wahl, und wechseln Sie zu dieser, nachdem sie erstellt wurde.
4. Notieren Sie sich auf der Seite mit der **Übersicht** (Overview) zu Ihrer Ressourcengruppe die Abonnement-ID (**Subscription ID**) und den Standort (**Location**) an. Sie benötigen diese Werte zusammen mit dem Namen der Ressourcengruppe in den nachfolgenden Schritten.
5. Erweitern Sie in Visual Studio Code den Ordner **24-knowledge-store**, und wählen Sie **setup.cmd** aus. Sie verwenden dieses Batchskript, um die Befehle der Azure-Befehlszeilenschnittstellen (CLI) auszuführen, die zum Erstellen der benötigten Azure-Ressourcen erforderlich sind.
6. Klicken Sie mit der rechten Maustaste auf den Ordner **24-knowledge-store**, und wählen Sie die Option **Open in Integrated Terminal** (In integriertem Terminal öffnen) aus.
7. Geben Sie im Terminalfenster den folgenden Befehl ein, um eine authentifizierte Verbindung mit Ihrem Azure-Abonnement herzustellen.

    ```
    az login --output none
    ```

8. Melden Sie sich bei Ihrem Azure-Abonnement an, wenn Sie dazu aufgefordert werden. Kehren Sie dann zu Visual Studio Code zurück, und warten Sie, bis der Anmeldevorgang abgeschlossen ist.
9. Führen Sie den folgenden Befehl aus, um Azure-Speicherorte auflisten.

    ```
    az account list-locations -o table
    ```

10. Suchen Sie in der Ausgabe nach dem Wert **Name**, der dem Standort Ihrer Ressourcengruppe entspricht (für *East US* ist der entsprechende Name beispielsweise *eastus).*
11. Ändern Sie im Skript **setup.cmd** die Deklarationen der Variablen **subscription_id**, **resource_group** und **location** mit den entsprechenden Werten für Ihre Abonnement-ID, den Namen der Ressourcengruppe und den Standortnamen. Speichern Sie anschließend die Änderungen.
12. Geben Sie im Terminal für den Ordner **24-knowledge-store** den folgenden Befehl ein, um das Skript auszuführen:

    ```
    setup
    ```
    > **Hinweis**: Das Search CLI-Modul befindet sich in der Vorschau und bleibt möglicherweise bei *- Running ..* hängen. kann ich den Rsync-Vorgang starten. Wenn dies länger als zwei Minuten der Fall ist, drücken Sie STRG+C, um den zeitintensiven Vorgang abzubrechen, und wählen Sie dann **N** aus, wenn Sie gefragt werden, ob Sie das Skript beenden möchten. Es sollte dann erfolgreich abgeschlossen werden.
    >
    > Wenn das Skript fehlschlägt, stellen Sie sicher, dass Sie es mit den richtigen Variablennamen gespeichert haben, und versuchen Sie es erneut.

13. Wenn das Skript abgeschlossen ist, überprüfen Sie die angezeigte Ausgabe, und notieren Sie sich die folgenden Informationen zu Ihren Azure-Ressourcen (diese Werte werden Sie später benötigen):
    - Speicherkontoname
    - Speicherverbindungszeichenfolge
    - Konto für Cognitive Services
    - Schlüssel für Cognitive Services
    - Suchdienstendpunkt
    - Administratorschlüssel für Suchdienst
    - Abfrageschlüssel für Suchdienst

14. Aktualisieren Sie im Azure-Portal die Ressourcengruppe, und überprüfen Sie, ob sie das Azure Storage-Konto, die Azure Cognitive Services-Ressource und die Azure Cognitive Search-Ressource enthält.

## <a name="create-a-search-solution"></a>Erstellen einer Suchlösung

Nachdem Sie nun über die erforderlichen Azure-Ressourcen verfügen, können Sie eine Suchlösung erstellen, die aus den folgenden Komponenten besteht:

- Eine **Datenquelle**, die auf die Dokumente in Ihrem Azure-Speichercontainer verweist.
- Ein **Skillset**, das eine Anreicherungspipeline von Qualifikationen definiert, um KI-generierte Felder aus den Dokumenten zu extrahieren. Das Skillset definiert auch die *Projektionen*, die in Ihrem *Wissensspeicher* generiert werden.
- Ein **Index**, der einen durchsuchbaren Satz von Dokumentdatensätzen definiert.
- Ein **Indexer**, der die Dokumente aus der Datenquelle extrahiert, das Skillset anwendet und den Index auffüllt. Bei der Indizierung werden auch die im Skillset definierten Projektionen im Wissensspeicher beibehalten.

In dieser Übung verwenden Sie die REST-Schnittstelle von Azure Cognitive Search, um diese Komponenten durch Übermitteln von JSON-Anforderungen zu erstellen.

### <a name="prepare-json-for-rest-operations"></a>Vorbereiten von JSON für REST-Vorgänge

Sie werden die REST-Schnittstelle verwenden, um JSON-Definitionen für Ihre Azure Cognitive Search-Komponenten zu übermitteln.

1. Erweitern Sie in Visual Studio Code im Ordner **24-knowledge-store** den Ordner **create-search**, und wählen Sie **data_source.json** aus. Diese Datei enthält eine JSON-Definition für eine Datenquelle namens **margies-knowledge-data**.
2. Ersetzen Sie den Platzhalter **YOUR_CONNECTION_STRING** durch die Verbindungszeichenfolge für Ihr Azure-Speicherkonto, die etwa wie folgt aussehen sollte:

    ```
    DefaultEndpointsProtocol=https;AccountName=ai102str123;AccountKey=12345abcdefg...==;EndpointSuffix=core.windows.net
    ```

    *Sie finden die Verbindungszeichenfolge auf der Seite mit den **Zugriffsschlüsseln** für Ihr Speicherkonto im Azure-Portal.*

3. Speichern und schließen Sie die aktualisierte JSON-Datei.
4. Öffnen Sie im Ordner **create-search** die Datei **skillset.json**. Diese Datei enthält eine JSON-Definition für ein Skillset namens **margies-knowledge-skillset**.
5. Ersetzen Sie oben in der Skillsetdefinition im **cognitiveServices**-Element den Platzhalter **YOUR_COGNITIVE_SERVICES_KEY** durch einen der Schlüssel für Ihre Cognitive Services-Ressourcen.

    *Sie finden diese Schlüssel auf der Seite **Schlüssel und Endpunkt** für Ihre Cognitive Services-Ressource im Azure-Portal.*

6. Am Ende der Sammlung von Skills in Ihrem Skillset finden Sie den **Microsoft.Skills.Util.ShaperSkill**-Skill namens **define-projection**. Dieser Skill definiert eine JSON-Struktur für die angereicherten Daten, die für die Projektionen verwendet werden, die die Pipeline im Wissensspeicher für jedes vom Indexer verarbeitete Dokument aufrechterhält.
7. Beachten Sie am unteren Ende der Skillsetdatei, dass das Skillset auch eine **knowledgeStore**-Definition enthält, die eine Verbindungszeichenfolge für das Azure Storage-Konto enthält, in dem der Wissensspeicher erstellt werden soll, sowie eine Sammlung von **Projektionen**. Dieses Skillset umfasst drei *Projektionsgruppen*:
    - Eine Gruppe, die eine *Objekt*-Projektion basierend auf der **knowledge_projection**-Ausgabe des Shaper-Skills im Skillset enthält.
    - Eine Gruppe, die eine *Datei*-Projektion basierend auf der **normalized_images**-Sammlung von Bilddaten enthält, die aus den Dokumenten extrahiert wurden.
    - Eine Gruppe, die die folgende *Tabellen*-Projektionen enthält:
        - **KeyPhrases**: Enthält eine automatisch generierte Schlüsselspalte (key) sowie eine **keyPhrase**-Spalte, die der **knowledge_projection/key_phrases/**-Sammlungsausgabe des Shaper-Skills zugeordnet ist.
        - **Locations** (Orte): Enthält eine automatisch generierte Schlüsselspalte (key) sowie eine **location**-Spalte, die der **knowledge_projection/location/**-Sammlungsausgabe des Shaper-Skills zugeordnet ist.
        - **ImageTags** (Bildtags): Enthält eine automatisch generierte Schlüsselspalte (key) sowie eine **tag**-Spalte, die der **knowledge_projection/image_tags/**-Sammlungsausgabe des Shaper-Skills zugeordnet ist.
        - **Docs** (Dokumente): Enthält eine automatisch generierte Schlüsselspalte sowie alle **knowledge_projection**-Ausgabewerte des Shaper-Skills, die noch keiner Tabelle zugeordnet sind.
8. Ersetzen Sie den Platzhalter **YOUR_CONNECTION_STRING** für den **storageConnectionString**-Wert durch die Verbindungszeichenfolge für Ihr Speicherkonto.
9. Speichern und schließen Sie die aktualisierte JSON-Datei.
10. Öffnen Sie im Ordner **create-search** die Datei **index.json**. Diese Datei enthält eine JSON-Definition für einen Index namens **margies-knowledge-index**.
11. Überprüfen Sie den JSON-Code für den Index, und schließen Sie die Datei, ohne Änderungen vorzunehmen.
12. Öffnen Sie im Ordner **create-search** die Datei **indexer.json**. Diese Datei enthält eine JSON-Definition für einen Indexer namens **margies-knowledge-indexer**.
13. Überprüfen Sie den JSON-Code für den Indexer, und schließen Sie die Datei, ohne Änderungen vorzunehmen.

### <a name="submit-rest-requests"></a>Übermitteln von REST-Anforderungen

Nachdem Sie nun die JSON-Objekte vorbereitet haben, die Ihre Komponenten für die Suchlösung definieren, können Sie die JSON-Dokumente an die REST-Schnittstelle übermitteln, um sie zu erstellen.

1. Öffnen Sie im Ordner **create-search** die Datei **create-search.cmd**. Dieses Batchskript verwendet das Hilfsprogramm cURL, um die JSON-Definitionen an die REST-Schnittstelle für Ihre Azure Cognitive Search-Ressource zu übermitteln.
2. Ersetzen Sie die Platzhalter für die Variablen **YOUR_SEARCH_URL** und **YOUR_ADMIN_KEY** durch die **URL** und einen der **Administratorschlüssel** für Ihre Azure Cognitive Search-Ressource.

    *Sie finden diese Werte auf der Seite mit der **Übersicht** (Overview) und der Seite mit den **Schlüsseln** (Keys) für Ihre Azure Cognitive Search-Ressource im Azure-Portal.*

3. Speichern Sie die aktualisierte Batchdatei.
4. Klicken Sie mit der rechten Maustaste auf den Ordner **create-search**, und wählen Sie die Option **Open in Integrated Terminal** (In integriertem Terminal öffnen) aus.
5. Geben Sie im Terminalbereich für den Ordner **create-search** den folgenden Befehl ein, um das Batchskript auszuführen.

    ```
    create-search
    ```

6. Wählen Sie nach Abschluss des Skripts im Azure-Portal auf der Seite für Ihre Azure Cognitive Search-Ressource die Seite **Indexer** aus, und warten Sie, bis der Indizierungsprozess abgeschlossen ist.

    *Sie können **Aktualisieren** auswählen, um den Fortschritt des Indizierungsvorgang zu verfolgen. Es kann eine Minute dauern, bis der Vorgang abgeschlossen ist.*

    > **Tipp**: Wenn beim Skript ein Fehler auftritt, überprüfen Sie die Platzhalter, die Sie in den Dateien **data_source.json** und **skillset.json** sowie in der Datei **create-search.cmd** hinzugefügt haben. Nachdem Sie alle Fehler behoben haben, müssen Sie möglicherweise die Benutzeroberfläche des Azure-Portals verwenden, um alle Komponenten zu löschen, die in Ihrer Suchressource erstellt wurden, bevor Sie das Skript erneut ausführen.

## <a name="view-the-knowledge-store"></a>Anzeigen des Wissensspeichers

Nachdem Sie einen Indexer ausgeführt haben, der ein Skillset zur Erstellung eines Wissensspeichers verwendet, werden die durch den Indizierungsprozess extrahierten angereicherten Daten in den Projektionen des Wissensspeichers dauerhaft gespeichert.

### <a name="view-object-projections"></a>Anzeigen von Objektprojektionen

Die im Skillset „Margie's Travel“ definierten *Objekt*-Projektionen bestehen aus einer JSON-Datei für jedes indizierte Dokument. Diese Dateien werden in einem Blobcontainer in dem in der Skillset-Definition angegebenen Azure Storage-Konto gespeichert.

1. Zeigen Sie im Azure-Portal das Azure Storage-Konto an, das Sie zuvor erstellt haben.
2. Wählen Sie die Registerkarte **Speicher-Explorer** aus (im linken Bereich), um das Speicherkonto in der Speicher-Explorer-Oberfläche im Azure-Portal anzuzeigen.
2. Erweitern Sie **BLOBCONTAINER**, um die Container im Speicherkonto anzuzeigen. Zusätzlich zum Container **margies**, in dem die Quelldaten gespeichert sind, sollten zwei neue Container vorhanden sein: **margies-images** und **margies-knowledge**. Diese wurden durch den Indizierungsprozess erzeugt.
3. Wählen Sie den Container **margies-knowledge** aus. Er sollte für jedes indizierte Dokument einen Ordner enthalten.
4. Öffnen Sie einen der Ordner, und laden Sie dann die darin enthaltene Datei **knowledge-projection.json** herunter und öffnen Sie sie. Jede JSON-Datei enthält eine Darstellung eines indizierten Dokuments, einschließlich der angereicherten Daten, die durch das Skillset extrahiert wurden, wie hier gezeigt.

```
{
    "file_id":"abcd1234....",
    "file_name":"Margies Travel Company Info.pdf",
    "url":"https://store....blob.core.windows.net/margies/...pdf",
    "language":"en",
    "sentiment":0.83164644241333008,
    "key_phrases":[
        "Margie’s Travel",
        "Margie's Travel",
        "best travel experts",
        "world-leading travel agency",
        "international reach"
        ],
    "locations":[
        "Dubai",
        "Las Vegas",
        "London",
        "New York",
        "San Francisco"
        ],
    "image_tags":[
        "outdoor",
        "tree",
        "plant",
        "palm"
        ]
}
```

Die Möglichkeit, *Objekt*-Projektionen wie diese zu erstellen, ermöglicht es Ihnen, angereicherte Datenobjekte zu generieren, die in eine Unternehmensdatenanalyselösung eingebunden werden können – zum Beispiel durch Erfassen der JSON-Dateien in eine Azure Data Factory-Pipeline zur weiteren Verarbeitung oder zum Laden in ein Data Warehouse.

### <a name="view-file-projections"></a>Anzeigen von Datei-Projektionen

Die im Skillset definierten *Datei* Projektionen erstellen JPEG-Dateien für jedes Bild, das während des Indizierungsprozesses aus den Dokumenten extrahiert wurde.

1. Wählen Sie in der Speicher-Explorer-Oberfläche im Azure-Portal den Blobcontainer **margies-images** aus. Dieser Container enthält einen Ordner für jedes Dokument, das Bilder enthält.
2. Öffnen Sie einen der Ordner, und zeigen Sie den Inhalt an – jeder Ordner enthält mindestens eine \*JPG-Datei.
3. Öffnen Sie eine der Bilddateien, um zu überprüfen, ob sie aus den Dokumenten extrahierte Bilder enthalten.

Die Möglichkeit, *Datei*-Projektionen wie diese zu generieren, macht die Indizierung zu einer effizienten Methode, um eingebettete Bilder aus einer großen Menge von Dokumenten zu extrahieren.

### <a name="view-table-projections"></a>Anzeigen von Tabellen-Projektionen

Die im Skillset definierten *Tabellen*-Projektionen bilden ein relationales Schema angereicherter Daten.

1. Erweitern Sie in der Speicher-Explorer-Oberfläche im Azure-Portal **TABELLEN**.
2. Wählen Sie die Tabelle **Docs** aus, um deren Spalten anzuzeigen. Die Spalten enthalten einige Standardspalten der Azure Storage-Tabelle – um diese auszublenden, ändern Sie die **Spaltenoptionen**, um nur die folgenden Spalten auszuwählen:
    - **document_id** (die durch den Indizierungsprozess automatisch erzeugte Schlüsselspalte)
    - **file_id** (die verschlüsselte Datei-URL)
    - **file_name** (der aus den Dokumentmetadaten extrahierte Dateiname)
    - **language** (die Sprache, in der das Dokument verfasst ist)
    - **sentiment** die für das Dokument berechnete Standpunktbewertung.
    - **url** die URL für den Dokumentenblob im Azure-Speicher.
3. Zeigen Sie die anderen Tabellen an, die durch den Indizierungsprozess erstellt wurden:
    - **ImageTags** (enthält eine Zeile für jedes einzelne Bildtag mit der **document_id** für das Dokument, in dem das Tag vorkommt).
    - **KeyPhrases** (enthält eine Zeile für jeden0 einzelnen Schlüsselbegriff mit der **document_id** für das Dokument, in dem der Begriff vorkommt).
    - **Locations** (enthält eine Zeile für jeden einzelnen Ort mit der **document_id** für das Dokument, in dem der Ort vorkommt).

Die Möglichkeit, *Tabellen*-Projektionen zu erstellen, ermöglicht es Ihnen, Analyse- und Berichtslösungen zu erstellen, die das relationale Schema abfragen, z. B. die Verwendung von Microsoft Power BI. Die automatisch generierten Schlüsselspalten können verwendet werden, um die Tabellen in Abfragen zu verknüpfen –- zum Beispiel, um alle in einem bestimmten Dokument erwähnten Orte zurückzugeben.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zum Erstellen von Wissensspeichern mit Azure Cognitive Search finden Sie in der [Azure Cognitive Search-Dokumentation](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro).
