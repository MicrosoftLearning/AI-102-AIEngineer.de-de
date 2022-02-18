---
lab:
  title: Verwalten der Sicherheit von Cognitive Services
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: dcab47cf20f54d6bcbed9a3e40081b703fc2d5ba
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2022
ms.locfileid: "135801334"
---
# <a name="manage-cognitive-services-security"></a>Verwalten der Sicherheit von Cognitive Services

Sicherheit ist ein wichtiger Aspekt für jede Anwendung, und als Entwickler sollten Sie sicherstellen, dass der Zugriff auf Ressourcen wie Cognitive Services nur denjenigen vorbehalten ist, die ihn benötigen.

Der Zugriff auf Cognitive Services wird in der Regel über Authentifizierungsschlüssel gesteuert, die bei der erstmaligen Erstellung einer Cognitive Services-Ressource generiert werden.

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
    - **Tarif**: Standard S0
3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.

## <a name="manage-authentication-keys"></a>Verwalten von Authentifizierungsschlüsseln

Beim Erstellen Ihrer Cognitive Services-Ressource wurden zwei Authentifizierungsschlüssel generiert. Sie können diese im Azure-Portal oder über die Azure-Befehlszeilenschnittstelle (CLI) verwalten.

1. Wechseln Sie im Azure-Portal zu Ihrer Cognitive Services-Ressource, und zeigen Sie die Seite **Schlüssel und Endpunkt** an. Diese Seite enthält die Informationen, die Sie benötigen, um eine Verbindung zu Ihrer Ressource herzustellen und sie aus den von Ihnen entwickelten Anwendungen zu nutzen. Dies betrifft insbesondere:
    - Ein HTTP-*Endpunkt*, an den Clientanwendungen Anforderungen senden können.
    - Zwei *Schlüssel*, die für die Authentifizierung verwendet werden können (Clientanwendungen können einen der beiden Schlüssel verwenden. Eine gängige Methode besteht darin, einen für die Entwicklung und einen anderen für die Produktion zu verwenden. Sie können den Entwicklungsschlüssel einfach neu generieren, nachdem die Entwickler ihre Arbeit beendet haben, um weiteren Zugriff zu verhindern).
    - Der *Standort*, an dem die Ressource gehostet wird. Dies ist für Anforderungen an einige (aber nicht alle) APIs erforderlich.
2. Klicken Sie in Visual Studio Code mit der rechten Maustaste auf den Ordner **02-cognitive-security**, und öffnen Sie ein integriertes Terminal. Geben Sie dann den folgenden Befehl ein, um sich über die Azure CLI bei Ihrem Azure-Abonnement anzumelden.

    ```
    az login
    ```

    Daraufhin wird eine Webbrowserregisterkarte mit der Aufforderung geöffnet, sich bei Azure anzumelden. Melden Sie sich an, schließen Sie dann die Browserregisterkarte, und kehren Sie zu Visual Studio Code zurück.

    > **Tipp**: Wenn Sie über mehrere Abonnements verfügen, müssen Sie sicherstellen, dass Sie in dem Abonnement arbeiten, das Ihre Cognitive Services-Ressource enthält.  Verwenden Sie diesen Befehl, um Ihr aktuelles Abonnement zu ermitteln – seine eindeutige ID ist der Wert **id** in dem zurückgegebenen JSON-Code.

    > **Warnung**: Wenn Sie einen Fehler bei der Zertifikatsüberprüfung für `az login` erhalten, warten Sie ein paar Minuten und versuchen Sie es erneut.
    >
    > ```
    > az account show
    > ```
    >
    > Wenn Sie das Abonnement ändern müssen, führen Sie diesen Befehl aus und ändern *&lt;Your_Subscription_Id&gt;* in die richtige Abonnement-ID.
    >
    > ```
    > az account set --subscription <Your_Subscription_Id>
    > ```
    >
    > Alternativ können Sie die Abonnement-ID explizit als Parameter *--subscription* in jedem folgenden Azure CLI-Befehl angeben.

3. Jetzt können Sie den folgenden Befehl verwenden, um die Liste der Cognitive Services-Schlüssel abzurufen. Ersetzen Sie dabei *&lt;resourceName&gt;* durch den Namen Ihrer Cognitive Services-Ressource und *&lt;resourceGroup&gt;* durch den Namen der Ressourcengruppe, in der Sie sie erstellt haben.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

Der Befehl gibt eine Liste der Schlüssel für Ihre Cognitive Services-Ressource zurück – es gibt zwei Schlüssel, die **key1** und **key2** heißen.

4. Zum Testen Ihres Cognitive Services-Diensts können Sie **curl** verwenden, ein Befehlszeilentool für HTTP-Anforderungen. Öffnen Sie im Ordner **02-cognitive-security** die Datei **rest-test.cmd**, und bearbeiten Sie den darin enthaltenen Befehl **curl** (siehe unten). Ersetzen Sie *&lt;yourEndpoint&gt;* und *&lt;yourKey&gt;* durch Ihren Endpunkt-URI und den Schlüssel **Key1**, um die Textanalyse-API in Ihrer Cognitive Services-Ressource zu verwenden.

    ```
    curl -X POST "<yourEndpoint>/text/analytics/v3.0/languages?" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <yourKey>" --data-ascii "{'documents':[{'id':1,'text':'hello'}]}"
    ```

5. Speichern Sie Ihre Änderungen, und führen Sie dann im integrierten Terminal für den Ordner **02-cognitive-security** den folgenden Befehl aus:

    ```
    rest-test
    ```

Der Befehl gibt ein JSON-Dokument zurück, das Informationen über die in den Eingabedaten erkannte Sprache enthält (dies sollte Englisch sein).

6. Wenn ein Schlüssel kompromittiert wird oder die Entwickler, die ihn besitzen, keinen Zugriff mehr benötigen, können Sie ihn im Portal oder über die Azure CLI neu generieren. Führen Sie den folgenden Befehl aus, um Ihren Schlüssel **key1** neu zu generieren (wobei Sie *&lt;resourceName&gt;* und *&lt;resourceGroup&gt;* für Ihre Ressource ersetzen).

    ```
    az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
    ```

Sie erhalten die Liste der Schlüssel für Ihre Cognitive Services-Ressource. Beachten Sie, dass sich **key1** seit dem letzten Abruf geändert hat.

7. Führen Sie den Befehl **rest-test** mit dem alten Schlüssel erneut aus (Sie können den Schlüssel **^** verwenden, um die vorherigen Befehle zu durchlaufen) und überprüfen Sie, dass jetzt ein Fehler auftritt.
8. Bearbeiten Sie den Befehl *curl* in **rest-test.cmd**, indem Sie den Schlüssel durch den neuen **key1**-Wert ersetzen, und speichern Sie die Änderungen. Führen Sie dann den Befehl **rest-test** erneut aus und überprüfen Sie, ob er erfolgreich ausgeführt wurde.

> **Tipp**: In dieser Übung haben Sie die vollständigen Namen der Azure CLI-Parameter verwendet, z. B. **--resource-group**.  Sie können auch kürzere Alternativen wie **-g** verwenden, um Ihre Befehle weniger ausführlich zu gestalten (aber etwas schwieriger zu verstehen).  Die [Cognitive Services CLI-Befehlsreferenz](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest) listet die Parameteroptionen für jeden Cognitive Services CLI-Befehl auf.

## <a name="secure-key-access-with-azure-key-vault"></a>Sicherer Schlüsselzugriff mit Azure Key Vault

Sie können Anwendungen entwickeln, die Cognitive Services-Dienste nutzen, indem Sie einen Schlüssel zur Authentifizierung verwenden. Das bedeutet jedoch, dass der Anwendungscode in der Lage sein muss, den Schlüssel zu erhalten. Eine Möglichkeit besteht darin, den Schlüssel in einer Umgebungsvariablen oder einer Konfigurationsdatei zu speichern, in der die Anwendung bereitgestellt wird. Bei diesem Ansatz bleibt der Schlüssel jedoch anfällig für nicht autorisierten Zugriff. Ein besserer Ansatz bei der Entwicklung von Anwendungen in Azure besteht darin, den Schlüssel sicher in Azure Key Vault zu speichern und den Zugriff auf den Schlüssel über eine *verwaltete Identität* bereitzustellen (mit anderen Worten, ein Benutzerkonto, das von der Anwendung selbst verwendet wird).

### <a name="create-a-key-vault-and-add-a-secret"></a>Erstellen eines Schlüsseltresors und Hinzufügen eines Geheimnisses

Zunächst müssen Sie einen Schlüsseltresor erstellen und ein *Geheimnis* für den Cognitive Services-Schlüssel hinzufügen.

1. Notieren Sie sich den Wert **key1** für Ihre Cognitive Services-Ressource (oder kopieren Sie ihn in die Zwischenablage).
2. Wählen Sie im Azure-Portal auf der Seite **Start** die Schaltfläche **&#65291;Ressource erstellen** aus, suchen Sie nach *Key Vault*, und erstellen Sie eine **Key Vault**-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Die gleiche Ressourcengruppe wie für Ihre Cognitive Services-Ressource*.
    - **Name des Schlüsseltresors**: *Geben Sie einen eindeutigen Namen ein*.
    - **Region**: *Die gleiche Region wie für Ihre Cognitive Services-Ressource*.
    - **Tarif**: Standard.
3. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zu Ihrer Key Vault-Ressource.
4. Klicken Sie im linken Navigationsbereich auf **Geheimnisse** (im Abschnitt „Einstellungen“).
5. Wählen Sie **+ Generieren/Importieren** aus, und fügen Sie ein neues Geheimnis mit den folgenden Einstellungen hinzu:
    - **Uploadoptionen**: Manuell.
    - **Name**: Cognitive-Services-Key *(es ist wichtig, dass dies genau übereinstimmt, da Sie später einen Code ausführen werden, der das Geheimnis auf der Grundlage dieses Namens abruft)* .
    - **Wert**: *Ihr Cognitive Services-Schlüssel **key1***.

### <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Für den Zugriff auf das Geheimnis im Schlüsseltresor muss Ihre Anwendung einen Dienstprinzipal verwenden, der Zugriff auf das Geheimnis hat. Sie verwenden die Azure-Befehlszeilenschnittstelle (CLI), um den Dienstprinzipal zu erstellen, die entsprechende Objekt-ID zu ermitteln und den Zugriff auf das Geheimnis in Azure Key Vault zu gewähren.

1. Kehren Sie zu Visual Studio Code zurück, und führen Sie im integrierten Terminal für den Ordner **02-cognitive-security** den folgenden Azure CLI-Befehl aus, wobei Sie *&lt;spName&gt;* durch einen geeigneten Namen für eine Anwendungsidentität ersetzen (z. B. *ai-app*). Ersetzen Sie auch *&lt;subscriptionId&gt;* und *&lt;resourceGroup&gt;* durch die richtigen Werten für Ihre Abonnement-ID und die Ressourcengruppe, die Ihre Cognitive Services- und Key Vault-Ressourcen enthält:

    > **Tipp**: Wenn Sie sich bei Ihrer Abonnement-ID nicht sicher sind, verwenden Sie den Befehl **az account show**, um Ihre Abonnementinformationen abzurufen – die Abonnement-ID ist das **id**-Attribut in der Ausgabe.

    ```
    az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    ```

Die Ausgabe dieses Befehls enthält Informationen über Ihren neuen Dienstprinzipal. Es sollte in etwa wie folgt aussehen:

    ```
    {
        "appId": "abcd12345efghi67890jklmn",
        "displayName": "ai-app",
        "name": "http://ai-app",
        "password": "1a2b3c4d5e6f7g8h9i0j",
        "tenant": "1234abcd5678fghi90jklm"
    }
    ```

Notieren Sie sich die Werte für **appId**, **password** und **tenant** – Sie werden sie später benötigen (wenn Sie dieses Terminal schließen, können Sie das Kennwort nicht mehr abrufen. Daher ist es wichtig, die Werte jetzt zu notieren. Sie können die Ausgabe in eine neue Textdatei in Visual Studio Code einfügen, um sicherzustellen, dass Sie die benötigten Werte später wiederfinden).

2. Führen Sie zum Abrufen der **Objekt-ID** Ihres Dienstprinzipals den folgenden Azure CLI-Befehl aus, um *&lt;appId&gt;* durch den Wert der App-ID Ihres Dienstprinzipals zu ersetzen.

    ```
    az ad sp show --id <appId> --query objectId --out tsv
    ```

3. Um Ihrem neuen Dienstprinzipal die Berechtigung für den Zugriff auf Geheimnisse in Ihrer Key Vault-Instanz zu erteilen, führen Sie den folgenden Azure CLI-Befehl aus. Ersetzen Sie *&lt;keyVaultName&gt;* durch den Namen Ihrer Azure Key Vault-Ressource und *&lt;objectId&gt;* durch den Wert der Objekt-ID Ihres Dienstprinzipals.

    ```
    az keyvault set-policy -n <keyVaultName> --object-id <objectId> --secret-permissions get list
    ```

### <a name="use-the-service-principal-in-an-application"></a>Verwenden des Dienstprinzipals in einer Anwendung

Jetzt können Sie die Identität des Dienstprinzipals in einer Anwendung verwenden, sodass diese auf den geheimen Cognitive Services-Schlüssel in Ihrem Schlüsseltresor zugreifen und sich damit mit Ihrer Cognitive Services-Ressource verbinden kann.

> **Hinweis**: In dieser Übung werden wir die Anmeldedaten des Dienstprinzipals in der Anwendungskonfiguration speichern und sie zur Authentifizierung einer **ClientSecretCredential**-Identität in Ihrem Anwendungscode verwenden. Für Entwicklung und Testen ist dies in Ordnung, aber in einer echten Produktionsanwendung würde ein Administrator der Anwendung eine *verwaltete Identität* zuweisen, sodass sie die Identität des Dienstprinzipals für den Zugriff auf Ressourcen verwendet, ohne das Kennwort zwischenzuspeichern oder zu speichern.

1. Erweitern Sie in Visual Studio Code den Ordner **02-cognitive-security** und den Ordner **C-Sharp** oder **Python**, je nachdem, welche Sprache Sie bevorzugen.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **keyvault-client**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann die Pakete, die Sie für die Verwendung von Azure Key Vault und der Textanalyse-API in Ihrer Cognitive Services-Ressource benötigen, indem Sie den entsprechenden Befehl für Ihre Spracheinstellung ausführen:

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.1.0
    dotnet add package Azure.Identity --version 1.5.0
    dotnet add package Azure.Security.KeyVault.Secrets --version 4.2.0-beta.3
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.1.0
    pip install azure-identity==1.5.0
    pip install azure-keyvault-secrets==4.2.0
    ```

3. Zeigen Sie den Inhalt des Ordners **keyvault-client** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei, und aktualisieren Sie die darin enthaltenen Konfigurationswerte, um die folgenden Einstellungen wiederzugeben:
    
    - Der **Endpunkt** für Ihre Cognitive Services-Ressource.
    - Der Name Ihrer **Azure Key Vault**-Ressource.
    - Der **Mandant** für Ihren Dienstprinzipal.
    - Die **appId** (Anwendungs-ID) für Ihren Dienstprinzipal.
    - Das **Kennwort** für Ihren Dienstprinzipal.

     Speichern Sie die Änderungen.
4. Beachten Sie, dass der Ordner **keyvault-client** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: keyvault-client.py

    Öffnen Sie die Codedatei, und überprüfen Sie den darin enthaltenen Code, wobei Sie sich die folgenden Details notieren:
    - Der Namespace für das von Ihnen installierte SDK wird importiert.
    - Der Code in der Funktion **Main** ruft die Konfigurationseinstellungen der Anwendung ab und verwendet dann die Anmeldeinformationen des Dienstprinzipals, um den Cognitive Services-Schlüssel aus dem Schlüsseltresor abzurufen.
    - Die Funktion **GetLanguage** verwendet das SDK, um einen Client für den Dienst zu erstellen, und verwendet dann den Client, um die Sprache des eingegebenen Texts zu erkennen.
5. Kehren Sie zum integrierten Terminal für den Ordner **keyvault-client** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python keyvault-client.py
    ```

6. Wenn Sie dazu aufgefordert werden, geben Sie einen Text ein und überprüfen Sie die Sprache, die vom Dienst erkannt wird. Geben Sie beispielsweise „Hello“, „Bonjour“ und „Hola“ ein.
7. Wenn Sie mit dem Testen der Anwendung fertig sind, geben Sie „quit“ ein, um das Programm zu beenden.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zum Schützen von Cognitive Services finden Sie in der [Dokumentation zur Sicherheit in Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security).
