---
lab:
  title: Aktivieren von Ressourcenanbietern
  module: Setup
ms.openlocfilehash: 2cd48d0d9f67b9bab3e64452bf6199e1f438492a
ms.sourcegitcommit: e06fbeaa3bc15e4dbe99f72216dfeeb27f58babd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2022
ms.locfileid: "138765491"
---
# <a name="enable-resource-providers"></a>Aktivieren von Ressourcenanbietern

Es gibt einige Ressourcenanbieter, die in Ihrem Azure-Abonnement registriert sein müssen. Befolgen Sie diese Schritte, um sicherzustellen, dass sie registriert sind.

1. Melden Sie sich mit den Microsoft-Anmeldeinformationen, die Ihrem Abonnement zugeordnet sind, beim Azure-Portal unter `https://portal.azure.com` an.
2. Wählen Sie auf der Seite **Start** die Option **Abonnements** aus, oder erweitern Sie das Menü **&#8801;** , und wählen Sie dann **Alle Dienste** und in der Kategorie **Allgemein** die Option **Abonnements** aus.
3. Wählen Sie Ihr Azure-Abonnement aus (wenn Sie mehrere Abonnements besitzen, wählen Sie dasjenige aus, das Sie durch Einlösen Ihrer Azure Pass-Instanz erstellt haben).
4. Wählen Sie auf dem Blatt für Ihr Abonnement im linken Fensterbereich im Abschnitt **Einstellungen** die Option **Ressourcenanbieter** aus.
5. Vergewissern Sie sich in der Liste der Ressourcenanbieter, dass die folgenden Anbieter registriert sind (falls nicht, wählen Sie sie aus und klicken auf **Registrieren**):
    - Microsoft.BotService
    - Microsoft.Web
    - Microsoft.ManagedIdentity
    - Microsoft.Search
    - Microsoft.Storage
    - Microsoft.CognitiveServices
    - Microsoft.AlertsManagement
    - microsoft.insights
    - Microsoft.KeyVault
    - Microsoft.ContainerInstance
