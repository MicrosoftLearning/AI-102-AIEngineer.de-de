---
lab:
  title: Aktivieren von Ressourcenanbietern
  module: Setup
ms.openlocfilehash: 5de107bd550a03696f2c1e6fc6bfb9e4a7bdcc12
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625812"
---
# <a name="enable-resource-providers"></a>Aktivieren von Ressourcenanbietern

Es gibt einige Ressourcenanbieter, die in Ihrem Azure-Abonnement registriert sein müssen. Befolgen Sie diese Schritte, um sicherzustellen, dass sie registriert sind.

1. Melden Sie sich mit den Microsoft-Anmeldeinformationen, die Ihrem Abonnement zugeordnet sind, beim Azure-Portal unter `https://portal.azure.com` an.
2. Wählen Sie auf der Seite **Start** die Option **Abonnements** aus (oder erweitern Sie das Menü **&#8801;** , wählen Sie dann **Alle Dienste** und in der Kategorie **Alle** die Option **Abonnements** aus).
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
