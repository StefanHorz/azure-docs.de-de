---
title: Auswahl zwischen Flow, Logic Apps, Functions und WebJobs | Microsoft Docs
description: "Anhand eines Vergleichs und einer Gegenüberstellung der vier Cloudintegrationsdienste von Microsoft können Sie ermitteln, welche am besten für Sie geeignet sind."
services: functions,app-service\logic
documentationcenter: na
author: ggailey777
manager: wpickett
tags: 
keywords: Microsoft Flow, Flow, Logic Apps, Azure Functions, Functions, Azure WebJobs, WebJobs, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 7ffe44828735a5687008ebc5a7d8d9f017f49daa
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Auswahl zwischen Flow, Logic Apps, Functions und WebJobs
In diesem Artikel werden die folgenden Dienste in der Microsoft Cloud verglichen und gegenübergestellt, die alle zur Behebung von Integrationsproblemen und zur Automatisierung von Geschäftsprozessen dienen:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure-Funktionen](https://azure.microsoft.com/services/functions/)
* [WebJobs in Azure App Service](../app-service/web-sites-create-web-jobs.md)

Alle diese Dienste sind nützlich, wenn verschiedenartige Systeme miteinander verbunden werden. Jeder der Dienste kann Eingaben, Aktionen, Bedingungen und Ausgaben definieren. Sie können die Dienste individuell nach einem Zeitplan oder auslöserbasiert ausführen. Jeder Dienst hat jedoch individuelle Vorteile, und der Vergleich basiert nicht auf der Frage „Welcher Dienst ist der beste?“, sondern auf der Frage „Welcher Dienst ist für diese Situation am besten geeignet?“. Eine Kombination dieser Dienste ist häufig die beste Möglichkeit, um schnell eine skalierbare Integrationslösung mit vollem Funktionsumfang zu erstellen.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow im Vergleich zu Logic Apps
Wir können Microsoft Flow und Azure Logic Apps zusammen erörtern, da beide *Configuration-First*-Integrationsdienste sind. Sie erleichtern das Erstellen von Prozessen und Workflows und werden in verschiedene SaaS- und Enterprise-Anwendungen integriert. 

* Flow baut auf Logic Apps auf
* Sie haben den gleichen Workflow-Designer
* [Connectors](../connectors/apis-list.md) sind immer mit beiden Diensten kompatibel

Flow ermöglicht allen Mitarbeitern im Büro das Durchführen einfacher Integrationen (z.B. einen Genehmigungsprozess für eine SharePoint-Dokumentbibliothek) ohne Umweg über Entwickler oder IT. Andererseits ermöglicht Logic Apps erweiterte Integrationen (z.B. B2B-Prozesse), die DevOps und Sicherheitsvorkehrungen auf Unternehmensebene erfordern. In der Regel werden Geschäftsworkflows im Laufe der Zeit immer komplexer. Entsprechend können Sie zunächst mit einem Datenfluss beginnen und diesen dann nach Bedarf in eine Logik-App konvertieren.

Anhand der folgenden Tabelle können Sie ermitteln, ob Flow oder Logic Apps für eine bestimmte Integration am besten geeignet ist.

|  | Flow | Logic Apps |
| --- | --- | --- |
| Zielgruppe |Büroangestellte, geschäftliche Benutzer, SharePoint-Administratoren |Professionelle Integratoren und Entwickler, IT-Experten |
| Szenarien |Self-Service |Erweiterte Integrationen |
| Designtool |Im Browser und in der mobilen App, nur über die Benutzeroberfläche |Im Browser und [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Codeansicht](../logic-apps/logic-apps-author-definitions.md) verfügbar |
| Application Lifecycle Management (ALM) |Entwerfen und Testen in Nicht-Produktionsumgebungen, Überführung in Produktion, nachdem die Bereitschaft erzielt wurde. |DevOps: Quellcodeverwaltung, Tests, Support, Automatisierung und Verwaltung in [Azure Resource Management](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Administratorerfahrung |Verwalten von Flow-Umgebungen und Richtlinien zur Verhinderung von Datenverlust (Data Loss Prevention, DLP), Nachverfolgen der Lizenzierung [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Verwalten von Ressourcengruppen, Verbindungen, Zugriffsverwaltung und Protokollierung [https://portal.azure.com](https://portal.azure.com) |
| Sicherheit |Office 365-Sicherheit und Konformitätsüberwachungsprotokolle, Verhinderung von Datenverlust (Data Loss Prevention, DLP), [Verschlüsselung ruhender Daten](https://wikipedia.org/wiki/Data_at_rest#Encryption) für sensible Daten usw. |Sicherheitsgarantie von Azure: [Azure Security](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [Überwachungsprotokolle](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) und vieles mehr. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions im Vergleich zu WebJobs
Azure Functions und WebJobs in Azure App Service können gemeinsam beschrieben werden, da es sich bei beiden um Integrationsdienste mit *Vorabcodierung* handelt, die für Entwickler entworfen wurden. Sie können damit ein Skript oder eine Codepassage als Reaktion auf verschiedene Ereignisse ausführen, z.B. [neue Speicherblobs](functions-bindings-storage.md) oder [eine Webhookanforderung](functions-bindings-http-webhook.md). Ähnlichkeiten zwischen den Diensten: 

* Beide basieren auf [Azure App Service](../app-service/app-service-web-overview.md) und verfügen über Funktionen wie [Quellcodeverwaltung](../app-service/app-service-continuous-deployment.md), [Authentifizierung](../app-service/app-service-authentication-overview.md) und [Überwachung](../app-service/web-sites-monitor.md).
* Beide sind entwicklerorientiert.
* Beide unterstützen standardmäßige Skript- und Programmiersprachen.
* Beide verfügen über NuGet- und NPM-Unterstützung.

Functions ist die natürliche Weiterentwicklung von WebJobs, wobei die größten Vorteile von WebJobs übernommen und verbessert werden. Die Verbesserungen umfassen: 

* Modell „[Serverlose](https://azure.microsoft.com/overview/serverless-computing/)-App“
* Optimierte Entwicklung, Tests und Codeausführung direkt im Browser.
* Standardmäßige Integration in mehr Azure-Dienste und Drittanbieterdienste wie [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
* Nutzungsbasierte Bezahlung; es fallen keine Kosten für einen [App Service-Plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)an.
* Automatische, [dynamische Skalierung](functions-scale.md).
* Für bestehende Kunden von App Service ist die Ausführung nach einem App Service-Plan weiterhin möglich (zur Nutzung von nicht ausgelasteten Ressourcen).
* Integration in Logic Apps.

In der folgenden Tabelle werden die Unterschiede zwischen Functions und WebJobs erläutert:

|  | Funktionen | WebJobs |
| --- | --- | --- |
| Skalieren |Skalierung ohne Konfiguration |Skalierung mit App Service-Plan |
| Preise |Nutzungsbasierte Bezahlung oder als Teil eines App Service-Plans |Teil eines App Service-Plans |
| Ausführungstyp |Ausgelöst, geplant (durch Trigger mit Timer) |Ausgelöst, fortlaufend, geplant |
| Auslösende Ereignisse |[Timer](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-cosmosdb.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Mobile Azure App Service-Apps](functions-bindings-mobile-apps.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [Azure Storage-Warteschlangen und -Blobs](functions-bindings-storage-blob.md), [Azure Service Bus-Warteschlangen und -Themen](functions-bindings-service-bus.md) |[Azure Storage-Warteschlangen und -Blobs](functions-bindings-storage-blob.md), [Azure Service Bus-Warteschlangen und -Themen](functions-bindings-service-bus.md) |
| Entwicklung im Browser |Unterstützt |Nicht unterstützt |
| C# |Unterstützt |Unterstützt |
| F# |Unterstützt |Nicht unterstützt |
| JavaScript |Unterstützt |Unterstützt |
| Java |Vorschau | Nicht unterstützt |
| Bash |Experimentell |Unterstützt |
| Windows-Skripts (.cmd, .bat) |Experimentell |Unterstützt |
| PowerShell |Experimentell |Unterstützt |
| PHP |Experimentell |Unterstützt |
| Python |Experimentell |Unterstützt |
| TypeScript |Experimentell |Nicht unterstützt |

Ob Sie Functions oder WebJobs verwenden sollten, hängt letztendlich davon ab, wofür Sie App Service bereits nutzen. Wenn Sie eine App Service-App haben, für die Sie Codeausschnitte ausführen und diese gemeinsam in der gleichen DevOps-Umgebung verwalten möchten, verwenden Sie WebJobs. Verwenden Sie in den folgenden Szenarien Functions.

* Sie möchten Codeausschnitte für andere Azure-Dienste oder Drittanbieter-Apps ausführen.
* Sie möchten Ihren Integrationscode unabhängig von Ihren App Service-Apps verwalten.
* Sie möchten Ihre Codeausschnitte aus einer Logik-App aufrufen. 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Kombination aus Flow, Logic Apps und Functions
Wie bereits erwähnt hängt es von der jeweiligen Situation ab, welcher Dienst für Sie am besten geeignet ist. 

* Verwenden Sie für eine einfache Geschäftsoptimierung Flow.
* Verwenden Sie Logic Apps, wenn Ihr Integrationsszenario zu komplex für Flow ist oder wenn Sie DevOps-Funktionen benötigen.
* Wenn ein Schritt in Ihrem Integrationsszenario eine stark benutzerdefinierte Transformation oder speziellen Code erfordert, schreiben Sie eine Funktion und lösen sie dann als Aktion in Ihrer Logik-App aus.

Sie können eine Logik-App in einem Fluss aufrufen. Sie können auch eine Funktion in einer Logik-App und eine Logik-App in einer Funktion aufrufen. Die Integration zwischen Flow, Logic Apps und Functions wird im Laufe der Zeit weiter verbessert. Sie können etwas in einem Dienst erstellen und in den anderen Diensten verwenden. Daher lohnt sich jede Investition in diese drei Technologien.

## <a name="next-steps"></a>Nächste Schritte
Machen Sie sich mit den einzelnen Diensten vertraut, indem Sie Ihren ersten Fluss, eine Logik-App, eine Funktionen-App oder einen Webauftrag erstellen. Klicken Sie auf einen der folgenden Links:

* [Erste Schritte mit Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Erstellen Sie Ihre erste Funktion in Azure Functions](functions-create-first-azure-function.md)
* [Bereitstellen von Webaufträgen mit Visual Studio](../app-service/websites-dotnet-deploy-webjobs.md)

Weitere Informationen zu diesen Integrationsdiensten finden Sie auch unter den folgenden Links:

* [Leveraging Azure Functions &amp; Azure App Service for integration scenarios (Nutzung von Azure Functions und Azure App Service für Integrationsszenarien) von Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrations Made Simple (Integrationen leicht gemacht) von Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps-Livewebcast](http://aka.ms/logicappslive)
* [Häufig gestellte Fragen zu Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)

