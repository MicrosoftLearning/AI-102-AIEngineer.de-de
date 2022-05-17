---
lab:
  title: Erstellen eines Language Understanding-Modells mit dem Language-Dienst (Vorschau)
ms.openlocfilehash: 92d556e50c8f5c827e16f1e5ad301d3b59c1ad6e
ms.sourcegitcommit: cb2edc850cec8390a81212d9b8f6a279d2f42b4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/04/2022
ms.locfileid: "141368842"
---
# <a name="create-a-language-understanding-model-with-the-language-service-preview"></a>Erstellen eines Language Understanding-Modells mit dem Language-Dienst (Vorschau)

> **Hinweis:** Das Feature „Conversational Language Understanding“ des Language-Diensts befindet sich derzeit in der Vorschau und kann geändert werden. In einigen Fällen kann beim Modelltraining ein Fehler auftreten. Versuchen Sie es in diesem Fall erneut.  

Mithilfe des Language-Diensts können Sie ein *Conversational Language Understanding-Modell* definieren, mit dem Anwendungen Benutzereingaben in natürlicher Sprache interpretieren, die *Absicht* der Benutzer*innen vorhersagen (was sie erreichen möchten) und alle *Entitäten* identifizieren können, auf die die Absicht angewendet werden soll.

Beispielsweise kann erwartet werden, dass ein Conversational Language-Modell für eine Uhranwendung Eingaben wie die folgenden verarbeitet:

*Wie spät ist es in London?*

Diese Art von Eingabe ist ein Beispiel für eine *Äußerung* (etwas, das ein Benutzer sagen oder eingeben kann), deren gewünschte *Absicht* es ist, die Zeit an einem bestimmten Ort (einer *Entität*) zu erfahren, in diesem Fall London.

> **Hinweis:** Die Aufgabe eines Conversational Language-Modells besteht darin, die Absicht von Benutzern vorherzusagen und alle Entitäten zu identifizieren, für die die Absicht gilt. Es ist <u>nicht</u> der Auftrag eines Conversational Language-Modells, die zur Erfüllung der Absicht erforderlichen Aktionen tatsächlich durchzuführen. Beispielsweise kann die Uhranwendung ein Conversational Language-Modell verwenden, um zu erkennen, dass der Benutzer die Uhrzeit in London erfahren möchte. Aber die Clientanwendung selbst muss dann die Logik implementieren, um die richtige Zeit zu bestimmen und dem Benutzer zu präsentieren.

## <a name="create-a-language-service-resource"></a>Erstellen einer Ressource vom Typ Sprachdienst

Um ein Conversational Language-Modell zu erstellen, benötigen Sie eine **Sprachdienstressource** in einer unterstützten Region. Zum Zeitpunkt der Erstellung dieses Artikels werden nur die Regionen „USA, Westen 2“ und „Europa, Westen“ unterstützt.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Klicken Sie auf die Schaltfläche **&#65291;Ressource erstellen**, suchen Sie nach *Sprache*, und erstellen Sie eine **Language**-Dienstressource mit den folgenden Einstellungen.

    - **Features**: Verwenden Sie die Standardfeatures, zu denen auch Conversational Language Understanding (Vorschau) gehört. 
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine Ressourcengruppe (wenn Sie eine gehostete Lab-Umgebung verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen, verwenden Sie dann die bereitgestellte Ressourcengruppe).*
    - **Region**: „USA, Westen 2“ oder „Europa, Westen“
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Wählen Sie den Tarif Standard (S) (Conversational Language Understanding wird derzeit im Free-Tarif nicht unterstützt).
    - **Rechtliche Bestimmungen**: _Zustimmen_ 
    - **Hinweis zur verantwortlichen Verwendung von KI**: _Zustimmen_
3. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.

## <a name="create-a-conversational-language-understanding-project"></a>Erstellen eines Conversational Language Understanding-Projekts

Nachdem Sie nun eine Dokumenterstellungsressource erstellt haben, können Sie sie zum Erstellen eines Conversational Language Understanding-Projekts verwenden.

1. Öffnen Sie auf einer neuen Browserregisterkarte das Language Studio-Portal unter `https://language.cognitive.azure.com/`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.

2. Wenn Sie zur Auswahl einer Sprachressource aufgefordert werden, wählen Sie die folgenden Einstellungen aus:

    - **Azure-Verzeichnis**: Das Azure-Verzeichnis mit Ihrem Abonnement.
    - **Azure-Abonnement**: Ihr Azure-Abonnement.
    - **Sprachressource**: Die Sprachressource, die Sie zuvor erstellt haben.

3. Sollten Sie <u>nicht</u> zur Auswahl einer Language-Ressource aufgefordert werden, kann dies daran liegen, dass Sie bereits eine andere Language-Ressource zugewiesen haben. Gehen Sie in diesem Fall wie folgt vor:

    1. Klicken Sie auf der Leiste oben auf die Schaltfläche **Einstellungen (&#9881;)**.
    2. Gehen Sie auf der Seite **Einstellungen** zur Registerkarte **Ressourcen**.
    3. Wählen Sie die soeben erstellte Sprachressource aus, und klicken Sie auf **Switch resource** (Ressource wechseln).
    4. Klicken Sie oben auf der Seite auf **Language Studio**, um zur Startseite von Language Studio zurückzukehren.

4. Klicken Sie oben im Portal im Menü **Neu erstellen** auf die Option **Conversational Language Understanding**.

5. Geben Sie im Dialogfeld **Projekt erstellen** auf der Seite **Enter basic information** (Grundlegende Informationen eingeben) die folgenden Details ein, und klicken Sie dann auf **Weiter**:
    - **Name**: `Clock`
    - **Beschreibung:** `Natural language clock`
    - **Utterances primary language** (Primäre Sprache für Äußerungen): Englisch
    - **Enable multiple languages in project?** (Mehrere Sprachen im Projekt aktivieren?): *Nicht ausgewählt*

7. Klicken Sie auf der Seite **Review and finish** (Überprüfen und Fertigstellen) auf **Create** (Erstellen).

## <a name="create-intents"></a>Erstellen von Absichten

Als Erstes definieren Sie im neuen Projekt einige Absichten.

> **Tipp:** Wenn Sie an Ihrem Projekt arbeiten und einige Tipps angezeigt werden, lesen Sie sie, und klicken Sie auf **Verstanden**, um sie zu schließen, oder klicken Sie auf **Alle überspringen**.

1. Klicken Sie auf der Seite **Build schema** (Schema erstellen) auf der Registerkarte **Intents** (Absichten) auf **&#65291;Hinzufügen**, um eine neue Absicht namens **GetTime** hinzuzufügen.

2. Klicken Sie auf die neue Absicht **GetTime**, um sie zu bearbeiten, und fügen Sie die folgenden Äußerungen als Beispielbenutzereingabe hinzu:

    `what is the time?`

    `what's the time?`

    `what time is it?`

    `tell me the time`

3. Nachdem Sie diese Äußerungen hinzugefügt haben, klicken Sie auf **Änderungen speichern**, und navigieren Sie zurück zur Seite **Build schema** (Schema erstellen).

4. Fügen Sie eine weitere neue Absicht namens **GetDay** mit den folgenden Äußerungen hinzu:

    `what day is it?`

    `what's the day?`

    `what is the day today?`

    `what day of the week is it?`

5. Nachdem Sie diese Äußerungen hinzugefügt und gespeichert haben, wechseln Sie zurück zur Seite **Build schema** (Schema erstellen), und fügen Sie eine weitere neue Absicht namens **GetDate** mit den folgenden Äußerungen hinzu:

    `what date is it?`

    `what's the date?`

    `what is the date today?`

    `what's today's date?`

6. Nachdem Sie diese Äußerungen hinzugefügt haben, speichern Sie sie, und löschen Sie den **GetDate**-Filter auf der Seite „Äußerungen“, um alle Äußerungen für alle Absichten anzuzeigen.

## <a name="train-and-test-the-model"></a>Trainieren und Testen des Modells

Nachdem Sie nun einige Absichten hinzugefügt haben, trainieren Sie das Sprachmodell und testen, ob es die Absichten aus Benutzereingaben richtig vorhersagen kann.

1. Wählen Sie im Bereich links die Seite **Modell trainieren** und dann die Option **Trainingsauftrag starten** aus.

2. Wählen Sie im Dialogfeld **Trainingsauftrag starten** die Option zum Trainieren eines neuen Modells aus, benennen Sie es **Clock**, und stellen Sie sicher, dass die Option zum Ausführen der Auswertung beim Training aktiviert ist. 

3. Um den Prozess des Modelltrainings zu beginnen, klicken Sie auf **Trainieren**.

4. Wenn das Training abgeschlossen ist (was mehrere Minuten dauern kann), ändert sich der **Status** für den Auftrag in **Training erfolgreich**.

5. Wählen Sie die Seite **Modelldetails anzeigen** und dann das Modell **Clock** aus. Sehen Sie sich die Metriken für die Gesamtauswertung und die Auswertung pro Absicht (*Genauigkeit*, *Abruf* und *F1-Score*) sowie die *Konfusionsmatrix* an, die durch die beim Training durchgeführte Auswertung generiert wurde. (Beachten Sie, dass aufgrund der geringen Anzahl von Beispieläußerungen möglicherweise nicht alle Absichten in die Ergebnisse einbezogen werden können).

    >**Hinweis:** Weitere Informationen zu den Auswertungsmetriken finden Sie in der [Dokumentation](https://docs.microsoft.com/azure/cognitive-services/language-service/conversational-language-understanding/concepts/evaluation-metrics).

5. Wählen Sie auf der Seite **Modell bereitstellen** die Option **Bereitstellung hinzufügen** aus.

5. Wählen Sie im Dialogfeld **Bereitstellung hinzufügen** die Option **Neuen Bereitstellungsnamen erstellen** aus, und geben Sie dann **Production** (Produktion) ein.

5. Wählen Sie das Modell **Clock** (Uhr) aus, und klicken Sie auf **Übermitteln**. Diese Bereitstellung kann einige Zeit in Anspruch nehmen.

6. Wenn das Modell bereitgestellt wurde, wählen Sie auf der Seite **Test model** (Modell testen) das Modell **Clock** (Uhr) aus.

6. Wählen Sie auf der Seite **Testmodell: Clock** in der Dropdownliste **Bereitstellungsname** die Option **Production** (Produktion) aus.  

7. Geben Sie den folgenden Text ein, und klicken Sie dann auf **Test ausführen**:

    `what's the time now?`

    Überprüfen Sie das zurückgegebene Ergebnis, und achten Sie darauf, dass es die vorhergesagte Absicht (bei der es sich um **GetTime** handeln sollte) und eine Zuverlässigkeitsbewertung enthält, die die Wahrscheinlichkeit angibt, die das Modell für die vorhergesagte Absicht berechnet hat. Auf der JSON-Registerkarte sehen Sie die Zuverlässigkeit der jeweiligen potenziellen Absicht (wobei die vorhergesagte Absicht die höchste Zuverlässigkeitsbewertung aufweist).

8. Löschen Sie das Textfeld, und führen Sie dann einen weiteren Test mit folgendem Text aus:

    `tell me the time`

    Überprüfen Sie erneut die vorhergesagte Absicht und die Zuverlässigkeitsbewertung.

9. Verwenden Sie den folgenden Text:

    `what's the day today?`

    Hoffentlich sagt das Modell die **GetDay**-Absicht vorher.

## <a name="add-entities"></a>Hinzufügen von Entitäten

Bisher haben Sie ein paar Beispieläußerungen definiert, die sich Absichten zuordnen lassen. Die meisten realen Anwendungen enthalten komplexere Äußerungen, aus denen bestimmte Datenentitäten extrahiert werden müssen, um mehr Kontext für die Absicht zu erhalten.

### <a name="add-a-learned-entity"></a>Hinzufügen einer *durch maschinelles Lernen erworbenen* Entität

Die häufigste Art von Entität ist eine *durch maschinelles Lernen erworbene* Entität, bei der das Modell lernt, Entitätswerte anhand von Beispielen zu identifizieren.

1. Kehren Sie in Language Studio zur Seite **Build schema** (Schema erstellen) zurück, und klicken Sie dann auf der Registerkarte **Entitäten** auf **&#65291; Hinzufügen**, um eine neue Entität hinzuzufügen.

2. Geben Sie im Dialogfeld **Add an entity** (Entität hinzufügen) den Entitätsnamen **Location** (Ort) ein, und stellen Sie sicher, dass **Learned** (Durch maschinelles Lernen erworben) ausgewählt ist. Klicken Sie dann auf **Entität hinzufügen**.

3. Kehren Sie nach dem Erstellen der Entität **Location** (Ort) zur Seite **Build schema** (Schema erstellen) zurück, und wählen Sie dann auf der Registerkarte **Intents** (Absichten) die Absicht **GetTime** aus.

4. Geben Sie die folgende neue Beispieläußerung ein:

    `what time is it in London?`

5. Wenn die Äußerung hinzugefügt wurde, wählen Sie das Wort ***London** _ aus, und wählen Sie dann in der angezeigten Dropdownliste _ *Location** (Ort) aus, um anzugeben, dass „London“ ein Beispiel für einen Standort ist.

6. Fügen Sie eine weitere Beispieläußerung hinzu:

    `Tell me the time in Paris?`

7. Nachdem die Äußerung hinzugefügt wurde, wählen Sie das Wort ***Paris** _ aus, und ordnen Sie es der Entität _ *Location** (Ort) zu.

8. Fügen Sie eine weitere Beispieläußerung hinzu:

    `what's the time in New York?`

9. Nachdem die Äußerung hinzugefügt wurde, wählen Sie die Wörter ***New York** _ aus, und ordnen Sie sie der Entität _ *Location** (Ort) zu.

10. Klicken Sie auf **Änderungen speichern**, um die neuen Äußerungen zu speichern.

### <a name="add-a-list-entity"></a>Hinzufügen einer *list*-Entität (Liste)

In einigen Fällen können gültige Werte für eine Entität auf eine Liste mit bestimmten Begriffen und Synonymen beschränkt werden. Dies kann der App helfen, Instanzen der Entität in Äußerungen zu identifizieren.

1. Kehren Sie in Language Studio zur Seite **Build schema** (Schema erstellen) zurück, und klicken Sie dann auf der Registerkarte **Entitäten** auf **&#65291; Hinzufügen**, um eine neue Entität hinzuzufügen.

2. Geben Sie im Dialogfeld **Add an entity** (Entität hinzufügen) den Entitätsnamen **Weekday** (Wochentag) ein, und wählen Sie dann den Entitätstyp **List** (Liste) aus. Klicken Sie dann auf **Entität hinzufügen**.

3. Klicken Sie auf der Seite für die Entität **Weekday** (Wochentag) im Abschnitt **List** (Liste) auf **&#65291; Neue Liste hinzufügen**. Geben Sie dann den folgenden Wert und das Synonym ein, und klicken Sie auf **Speichern**:

    | Listenschlüssel | Synonyme|
    |-------------------|---------|
    | Sonntag | Sun |

4. Wiederholen Sie den vorherigen Schritt, um die folgenden Listenkomponenten hinzuzufügen:

    | Wert | Synonyme|
    |-------------------|---------|
    | Montag | Mon |
    | Dienstag | Tue, Tues |
    | Wednesday | Wed, Weds |
    | Thursday | Thur, Thurs |
    | Freitag | Fri |
    | Samstag | Sat |

5. Navigieren Sie zurück zur Seite **Build schema** (Schema erstellen), und wählen Sie auf der Registerkarte **Intents** (Absichten) die Absicht **GetDate** aus.

6. Geben Sie die folgende neue Beispieläußerung ein:

    `what date was it on Saturday?`

7. Wenn die Äußerung hinzugefügt wurde, wählen Sie das Wort ***Saturday** _ (Samstag) und in der angezeigten Dropdownliste _*Weekday** (Wochentag) aus.

8. Fügen Sie eine weitere Beispieläußerung hinzu:

    `what date will it be on Friday?`

9. Wenn die Äußerung hinzugefügt wurde, ordnen Sie **Friday** (Freitag) der Entität **Weekday** (Wochentag) hinzu.

10. Fügen Sie eine weitere Beispieläußerung hinzu:

    `what will the date be on Thurs?`

11. Wenn die Äußerung hinzugefügt wurde, ordnen Sie **Thurs** (Donners.) der Entität **Weekday** (Wochentag) hinzu.

12. Klicken Sie auf **Änderungen speichern**, um die neuen Äußerungen zu speichern.

### <a name="add-a-prebuilt-entity"></a>Hinzufügen einer *vordefinierten* Entität

Der Language-Dienst stellt eine Reihe *vordefinierter* Entitäten bereit, die häufig in Konversationsanwendungen verwendet werden.

1. Kehren Sie in Language Studio zur Seite **Build schema** (Schema erstellen) zurück, und klicken Sie dann auf der Registerkarte **Entitäten** auf **&#65291; Hinzufügen**, um eine neue Entität hinzuzufügen.

2. Geben Sie im Dialogfeld **Add an entity** (Entität hinzufügen) den Entitätsnamen **Date** (Datum) ein, und wählen Sie dann den Entitätstyp **Prebuilt** (vordefiniert) aus. Klicken Sie dann auf **Entität hinzufügen**.

3. Klicken Sie auf der Seite für die Entität **Date** (Datum) im Abschnitt **Prebuilt** (Vordefiniert) auf **&#65291; Add new prebuilt** (Neue vordefinierte Entität hinzufügen).

4. Wählen Sie in der Liste **Select prebuilt** (Vordefinierte Entität auswählen) die Option **DateTime** aus, und klicken Sie dann auf **Speichern**.

5. Navigieren Sie zurück zur Seite **Build schema** (Schema erstellen), und wählen Sie auf der Registerkarte **Intents** (Absichten) die Absicht **GetDay** aus.

6. Geben Sie die folgende neue Beispieläußerung ein:

    `what day was 01/01/1901?`

7. Wenn die Äußerung hinzugefügt wurde, wählen Sie ***01/01/1901** _ und in der angezeigten Dropdownliste _*Date** (Datum) aus.

8. Fügen Sie eine weitere Beispieläußerung hinzu:

    `what day will it be on Dec 31st 2099?`

9. Wenn die Äußerung hinzugefügt wurde, ordnen Sie **Dec 31st 2099** der Entität **Date** (Datum) zu.

10. Klicken Sie auf **Änderungen speichern**, um die neuen Äußerungen zu speichern.

### <a name="retrain-the-model"></a>Erneutes Trainieren des Modells

Nachdem Sie das Schema geändert haben, müssen Sie das Modell erneut trainieren und testen.

1. Wählen Sie auf der Seite **Modell trainieren** die Option **Trainingsauftrag starten** aus.

1. Wählen Sie im Dialogfeld **Trainingsauftrag starten** die Option zum Überschreiben eines vorhandenen Modells aus, und geben Sie das Modell **Clock** an. Stellen Sie sicher, dass die Option zum Durchführen der Auswertung beim Training ausgewählt ist, und klicken Sie auf **Trainieren**, um das Modell zu trainieren. Bestätigen Sie, dass Sie das vorhandene Modell überschreiben möchten.

2. Wenn das Training abgeschlossen ist, wird der **Status** des Auftrags zu **Training erfolgreich** aktualisiert. 

2. Wählen Sie die Seite **Modelldetails anzeigen** und dann das Modell **Clock** aus. Sehen Sie sich die Metriken für die Gesamtauswertung, die Auswertung pro Entität und die Auswertung pro Absicht (*Genauigkeit*, *Abruf* und *F1-Score*) sowie die *Konfusionsmatrix* an, die durch die beim Training durchgeführte Auswertung generiert wurde. (Beachten Sie, dass aufgrund der geringen Anzahl von Beispieläußerungen möglicherweise nicht alle Absichten in die Ergebnisse einbezogen werden können).

3. Wählen Sie auf der Seite **Modell bereitstellen** die Option **Bereitstellung hinzufügen** aus.

3. Wählen Sie im Dialogfeld **Bereitstellung hinzufügen** die Option **Vorhandenen Bereitstellungsnamen außer Kraft setzen** aus, und wählen Sie dann **Production** (Produktion) aus.

3. Wählen Sie das Modell **Clock** aus, und klicken Sie dann auf **Übermitteln**, um es bereitzustellen. Dieser Vorgang kann einige Zeit dauern.

4. Wenn das Modell bereitgestellt wird, wählen Sie auf der Seite **Testmodell** das Modell **Clock** aus, wählen Sie die Bereitstellung **production** (Produktion) aus, und testen Sie es dann mit dem folgenden Text:

    `what's the time in Edinburgh?`

5. Überprüfen Sie das zurückgegebene Ergebnis, das die Absicht **GetTime** und die Entität **Location** (Ort) mit dem Textwert „Edinburgh“ vorhersagen sollte.

6. Testen Sie die folgenden Äußerungen:

    `what time is it in Tokyo?`
    
    `what date is it on Friday?`

    `what's the date on Weds?`

    `what day was 01/01/2020?`

    `what day will Mar 7th 2030 be?`

## <a name="use-the-model-from-a-client-app"></a>Verwenden des Modells aus einer Client-App

In einem echten Projekt würden Sie Absichten und Entitäten iterativ optimieren, erneut trainieren und erneut testen, bis Sie mit der Vorhersageleistung zufrieden sind. Wenn Sie das Modell getestet haben und mit der Vorhersageleistung zufrieden sind, können Sie es in einer Client-App verwenden, indem Sie die entsprechende REST-Schnittstelle aufrufen. In dieser Übung verwenden Sie das Hilfsprogramm *cURL*, um den REST-Endpunkt für Ihr Modell aufzurufen.

1. Wählen Sie in Language Studio auf der Seite **Modell bereitstellen** das Modell **Clock** (Uhr) aus. Klicken Sie dann auf **Get prediction URL** (Vorhersage-URL abrufen).

2. Beachten Sie im Dialogfeld **Get prediction URL** (Vorhersage-URL abrufen), dass die URL für den Vorhersageendpunkt zusammen mit einer Beispielanforderung angezeigt wird, die aus einem **cURL**-Befehl besteht, der eine HTTP POST-Anforderung an den Endpunkt sendet, um den Schlüssel für Ihre Language-Ressource im Header anzugeben und eine Abfrage und Sprache in die Anforderungsdaten einzuschließen.

3. Kopieren Sie die Beispielanforderung, und fügen Sie sie in Ihren bevorzugten Text-Editor ein (z. B. Editor).

4. Ersetzen Sie die folgenden Platzhalter:
    - **YOUR_QUERY_HERE**: *What's the time in Sydney* (Wie viel Uhr ist es in Sydney)
    - **QUERY_LANGUAGE_HERE**: *EN*

    Der Befehl sollte dem folgenden Code ähneln:

    ```
    curl -X POST "https://some-name.cognitiveservices.azure.com/language/:analyze-conversations?projectName=Clock&deploymentName=production&api-version=2021-11-01-preview" -H "Ocp-Apim-Subscription-Key: 0ab1c23de4f56..."  -H "Apim-Request-Id: 9zy8x76wv5u43...." -H "Content-Type: application/json" -d "{\"verbose\":true,\"query\":\"What's the time in Sydney?\",\"language\":\"EN\"}"
    ```

5. Öffnen Sie eine Eingabeaufforderung (Windows) oder eine Bash-Shell (Linux/Mac).

6. Kopieren Sie den bearbeiteten cURL-Befehl, fügen Sie ihn in Ihre Befehlszeilenschnittstelle ein, und führen Sie ihn aus.

7. Zeigen Sie den resultierenden JSON-Code an, der wie im Folgenden gezeigt die vorhergesagte Absicht und Entitäten enthalten sollte:

    ```
    {"query":"What's the time in Sydney?","prediction":{"topIntent":"GetTime","projectKind":"conversation","intents":[{"category":"GetTime","confidenceScore":0.9998859},{"category":"GetDate","confidenceScore":9.8372206E-05},{"category":"GetDay","confidenceScore":1.5763446E-05}],"entities":[{"category":"Location","text":"Sydney","offset":19,"length":6,"confidenceScore":1}]}}
    ```

8. Überprüfen Sie die von Ihrem Modell zurückgegebene JSON-Antwort, um sicherzustellen, dass die am besten bewertete Absicht **GetTime** ist.

9. Ändern Sie die Abfrage im cURL-Befehl in `What's today's date?`, führen Sie ihn dann aus, und überprüfen Sie den resultierenden JSON-Code.

10. Probieren Sie die folgenden Abfragen aus:

    `What day will Jan 1st 2050 be?`

    `What time is it in Glasgow?`

    `What date will next Monday be?`

## <a name="export-the-project"></a>Exportieren des Projekts

Sie können Language Studio verwenden, um Ihr Language Understanding-Modell zu entwickeln und zu testen. In einem Softwareentwicklungsprozess für DevOps sollten Sie jedoch eine Definition mit Quellcodeverwaltung des Projekts beibehalten, die in CI/CD-Pipelines (Continuous Integration und Continuous Delivery) eingeschlossen werden kann. Sie *können* die Sprach-REST-API zwar in Codeskripts verwenden, um das Modell zu erstellen und zu trainieren, aber eine einfachere Möglichkeit besteht darin, das Modellschema über das Portal zu erstellen und als *JSON-Datei* zu exportieren, die in einer anderen Instanz des Language Understanding-Diensts importiert und neu trainiert werden kann. Dieser Ansatz ermöglicht es Ihnen, die Produktivitätsvorteile der grafischen Language Studio-Benutzeroberfläche zu nutzen und gleichzeitig die Portabilität und Reproduzierbarkeit für das Modell beizubehalten.

1. Wählen Sie in Language Studio auf der Seite **Projekte** das Projekt **Clock** (Uhr) aus. Klicken Sie nicht auf **Clock**, wählen Sie das Kreissymbol aus, um das Projekt „Clock“ auszuwählen.

2. Klicken Sie auf die Schaltfläche **&#x2913; Exportieren**.

3. Speichern Sie die generierte Datei **Clock.json** an einem beliebigen Speicherort.

4. Öffnen Sie die heruntergeladene Datei in Ihrem bevorzugten Code-Editor (z. B. Visual Studio Code), um die JSON-Definition Ihres Projekts zu überprüfen.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zur Verwendung des **Sprachdiensts** zum Erstellen von Conversational Language Understanding-Lösungen finden Sie unter [Was ist Conversational Language Understanding (Vorschau)?](https://docs.microsoft.com/azure/cognitive-services/language-service/conversational-language-understanding/overview).
