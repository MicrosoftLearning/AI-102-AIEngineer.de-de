---
lab:
  title: Erstellen eines Bots mit dem Bot Framework SDK
  module: Module 7 - Conversational AI and the Azure Bot Service
ms.openlocfilehash: 7f2d78bec2ee9fab7bf5fad9ec9ef8ebc76b7fba
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625805"
---
# <a name="create-a-bot-with-the-bot-framework-sdk"></a>Erstellen eines Bots mit dem Bot Framework SDK

*Bots* sind Software-Agents, die an Dialogen von Konversationen mit menschlichen Benutzern teilnehmen können. Das Microsoft Bot Framework bietet eine umfassende Plattform für die Erstellung von Bots, die als Clouddienste über den Azure Bot Service bereitgestellt werden können.

In dieser Übung werden Sie das Microsoft Bot Framework SDK verwenden, um einen Bot zu erstellen und bereitzustellen.

## <a name="before-you-start"></a>Vorbereitung

Beginnen wir damit, die Umgebung für die Bot-Entwicklung vorzubereiten.

### <a name="update-the-bot-framework-emulator"></a>Aktualisieren des Bot Framework Emulators

Sie werden das Bot Framework SDK verwenden, um Ihren Bot zu erstellen, und den Bot Framework Emulator, um ihn zu testen. Der Bot Framework Emulator wird regelmäßig aktualisiert. Stellen Sie also sicher, dass Sie die neueste Version installiert haben.

> **Hinweis**: Updates können Änderungen an der Benutzeroberfläche umfassen, die sich auf die Anweisungen in dieser Übung auswirken.

1. Starten Sie den **Bot Framework Emulator**, und wenn Sie aufgefordert werden, ein Update zu installieren, führen Sie dies für den aktuell angemeldeten Benutzer durch. Wenn Sie nicht automatisch dazu aufgefordert werden, verwenden Sie die Option **Nach Updates suchen** im Menü **Hilfe**, um nach Updates zu suchen.
2. Nachdem Sie alle verfügbaren Updates installiert haben, schließen Sie den Bot Framework Emulator, bis Sie ihn später wieder benötigen.

### <a name="clone-the-repository-for-this-course"></a>Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **AI-102-AIEngineer** noch nicht in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, führen Sie die folgenden Schritte aus, um dies zu tun. Öffnen Sie andernfalls den geklonten Ordner in Visual Studio Code.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/AI-102-AIEngineer` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Jetzt nicht** aus.

## <a name="create-a-bot"></a>Erstellen eines Bots

Sie können das Bot Framework SDK verwenden, um einen Bot auf der Grundlage einer Vorlage zu erstellen, und den Code dann an Ihre spezifischen Anforderungen anpassen.

> **Hinweis**: In dieser Übung können Sie entweder **C#** oder **Python** verwenden. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

1. Wechseln Sie in Visual Studio Code im **Explorer**-Bereich zum Ordner **13-bot-framework**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner für die von Ihnen gewählte Sprache, und öffnen Sie ein integriertes Terminal.
3. Führen Sie im Terminal die folgenden Befehle aus, um die Botvorlagen und Pakete zu installieren, die Sie benötigen:

**C#**

```C#
dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
dotnet new -i Microsoft.Bot.Framework.CSharp.CoreBot
dotnet new -i Microsoft.Bot.Framework.CSharp.EmptyBot
```

**Python**

```Python
pip install botbuilder-core
pip install asyncio
pip install aiohttp
pip install cookiecutter==1.7.0
```

4. Nachdem Sie die Vorlagen und Pakete installiert haben, führen Sie den folgenden Befehl aus, um einen Bot auf der Grundlage der Vorlage *EchoBot* zu erstellen:

**C#**

```C#
dotnet new echobot -n TimeBot
```

**Python**

```Python
cookiecutter https://github.com/microsoft/botbuilder-python/releases/download/Templates/echo.zip
```

Wenn Sie Python verwenden, geben Sie, wenn Sie von cookiecutter dazu aufgefordert werden, die folgenden Details ein:
- **bot_name**: TimeBot
- **bot_description**: Ein Bot für unsere Zeiten
    
5. Geben Sie im Terminalbereich die folgenden Befehle ein, um das aktuelle Verzeichnis in den Ordner **TimeBot** zu ändern und die Codedateien aufzulisten, die für Ihren Bot generiert wurden:

    ```Code
    cd TimeBot
    dir
    ```

## <a name="test-the-bot-in-the-bot-framework-emulator"></a>Testen des Bots im Bot Framework Emulator

Sie haben einen Bot auf der Grundlage der Vorlage *EchoBot* erstellt. Jetzt können Sie ihn lokal ausführen und mit dem Bot Framework Emulator (der auf Ihrem System installiert sein sollte) testen.

1. Vergewissern Sie sich im Terminalbereich, dass das aktuelle Verzeichnis der Ordner **TimeBot** ist, in dem sich Ihre Botcodedateien befinden. Geben Sie dann den folgenden Befehl ein, um Ihren Bot lokal auszuführen.

**C#**

```C#
dotnet run
```

**Python**

```Python
python app.py
```
    
Wenn der Bot startet, beachten Sie, dass der Endpunkt, an dem er ausgeführt wird, angezeigt wird. Dies sollte ähnlich sein wie **http://localhost:3978** .

2. Starten Sie den Bot Framework Emulator, und öffnen Sie Ihren Bot, indem Sie den Endpunkt mit dem angefügten Pfad **/api/messages** wie folgt angeben:

    `http://localhost:3978/api/messages`

3. Nachdem die Konversation in einem **Livechat**-Bereich geöffnet wurde, warten Sie auf die Nachricht *Hello and welcome!* (Hallo und willkommen!).
4. Geben Sie eine Nachricht wie *Hello* ein, und sehen Sie sich die Antwort des Bots an, der die von Ihnen eingegebene Nachricht zurückgeben sollte.
5. Schließen Sie den Bot Framework Emulator, und kehren Sie zu Visual Studio Code zurück. Geben Sie dann im Terminalfenster **STRG+C** ein, um den Bot zu beenden.

## <a name="modify-the-bot-code"></a>Ändern des Botcodes

Sie haben einen Bot erstellt, der die Eingaben des Benutzers an ihn zurücksendet. Dies ist nicht besonders nützlich, dient aber dazu, den grundlegenden Ablauf eines Dialogs während einer Konversation zu veranschaulichen. Eine Konversation mit einem Bot besteht aus einer Abfolge von *Aktivitäten*, bei denen Text, Grafiken oder *Karten* der Benutzeroberfläche zum Austausch von Informationen verwendet werden. Der Bot beginnt die Konversation mit einer Begrüßung, die das Ergebnis einer Aktivität zur *Konversationsaktualisierung* ist, die ausgelöst wird, wenn ein Benutzer eine Chatsitzung mit dem Bot initialisiert. Anschließend besteht die Konversation aus einer Abfolge weiterer Aktivitäten, bei denen der Benutzer und der Bot abwechselnd *Nachrichten* senden.

1. Öffnen Sie in Visual Studio Code die folgende Codedatei für Ihren Bot:
    - **C#** : TimeBot/Bots/EchoBot.cs
    - **Python**: TimeBot/bot.py

    Beachten Sie, dass der Code in dieser Datei aus *Aktivitätshandlerfunktionen* besteht. Eine für die Aktivität *Member Added* (Mitglied hinzugefügt) zur Konversationsaktualisierung (wenn jemand der Chatsitzung beitritt) und eine weitere für die Aktivität *Message* (Nachricht) (wenn eine Nachricht empfangen wird). Die Konversation basiert auf dem Konzept der *Turns* (Züge), bei dem jeder Turn eine Interaktion darstellt, bei der der Bot eine Aktivität empfängt, verarbeitet und darauf antwortet. Der *Turn-Kontext* wird verwendet, um Informationen über die Aktivität nachzuverfolgen, die im aktuellen Turn bearbeitet wird.

2. Fügen Sie am Anfang der Codedatei die folgende Anweisung für den Namespaceimport hinzu:

**C#**

```C#
using System;
```

**Python**

```Python
from datetime import datetime
```

3. Ändern Sie die Aktivitätshandlerfunktion für die Aktivität *Message* (Nachricht) so, dass sie dem folgenden Code entspricht:

**C#**

```C#
protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    string inputMessage = turnContext.Activity.Text;
    string responseMessage = "Ask me what the time is.";
    if (inputMessage.ToLower().StartsWith("what") && inputMessage.ToLower().Contains("time"))
    {
        var now = DateTime.Now;
        responseMessage = "The time is " + now.Hour.ToString() + ":" + now.Minute.ToString("D2");
    }
    await turnContext.SendActivityAsync(MessageFactory.Text(responseMessage, responseMessage), cancellationToken);
}
```

**Python**

```Python
async def on_message_activity(self, turn_context: TurnContext):
    input_message = turn_context.activity.text
    response_message = 'Ask me what the time is.'
    if (input_message.lower().startswith('what') and 'time' in input_message.lower()):
        now = datetime.now()
        response_message = 'The time is {}:{:02d}.'.format(now.hour,now.minute)
    await turn_context.send_activity(response_message)
```
    
4. Speichern Sie Ihre Änderungen und vergewissern Sie sich dann im Terminalbereich, dass das aktuelle Verzeichnis der Ordner **TimeBot** ist, in dem sich Ihre Botcodedateien befinden. Geben Sie dann den folgenden Befehl ein, um Ihren Bot lokal auszuführen.

**C#**

```C#
dotnet run
```

**Python**

```Python
python app.py
```

Wenn der Bot startet, beachten Sie wie zuvor, dass der Endpunkt, an dem er ausgeführt wird, angezeigt wird.

5. Starten Sie den Bot Framework Emulator, und öffnen Sie Ihren Bot, indem Sie den Endpunkt mit dem angefügten Pfad **/api/messages** wie folgt angeben:

    `http://localhost:3978/api/messages`

6. Nachdem die Konversation in einem **Livechat**-Bereich geöffnet wurde, warten Sie auf die Nachricht *Hello and welcome!* (Hallo und willkommen!).
7. Geben Sie eine Nachricht wie *Hello* ein, und betrachten Sie die Antwort des Bots, die *Ask me what the time is* (Frag mich, wie spät es ist) lauten sollte.
8. Geben Sie *What is the time?* (Wie spät ist es?) ein, und sehen Sie sich die Antwort an.

    Der Bot antwortet nun auf die Frage „What is the time?“ (Wie spät ist es?), durch Anzeigen der Ortszeit für den Standort, an dem der Bot ausgeführt wird. Bei allen anderen Abfragen wird der Benutzer aufgefordert, nach der Uhrzeit zu fragen. Dies ist ein sehr eingeschränkter Bot, der durch die Integration mit dem Language Understanding-Dienst und zusätzlichen benutzerdefinierten Code verbessert werden könnte. Er dient jedoch als funktionierendes Beispiel dafür, wie Sie mit dem Bot Framework SDK eine Lösung erstellen können, indem Sie einen Bot erweitern, der aus einer Vorlage erstellt wurde.

9. Schließen Sie den Bot Framework Emulator, und kehren Sie zu Visual Studio Code zurück. Geben Sie dann im Terminalfenster **STRG+C** ein, um den Bot zu beenden.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zum Bot Framework finden Sie in der [Bot Framework-Dokumentation](https://docs.microsoft.com/azure/bot-service/index-bf-sdk).
