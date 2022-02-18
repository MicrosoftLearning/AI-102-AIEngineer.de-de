---
lab:
  title: Überwachen von Cognitive Services
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: e0e0042421a4f7150fc3b95cef80887c81a78f3a
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2022
ms.locfileid: "135801337"
---
# <a name="monitor-cognitive-services"></a>Überwachen von Cognitive Services

Azure Cognitive Services können ein entscheidender Teil der gesamten Anwendungsinfrastruktur sein. Es ist wichtig, dass Sie die Aktivitäten überwachen können und auf Probleme aufmerksam gemacht werden, die möglicherweise Aufmerksamkeit erfordern.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** bereits in die Umgebung geklont haben, in der Sie an dieser Übung arbeiten, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es jetzt zu klonen.

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
    - **Tarif**: Standard S0.
3. Aktivieren Sie alle erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
5. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. Notieren Sie sich den Endpunkt-URI. Sie benötigen ihn später.

## <a name="configure-an-alert"></a>Konfigurieren einer Warnung

Beginnen wir mit der Überwachung, indem wir eine Warnungsregel definieren, damit Sie Aktivitäten in Ihrer Cognitive Services-Ressource erkennen können.

1. Wechseln Sie im Azure-Portal zu Ihrer Cognitive Services-Ressource, und zeigen Sie die Seite **Warnungen** an (im Abschnitt **Überwachung**).
2. Wählen Sie **+ Neue Warnungsregel** aus.
3. Vergewissern Sie sich auf der Seite **Warnungsregel erstellen** unter **Bereich**, dass Ihre Cognitive Services-Ressource aufgeführt ist.
4. Klicken Sie unter **Bedingung** auf **Bedingung hinzufügen**, und sehen Sie sich den rechts angezeigten Bereich **Signal auswählen** an, in dem Sie einen Signaltyp zur Überwachung auswählen können.
5. Wählen Sie in der Liste **Signaltyp** die Option **Aktivitätsprotokoll** und dann in der gefilterten Liste die Option **Schlüssel auflisten** aus.
6. Überprüfen Sie die Aktivität der letzten sechs Stunden.
7. Navigieren Sie zur Registerkarte **Aktionen**. Beachten Sie, dass Sie eine *Aktionsgruppe* angeben können. Damit können Sie automatische Aktionen konfigurieren, wenn eine Warnung ausgelöst wird – z. B. das Senden einer E-Mail-Benachrichtigung. In dieser Übung werden wir das nicht vornehmen, aber es kann nützlich sein, dies in einer Produktionsumgebung durchzuführen.
8. Legen Sie auf der Registerkarte **Details** die Option **Name der Warnungsregel** auf **Key List Alert** (Schlüssellistenwarnung) fest.
9. Klicken Sie auf **Überprüfen + erstellen**. 
10. Überprüfen Sie die Konfiguration für die Warnung. Klicken Sie auf **Erstellen**, und warten Sie, bis die Warnungsregel erstellt wurde.
11. Klicken Sie in Visual Studio Code mit der rechten Maustaste auf den Ordner **03-monitor**, und öffnen Sie ein integriertes Terminal. Geben Sie dann den folgenden Befehl ein, um sich über die Azure CLI bei Ihrem Azure-Abonnement anzumelden.

    ```
    az login
    ```

    Daraufhin wird eine Webbrowserregisterkarte mit der Aufforderung geöffnet, sich bei Azure anzumelden. Melden Sie sich an, schließen Sie dann die Browserregisterkarte, und kehren Sie zu Visual Studio Code zurück.

    > **Tipp**: Wenn Sie über mehrere Abonnements verfügen, müssen Sie sicherstellen, dass Sie in dem Abonnement arbeiten, das Ihre Cognitive Services-Ressource enthält.  Verwenden Sie diesen Befehl, um Ihr aktuelles Abonnement zu ermitteln.
    >
    > ```
    > az account show
    > ```
    >
    > Wenn Sie das Abonnement ändern müssen, führen Sie diesen Befehl aus und ändern *&lt;subscriptionName&gt;* in den richtigen Abonnementnamen.
    >
    > ```
    > az account set --subscription <subscriptionName>
    > ```

10. Jetzt können Sie den folgenden Befehl verwenden, um die Liste der Cognitive Services-Schlüssel abzurufen. Ersetzen Sie dabei *&lt;resourceName&gt;* durch den Namen Ihrer Cognitive Services-Ressource und *&lt;resourceGroup&gt;* durch den Namen der Ressourcengruppe, in der Sie sie erstellt haben.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

Der Befehl gibt eine Liste der Schlüssel für Ihre Cognitive Services-Ressource zurück.

11. Wechseln Sie zurück zu dem Browser, in dem das Azure-Portal geöffnet ist, und aktualisieren Sie die **Warnungsseite**. In der Tabelle sollte die Warnung **Sev 4** aufgeführt sein (andernfalls warten Sie bis zu fünf Minuten und aktualisieren Sie sie erneut).
12. Wählen Sie die Warnung aus, um ihre Details anzuzeigen.

## <a name="visualize-a-metric"></a>Visualisieren einer Metrik

Sie können nicht nur Warnungen definieren, sondern auch Metriken für Ihre Cognitive Services-Ressource anzeigen, um deren Auslastung zu überwachen.

1. Wählen Sie im Azure-Portal auf der Seite für Ihre Cognitive Services-Ressource die Option **Metriken** (im Abschnitt **Überwachung**) aus.
2. Wenn kein Diagramm vorhanden ist, wählen Sie **+ Neues Diagramm** aus. Überprüfen Sie dann in der Liste **Metrik** die möglichen Metriken, die Sie visualisieren können, und wählen Sie **Aufrufe gesamt** aus.
3. Wählen Sie in der Liste **Aggregation** die Option **Anzahl** aus.  Dadurch können Sie die Gesamtzahl der Aufrufe an Ihre Cognitive Service-Ressource überwachen, was nützlich ist, um festzustellen, wie stark der Dienst über einen bestimmten Zeitraum genutzt wird.
4. Um einige Anforderungen an Ihren kognitiven Dienst zu generieren, werden Sie **curl** verwenden – ein Befehlszeilentool für HTTP-Anforderungen. Öffnen Sie in Visual Studio Code im Ordner **03-monitor** die Datei **rest-test.cmd**, und bearbeiten Sie den darin enthaltenen Befehl **curl** (siehe unten). Ersetzen Sie *&lt;yourEndpoint&gt;* und *&lt;yourKey&gt;* durch Ihren Endpunkt-URI und den Schlüssel **Key1**, um die Textanalyse-API in Ihrer Cognitive Services-Ressource zu verwenden.

    ```
    curl -X POST "<yourEndpoint>/text/analytics/v3.1/languages?" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <yourKey>" --data-ascii "{'documents':           [{'id':1,'text':'hello'}]}"
    ```

5. Speichern Sie Ihre Änderungen, und führen Sie dann im integrierten Terminal für den Ordner **03-monitor** den folgenden Befehl aus:

    ```
    rest-test
    ```

Der Befehl gibt ein JSON-Dokument zurück, das Informationen über die in den Eingabedaten erkannte Sprache enthält (dies sollte Englisch sein).

6. Führen Sie den Befehl **rest-test** mehrmals aus, um eine Aufrufaktivität zu generieren (Sie können die Taste **^** verwenden, um vorherige Befehle zu durchlaufen).
7. Kehren Sie zur Seite **Metriken** im Azure-Portal zurück, und aktualisieren Sie das Diagramm **Anrufe gesamt**. Es kann ein paar Minuten dauern, bis die Aufrufe, die Sie mit *curl* durchgeführt haben, im Diagramm angezeigt werden. Aktualisieren Sie das Diagramm so lange, bis es diese Aufrufe enthält.

## <a name="more-information"></a>Weitere Informationen

Eine der Optionen für die Überwachung von Cognitive Services ist die Verwendung der *Diagnoseprotokollierung*. Sobald die Diagnoseprotokollierung aktiviert ist, erfasst sie umfangreiche Informationen über die Auslastung Ihrer Cognitive Services-Ressource und kann ein nützliches Tool zum Überwachen und Debuggen sein. Nach dem Einrichten der Diagnoseprotokollierung kann es über eine Stunde dauern, bis Informationen generiert werden. Deshalb haben wir uns in dieser Übung nicht damit befasst, aber Sie können in der [Dokumentation zu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/diagnostic-logging) mehr darüber erfahren.
