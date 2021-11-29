---
lab:
  title: Einrichten der Laborumgebung
  module: Setup
ms.openlocfilehash: 57363ce9fec94cb3a1a6ef7622a10bca48a6f055
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625813"
---
# <a name="lab-environment-setup"></a>Einrichten der Laborumgebung

Diese Übungen sind so konzipiert, dass sie in einer gehosteten Laborumgebung ausgeführt werden. Wenn Sie sie auf Ihrem eigenen Computer durchführen möchten, können Sie dazu die folgende Software installieren. Bei der Verwendung Ihrer eigenen Umgebung können unerwartete Dialoge und Verhaltensweisen auftreten. Aufgrund der Vielzahl möglicher lokaler Konfigurationen kann das Kursteam keine Unterstützung für Probleme leisten, die in Ihrer eigenen Umgebung auftreten können.

> **Hinweis**: Die folgenden Anweisungen beziehen sich auf einen Windows 10-Computer. Sie können auch Linux oder MacOS verwenden. Möglicherweise müssen Sie die Lab-Anweisungen für Ihr gewähltes Betriebssystem anpassen.

### <a name="base-operating-system-windows-10"></a>Basisbetriebssystem (Windows 10)

#### <a name="windows-10"></a>Windows 10

Installieren Sie Windows 10, und wenden Sie alle Updates an.

#### <a name="edge"></a>Microsoft Edge

Installieren von [Edge (Chromium)](https://microsoft.com/edge)

### <a name="net-core-sdk"></a>.NET Core SDK

1. Herunterladen und Installieren von https://dotnet.microsoft.com/download (Herunterladen des .NET Core SDK – nicht nur der Runtime)

### <a name="c-redistributable"></a>C++ Redistributable

1. Laden Sie das Visual C++ Redistributable (x64)-Paket von https://aka.ms/vs/16/release/vc_redist.x64.exe herunter, und installieren Sie es.

### <a name="nodejs"></a>Node.JS

1. Laden Sie die neueste LTS-Version von https://nodejs.org/en/download/ herunter. 
2. Installieren mithilfe der Standardoptionen

### <a name="python-and-required-packages"></a>Python (und erforderliche Pakete)

1. Laden Sie Version 3.8 von https://docs.conda.io/en/latest/miniconda.html herunter. 
2. Ausführen des Setups für die Installation – **Wichtig**: Wählen Sie die Optionen aus, um Miniconda zur PATH-Variablen hinzuzufügen und um Miniconda als Python-Standardumgebung zu registrieren.
3. Öffnen Sie nach der Installation die Anaconda-Eingabeaufforderung, und geben Sie die folgenden Befehle ein, um die Pakete zu installieren: 

```
pip install flask requests python-dotenv pylint matplotlib pillow
pip install --upgrade numpy
```

### <a name="azure-cli"></a>Azure CLI

1. Herunterladen von https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest 
2. Installieren mithilfe der Standardoptionen

### <a name="git"></a>Git

1. Herunterladen und Installieren von https://git-scm.com/download.html unter Verwendung der Standardoptionen


### <a name="visual-studio-code-and-extensions"></a>Visual Studio Code (und Erweiterungen)

1. Herunterladen von https://code.visualstudio.com/Download 
2. Installieren mithilfe der Standardoptionen 
3. Starten Sie Visual Studio Code nach der Installation, suchen Sie auf der Registerkarte **Erweiterungen** (STRG+UMSCHALT+X) nach den folgenden Erweiterungen von Microsoft, und installieren Sie sie:
    - Python
    - C#
    - Azure-Funktionen
    - PowerShell


### <a name="bot-framework-emulator"></a>Bot Framework Emulator

Befolgen Sie die Anweisungen unter https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md, um die neueste stabile Version des Bot Framework Emulators für Ihr Betriebssystem herunterzuladen und zu installieren.

### <a name="bot-framework-composer"></a>Bot Framework Composer

Installieren Sie dieses Feature über https://docs.microsoft.com/en-us/composer/install-composer.
