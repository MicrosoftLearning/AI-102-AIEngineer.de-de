---
lab:
  title: Erste Schritte mit Cognitive Services
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: a05256a78dee051041320aa3556a43add5596ce9
ms.sourcegitcommit: 5ffc20f6a590fe643c2b695b8dc04589411be36e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2022
ms.locfileid: "145951189"
---
# <a name="get-started-with-cognitive-services"></a>Erste Schritte mit Cognitive Services

In dieser Übung werden Sie erste Schritte mit Cognitive Services unternehmen, indem Sie eine **Cognitive Services**-Ressource in Ihrem Azure-Abonnement erstellen und sie von einer Clientanwendung aus nutzen. Ziel der Übung ist es nicht, Fachwissen über einen bestimmten Dienst zu erlangen, sondern sich mit einem allgemeinen Muster für die Bereitstellung von und die Arbeit mit kognitiven Diensten als Entwickler vertraut zu machen.

## <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** noch nicht in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, führen Sie die folgenden Schritte aus, um dies zu tun. Öffnen Sie andernfalls den geklonten Ordner in Visual Studio Code.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Jetzt nicht** aus.

## <a name="provision-a-cognitive-services-resource"></a>Bereitstellen einer Cognitive Services-Ressource

Azure Cognitive Services sind cloudbasierte Dienste, die KI-Funktionen kapseln, die Sie in Ihre Anwendungen integrieren können. Sie können einzelne Cognitive Services-Ressourcen für bestimmte APIs bereitstellen (z. B. **Language** oder **maschinelles Sehen**). Sie können auch eine allgemeine **Cognitive Services**-Ressource bereitstellen, die über einen einzelnen Endpunkt und Schlüssel Zugriff auf mehrere Cognitive Services-APIs bietet. In diesem Fall verwenden Sie eine einzelne **Cognitive Services**-Ressource.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Wählen Sie die Schaltfläche **&#65291;Ressource erstellen**, suchen Sie nach *Cognitive Services*, und erstellen Sie eine **Cognitive Services**-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0
3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
5. Wechseln Sie zur Ressource, und zeigen Sie die Seite **Schlüssel und Endpunkt** an. Diese Seite enthält die Informationen, die Sie benötigen, um eine Verbindung zu Ihrer Ressource herzustellen und sie aus den von Ihnen entwickelten Anwendungen zu nutzen. Dies betrifft insbesondere:
    - Ein HTTP-*Endpunkt*, an den Clientanwendungen Anforderungen senden können.
    - Zwei *Schlüssel*, die für die Authentifizierung verwendet werden können (Clientanwendungen können beide Schlüssel zur Authentifizierung verwenden).
    - Der *Standort*, an dem die Ressource gehostet wird. Dies ist für Anforderungen an einige (aber nicht alle) APIs erforderlich.

## <a name="use-a-rest-interface"></a>Verwenden einer REST-Schnittstelle

Die Cognitive Services-APIs sind REST-basiert, d. h. Sie können sie nutzen, indem Sie JSON-Anforderungen über HTTP übermitteln. In diesem Beispiel erkunden Sie eine Konsolenanwendung, die die **Language**-REST-API zur Spracherkennung verwendet. Das Grundprinzip ist jedoch für alle von der Cognitive Services-Ressource unterstützten APIs gleich.

> **Hinweis**: In dieser Übung können Sie wahlweise die REST-API von **C#** oder **Python** verwenden. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **01-getting-started**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Zeigen Sie den Inhalt des Ordners **rest-client** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass sie dem **Endpunkt** und einem Authentifizierungs **schlüssel** für Ihre Cognitive Services-Ressource entsprechen. Speichern Sie die Änderungen.
3. Beachten Sie, dass der Ordner **rest-client** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: rest-client.py

    Öffnen Sie die Codedatei, und überprüfen Sie den darin enthaltenen Code, wobei Sie sich die folgenden Details notieren:
    - Verschiedene Namespaces werden importiert, um die HTTP-Kommunikation zu ermöglichen.
    - Der Code in der Funktion **Main** ruft den Endpunkt und den Schlüssel für Ihre Cognitive Services-Ressource ab. Diese werden verwendet, um REST-Anforderungen an den Textanalysedienst zu senden.
    - Das Programm akzeptiert Benutzereingaben und verwendet die Funktion **GetLanguage**, um die Spracherkennungs-REST-API für die Textanalyse für Ihren Cognitive Services-Endpunkt aufzurufen, um die Sprache des eingegebenen Texts zu erkennen.
    - Die an die API gesendete Anforderung besteht aus einem JSON-Objekt, das die Eingabedaten enthält. In diesem Fall eine Sammlung von **document**-Objekten, von denen jedes eine **ID** und **Text** aufweist.
    - Der Schlüssel für Ihren Dienst ist im Anforderungsheader enthalten, um Ihre Clientanwendung zu authentifizieren.
    - Die Antwort des Diensts ist ein JSON-Objekt, das die Clientanwendung analysieren kann.
4. Klicken Sie mit der rechten Maustaste auf den Ordner **rest-client**, und öffnen Sie ein integriertes Terminal. Geben Sie dann den folgenden sprachspezifischen Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python rest-client.py
    ```

5. Wenn Sie dazu aufgefordert werden, geben Sie etwas Text ein und überprüfen Sie die Sprache, die der Dienst erkennt und die in der JSON-Antwort zurückgegeben wird. Geben Sie beispielsweise „Hello“, „Bonjour“ und „Gracias“ ein.
6. Wenn Sie mit dem Testen der Anwendung fertig sind, geben Sie „quit“ ein, um das Programm zu beenden.

## <a name="use-an-sdk"></a>Verwenden eines SDK

Sie können Code schreiben, der die Cognitive Services-REST-APIs direkt nutzt, aber es gibt auch Software Development Kits (SDKs) für viele beliebte Programmiersprachen, darunter Microsoft C#, Python und Node.js. Die Verwendung eines SDK kann die Entwicklung von Anwendungen, die Cognitive Services nutzen, erheblich vereinfachen.

1. Erweitern Sie in Visual Studio Code im **Explorer**-Bereich im Ordner **01-getting-started** je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **sdk-client**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Textanalyse-SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.1.0
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.1.0
    ```

3. Zeigen Sie den Inhalt des Ordners **sdk-client** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass sie dem **Endpunkt** und einem Authentifizierungs **schlüssel** für Ihre Cognitive Services-Ressource entsprechen. Speichern Sie die Änderungen.
    
4. Beachten Sie, dass der Ordner **sdk-client** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: sdk-client.py

    Öffnen Sie die Codedatei, und überprüfen Sie den darin enthaltenen Code, wobei Sie sich die folgenden Details notieren:
    - Der Namespace für das von Ihnen installierte SDK wird importiert.
    - Der Code in der Funktion **Main** ruft den Endpunkt und den Schlüssel für Ihre Cognitive Services-Ressource ab. Diese werden mit dem SDK verwendet, um einen Client für den Textanalysedienst zu erstellen.
    - Die Funktion **GetLanguage** verwendet das SDK, um einen Client für den Dienst zu erstellen, und verwendet dann den Client, um die Sprache des eingegebenen Texts zu erkennen.
5. Kehren Sie zum integrierten Terminal für den Ordner **sdk-client** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python sdk-client.py
    ```

6. Wenn Sie dazu aufgefordert werden, geben Sie einen Text ein und überprüfen Sie die Sprache, die vom Dienst erkannt wird. Geben Sie beispielsweise „Goodbye“, „Au revoir“ und „Hasta la vista“ ein.
7. Wenn Sie mit dem Testen der Anwendung fertig sind, geben Sie „quit“ ein, um das Programm zu beenden.

> **Hinweis**: Einige Sprachen, die Unicode-Zeichensätze erfordern, werden in dieser einfachen Konsolenanwendung möglicherweise nicht erkannt.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zu Azure Cognitive Services finden Sie in der [Dokumentation zu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/what-are-cognitive-services).
