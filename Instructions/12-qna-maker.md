---
lab:
  title: Erstellen einer „Fragen und Antworten“-Lösung
  module: Module 6 - Building a QnA Solution
ms.openlocfilehash: 786d4c30b4b4f85b5c85a7a9b8d500a497bb832d
ms.sourcegitcommit: 6c1ad9a67d6caadcfb7edb4f58e314eab306f720
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2021
ms.locfileid: "134463603"
---
# <a name="create-a-question-answering-solution"></a>Erstellen einer „Fragen und Antworten“-Lösung

Eines der häufigsten Unterhaltungsszenarien ist die Bereitstellung von Unterstützung über eine Wissensdatenbank mit häufig gestellten Fragen (FAQs). Viele Organisationen veröffentlichen FAQs als Dokumente oder Webseiten. Dies funktioniert gut bei einer kleinen Menge von Frage-Antwort-Paaren, aber die Suche in umfangreichen Dokumenten kann schwierig und zeitaufwändig sein.

Der **Sprachdienst** umfasst eine *Fragen und Antworten*-Funktion, mit der Sie eine Wissensdatenbank mit Frage-Antwort-Paaren erstellen können. Diese Datenbank kann mithilfe von Eingaben in natürlicher Sprache abgefragt werden und wird am häufigsten als Ressource verwendet, mit der ein Bot Antworten auf Fragen suchen kann, die von Benutzer*innen gesendet wurden.

> **Hinweis:** Die „Fragen und Antworten“-Funktion im Sprachdienst ist eine neuere Version des QnA Maker-Diensts, der weiterhin als separater Dienst verfügbar ist.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** noch nicht in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, führen Sie die folgenden Schritte aus, um dies zu tun. Öffnen Sie andernfalls den geklonten Ordner in Visual Studio Code.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## <a name="create-a-language-resource"></a>Erstellen einer Language-Ressource

Um eine Wissensdatenbank für die Fragen und Antworten zu erstellen und zu hosten, benötigen Sie eine **Sprachdienstressource** in Ihrem Azure-Abonnement.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Klicken Sie auf die Schaltfläche **&#65291;Ressource erstellen**, suchen Sie nach *Sprache*, und erstellen Sie eine **Sprachdienstressource**.
3. Klicken Sie im Block **Benutzerdefinierte Fragebeantwortung** auf **Auswählen**. Klicken Sie dann auf **Fortfahren, um Ihre Ressource zu erstellen**. Sie müssen die folgenden Einstellungen eingeben:
    
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie einen beliebigen verfügbaren Speicherort aus.*
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S.
    - **Azure Search-Speicherort**\*: *Wählen Sie einen Speicherort in derselben globalen Region wie Ihre Sprachressource aus.*
    - **Azure Search-Tarif**: „Free (F)“ (*Wenn dieser Tarif nicht verfügbar ist, wählen Sie „Basic (B)“* ) aus.
    - **Rechtliche Bestimmungen**: _Zustimmen_ 
    - **Hinweis zur verantwortlichen Verwendung von KI**: _Zustimmen_
    
    \*„Benutzerdefinierte Fragen und Antworten“ verwendet Azure Search zum Indizieren und Abfragen der Wissensdatenbank mit Fragen und Antworten.

4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.

## <a name="create-a-question-answering-project"></a>Erstellen eines „Fragen und Antworten“-Projekt

Wenn Sie eine Wissensdatenbank für Fragen und Antworten in Ihrer Sprachressource erstellen möchten, können Sie das Language Studio-Portal zum Erstellen eines „Fragen und Antworten“-Projekts verwenden. In diesem Fall erstellen Sie eine Wissensdatenbank mit Fragen und Antworten zu [Microsoft Learn](https://docs.microsoft.com/learn).

1. Navigieren Sie auf einer neuen Browserregisterkarte das *Language Studio*-Portal unter `https://language.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Wenn Sie zur Auswahl einer Sprachressource aufgefordert werden, wählen Sie die folgenden Einstellungen aus:
    - **Azure-Verzeichnis**: Das Azure-Verzeichnis mit Ihrem Abonnement.
    - **Azure-Abonnement**: Ihr Azure-Abonnement.
    - **Sprachressource**: Die Sprachressource, die Sie zuvor erstellt haben.
3. Sollten Sie <u>nicht</u> zur Auswahl einer Sprachressource aufgefordert, kann dies daran liegen, dass Ihr Abonnement mehrere Sprachressourcen enthält. Gehen Sie in diesem Fall folgendermaßen vor:
    1. Klicken Sie auf der Leiste oben auf die Schaltfläche **Einstellungen (&#9881;)**.
    2. Gehen Sie auf der Seite **Einstellungen** zur Registerkarte **Ressourcen**.
    3. Wählen Sie die soeben erstellte Sprachressource aus, und klicken Sie auf **Switch resource** (Ressource wechseln).
    4. Klicken Sie oben auf der Seite auf **Language Studio**, um zur Startseite von Language Studio zurückzukehren.
3. Klicken Sie oben im Portal im Menü **Neu erstellen** auf **Benutzerdefinierte Fragebeantwortung**.
4. Klicken Sie im Assistenten ***Projekt erstellen** auf der Seite **Choose language setting** (Spracheinstellung auswählen) auf die Option zum Festlegen der Sprache für alle Projekte in dieser Ressource. Wählen Sie anschließend **Englisch** als Sprache aus. Klicken Sie dann auf **Weiter**.
5. Geben Sie auf der Seite **Enter basic information** (Grundlegende Informationen eingeben) die folgenden Informationen ein, und klicken Sie dann auf **Weiter**:
    - **Name:** LearnFAQ
    - **Beschreibung**: FAQ für Microsoft Learn
    - **Default answer when no answer is returned** (Standardantwort, wenn keine Antwort zurückgegeben wird): „Sorry, I don't understand the question.“ (Entschuldigung, ich verstehe die Frage nicht.)
6. Klicken Sie auf der Seite **Überprüfen und fertigstellen** auf **Projekt erstellen**.

## <a name="add-a-sources-to-the-knowledge-base"></a>Hinzufügen von Quellen zur Wissensdatenbank

Sie können eine Wissensdatenbank von Grund auf neu erstellen, aber es ist üblich, zuerst Fragen und Antworten von einer vorhandenen FAQ-Seite oder aus einem vorhandenen Dokument zu importieren. In diesem Fall importieren Sie Daten von einer vorhandenen FAQ-Webseite für Microsoft Learn, und Sie importieren auch einige vordefinierte Fragen und Antworten für Smalltalk, um gängige Konversationsszenarios zu unterstützen.

1. Wählen Sie auf der Seite **Manage sources** (Quellen verwalten) für Ihr „Fragen und Antworten“-Projekt in der Liste **&#9547; Add source** (Quelle hinzufügen) die Option **URLs** aus. Klicken Sie dann im Dialogfeld **Add URLs** (URLs hinzufügen) auf **&#9547; Add URL** (URL hinzufügen), fügen Sie die folgende URL hinzu, und klicken Sie dann auf **Alle hinzufügen**, um die URL zur Wissensdatenbank hinzuzufügen:
    - **Name**: `Learn FAQ Page`
    - **URL**: `https://docs.microsoft.com/en-us/learn/support/faq`
2. Wählen Sie auf der Seite **Manage sources** (Quellen verwalten) für Ihr „Fragen und Antworten“-Projekt in der Liste **&#9547; Add source** (Quelle hinzufügen) die Option **Chitchat** (Smalltalk) aus. Wählen Sie im Dialogfeld **Add chit chat** (Smalltalkelement hinzufügen) die Option **Friendly** (Freundlich) aus, und klicken Sie auf **Add chit chat** (Smalltalkelement hinzufügen).

## <a name="edit-the-knowledge-base"></a>Bearbeiten der Wissensdatenbank

Ihre Wissensdatenbank wurde mit Frage-Antwort-Paaren aus den häufig gestellten Fragen zu Microsoft Learn aufgefüllt, die durch eine Reihe von *Smalltalk*-Frage-Antwort-Paaren ergänzt wurden. Sie können die Wissensdatenbank erweitern, indem Sie zusätzliche Frage-Antwort-Paare hinzufügen.

1. Navigieren Sie im Projekt **LearnFAQ** in Language Studio zur Seite **Edit knowledge base** (Wissensdatenbank bearbeiten), um die vorhandenen Frage-Antwort-Paare anzuzeigen. (Falls einige Tipps angezeigt werden, lesen Sie sie durch, und klicken Sie auf **Verstanden**, um sie zu schließen, oder klicken Sie auf **Alle überspringen**.)
2. Klicken Sie in der Wissensdatenbank auf **&#65291; Add question pair** (Frage-Antwort-Paar hinzufügen).
3. Geben Sie im Feld **Frage** `What is Microsoft certification?` ein, und drücken Sie die **EINGABETASTE****.
4. Klicken Sie auf **&#65291; Alternative Formulierung hinzufügen**, geben Sie `How can I demonstrate my Microsoft technology skills?` ein, und drücken Sie die **EINGABETASTE**.
5. Geben Sie im Feld **Antwort** `The Microsoft Certified Professional program enables you to validate and prove your skills with Microsoft technologies.` ein, drücken Sie die **EINGABETASTE**, und klicken Sie auf **Übermitteln**, um die Frage (einschließlich der alternativen Formulierung) und die Antwort zur Wissensdatenbank hinzuzufügen.

    In einigen Fällen ist es sinnvoll, Benutzer*innen eine Nachverfolgung der Antwort zu ermöglichen, um eine *mehrteilige* Unterhaltung zu erstellen. Mit deren Hilfe können Benutzer*innen die Frage iterativ verfeinern, um die benötigte Antwort zu erhalten.

6. Klicken Sie unter der Antwort, die Sie für die Zertifizierungsfrage eingegeben haben, auf **&#65291; Add follow-up prompts** (Folgeäußerungen hinzufügen).
7. Geben Sie im Dialogfeld **Follow-up Prompt** (Folgeäußerung) die folgenden Einstellungen ein, und klicken Sie dann auf **Add prompt** (Äußerung hinzufügen):
    - **Text, der Benutzer*innen in der Äußerung angezeigt wird:** „Learn more about certification“ (Weitere Informationen zur Zertifizierung)
    - Klicken Sie auf **Create link to new pair** (Link zu neuem Paar erstellen), und geben Sie den folgenden Text ein: `You can learn more about certification on the [Microsoft certification page](https://docs.microsoft.com/learn/certifications/).`
    - **Show in contextual flow only** (Nur im kontextbezogenen Flow anzeigen): Diese Option ist aktiviert. *Mit dieser Option wird sichergestellt, dass die Antwort nur jeweils im Kontext einer Folgefrage aus der ursprünglichen Zertifizierungsfrage zurückgegeben wird.*

## <a name="train-and-test-the-knowledge-base"></a>Trainieren und Testen der Wissensdatenbank

Nachdem Sie nun über eine Wissensdatenbank verfügen, können Sie sie im QnA Maker-Portal testen.

1. Klicken Sie oben rechts auf der Seite auf **Änderungen speichern**.
2. Klicken Sie nach dem Speichern der Änderungen auf **Testen**, um den Testbereich zu öffnen.
3. *Deaktivieren* Sie im Testbereich oben die Option zum Anzeigen kurzer Antworten. Geben Sie dann unten die Nachricht `Hello` ein. Daraufhin sollte eine geeignete Antwort zurückgegeben werden.
4. Geben Sie im Testbereich unten die Nachricht `What is Microsoft Learn?` ein. Es sollte eine entsprechende Antwort aus den häufig gestellten Fragen zurückgegeben werden.
5. Geben Sie die Nachricht `Thanks!` ein. Eine entsprechende Smalltalkantwort sollte zurückgegeben werden.
6. Geben Sie die Nachricht `Tell me about certification` ein. Die von Ihnen erstellte Antwort sollte zusammen mit einem Link zu einer Folgeäußerung zurückgegeben werden.
7. Klicken Sie auf den Link zur Folgeäußerung **Learn more about certification** (Weitere Informationen zur Zertifizierung). Die Folgeantwort mit einem Link zur Zertifizierungsseite sollte zurückgegeben werden.
8. Wenn Sie mit dem Testen der Wissensdatenbank fertig sind, schließen Sie den Testbereich.

## <a name="deploy-and-test-the-knowledge-base"></a>Bereitstellen und Testen der Wissensdatenbank

Die Wissensdatenbank stellt einen Back-End-Dienst bereit, den Clientanwendungen zum Beantworten von Fragen nutzen können. Jetzt können Sie Ihre Wissensdatenbank veröffentlichen und über einen Client auf deren REST-Schnittstelle zugreifen.

1. Navigieren Sie im Projekt **LearnFAQ** in Language Studio zur Seite **Deploy knowledge base** (Wissensdatenbank bereitstellen).
2. Klicken Sie oben auf der Seite auf **Bereitstellen**. Klicken Sie dann auf **Bereitstellen**, um zu bestätigen, dass Sie die Wissensdatenbank bereitstellen möchten.
3. Klicken Sie nach Abschluss der Bereitstellung auf **Get prediction URL** (Vorhersage-URL abrufen), um den REST-Endpunkt für Ihre Wissensdatenbank anzuzeigen, und kopieren Sie ihn in die Zwischenablage (Dialogfeld jedoch noch nicht schließen).
4. Öffnen Sie **ask-question.cmd** in Visual Studio Code im Ordner **12-qna**. Dieses Skript verwendet *cURL*, um die REST-Schnittstelle eines „Fragen und Antworten“-Endpunkts aufzurufen.
5. Ersetzen Sie *YOUR_PREDICTION_ENDPOINT* im Skript durch den kopierten Vorhersageendpunkt. (Stellen Sie dabei sicher, dass er in Anführungszeichen eingeschlossen ist.)
6. Kehren Sie zum Browser zurück, und beachten Sie, dass die Beispielanforderung im Dialogfeld **Get prediction URL** (Vorhersage-URL abrufen) einen Wert für den Parameter **Ocp-Apim-Subscription-Key** enthält, der in etwa wie folgt aussieht: *ab12c345de678fg9hijk01lmno2pqrs34*. Dies ist der Autorisierungsschlüssel für Ihre Ressource. Kopieren Sie ihn in die Zwischenablage, und klicken Sie dann auf **Verstanden**, um das Dialogfeld zu schließen.
7. Navigieren Sie zurück zu Visual Studio Code, und ersetzen Sie *YOUR_KEY* im Skript **ask-question.cmd** durch den kopierten Schlüssel. (Stellen Sie dabei sicher, dass er in Anführungszeichen eingeschlossen ist.)
8. Beachten Sie, dass der cURL-Befehl im Skript einen **question**-Parameter mit dem Wert **What is a Learning Path?** (Was ist ein Lernpfad?) übermittelt.
9. Vergewissern Sie sich, dass das gesamte Skript dem folgenden Code ähnelt, und speichern Sie dann die Datei.

    ```
    @echo off
    SETLOCAL ENABLEDELAYEDEXPANSION

    rem Set variables
    set prediction_url="https://some-name.cognitiveservices.azure.com/language/......."
    set key="ab12c345de678fg9hijk01lmno2pqrs34"

    curl -X POST !prediction_url! -H "Ocp-Apim-Subscription-Key: !key!" -H "Content-Type: application/json" -d "{'question': 'What is a learning Path?' }"
    ```

10. Klicken Sie in Visual Studio Code im Explorer-Bereich mit der rechten Maustaste auf das Skript **ask-question.cmd**, und wählen Sie dann **In integriertem Terminal öffnen** aus.
11. Geben Sie im Terminalbereich den Befehl `ask-question.cmd` zum Ausführen des Skripts ein, und zeigen Sie die vom Dienst zurückgegebene JSON-Antwort an, die eine geeignete Antwort auf die Frage *What is a learning path?* (Was ist ein Lernpfad?) enthalten sollte.

## <a name="create-a-bot-for-the-knowledge-base"></a>Erstellen eines Bots für die Wissensdatenbank

Am häufigsten sind die Clientanwendungen, mit denen Antworten aus einer Wissensdatenbank abgerufen werden, Bots.

1. Navigieren Sie im Browser zurück zu Language Studio, und klicken Sie auf der Seite **Deploy knowledge base** (Wissensdatenbank bereitstellen) auf **Create Bot** (Bot erstellen). Dadurch wird das Azure-Portal auf einer neuen Browserregisterkarte geöffnet, sodass Sie einen Web-App-Bot in Ihrem Azure-Abonnement erstellen können. (Melden Sie sich bei Aufforderung an.)
2. Erstellen Sie im Azure-Portal einen Web-App-Bot mit den folgenden Einstellungen (die meisten davon werden für Sie vorab ausgefüllt):

    *Wenn einige Werte fehlen, aktualisieren Sie Ihren Browser.*  

  - **Bot-Handle**: *Ein eindeutiger Name für Ihren Bot*.
  - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
  - **Ressourcengruppe**: *Dies ist die Ressourcengruppe, die Ihre Sprachressource enthält.*
  - **Speicherort**: *Derselbe Speicherort wie Ihr Textanalysedienst*.
  - **Tarif**: F0
  - **App-Name**: *Dieser ist identisch mit dem **Bot-Handle** mit einer eindeutigen ID, wobei *.azurewebsites.net* automatisch angefügt wird.
  - **SDK-Sprache**: *Wählen Sie entweder C# oder Node.js aus*.
  - **QnA-Authentifizierungsschlüssel**: *Dieser sollte automatisch auf den Authentifizierungsschlüssel für Ihre QnA-Wissensdatenbank festgelegt werden*.
  - **App Service-Plan/-Speicherort**: *Diese Einstellung kann automatisch auf einen geeigneten Plan und Speicherort festgelegt werden, sofern vorhanden. Falls nicht, erstellen Sie einen neuen Plan*.
  - **Application Insights**: Aus
  - **Microsoft-App-ID und -Kennwort**: App-ID und Kennwort automatisch erstellen.
3. Warten Sie, bis Ihr Bot erstellt wurde. Klicken Sie anschließend auf **Zu Ressource wechseln**. (Alternativ können Sie auf der Startseite auf **Ressourcengruppen** klicken, die Ressourcengruppe öffnen, in der der Web-App-Bot erstellt wurde, und diesen dann auswählen.)
4. Öffnen Sie die Seite **In Web Chat testen** im Blatt für Ihren Bot, und warten Sie, bis der Bot die Nachricht **Hello and Welcome!** ausgibt (Die Initialisierung kann einige Sekunden dauern).
5. Verwenden Sie die Testchatschnittstelle, um sicherzustellen, dass Ihr Bot Fragen aus Ihrer Wissensdatenbank wie erwartet beantwortet. Versuchen Sie beispielsweise, `What is Microsoft certification?` zu übermitteln.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zu Fragen und Antworten im Sprachdienst finden Sie in der [Dokumentation zum Sprachdienst](https://docs.microsoft.com/en-us/azure/cognitive-services/language-service/question-answering/overview).
