---
title: Intelligente Erkennung in Application Insights | Microsoft Docs
description: "Application Insights führt eine automatische umfassende Analyse Ihrer App-Telemetrie durch und warnt Sie vor potenziellen Problemen."
services: application-insights
documentationcenter: windows
author: rakefetj
manager: douge
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 63c901529b81c75f46f1b21219054817c148063a
ms.openlocfilehash: 30124e697af5f7560e8070ce43c5a985bd2c403b


---
# <a name="smart-detection-in-application-insights"></a>Intelligente Erkennung in Application Insights
 Die intelligente Erkennung warnt Sie automatisch vor potenziellen Leistungsproblemen in Ihrer Webanwendung. Sie führt eine proaktive Analyse der Telemetriedaten durch, die Ihre App an [Application Insights](app-insights-overview.md) sendet. Bei einem plötzlichen Anstieg der Fehlerraten oder bei ungewöhnlichen Mustern in der Client- oder Serverleistung erhalten Sie eine Warnung. Diese Funktion muss nicht konfiguriert werden. Sie wird ausgeführt, wenn Ihre Anwendung genügend Telemetriedaten sendet.

Sie können auf Warnungen der intelligenten Erkennung über die erhaltenen E-Mails oder das Blatt für die intelligenten Erkennung zugreifen.

## <a name="review-your-smart-detections"></a>Überprüfen der Ergebnisse der intelligenten Erkennung
Sie haben zwei Möglichkeiten, Erkennungen anzuzeigen:

* **Sie erhalten eine E-Mail** von Application Insights. Hier sehen Sie ein typisches Beispiel:
  
    ![E-Mail-Warnung](./media/app-insights-proactive-diagnostics/03.png)
  
    Klicken Sie auf die große Schaltfläche, um ausführlichere Informationen im Portal zu öffnen.
* Die Kachel für die **intelligente Erkennung** auf dem Blatt „Übersicht“ Ihrer App zeigt die Anzahl aktueller Warnungen an. Klicken Sie auf die Kachel, um eine Liste der aktuellen Warnungen anzuzeigen.

![Aktuelle Erkennungen anzeigen](./media/app-insights-proactive-diagnostics/04.png)

Wählen Sie eine Warnung aus, um Details anzuzeigen.

## <a name="what-problems-are-detected"></a>Welche Probleme werden erkannt?
Es gibt drei Arten der Erkennung:

* [Intelligente Erkennung – Fehlerabweichungen](app-insights-proactive-failure-diagnostics.md). Wir nutzen Machine Learning, um die voraussichtliche Rate fehlerhafter Anforderungen für Ihre App (korreliert mit Lastangaben und anderen Faktoren) festzulegen. Falls die Fehlerrate den erwarteten Rahmen überschreitet, wird eine Warnung gesendet.
* [Intelligente Erkennung – Leistungsabweichungen](app-insights-proactive-performance-diagnostics.md). Wir suchen jeden Tag nach anormalem Verhalten bei Reaktionszeiten und Fehlerraten. Diese Probleme werden mit Eigenschaften wie Standort, Browser, Clientbetriebssystem, Serverinstanz und Tageszeit in Beziehung gesetzt.
* [Intelligente Erkennung – Azure-Clouddienstprobleme](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Sie erhalten Warnungen, wenn Ihre App in Azure Cloud Services gehostet wird und bei einer Rolleninstanz Startfehler, häufige Wiederverwendungen oder Abstürze zur Laufzeit auftreten.

(Über die Hilfelinks in den jeweiligen Benachrichtigungen gelangen Sie zu den relevanten Artikeln.)

## <a name="next-steps"></a>Nächste Schritte
Mit den folgenden Diagnosetools können Sie die Telemetrie Ihrer App untersuchen:

* [Metrik-Explorer](app-insights-metrics-explorer.md)
* [Suchexplorer](app-insights-diagnostic-search.md)
* [Analytics: Leistungsfähige Abfragesprache](app-insights-analytics-tour.md)

Die intelligente Erkennungen erfolgt vollkommen automatisch. Vielleicht möchten Sie aber weitere Warnungen einrichten?

* [Einrichten von Warnungen in Application Insights](app-insights-alerts.md)
* [Verfügbarkeitswebtests](app-insights-monitor-web-app-availability.md) 




<!--HONumber=Nov16_HO3-->


