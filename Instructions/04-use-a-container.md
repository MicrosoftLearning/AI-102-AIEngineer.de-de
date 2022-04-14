---
lab:
  title: Verwenden eines Cognitive Services-Containers
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: 3c9e51562e4fb93809855e3ae8707d6aeaf83c97
ms.sourcegitcommit: e9fc70625e09e4f4585a08e1668597f5d82616a0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/11/2022
ms.locfileid: "141605463"
---
# <a name="use-a-cognitive-services-container"></a>Verwenden eines Cognitive Services-Containers

Die Verwendung von in Azure gehosteten Cognitive Services ermöglicht Anwendungsentwicklern, sich auf die Infrastruktur für ihren eigenen Code zu konzentrieren und gleichzeitig von skalierbaren Diensten zu profitieren, die von Microsoft verwaltet werden. In vielen Szenarien benötigen Organisationen jedoch mehr Kontrolle über ihre Dienstinfrastruktur und die Daten, die zwischen Diensten übergeben werden.

Viele der Cognitive Services-APIs können in einen *Container* gepackt und darin bereitgestellt werden, sodass Organisationen Cognitive Services in ihrer eigenen Infrastruktur hosten können, beispielsweise in lokalen Docker-Servern, Azure Container Instances oder Azure Kubernetes Services-Clustern. Containerisierte Cognitive Services müssen mit einem Azure-basierten Cognitive Services-Konto kommunizieren, um die Abrechnung zu unterstützen. Aber Anwendungsdaten werden nicht an den Back-End-Dienst übergeben, und Organisationen haben mehr Kontrolle über die Bereitstellungskonfiguration ihrer Container, sodass benutzerdefinierte Lösungen für Authentifizierung, Skalierbarkeit und andere Überlegungen ermöglicht werden.

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
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0
3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
5. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. Sie benötigen den Endpunkt und einen der Schlüssel von dieser Seite im nächsten Verfahren.

## <a name="deploy-and-run-a-text-analytics-container"></a>Bereitstellen und Ausführen eines Textanalyse-Containers

Viele häufig verwendete Cognitive Services-APIs sind in Containerimages verfügbar. Eine vollständige Liste finden Sie in der [Cognitive Services-Dokumentation](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#container-availability-in-azure-cognitive-services). In dieser Übung verwenden Sie das Containerimage für die *Sprachenerkennungs*-API der Textanalyse, aber die Prinzipien sind für alle verfügbaren Images identisch.

1. Wählen Sie im Azure-Portal auf der Seite **Start** die Schaltfläche **&#65291;Ressource erstellen** aus, suchen Sie nach *Container Instances*, und erstellen Sie eine **Container Instances**-Ressource mit den folgenden Einstellungen:

    - **Grundlagen**:
        - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
        - **Ressourcengruppe**: *Wählen Sie die Ressourcengruppe aus, die Ihre Cognitive Services-Ressource enthält.*
        - **Containername**: *Geben Sie einen eindeutigen Namen ein.*
        - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
        - **Imagequelle**: Docker Hub oder andere Registrierung
        - **Image**: `mcr.microsoft.com/azure-cognitive-services/textanalytics/language:1.1.013570001-amd64`
        - **Betriebssystemtyp**: Linux
        - **Größe**: 1 virtuelle CPU, 4 GB Arbeitsspeicher
    - **Netzwerk**:
        - **Netzwerktyp**: Öffentlich
        - **DNS-Namensbezeichnung**: *Geben Sie einen eindeutigen Namen für den Containerendpunkt ein.*
        - **Ports**: *Ändern Sie den TCP-Port von 80 in **5000**.*
    - **Erweitert**:
        - **Neustartrichtlinie**: Bei Ausfall
        - **Environment variables** (Umgebungsvariablen):

            | Als sicher markieren | Schlüssel | Wert |
            | -------------- | --- | ----- |
            | Ja | `ApiKey` | *Jeder der Schlüssel für Ihre Cognitive Services-Ressource* |
            | Yes | `Billing` | *Der Endpunkt-URI für Ihre Cognitive Services-Ressource* |
            | No | `Eula` | `accept` |

        - **Außerkraftsetzung von Befehl**: [ ]
    - **Tags**:
        - *Fügen Sie keine Tags hinzu.*

2. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
3. Beachten Sie die folgenden Eigenschaften Ihrer Containerinstanzressource auf ihrer Seite **Übersicht**:
    - **Status**: Dieser sollte *Wird ausgeführt* lauten.
    - **IP-Adresse**: Dies ist die öffentliche IP-Adresse, die Sie für den Zugriff auf Ihre Containerinstanzen verwenden können.
    - **FQDN**: Dies ist der *vollqualifizierte Domänenname* der Container Instances-Ressource. Sie können diesen anstelle der IP-Adresse verwenden, um auf die Containerinstanzen zuzugreifen.

    > **Hinweis**: In dieser Übung haben Sie das Cognitive Services-Containerimage für die Textübersetzung in einer Azure Container Instances-Ressource (ACI) bereitgestellt. Sie können einen ähnlichen Ansatz verwenden, um es auf einem *[Docker](https://www.docker.com/products/docker-desktop)* -Host auf Ihrem eigenen Computer oder im eigenen Netzwerk bereitzustellen, indem Sie den folgenden Befehl (in einer einzigen Zeile) ausführen, um den Sprachenerkennungscontainer in Ihrer lokalen Docker-Instanz bereitzustellen. Ersetzen Sie dabei *&lt;yourEndpoint&gt;* und *&lt;yourKey&gt;* durch Ihren Endpunkt-URI und einen der Schlüssel für Ihre Cognitive Services-Ressource.
    >
    > ```
    > docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/language Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
    > ```
    >
    > Der Befehl sucht nach dem Image auf Ihrem lokalen Computer, und wenn er ihn dort nicht findet, wird er aus der Imageregistrierung *mcr.microsoft.com* abgerufen und in Ihrer Docker-Instanz bereitgestellt. Nach Abschluss der Bereitstellung startet der Container und lauscht auf eingehende Anforderungen an Port 5000.

## <a name="use-the-container"></a>Verwenden des Containers

1. Öffnen Sie in Visual Studio Code im Ordner **04-containers** die Datei **rest-test.cmd**, und bearbeiten Sie den darin enthaltenen Befehl **curl** (siehe unten). Ersetzen Sie dabei *&lt;your_ACI_IP_address_or_FQDN&gt;* durch die IP-Adresse oder den FQDN für Ihren Container.

    ```
    curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.0/languages?" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'Hello world.'},{'id':2,'text':'Salut tout le monde.'}]}"
    ```

2. Speichern Sie Ihre Änderungen im Skript. Beachten Sie, dass Sie weder den Endpunkt noch den Schlüssel von Cognitive Services angeben müssen. Die Anforderung wird vom containerisierten Dienst verarbeitet. Der Container kommuniziert wiederum regelmäßig mit dem Dienst in Azure, um die Nutzung für die Abrechnung zu melden, sendet aber keine Anforderungsdaten.
3. Klicken Sie mit der rechten Maustaste auf den Ordner **04-containers**, und öffnen Sie ein integriertes Terminal. Geben Sie den folgenden Befehl ein, um das Skript auszuführen:

    ```
    rest-test
    ```

4. Vergewissern Sie sich, dass der Befehl ein JSON-Dokument zurückgibt, das Informationen über die in den beiden Eingabedokumenten erkannte Sprache enthält (dies sollten Englisch und Französisch sein).

## <a name="clean-up"></a>Bereinigen

Wenn Sie die Experimente mit Ihrer Containerinstanz abgeschlossen haben, sollten Sie sie löschen.

1. Öffnen Sie im Azure-Portal die Ressourcengruppe, in der Sie Ihre Ressourcen für diese Übung erstellt haben.
2. Wählen Sie die Containerinstanzressource aus, und löschen Sie sie.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zum Containerisieren von Cognitive Services finden Sie in der [Dokumentation zu Cognitive Services-Containern](https://docs.microsoft.com/azure/cognitive-services/containers/).
