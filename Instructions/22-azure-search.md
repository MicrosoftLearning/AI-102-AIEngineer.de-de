---
lab:
  title: Erstellen einer Azure Cognitive Search-Lösung
  module: Module 12 - Creating a Knowledge Mining Solution
ms.openlocfilehash: 38d5d50ba7e906ee0842a076ec08ad1e92d10293
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625788"
---
# <a name="create-an-azure-cognitive-search-solution"></a>Erstellen einer Azure Cognitive Search-Lösung

Alle Organisationen benötigen Informationen, um Entscheidungen zu treffen, Fragen zu beantworten und effizient zu arbeiten. Für die meisten Organisationen besteht das Problem nicht im Mangel an Informationen. Stattdessen stehen sie vor der Herausforderung, die Informationen in der gewaltigen Menge von Dokumenten, Datenbanken und anderen Quellen, in denen die Informationen gespeichert sind, zu finden und zu extrahieren.

Nehmen wir beispielsweise an, dass *Margie's Travel* ein Reisebüro ist, das Städtereisen überall auf der Welt organisiert. Im Laufe der Zeit hat das Unternehmen eine große Menge an Informationen in Dokumenten (z. B. in Form von Broschüren) und Bewertungen von Hotels durch Kunden gesammelt. Diese Daten sind eine wertvolle Informationsquelle für Reisevermittler und Kunden, die Reisen planen. Aufgrund der großen Datenmenge kann es jedoch schwierig sein, die relevanten Informationen zu finden, um eine bestimmte Kundenfrage zu beantworten.

Um diese Herausforderung zu bewältigen, kann Margie‘s Travel Azure Cognitive Search verwenden, um eine Lösung zu implementieren, in der die Dokumente indiziert und angereichert werden, indem KI-basierte kognitive Skills verwendet werden, um die Suche zu vereinfachen.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** noch nicht in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, führen Sie die folgenden Schritte aus, um dies zu tun. Öffnen Sie andernfalls den geklonten Ordner in Visual Studio Code.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Jetzt nicht** aus.

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Die Lösung, die Sie für Margie's Travel erstellen werden, benötigt die folgenden Ressourcen in Ihrem Azure-Abonnement:

- Eine **Azure Cognitive Search**-Ressource, die die Indizierung und Abfrage verwaltet
- Eine **Cognitive Services**-Ressource, die KI-Dienste für Skills bereitstellt, die Ihre Suchlösung verwenden kann, um die Daten in der Datenquelle mit KI-generierten Erkenntnissen anzureichern
- Ein **Speicherkonto** mit einem Blobcontainer, in dem die zu durchsuchenden Dokumente gespeichert werden

> **Wichtig**: Ihre Azure Cognitive Search- und Cognitive Services-Ressourcen müssen sich am selben Ort befinden!

### <a name="create-an-azure-cognitive-search-resource"></a>Erstellen einer Ressource für Azure Cognitive Search

1. Öffnen Sie in einem Webbrowser das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Wählen Sie die Schaltfläche **&#65291;Ressource erstellen** aus, suchen Sie nach *Search*, und erstellen Sie eine **Azure Cognitive Search**-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe (wenn Sie eine gehostete Lab-Umgebung verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen, verwenden Sie dann die bereitgestellte Ressourcengruppe).*
    - **Dienstname:** *Geben Sie einen eindeutigen Namen ein.*
    - **Speicherort**: *Wählen Sie einen Speicherort aus. Beachten Sie, dass sich Ihre Azure Cognitive Search- und Cognitive Services-Ressourcen sich am selben Ort befinden müssen.*
    - **Tarif**: Basic

3. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
4. Überprüfen Sie die Seite **Übersicht** auf dem Blatt für Ihre Azure Cognitive Search-Ressource im Azure-Portal. Hier können Sie eine grafische Benutzeroberfläche verwenden, um die verschiedenen Komponenten einer Suchlösung, z. B. Datenquellen, Indizes, Indexer und Skillsets, zu erstellen, zu testen, zu verwalten und zu überwachen.

### <a name="create-a-cognitive-services-resource"></a>Erstellen einer Cognitive Services-Ressource

Wenn Sie noch keine in Ihrem Abonnement haben, müssen Sie eine **Cognitive Services**-Ressource bereitstellen. Ihre Suchlösung verwendet diese, um die Daten im Datenspeicher mit KI-generierten Erkenntnissen anzureichern.

1. Kehren Sie zur Homepage des Azure-Portals zurück. Wählen Sie dann die Schaltfläche **&#65291;Ressource erstellen** aus, suchen Sie nach *Cognitive Services*, und erstellen Sie eine **Cognitive Services**-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Die gleiche Ressourcengruppe wie Ihre Azure Cognitive Search-Ressource*
    - **Region**: *Der gleiche Ort wie Ihre Azure Cognitive Search-Ressource*
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0
2. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
3. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

1. Kehren Sie zur Homepage des Azure-Portals zurück. Wählen Sie dann die Schaltfläche **&#65291;Ressource erstellen** aus, suchen Sie nach *Speicherkonto*, und erstellen Sie eine **Speicherkonto**-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: **Geben Sie die gleiche Ressourcengruppe wie Ihre Azure Cognitive Search- und Cognitive Services-Ressourcen an.*
    - **Speicherkontoname:** *Geben Sie einen eindeutigen Namen ein.*
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus.*
    - **Leistung**: Standard
    - **Replikation**: Lokal redundanter Speicher (LRS)
2. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
3. Notieren Sie sich auf der Seite **Übersicht** die **Abonnement-ID**. Diese gibt das Abonnement an, in dem das Speicherkonto bereitgestellt wird.
4. Beachten Sie auf der Seite **Zugriffsschlüssel**, dass zwei Schlüssel für Ihr Speicherkonto generiert wurden. Wählen Sie dann **Schlüssel anzeigen** aus, um die Schlüssel anzuzeigen.

    > **Tipp**: Lassen Sie das Blatt **Speicherkonto** geöffnet, da die Abonnement-ID und einer der Schlüssel im nächsten Verfahren benötigt werden.

## <a name="upload-documents-to-azure-storage"></a>Hochladen von Dokumenten in Azure Storage

Nachdem Sie nun über die erforderlichen Ressourcen verfügen, können Sie einige Dokumente in Ihr Azure Storage-Konto hochladen.

1. Erweitern Sie in Visual Studio im **Explorer**-Bereich den Ordner **22-create-a-search-solution**, und wählen Sie dann **UploadDocs.cmd** aus.
2. Bearbeiten Sie die Batchdatei, um die Platzhalter **YOUR_SUBSCRIPTION_ID**, **YOUR_AZURE_STORAGE_ACCOUNT_NAME** und **YOUR_AZURE_STORAGE_KEY** durch die entsprechenden Werte für Abonnement-ID, Azure Storage-Kontoname und Azure Storage-Kontoschlüssel für das zuvor erstellte Speicherkonto zu ersetzen.
3. Speichern Sie Ihre Änderungen, klicken Sie dann mit der rechten Maustaste auf den Ordner **22-create-a-search-solution**, und öffnen Sie ein integriertes Terminal.
4. Geben Sie den folgenden Befehl ein, um sich über die Azure CLI bei Ihrem Azure-Abonnement anzumelden.

    ```
    az login
    ```

Daraufhin wird eine Webbrowserregisterkarte mit der Aufforderung geöffnet, sich bei Azure anzumelden. Melden Sie sich an, schließen Sie dann die Browserregisterkarte, und kehren Sie zu Visual Studio Code zurück.

5. Geben Sie den folgenden Befehl ein, um die Batchdatei auszuführen: Dadurch wird ein Blobcontainer in Ihrem Speicherkonto erstellt, und die Dokumente im Ordner **data** werden in diesen Container hochgeladen.

    ```
    UploadDocs
    ```

## <a name="index-the-documents"></a>Indizieren der Dokumente

Nachdem sich die Dokumente jetzt an der sichtigen Stelle befinden, können Sie eine Suchlösung erstellen, indem Sie die Dokumente indizieren.

1. Navigieren Sie im Azure-Portal zu Ihre Azure Cognitive Search-Ressource. Wählen Sie dann auf der Seite **Übersicht** der Ressource **Daten importieren** aus.
2. Wählen Sie auf der Seite **Mit Ihren Daten verbinden** in der Liste **Datenquelle** die Option **Azure Blob Storage** aus. Vervollständigen Sie dann die Datenspeicherdetails mit den folgenden Werten:
    - **Datenquelle**: Azure Blob Storage
    - **Datenquellenname**: margies-data
    - **Zu extrahierende Daten**: Inhalt und Metadaten
    - **Analysemodus**: Standard
    - **Verbindungszeichenfolge:** *Wählen Sie **Vorhandene Verbindung auswählen** aus. Wählen Sie dann Ihr Speicherkonto und schließlich den **margies**-Container aus, der vom Skript „UploadDocs.cmd“ erstellt wurde.*
    - **Mit verwalteter Identität authentifizieren**: Deaktiviert
    - **Containername**: margies
    - **Blobordner:** *Lassen Sie dieses Feld leer.*
    - **Beschreibung**: Brochures and reviews in Margie's Travel web site.
3. Wechseln Sie zum nächsten Schritt (*Kognitive Skills hinzufügen*).
4. Wählen Sie im Abschnitt **Cognitive Services-Instanz anfügen** Ihre Cognitive Services-Ressource aus.
5. Gehen Sie im Abschnitt **Anreicherungen hinzufügen** folgendermaßen vor:
    - Ändern Sie den **Skillsetnnamen** in **margies-skillset**.
    - Wählen Sie die Option **OCR aktivieren und den gesamten Text im Feld "merged_content" zusammenführen** aus.
    - Vergewissern Sie sich, dass **Quelldatenfeld** auf **merged_content** festgelegt ist.
    - Übernehmen Sie **Granularitätsstufe für die Anreicherung** als **Quellfeld**, sodass der gesamte Inhalt des Dokuments indiziert wird. Sie können diese Einstellung jedoch ändern, um Informationen auf präziseren Ebenen wie Seiten oder Sätzen zu extrahieren.
    - Wählen Sie die folgenden angereicherten Felder aus:

        | Kognitive Fähigkeit | Parameter | Feldname |
        | --------------- | ---------- | ---------- |
        | Ortsnamen extrahieren | | locations |
        | Schlüsselbegriffe extrahieren | | keyphrases |
        | Sprache erkennen | | language |
        | Tags aus Bildern generieren | | imageTags |
        | Untertitel aus Bildern generieren | | imageCaption |

6. Überprüfen Sie Ihre Auswahl genau (es kann schwierig sein, sie später zu ändern). Wechseln Sie dann zum nächsten Schritt (*Zielindex anpassen*).
7. Ändern Sie den **Indexnamen** in **margies-index**.
8. Vergewissern Sie sich, dass der **Schlüssel** auf **metadata_storage_path** festgelegt ist, und lassen Sie **Name des Vorschlagsmoduls** und **Suchmodus** leer.
9. Nehmen Sie die folgenden Änderungen an den Indexfeldern vor, und übernehmen Sie für alle anderen Felder deren Standardeinstellungen (**WICHTIG**: Sie müssen möglicherweise nach rechts scrollen, um die ganze Tabelle anzuzeigen.):

    | Feldname | Abrufbar | Filterbar | Sortierbar | In Facets einteilbar | Suchbar |
    | ---------- | ----------- | ---------- | -------- | --------- | ---------- |
    | metadata_storage_size | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | |
    | metadata_storage_last_modified | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | |
    | metadata_storage_name | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | metadata_author | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | locations | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | keyphrases | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | language | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | | |

11. Überprüfen Sie Ihre Auswahl, und achten Sie besonders darauf, dass für jedes Feld die richtigen Optionen für **Abrufbar**, **Filterbar**, **Sortierbar**, **In Facets einteilbar** und **Suchbar** ausgewählt sind, da diese später u. U. schwer geändert werden können. Wechsel Sie dann zum nächsten Schritt (*Indexer erstellen*).
12. Ändern Sie den **Indexernamen** in **margies-indexer**.
13. Lassen Sie **Zeitplan** auf **Einmal** festgelegt.
14. Erweitern Sie die **erweiterten** Optionen, und vergewissern Sie sich, dass die Option **Base-64-Codierungsschlüssel** ausgewählt ist (Codierungsschlüssel bewirken in der Regel einen effizienteren Index).
15. Wählen Sie **Senden** aus, um Datenquelle, Skillset, Index und Indexer zu erstellen. Der Indexer wird automatisch ausgeführt und führt die Indizierungspipeline aus. Dieser führt folgende Aufgaben aus:
    1. Extrahieren der Felder und Inhalte von Dokumentmetadaten aus der Datenquelle
    2. Ausführen des Skillset für kognitive Skills aus, um zusätzliche angereicherte Felder zu generieren
    3. Zuordnen der extrahierten Felder zum Index
16. Zeigen Sie in der unteren Hälfte der Seite **Übersicht** für Ihre Azure Cognitive Search-Ressource die Registerkarte **Indexer** an, auf der der neu erstellte **margies-indexer** angezeigt werden sollte. Warten Sie einige Minuten, und klicken Sie auf **&orarr; Aktualisieren**, bis der **Status** „Erfolgreich“ angezeigt wird.

## <a name="search-the-index"></a>Durchsuchen des Index

Nachdem Sie nun über einen Index verfügen, können Sie ihn durchsuchen.

1. Wählen Sie oben auf der Seite **Übersicht** für Ihre Azure Cognitive Search-Ressource die Option **Suchexplorer** aus.
2. Geben Sie im Suchexplorer im Feld **Abfragezeichenfolge** `*` (ein einzelnes Sternchen) ein, und wählen Sie dann **Suchen** aus.

    Diese Abfrage ruft alle Dokumente im Index im JSON-Format ab. Überprüfen Sie die Ergebnisse, und notieren Sie sich die Felder für jedes Dokument, die Dokumentinhalte, Metadaten und angereicherte Daten enthalten, die von den ausgewählten kognitiven Skills extrahiert wurden.

3. Ändern Sie die Abfragezeichenfolge in `search=*&$count=true`, und übermitteln Sie die Suche.

    Dieses Mal enthalten die Ergebnisse ein **@odata.count** -Feld oben in den Ergebnissen, das die Anzahl der von der Suche zurückgegebenen Dokumente angibt.

4. Probieren Sie die folgende Abfragezeichenfolge aus:

    ```
    search=*&$count=true&$select=metadata_storage_name,metadata_author,locations
    ```

    Dieses Mal enthalten die Ergebnisse nur den Dateinamen, den Autor und alle Orte, die im Dokumentinhalt erwähnt werden. Der Dateiname und der Autor befinden sich in den Feldern **metadata_storage_name** und **metadata_author**, die aus dem Quelldokument extrahiert wurden. Das Feld **locations** wurde von einem kognitiven Skill generiert.

5. Probieren Sie nun die folgende Abfragezeichenfolge aus:

    ```
    search="New York"&$count=true&$select=metadata_storage_name,keyphrases
    ```

    Diese Suche findet Dokumente, die „New York“ in einem der durchsuchbaren Felder erwähnen, und gibt den Dateinamen und Schlüsselbegriffe im Dokument zurück.

6. Probieren wir eine weitere Abfragezeichenfolge aus:

    ```
    search="New York"&$count=true&$select=metadata_storage_name&$filter=metadata_author eq 'Reviewer'
    ```

    Diese Abfrage gibt den Dateinamen aller Dokumente zurück, die von *Reviewer* erstellt wurden und „New York“ erwähnen.

## <a name="explore-and-modify-definitions-of-search-components"></a>Untersuchen und Ändern von Definitionen von Suchkomponenten

Die Komponenten der Suchlösung basieren auf JSON-Definitionen, die Sie im Azure-Portal anzeigen und bearbeiten können.

Obwohl Sie das Portal zum Erstellen und Ändern von Suchlösungen verwenden können, ist es oft wünschenswert, die Suchobjekte in JSON zu definieren und die REST-Schnittstelle von Azure Cognitive Service zu verwenden, um Suchlösungen zu erstellen und zu ändern.

### <a name="get-the-endpoint-and-key-for-your-azure-cognitive-search-resource"></a>Abrufen des Endpunkts und Schlüssels für Ihre Azure Cognitive Search-Ressource

1. Kehren Sie im Azure-Portal zur Seite **Übersicht** für Ihre Azure Cognitive Search Ressource zurück. Suchen Sie im oberen Abschnitt der Seite nach der **URL** für Ihre Ressource (die wie **https://resource_name.search.windows.net** aussieht), und kopieren Sie diese in die Zwischenablage.
2. Erweitern Sie in Visual Studio im Explorer-Bereich den Ordner **22-create-a-search-solution** sowie den Unterordner **modify-search** im Ordner, und öffnen Sie die Datei **modify-search.cmd**. Sie verwenden diese Skriptdatei, um *cURL*-Befehle auszuführen, die JSON-Definitionen an die REST-Schnittstelle von Azure Cognitive Service senden.
3. Ersetzen Sie in **modify-search.cmd** den Platzhalter **YOUR_SEARCH_URL** durch die in die Zwischenablage kopierte URL.
4. Zeigen Sie im Azure-Portal die Seite **Schlüssel** für Ihre Azure Cognitive Search-Ressource an, und kopieren Sie den **primären Administratorschlüssel** in die Zwischenablage.
5. Ersetzen Sie in Visual Studio Code den Platzhalter **YOUR_ADMIN_KEY** durch den Schlüssel, den Sie in die Zwischenablage kopiert haben.
6. Speichern Sie die Änderungen in **modify-search.cmd**. (Führen Sie die Datei aber noch nicht aus!)

### <a name="review-and-modify-the-skillset"></a>Überprüfen und Ändern des Skillsets

1. Öffnen Sie in Visual Studio Code im Ordner **modify-search** die Datei **skillset.json**. Daraufhin wird eine JSON-Definition für **margies-skillset** angezeigt.
2. Beachten Sie das **cognitiveServices**-Objekt am Anfang der Skillsetdefinition, das für die Verbindung der Cognitive Services-Ressource mit dem Skillset verwendet wird.
3. Öffnen Sie im Azure-Portal Ihre Cognitive Services-Ressource (<u>nicht</u> Ihre Azure Cognitive Search-Ressource!), und zeigen Sie die zugehörige **Schlüssel**-Seite an. Kopieren Sie dann den **Schlüssel 1** in die Zwischenablage.
4. Ersetzen Sie in Visual Studio Code in **skillset.json** den Platzhalter **YOUR_COGNITIVE_SERVICES_KEY** durch den Cognitive Services-Schlüssel, den Sie in die Zwischenablage kopiert haben.
5. Scrollen Sie durch die JSON-Datei. Sie sehen, dass die Datein Definitionen für die Skills enthält, die Sie in der Azure Cognitive Search-Benutzeroberfläche im Azure-Portal erstellt haben. Am Ende der Liste der Skills wurde ein weiterer Skill mit der folgenden Definition hinzugefügt:

    ```
    {
        "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
        "defaultLanguageCode": "en",
        "name": "get-sentiment",
        "description": "New skill to evaluate sentiment",
        "context": "/document",
        "inputs": [
            {
                "name": "text",
                "source": "/document/merged_content"
            },
            {
                "name": "languageCode",
                "source": "/document/language"
            }
        ],
        "outputs": [
            {
                "name": "score",
                "targetName": "sentimentScore"
            }
        ]
    }
    ```

Der neue Skill heißt **get-sentiment** und wertet für jede **document**-Ebene in einem Dokument den Text aus, der im Feld **merged_content** des indizierten Dokuments gefunden wurde. (Dieses Feld enthält den Inhalt der Quelle sowie alle Texte, die aus Bildern im Inhalt extrahiert wurden.) Dabei wird die extrahierte **Sprache** des Dokuments verwendet (standardmäßig Englisch) und eine Bewertung für die Stimmung des Inhalts ermittelt. Diese Bewertung wird dann in einem neuen Feld namens **sentimentScore** ausgegeben.

6. Speichern Sie die Änderungen, die Sie an **skillset.json** vorgenommen haben.

### <a name="review-and-modify-the-index"></a>Überprüfen und Ändern des Index

1. Öffnen Sie in Visual Studio Code im Ordner **modify-search** die Datei **index.json**. Daraufhin wird eine JSON-Definition für **margies-index** angezeigt.
2. Scrollen Sie durch den Index, und schauen Sie sich die Felddefinitionen an. Einige Felder basieren auf Metadaten und Inhalten im Quelldokument, andere sind die Ergebnisse der Skills im Skillset.
3. Beachten Sie, dass am Ende der Liste der Felder, die Sie im Azure-Portal definiert haben, zwei zusätzliche Felder hinzugefügt wurden:

    ```
    {
        "name": "sentiment",
        "type": "Edm.Double",
        "facetable": false,
        "filterable": true,
        "retrievable": true,
        "sortable": true
    },
    {
        "name": "url",
        "type": "Edm.String",
        "facetable": false,
        "filterable": true,
        "retrievable": true,
        "searchable": false,
        "sortable": false
    }
    ```

4. Das Feld **sentiment** wird verwendet, um die Ausgabe des Skills **get-sentiment** hinzuzufügen, der zum Skillset hinzugefügt wurde. Das Feld **url** wird verwendet, um die URL für jedes indizierte Dokument basierend auf dem aus der Datenquelle extrahierten **metadata_storage_path**-Wert hinzuzufügen. Beachten Sie, dass der Index bereits das Feld **metadata_storage_path** enthält. Dieses Base-64-codierte Feld wird jedoch als Indexschlüssel verwendet, sodass es als Schlüssel wirksam ist. Clientanwendungen müssen das Feld jedoch decodieren, wenn sie den tatsächlichen URL-Wert als Feld verwenden möchten. Dieses Problem kann durch Hinzufügen eines zweiten Felds für den nicht codierten Wert gelöst werden.

### <a name="review-and-modify-the-indexer"></a>Überprüfen und Ändern des Indexers

1. Öffnen Sie in Visual Studio Code im Ordner **modify-search** die Datei **indexer.json**. Daraufhin wird eine JSON-Definition für **margies-indexer** angezeigt, die Felder, die aus den Inhalten und Metadaten von Dokumenten extrahiert wurden (im Abschnitt **fieldMappings**), sowie Werte, die von Skills im Skillset extrahiert wurden (im Abschnitt **outputFieldMappings**), Feldern im Index zuordnet.
3. Beachten Sie in der Liste **fieldMappings**, dass der **metadata_storage_path**-Wert dem Base-64-codierten Schlüsselfeld zugeordnet ist. Dieser wurde erstellt, als **metadata_storage_path** als Schlüssel zugewiesen und die Option zum Codieren des Schlüssels im Azure-Portal ausgewählt wurde. Darüber hinaus ordnet eine neue Zuordnung dem **url**-Feld explizit den gleichen Wert zu, aber ohne Base-64-Codierung:

    ```
    {
        "sourceFieldName" : "metadata_storage_path",
        "targetFieldName" : "url"
    }
    
    ```

Alle anderen Metadaten- und Inhaltsfelder im Quelldokument werden implizit gleichnamigen Feldern im Index zugeordnet.

4. Überprüfen Sie den Abschnitt **ouputFieldMappings**, in dem die Ausgaben der Skills im Skillset Indexfeldern zugeordnet werden. Die meisten Zuordnungen entsprechen den Auswahlen, die Sie in der Benutzeroberfläche vorgenommen haben. Die folgende Zuordnung wurde jedoch hinzugefügt, um den **sentimentScore**-Wert, der aus dem „get-sentiment“-Skill extrahiert wurde, dem **sentiment**-Feld zuzuordnen, das zum Index hinzugefügt wurde:

    ```
    {
        "sourceFieldName": "/document/sentimentScore",
        "targetFieldName": "sentiment"
    }
    ```

### <a name="use-the-rest-api-to-update-the-search-solution"></a>Verwenden der REST-API zum Aktualisieren der Suchlösung

1. Klicken Sie mit der rechten Maustaste auf den Ordner **modify-search**, und öffnen Sie ein integriertes Terminal.
2. Geben Sie im Terminalbereich für den **modify-search**-Ordner den folgenden Befehl ein, um das Skript **modify-search.cmd** auszuführen, das die JSON-Definitionen an die REST-Schnittstelle sendet und die Indizierung einleitet.

    ```
    modify-search
    ```

3. Kehren Sie zur Seite **Übersicht** für Ihre Azure Cognitive Search-Ressource im Azure-Portal zurück, nachdem das Skript ausgeführt wurde, und zeigen Sie die Seite **Indexer** an. Wählen Sie dann regelmäßig **Aktualisieren** aus, um den Fortschritt des Indizierungsvorgangs nachzuverfolgen. Dies kann eine Minuten in Anspruch nehmen.

    *Möglicherweise werden für einige Dokumente, die zu groß sind, um die Stimmung auszuwerten, einige Warnungen angezeigt. Häufig erfolgt die Stimmungsanalyse auf Seiten- oder Satzebene und nicht für das vollständige Dokument. In diesem Fall sind jedoch die meisten Dokumente – insbesondere die Hotelbewertungen – kurz genug, damit nützliche Stimmungsbewertungen auf Dokumentebene ausgewertet werden können.*

### <a name="query-the-modified-index"></a>Abfragen des geänderten Index

1. Wählen Sie oben auf dem Blatt für Ihre Azure Cognitive Search-Ressource die Option **Suchexplorer** aus.
2. Geben Sie im Suchexplorer im Feld **Abfragezeichenfolge** die folgende Zeichenfolge ein, und wählen Sie dann **Suchen** aus.

    ```
    search=London&$select=url,sentiment,keyphrases&$filter=metadata_author eq 'Reviewer' and sentiment gt 0.5
    ```

    Diese Abfrage ruft die Elemente **url**, **sentiment** und **keyphrases** für alle Dokumente ab, in denen *London* erwähnt wird, die von *Reviewer* erstellt wurden und deren **sentiment**-Bewertung größer als *0,5* ist (d. h. positive Rezensionen, die London erwähnen).

3. Schließen Sie die **Suchexplorer**-Seite, um zur Seite **Übersicht** zurückzukehren.

## <a name="create-a-search-client-application"></a>Erstellen einer Suchclientanwendung

Nachdem Sie nun über einen brauchbaren Index verfügen, können Sie ihn in einer Clientanwendung verwenden. Sie können dafür die REST-Schnittstelle in Anspruch nehmen, um Anforderungen zu übermitteln und Antworten im JSON-Format über HTTP zu empfangen. Sie können auch das Software Development Kit (SDK) für Ihre bevorzugte Programmiersprache verwenden. In dieser Übung wird das SDK verwendet.

> **Hinweis**: Sie können auswählen, ob Sie das SDK für **C#** oder **Python** verwenden möchten. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

### <a name="get-the-endpoint-and-keys-for-your-search-resource"></a>Abrufen des Endpunkts und der Schlüssel für Ihre Suchressource

1. Notieren Sie sich im Azure-Portal auf der Seite **Übersicht** für Ihre Azure Cognitive Search Ressource den **Url**-Wert, der ähnlich wie **https://*Name_Ihrer_Ressource*.search.windows.net** aussehen sollte. Dies ist der Endpunkt für Ihre Suchressource.
2. Beachten Sie auf der Seite **Schlüssel**, dass zwei **Administratorschlüssel** und ein einzelner **Abfrageschlüssel** vorhanden sind. Ein *Administratorschlüssel* wird zum Erstellen und Verwalten von Suchressourcen verwendet. Ein *Abfrageschlüssel* wird von Clientanwendungen verwendet, die nur Suchabfragen ausführen müssen.

    *Sie benötigen den Endpunkt und den Abfrageschlüssel für Ihre Clientanwendung.*

### <a name="prepare-to-use-the-azure-cognitive-search-sdk"></a>Vorbereiten der Verwendung des Azure Cognitive Search SDK

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **22-create-a-search-solution**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **margies-travel**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Azure Cognitive Search SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**
    
    ```
    dotnet add package Azure.Search.Documents --version 11.1.1
    ```
    
    **Python**
    
    ```
    pip install azure-search-documents==11.0.0
    ```
    
3. Zeigen Sie den Inhalt des Ordners **margies-travel** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass sie dem **Endpunkt** und dem **Abfrageschlüssel** für Ihre Azure Cognitive Search-Ressource entsprechen. Speichern Sie die Änderungen.

### <a name="explore-code-to-search-an-index"></a>Untersuchen des Codes zum Durchsuchen eines Index

Der Ordner **margies-travel** enthält Codedateien für eine Webanwendung (eine in Microsoft C# geschriebene *ASP.NET Razor*-Webanwendung bzw. eine Python-basierte *Flask*-Anwendung), die Suchfunktionen enthält.

1. Öffnen Sie abhängig von der ausgewählten Programmiersprache die folgende Codedatei in der Webanwendung:
    - **C#** : Pages/Index.cshtml.cs
    - **Python**: app.py
2. Suchen Sie am Anfang der Codedatei den Kommentar **Import search namespaces**, und notieren Sie sich die Namespaces, die für die Arbeit mit dem Azure Cognitive Search SDK importiert wurden:
3. Suchen Sie in der Funktion **search_query** den Kommentar **Create a search client**, und beachten Sie, dass der Code mit dem Endpunkt und dem Abfrageschlüssel für Ihre Azure Cognitive Search-Ressource ein **SearchClient**-Objekt erstellt:
4. Suchen Sie in der Funktion **search_query** den Kommentar **Submit search query**, und überprüfen Sie den Code zum Übermitteln einer Suche nach dem angegebenen Text mit den folgenden Optionen:
    - Ein *Suchmodus*, der vorschreibt, dass **alle** einzelnen im Suchtext enthaltenen Wörter gefunden werden.
    - In den Ergebnissen ist die Gesamtanzahl der von der Suche gefundenen Dokumente enthalten.
    - Die Ergebnisse werden so gefiltert, dass sie nur Dokumente enthalten, die mit dem angegebenen Filterausdruck übereinstimmen.
    - Die Ergebnisse werden in der angegebenen Sortierreihenfolge sortiert.
    - Jeder diskrete Wert des **metadata_author**-Felds wird als *Facet* zurückgegeben, die zum Anzeigen vordefinierter Werte für die Filterung verwendet werden kann.
    - In den Ergebnissen sind bis zu drei Extrakte der Felder **merged_content** und **imageCaption** mit hervorgehobenen Suchbegriffen enthalten.
    - Die Ergebnisse enthalten nur die angegebenen Felder.

### <a name="explore-code-to-render-search-results"></a>Untersuchen des Codes zum Rendern von Suchergebnissen

Die Web-App enthält bereits Code zum Verarbeiten und Rendern der Suchergebnisse.

1. Öffnen Sie abhängig von der ausgewählten Programmiersprache die folgende Codedatei in der Webanwendung:
    - **C#** :Pages/Index.cshtml
    - **Python**: templates/search.html
2. Untersuchen Sie den Code zum Rendern der Seite, auf der die Suchergebnisse angezeigt werden. Beachten Sie, Folgendes:
    - Die Seite beginnt mit einem Suchformular, mit dem der Benutzer eine neue Suche übermitteln kann (in der Python-Version der Anwendung ist dieses Formular in der Vorlage **base.html** definiert), auf die am Anfang der Seite verwiesen wird.
    - Anschließend wird ein zweites Formular gerendert, in dem der Benutzer die Suchergebnisse einschränken kann. Der Code für dieses Formular erledigt Folgendes:
        - Abrufen und Anzeigen der Anzahl der Dokumente in den Suchergebnissen.
        - Abrufen der Facetwerte für das **metadata_author**-Feld und Anzeigen der Werte als Optionsliste für die Filterung.
        - Erstellen einer Dropdownliste mit Sortieroptionen für die Ergebnisse.
    - Der Code durchläuft dann die Suchergebnisse und rendert das jeweilige Ergebnis wie folgt:
        - Anzeigen des Felds **metadata_storage_name** (Dateiname) als Link zur Adresse im **url**-Feld.
        - Anzeigen einer *Hervorhebung* für Suchbegriffe, die in den Feldern **merged_content** und **imageCaption** gefunden wurden, um die Suchbegriffe im jeweiligen Kontext besser sichtbar zu machen.
        - Anzeigen der Felder **metadata_author**, **metadata_storage_size**, **metadata_storage_last_modified** und **language**.
        - Anzeigen der **Stimmung** mithilfe eines Emoticons (&#128578; für Bewertungen von 0,5 oder höher und &#128577; für Bewertungen kleiner als 0,5).
        - Anzeigen der ersten fünf **keyphrases** (sofern vorhanden).
        - Anzeigen der ersten fünf **locations** (sofern vorhanden).
        - Anzeigen der ersten fünf **imageTags** (sofern vorhanden) an.

### <a name="run-the-web-app"></a>Ausführen der Web-App

 1. Kehren Sie zum integrierten Terminal für den Ordner **margies-travel** zurück, und geben Sie dann den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    flask run
    ```

2. Folgen Sie in der Meldung, die bei einem erfolgreichen Start der App angezeigt wird, dem Link zur ausgeführten Webanwendung ( *http://localhost:5000/* oder *http://127.0.0.1:5000/* ), um die Margies Travel-Website in einem Webbrowser zu öffnen.
3. Geben Sie auf der Website von Margie's Travel im Suchfeld den Begriff **London hotel** ein, und klicken Sie auf **Search** (Suchen).
4. Sehen Sie sich die Suchergebnisse an. Sie enthalten den Dateinamen (mit einem Link zur Datei-URL), extrahierte Dateiinhalte, in denen die Suchbegriffe (*London* und *hotel*) hervorgehoben sind, sowie weitere Attribute der Datei aus den Indexfeldern.
5. Beachten Sie, dass die Ergebnisseite einige Benutzeroberflächenelemente enthält, mit denen Sie die Ergebnisse einschränken können. Dazu gehören:
    - Ein *Filter* basierend auf einem Facettenwert für das **metadata_author**-Feld. Dies veranschaulicht, wie Sie *in Facets einteilbar* Felder verwenden können, um eine *Facet*-Liste zurückzugeben. Dabei handelt es sich um Felder mit einer kleinen Menge diskreter Werte, die als mögliche Filterwerte auf der Benutzeroberfläche angezeigt werden können.
    - Die Möglichkeit, die Ergebnisse basierend auf einem angegebenen Feld und der Sortierrichtung (aufsteigend oder absteigend) zu *anzuordnen*. Die Standardreihenfolge basiert auf der *Relevanz*, die als **search.score()** -Wert basierend auf einem *Bewertungsprofil* berechnet wird, das die Häufigkeit und Wichtigkeit von Suchbegriffen in den Indexfeldern auswertet.
6. Wählen Sie den Filter **Reviewer**, die Sortieroption **Positive to negative** und dann **Refine Results** aus.
7. Beachten Sie, dass die gefilterten Ergebnisse nur Bewertungen enthalten und in absteigender Reihenfolge Stimmung sortiert werden.
8. Geben Sie im **Search**-Feld für eine neue Suche **quiet hotel in New York** ein, und überprüfen Sie die Ergebnisse.
9. Probieren Sie die folgenden Suchbegriffe aus:
    - **Tower of London** (Beachten Sie, dass dieser Begriff in einigen Dokumenten als *Schlüsselbegriff* angegeben wird.)
    - **skyscraper** (Beachten Sie, dass dieses Wort nicht im eigentlichen Inhalt von Dokumenten enthalten ist, sondern in den *Bildbeschriftungen* und *Bildtags*, die für Bilder in einigen Dokumenten generiert wurden.)
    - **Mojave desert** (Beachten Sie, dass dieser Begriff in einigen Dokumenten als *Ort* angegeben wird.)
10. Schließen Sie die Browserregisterkarte mit der Website von Margie's Travel, und kehren Sie zu Visual Studio Code zurück. Drücken Sie dann im Python-Terminal für den Ordner **margies-travel** (in dem die .NET- bzw. Flask-Anwendung ausgeführt wird) STRG+C, um die App zu beenden.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zu Azure Cognitive Search finden Sie in der [Azure Cognitive Search-Dokumentation](https://docs.microsoft.com/azure/search/search-what-is-azure-search).
