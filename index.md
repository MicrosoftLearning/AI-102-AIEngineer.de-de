---
title: Online gehostete Anweisungen
permalink: index.html
layout: home
ms.openlocfilehash: ba3d2a5154e5e9b08e750f7d9ced8b62048dd66d
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625787"
---
# <a name="content-directory"></a>Inhaltsverzeichnis

Dieses Repository enthält die Übungen für das Praxislab zum Microsoft-Kurs [AI-102: Entwerfen und Implementieren einer Microsoft Azure KI-Lösung](https://docs.microsoft.com/learn/certifications/courses/ai-102t00) und den zugehörigen [Selbstlernkursen in Microsoft Learn](https://aka.ms/AzureLearn_AIEngineer). Diese Übungen begleiten die Lernmaterialen und erleichtern die praktische Anwendung der beschriebenen Technologien.

Sie benötigen ein Microsoft Azure-Abonnement für diese Übungen. Falls Sie kein Abonnement von Ihrem Kursleiter erhalten haben, können Sie sich unter [https://azure.microsoft.com](https://azure.microsoft.com) für eine kostenlose Testversion registrieren.

## <a name="labs"></a>Labs

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %}
| Modul | Labor |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} – {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

