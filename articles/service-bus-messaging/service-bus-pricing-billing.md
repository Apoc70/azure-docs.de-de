---
title: "Service Bus – Preise und Abrechnung | Microsoft Docs"
description: "Übersicht über die Service Bus-Preisstruktur."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: 58d1b84c1a9fe19937846842f30f5e7b48cda1c7


---
# <a name="service-bus-pricing-and-billing"></a>Service Bus – Preise und Abrechnung
Service Bus wird in den Tarifen Basic, Standard und [Premium](service-bus-premium-messaging.md) angeboten. Sie können einen Diensttarif für jeden von Ihnen erstellten Service Bus-Dienstnamespace auswählen, und diese Tarifauswahl gilt für alle Warteschlangen, Entitäten, die in diesem Namespace erstellt werden.

> [!NOTE]
> Ausführliche Informationen zu den aktuellen Service Bus-Preisen finden Sie auf der [Azure Service Bus-Preisseite](https://azure.microsoft.com/pricing/details/service-bus/) und in den [häufig gestellten Fragen zu Service Bus](service-bus-faq.md#service-bus-pricing).
>
>

Service Bus verwendet die zwei folgenden Messgrößen für Warteschlangen und Themen/Abonnements:

1. **Messagingvorgänge**: Definiert als API-Aufrufe für Warteschlangen- oder Themen-/Abonnementdienstendpunkte. Diese Messgröße ersetzt gesendete oder empfangene Nachrichten als primäre Einheit der abrechenbaren Nutzung für Warteschlangen und Themen/Abonnements.
2. **Brokerverbindungen**: Definiert als die Höchstanzahl dauerhafter Verbindungen, die für Warteschlangen, Themen oder Abonnements während eines angegebenen einstündigen Stichprobenzeitraums geöffnet sind. Diese Messgröße gilt nur im Tarif „Standard“, in dem Sie zusätzliche Verbindungen für eine nominale Gebühr pro Verbindung öffnen können (zuvor waren Verbindungen auf 100 Verbindungen pro Warteschlange/Thema/Abonnement begrenzt).

Mit dem Tarif **Standard** werden gestaffelte Preise für Vorgänge eingeführt, die mit Warteschlangen und Themen/Abonnements ausgeführt werden. Dies führt zu volumenbasierten Rabatten von bis zu 80 Prozent in den höchsten Nutzungsebenen. Außerdem wird eine Basisgebühr von 10 US-Dollar pro Monat für den Tarif „Standard“ erhoben, die es Ihnen ermöglicht, bis zu 12,5 Millionen Vorgänge pro Monat ohne zusätzliche Kosten auszuführen.

Der Tarif **Premium** bietet Ressourcenisolierung auf CPU- und Arbeitsspeicherebene, sodass die Workloads der einzelnen Kunden voneinander isoliert ausgeführt werden. Dieser Ressourcencontainer wird als *Messaging-Einheit* bezeichnet. Jedem Premium-Namespace wird mindestens eine Messaging-Einheit zugeordnet. Sie können 1, 2 oder 4 Messaging-Einheiten für jeden Service Bus Premium-Namespace erwerben. Eine einzelne Workload oder Entität kann mehrere Messaging-Einheiten umfassen, und die Anzahl der Einheiten kann beliebig geändert werden. Gebühren werden jedoch im 24-Stunden- bzw. Tagesrhythmus berechnet. Das Ergebnis ist eine vorhersehbare und wiederholbare Leistung Ihrer Service Bus-basierten Lösung. Diese Leistung ist nicht nur besser vorhersehbar und verfügbar, sondern auch schneller. Azure Service Bus Premium-Messaging basiert auf dem in Azure Event Hubs eingeführten Speichermodul. Mit Premium-Messaging wird bei Spitzenleistung eine viel höhere Geschwindigkeit erzielt, als bei der Standard-Stufe.

Beachten Sie, dass die Standardbasisgebühr nur einmal pro Monat pro Azure-Abonnement berechnet wird. Dies bedeutet, dass Sie nach dem Erstellen eines einzelnen Service Bus-Namespaces des Tarifs „Standard“ oder „Premium“ beliebig viele weitere Namespaces des Tarifs „Standard“ oder „Premium“ unter dem gleichen Azure-Abonnement erstellen können, ohne dass zusätzliche Basisgebühren entstehen.

Alle vorhandenen Service Bus-Namespaces, die vor dem 1. November 2014 erstellt wurden, werden automatisch dem Tarif „Standard“ zugeordnet. Dadurch wird sichergestellt, dass Sie auch weiterhin Zugriff auf alle Funktionen haben, die derzeit mit Service Bus verfügbar sind. Anschließend können Sie über das [klassische Azure-Portal][Klassisches Azure-Portal] bei Bedarf eine Herabstufung auf den Tarif „Basic“ durchführen.

In der folgenden Tabelle sind die Funktionsunterschiede zwischen den Tarifen „Basic“ und „Standard“/„Premium“ zusammengefasst.

| Funktion | Basic | Standard/Premium |
| --- | --- | --- |
| Warteschlangen |Ja |Ja |
| Geplante Nachrichten |Ja |Ja |
| Themen/Abonnements |Nein |Ja |
| Relays |Nein |Ja |
| Transaktionen |Nein |Ja |
| Deduplizierung |Nein |Ja |
| Sitzungen |Nein |Ja |
| Umfangreiche Nachrichten |Nein |Ja |
| ForwardTo |Nein |Ja |
| SendVia |Nein |Ja |
| Brokerverbindungen (enthalten) |100 pro Service Bus-Namespace |1.000 pro Azure-Abonnement |
| Brokerverbindungen (Überschreitung zulässig) |Nein |Ja (abrechenbar) |

## <a name="messaging-operations"></a>Messagingvorgänge
Als Teil des neuen Preismodells ändert sich die Abrechnung für Warteschlangen und Themen/Abonnements. Diese Entitäten wechseln aus einer Abrechnung pro Nachricht zu einer Abrechnung pro Vorgang. Ein „Vorgang“ bezieht sich auf jeden API-Aufruf für einen Warteschlangen- oder Themen-/Abonnementdienstendpunkt. Dies umfasst Verwaltungs-, Sende-/Empfangs- und Sitzungsstatusvorgänge.

| Vorgangstyp | Beschreibung |
| --- | --- |
| Verwaltung |Erstellen, Lesen, Aktualisieren und Löschen (CRUD) von Warteschlangen oder Themen/Abonnements. |
| Nachrichten |Senden und Empfangen von Nachrichten mit Warteschlangen oder Themen/Abonnements. |
| Sitzungsstatus |Abrufen oder Festlegen des Sitzungsstatus für eine Warteschlange oder ein Thema/Abonnement. |

Seit dem 1. November 2014 gelten die folgenden Preise:

| Basic | Kosten |
| --- | --- |
| Vorgänge |0,05 US-Dollar pro 1 Million Vorgänge |

| Standard | Kosten |
| --- | --- |
| Basisgebühr |10 US-Dollar/Monat |
| Erste 12,5 Millionen Vorgänge/Monat |Enthalten |
| 12,5–100 Millionen Vorgänge/Monat |0,80 US-Dollar pro 1 Million Vorgänge |
| 100–2.500 Millionen Vorgänge/Monat |0,50 US-Dollar pro 1 Million Vorgänge |
| Mehr als 2.500 Millionen Vorgänge/Monat |0,20 US-Dollar pro 1 Million Vorgänge |

| Premium | Kosten |
| --- | --- |
| Täglich |11,13 US-Dollar Festpreis pro Nachrichteneinheit |

## <a name="brokered-connections"></a>Brokerverbindungen
*Brokerverbindungen* ermöglichen Nutzungsmuster von Kunden, die eine große Anzahl „dauerhaft verbundener“ Absender/Empfänger für Warteschlangen, Themen oder Abonnements umfassen. Dauerhaft verbundene Absender/Empfänger sind die Absender/Empfänger, die mithilfe von AMQP oder HTTP mit einem Empfangstimeout ungleich null eine Verbindung herstellen (z. B. langer HTTP-Abruf). HTTP-Absender und -Empfänger mit einem sofortigen Timeout generieren keine Brokerverbindungen.

Zuvor galt für Warteschlangen und Themen/Abonnements ein Grenzwert von 100 gleichzeitigen Verbindungen pro URL. Mit dem aktuellen Abrechnungsschema entfällt der Grenzwert pro URL für Warteschlangen und Themen/Abonnements. Stattdessen werden Kontingente und Messgrößen für Brokerverbindungen auf der Ebene des Service Bus-Namespace und des Azure-Abonnements implementiert.

Der Tarif „Basic“ umfasst 100 Brokerverbindungen pro Service Bus-Namespace (und ist streng auf diese Anzahl beschränkt). Verbindungen, die diese Anzahl überschreiten, werden im Tarif „Basic“ zurückgewiesen. Im Tarif „Standard“ entfällt der Grenzwert pro Namespace. Es zählt die aggregierte Verwendung von Brokerverbindungen im Azure-Abonnement. Im Tarif „Standard“ sind 1.000 Brokerverbindungen pro Azure-Abonnement ohne zusätzliche Kosten (die über die Basisgebühr hinausgehen) zulässig. Wenn mehr als insgesamt 1.000 Brokerverbindungen für Service Bus-Namespaces des Tarifs „Standard“ in einem Azure-Abonnement verwendet werden, erfolgt eine gestaffelte Abrechnung entsprechend der folgenden Tabelle:

| Brokerverbindungen (Tarif „Standard“) | Kosten |
| --- | --- |
| Erste 1.000/Monat |In der Basisgebühr enthalten |
| 1.000–100.000/Monat |0,03 US-Dollar pro Verbindung/Monat |
| 100.000–500.000/Monat |0,025 US-Dollar pro Verbindung/Monat |
| Mehr als 500.000/Monat |0,015 US-Dollar pro Verbindung/Monat |

> [!NOTE]
> 1.000 Brokerverbindungen sind im Messagingtarif „Standard“ (über die Basisgebühr) enthalten und können für alle Warteschlangen, Themen und Abonnements im zugehörigen Azure-Abonnement gemeinsam verwendet werden.
>
>

<br />

> [!NOTE]
> Die Abrechnung erfolgt anhand der höchsten Anzahl gleichzeitiger Verbindungen und wird anteilsmäßig auf Basis von 744 Stunden pro Monat berechnet.
>
>

| Tarif „Premium“ |
| --- |
| Brokerverbindungen werden im Tarif „Premium“ nicht in Rechnung gestellt. |

Weitere Informationen zu Brokerverbindungen finden Sie weiter unten im Abschnitt [Häufig gestellte Fragen](#faq) .

## <a name="wcf-relay"></a>WCF-Relay
WCF-Relays sind nur in den Namespaces des Tarifs „Standard“ verfügbar. Preise und Verbindungskontingente für Relays sind ansonsten unverändert. Dies bedeutet, dass Relays auch weiterhin anhand der Anzahl der Nachrichten (nicht der Vorgänge) und der Relaystunden abgerechnet werden.

| WCF-Relay – Preise | Kosten |
| --- | --- |
| WCF-Relaystunden |0,10 US-Dollar pro 100 Relaystunden |
| Meldungen |0,01 US-Dollar pro 10.000 Nachrichten |

## <a name="faq"></a>Häufig gestellte Fragen
### <a name="how-is-the-wcf-relay-hours-meter-calculated"></a>Wie werden WCF-Relaystunden berechnet?
Informationen dazu finden Sie in [diesem Thema](service-bus-faq.md).

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Was sind Brokerverbindungen, und wie erfolgt ihre Abrechnung?
Eine Brokerverbindung ist als eine der folgenden Verbindungen definiert:

1. Eine AMQP-Verbindung von einem Client mit einer Service Bus-Warteschlange oder einem Service Bus-Thema bzw. -Abonnement.
2. Ein HTTP-Aufruf zum Empfangen einer Nachricht von einem Service Bus-Thema oder einer Service Bus-Warteschlange mit einem Empfangstimeout größer null.

Für Service Bus wird die höchste Anzahl gleichzeitiger Brokerverbindungen in Rechnung gestellt, die die enthaltene Menge (1.000 Verbindungen im Tarif Standard“) überschreiten. Spitzenwerte werden stündlich gemessen, anteilig (dividiert durch 744 Stunden pro Monat) ermittelt und für den monatlichen Abrechnungszeitraum addiert. Die enthaltene Menge (1.000 Brokerverbindungen pro Monat) wird am Ende des Abrechnungszeitraums auf die Summe der anteiligen stündlichen Spitzenwerte angewendet.

Beispiel:

1. Alle 10.000 Geräte stellen eine Verbindung über eine einzelne AMQP-Verbindung her und empfangen Befehle von einem Service Bus-Thema. Die Geräte senden Telemetrieereignisse an einen Event Hub. Wenn alle Geräte jeden Tag 12 Stunden verbunden sind, entstehen die folgenden Verbindungsgebühren (zusätzlich zu eventuellen weiteren Gebühren für das Service Bus-Thema): 10.000 Verbindungen * 12 Stunden * 31 Tage / 744 = 5.000 Brokerverbindungen. Nach Abzug der monatlich zulässigen Menge von 1.000 Brokerverbindungen werden Ihnen 4.000 Brokerverbindungen zu einem Tarif von 0,03 US-Dollar pro Brokerverbindung berechnet. Dies ergibt eine Summe von 120 US-Dollar.
2. 10.000 Geräte empfangen Nachrichten von einer Service Bus-Warteschlange über HTTP mit einem festgelegten Timeout ungleich null. Wenn alle Geräte 12 Stunden täglich verbunden sind, entstehen die folgenden Verbindungsgebühren (zusätzlich zu eventuellen weiteren Service Bus-Gebühren): 10.000 HTTP Empfangsverbindungen * 12 Stunden pro Tag * 31 Tage/744 Stunden = 5.000 Brokerverbindungen.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Gelten für Warteschlangen und Themen/Abonnements Gebühren für Brokerverbindungen?
Ja. Es gibt keine Verbindungsgebühren für das Senden von Ereignissen über HTTP, unabhängig von der Anzahl der sendenden Systeme oder Geräte. Durch das Empfangen von Ereignissen mit HTTP mithilfe eines Timeouts größer als null (manchmal als "langer Abruf" bezeichnet) entstehen Gebühren für Brokerverbindungen. Durch AMQP-Verbindungen entstehen Gebühren für Brokerverbindungen, unabhängig davon, ob die Verbindungen zum Senden oder Empfangen verwendet werden. Beachten Sie, dass 100 Brokerverbindungen in einem Basic-Namespace kostenlos zulässig sind. Dies ist auch die maximale Anzahl von Brokerverbindungen, die in einem Azure-Abonnement zulässig ist. Die ersten 1.000 Brokerverbindungen in allen Standard-Namespaces in einem Azure-Abonnement sind ohne zusätzliche Kosten (über die Basisgebühr hinaus) enthalten. Da diese Kontingente für viele Dienst-zu-Dienst-Messaging-Szenarios ausreichend sind, werden Gebühren für Brokerverbindungen normalerweise nur dann relevant, wenn geplant ist, lange AMQP-oder HTTP-Abrufe mit einer großen Anzahl von Clients zu verwenden (z. B., um effizienteres Ereignisstreaming zu erreichen oder die bidirektionale Kommunikation mit zahlreichen Geräten oder Anwendungsinstanzen zu ermöglichen).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu Service Bus-Preisen finden Sie auf der [Azure Service Bus-Preisseite](https://azure.microsoft.com/pricing/details/service-bus/).
* In den [häufig gestellten Fragen zu Service Bus](service-bus-faq.md#service-bus-pricing) werden einige allgemeine Fragen zur Preisgestaltung und Abrechnung für Service Bus beantwortet.

[Klassisches Azure-Portal]: http://manage.windowsazure.com



<!--HONumber=Nov16_HO3-->


