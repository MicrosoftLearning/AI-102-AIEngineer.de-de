---
lab:
  title: Erstellen einer QnA-Lösung
  module: Module 6 - Building a QnA Solution
ms.openlocfilehash: 15b1b41d16f389392315f31c3daba81bc18101c9
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625867"
---
# <a name="create-a-qna-solution"></a>Erstellen einer QnA-Lösung

Eines der häufigsten Unterhaltungsszenarien ist die Bereitstellung von Unterstützung über eine Wissensdatenbank mit häufig gestellten Fragen (FAQs). Viele Organisationen veröffentlichen FAQs als Dokumente oder Webseiten. Dies funktioniert gut bei einer kleinen Menge von Frage-Antwort-Paaren, aber die Suche in umfangreichen Dokumenten kann schwierig und zeitaufwändig sein.

QnA Maker ist ein Cognitive Service, mit dem Sie eine Wissensdatenbank mit Frage-Antwort-Paaren erstellen können. Diese Datenbank kann mithilfe von Eingaben in natürlicher Sprache abgefragt werden und wird am häufigsten als Ressource verwendet, mit der ein Bot Antworten auf Fragen suchen kann, die von Benutzern gesendet wurden.

In diesem Lab verwenden wir QnA Maker Managed, das ein Feature innerhalb von „Textanalyse“ ist. 

## <a name="create-a-text-analytics-resource"></a>Erstellen einer Textanalyseressource 

Zum Erstellen und Hosten einer Wissensdatenbank mithilfe von QnA Maker Managed benötigen Sie eine Textanalyseressource in Ihrem Azure-Abonnement.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Wählen Sie die Schaltfläche **&#65291;Ressource erstellen** aus, suchen Sie nach *Textanalyse*, und erstellen Sie eine **Textanalyseressource**. 
3. Klicken Sie im Block **Benutzerdefinierte Fragen und Antworten (Vorschau)** auf **Auswählen**. Klicken Sie dann auf **Fortfahren, um Ihre Ressource zu erstellen**. Sie müssen die folgenden Einstellungen eingeben:
    
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie einen beliebigen verfügbaren Speicherort aus.*
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S.
    - **Azure Search-Speicherort**\*: *Wählen Sie einen Speicherort in derselben globalen Region wie Ihre QnA Maker-Ressource* aus.
    - **Azure Search-Tarif**: „Free (F)“ (*Wenn dieser Tarif nicht verfügbar ist, wählen Sie „Basic (B)“* ) aus.
    - **Rechtliche Bestimmungen**: _Zustimmen_ 
    - **Hinweis zur verantwortlichen Verwendung von KI**: _Zustimmen_
    
    \*„Benutzerdefinierte Fragen und Antworten“ verwendet Azure Search zum Indizieren und Abfragen der Wissensdatenbank mit Fragen und Antworten.

4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank

Zum Erstellen einer Wissensdatenbank in Ihrer Textanalyseressource können Sie das QnA Maker-Portal verwenden. In diesem Fall erstellen Sie eine Wissensdatenbank mit Fragen und Antworten zu [Microsoft Learn](https://docs.microsoft.com/learn).

1. Wechseln Sie auf einer neuen Browserregisterkarte unter `https://qnamaker.ai` zum QnA Maker-Portal, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Wählen Sie dann oben im Portal **Create a knowledge base** (Wissensdatenbank erstellen) aus.
3. Weil Sie bereits eine QnA Maker-Ressource erstellt haben, können Sie Schritt 1 überspringen. Wählen Sie im Abschnitt **Schritt 2** die folgenden Einstellungen aus:
    - **Microsoft Azure-Verzeichnis-ID**: Das Azure-Verzeichnis mit Ihrem Abonnement.
    - **Azure-Abonnementname**: Ihr Azure-Abonnement.
    - **Azure QnA-Dienst:** Die Textanalyseressource, die Sie zuvor erstellt haben.
    - **Sprache**: Englisch (*Diese Option ist standardmäßig nur für die erste Wissensdatenbank verfügbar, die Sie erstellen)* .
4. Geben Sie im Abschnitt **Schritt 3** die Zeichenfolge **Learn FAQ** als Namen für Ihre Wissensdatenbank ein.

    Sie können eine Wissensdatenbank von Grund auf neu erstellen, aber es ist üblich, zuerst Fragen und Antworten von einer vorhandenen FAQ-Seite oder aus einem vorhandenen Dokument zu importieren.

5. Im Abschnitt **Schritt 4**:
    - Geben Sie im Feld **URL** den Text `https://docs.microsoft.com/en-us/learn/support/faq` ein und klicken dann auf **&#65291; URL hinzufügen**.
    - Wählen Sie im Abschnitt **Smalltalk** die Option **Freundlich** aus.
6. Wählen Sie im Abschnitt **Schritt 5** die Option **Create your KB** (Wissensdatenbank erstellen) aus, und warten Sie, bis Ihre Wissensdatenbank erstellt wurde.

## <a name="modify-the-knowledge-base"></a>Ändern der Wissensdatenbank

Ihre Wissensdatenbank wurde mit Frage-Antwort-Paaren aus den häufig gestellten Fragen zu Microsoft Learn aufgefüllt, die durch eine Reihe von *Smalltalk*-Frage-Antwort-Paaren ergänzt wurden. Sie können die Wissensdatenbank erweitern, indem Sie zusätzliche Frage-Antwort-Paare hinzufügen.

1. Wählen Sie in der Wissensdatenbank **&#65291; Add QnA pair** (Frage-Antwort-Paar hinzufügen) aus.
2. Geben Sie im Feld **Question** (Frage) den Text `What is Microsoft certification?` ein.
3. Wählen Sie **&#65291; + Add alternative phrasing** (Alternative Formulierung hinzufügen) aus, und geben Sie `How can I demonstrate my Microsoft technology skills?` ein.
4. Geben Sie im Feld **Answer** (Antwort) den Text `The Microsoft Certified Professional program enables you to validate and prove your skills with Microsoft technologies.` ein.

    In einigen Fällen ist es sinnvoll, dem Benutzer eine Nachverfolgung der Antwort zu ermöglichen, um eine *mehrteilige* Unterhaltung zu erstellen. Mit deren Hilfe kann der Benutzer die Frage iterativ verfeinern, um die benötigte Antwort zu erhalten.

5. Wählen Sie unter der Antwort, die Sie für die Zertifizierungsfrage eingegeben haben, **&#65291; Add follow-up prompt** (Folgeäußerung hinzufügen) aus.
6. Geben Sie im Dialogfeld **Follow-up Prompt** (Folgeäußerung) die folgenden Einstellungen ein:
    - **Display Text** (Anzeigetext): `Learn more about certification.`
    - **Link to QnA**\* (Link zu QnA): `You can learn more about certification on the [Microsoft certification page](https://docs.microsoft.com/learn/certifications/).`
    - **Context-only** (Nur Kontext): Ausgewählt. *Mit dieser Option wird sichergestellt, dass die Antwort nur jeweils im Kontext einer Folgefrage aus der ursprünglichen Zertifizierungsfrage zurückgegeben wird.*

    \*Wenn Sie im Feld **Link to QnA** etwas eingeben, werden die vorhandenen Antworten in der Wissensdatenbank durchsucht. Wenn keine Übereinstimmung gefunden wurde, wird standardmäßig ein neues Frage-Antwort-Paar erstellt. Beachten Sie, dass Sie hier Text im Markdown-Format eingeben.

## <a name="train-and-test-the-knowledge-base"></a>Trainieren und Testen der Wissensdatenbank

Nachdem Sie nun über eine Wissensdatenbank verfügen, können Sie sie im QnA Maker-Portal testen.

1. Klicken Sie oben rechts auf der Seite auf **Speichern und trainieren**, um Ihre Wissensdatenbank zu trainieren.
2. Nachdem das Training abgeschlossen ist, klicken Sie auf **&larr;-Test**, um den Testbereich zu öffnen.
3. *Deaktivieren* Sie im Testbereich oben das Feld mit dem Text *Display Short Answer* (Kurze Antwort anzeigen). Geben Sie dann unten die Meldung *Hello* ein. Daraufhin sollte eine geeignete Antwort zurückgegeben werden.
4. Geben Sie im Testbereich unten die Frage *What is Microsoft Learn?* ein. Es sollte eine entsprechende Antwort aus den häufig gestellten Fragen zurückgegeben werden.
5. Geben Sie den Text *That makes me happy!* ein. Eine entsprechende Smalltalk-Antwort sollte zurückgegeben werden.
6. Geben Sie den Text *Tell me about certification* ein. Die von Ihnen erstellte Antwort sollte zusammen mit einer Folgeäußerungsschaltfläche zurückgegeben werden.
7. Wählen Sie die Schaltfläche **Learn more about certification** (Weitere Informationen zur Zertifizierung) aus. Die Folgeantwort mit einem Link zur Zertifizierungsseite sollte zurückgegeben werden.
8. Wenn Sie mit dem Testen der Wissensdatenbank fertig sind, klicken Sie auf **&rarr;-Test**, um den Testbereich zu schließen.

## <a name="publish-the-knowledge-base"></a>Veröffentlichen der Wissensdatenbank

Die Wissensdatenbank stellt einen Back-End-Dienst bereit, den Clientanwendungen zum Beantworten von Fragen nutzen können. Jetzt können Sie Ihre Wissensdatenbank veröffentlichen und über einen Client auf deren REST-Schnittstelle zugreifen.

1. Klicken Sie oben auf der Seite „QnA Maker“ auf **Publish** (Veröffentlichen). Klicken Sie dann auf der Seite **Learn FAQ** (Häufig gestellte Fragen zu Learn) auf **Publish**.
2. Wenn die Veröffentlichung abgeschlossen ist, sehen Sie sich den Beispielcode an, der für die Verwendung des REST-Endpunkts Ihrer Wissensdatenbank bereitgestellt wurde. Es gibt ein Beispiel für *Postman* und ein Beispiel für *Curl*.
3. Zeigen Sie die Registerkarte **Curl** an, und kopieren Sie den Beispielcode.
4. Starten Sie Visual Studio Code, und öffnen Sie einen Terminalbereich.
5. Fügen Sie den kopierten Code in das Terminal ein, und bearbeiten Sie ihn, um **&lt;your question&gt;** durch **What is a learning path?** zu ersetzen.
6. Geben Sie den Befehl ein, und zeigen Sie die JSON-Antwort an, die aus Ihrer Wissensdatenbank zurückgegeben wird.

## <a name="create-a-bot-for-the-knowledge-base"></a>Erstellen eines Bots für die Wissensdatenbank

Am häufigsten sind die Clientanwendungen, mit denen Antworten aus einer Wissensdatenbank abgerufen werden, Bots.

1. Wählen Sie auf der Seite mit der Veröffentlichungsbestätigung und dem Curl-Beispielcode **Create Bot** (Bot erstellen) aus. Dadurch wird das Azure-Portal auf einer neuen Browserregisterkarte geöffnet, sodass Sie einen Web-App-Bot in Ihrem Azure-Abonnement erstellen können.
2. Erstellen Sie im Azure-Portal einen Web-App-Bot mit den folgenden Einstellungen (die meisten davon werden für Sie vorab ausgefüllt):

    *Wenn einige Werte fehlen, aktualisieren Sie Ihren Browser.*  

  - **Bot-Handle**: *Ein eindeutiger Name für Ihren Bot*.
  - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
  - **Ressourcengruppe**: *Die Ressourcengruppe, die Ihre QnA Maker-Ressource enthält*.
  - **Speicherort**: *Derselbe Speicherort wie Ihr Textanalysedienst*.
  - **Tarif**: F0
  - **App-Name**: *Identisch mit dem **Bot-Handle**, wobei *.azurewebsites.net* automatisch angefügt wird.
  - **SDK-Sprache**: *Wählen Sie entweder C# oder Node.js aus*.
  - **QnA-Authentifizierungsschlüssel**: *Dieser sollte automatisch auf den Authentifizierungsschlüssel für Ihre QnA-Wissensdatenbank festgelegt werden*.
  - **App Service-Plan/-Speicherort**: *Diese Einstellung kann automatisch auf einen geeigneten Plan und Speicherort festgelegt werden, sofern vorhanden. Falls nicht, erstellen Sie einen neuen Plan*.
  - **Application Insights**: Aus
  - **Microsoft-App-ID und -Kennwort**: App-ID und Kennwort automatisch erstellen.
3. Warten Sie darauf, dass Ihr Bot erstellt wird (das Benachrichtigungssymbol oben rechts, das wie eine Glocke aussieht, wird animiert, während Sie warten). Klicken Sie dann in der Benachrichtigung, dass die Bereitstellung abgeschlossen ist, auf **Go to resource** (Zur Ressource wechseln) (oder klicken Sie alternativ auf der Startseite auf **Resource groups** (Ressourcengruppen), öffnen Sie die Ressourcengruppe, in der Sie den Web-App-Bot erstellt haben, und klicken Sie darauf).
4. Öffnen Sie die Seite **In Web Chat testen** im Blatt für Ihren Bot, und warten Sie, bis der Bot die Nachricht **Hello and Welcome!** ausgibt (Die Initialisierung kann einige Sekunden dauern).
5. Verwenden Sie die Testchatschnittstelle, um sicherzustellen, dass Ihr Bot Fragen aus Ihrer Wissensdatenbank wie erwartet beantwortet. Versuchen Sie beispielsweise, *What is Microsoft certification?* zu senden.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zu QnA Maker finden Sie in der [QnA Maker-Dokumentation](https://docs.microsoft.com/azure/cognitive-services/qnamaker/).