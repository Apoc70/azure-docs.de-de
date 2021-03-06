---
title: "Konfigurierbare Tokengültigkeitsdauern in Azure Active Directory | Microsoft Docs"
description: Diese Funktion wird von Administratoren und Entwicklern verwendet, um die Lebensdauer der von Azure AD ausgegebenen Token festzulegen.
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/06/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f61d23fec6badb8dd53379d183b177e4c19e5711


---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Konfigurierbare Tokengültigkeitsdauern in Azure Active Directory (öffentliche Vorschau)
> [!NOTE]
> Diese Funktion befindet sich derzeit in der Phase der öffentlichen Vorschau.  Sie sollten darauf vorbereitet sein, Änderungen zurückzusetzen bzw. zu löschen.  Im Rahmen der öffentlichen Vorschau kann diese Funktion von jedermann getestet werden. Bestimmte Aspekte erfordern jedoch unter Umständen ein [Azure AD-Premium-Abonnement](active-directory-get-started-premium.md), sobald sie allgemein verfügbar sind.
> 
> 

## <a name="introduction"></a>Einführung
Diese Funktion wird von Administratoren und Entwicklern verwendet, um die Lebensdauer der von Azure AD ausgegebenen Token festzulegen. Die Tokengültigkeitsdauer kann für alle Apps in einem Mandanten, für eine mehrinstanzenfähige Anwendung oder für einen bestimmten Dienstprinzipal in einem Mandanten konfiguriert werden.

In Azure AD steht ein Richtlinienobjekt für eine Reihe von Regeln, die für einzelne Anwendungen oder alle Anwendungen in einem Mandanten erzwungen werden.  Jede Art von Richtlinie verfügt über eine eindeutige Struktur mit einem Satz von Eigenschaften, die auf Objekte angewendet werden, denen sie zugewiesen sind.

Eine Richtlinie kann für einen Mandanten als Standard festgelegt werden. Diese Richtlinie wird dann für jede Anwendung wirksam, die sich innerhalb dieses Mandanten befindet, solange sie nicht durch eine Richtlinie mit höherer Priorität außer Kraft gesetzt wird. Richtlinien können auch bestimmten Anwendungen zugewiesen werden. Die Reihenfolge der Priorität variiert je nach Richtlinientyp.

Tokengültigkeitsdauer-Richtlinien können für Aktualisierungstoken, Zugriffstoken, Sitzungstoken und ID-Token konfiguriert werden.

### <a name="access-tokens"></a>Zugriffstoken
Ein Zugriffstoken wird von einem Client verwendet, um auf eine geschützte Ressource zuzugreifen. Ein Zugriffstoken kann nur für eine bestimmte Kombination aus Benutzer, Client und Ressource verwendet werden. Zugriffstoken können nicht widerrufen werden und sind bis zu ihrem Ablauf gültig. Ein böswilliger Akteur, der ein Zugriffstoken abgerufen hat, kann es während seiner gesamten Lebensdauer verwenden.  Das Anpassen der Gültigkeitsdauer von Zugriffstoken erfordert einen Kompromiss. Hierbei steht eine Verbesserung der Systemleistung einer Verlängerung der Zeitspanne gegenüber, die der Client weiterhin Zugriff hat, nachdem das Konto des Benutzers deaktiviert wurde.  Eine verbesserte Systemleistung wird dadurch erzielt, dass ein Client weniger oft ein neues Zugriffstoken abrufen muss. 

### <a name="refresh-tokens"></a>Aktualisierungstoken
Wenn ein Client ein Zugriffstoken für den Zugriff auf eine geschützte Ressource abruft, erhält er ein Aktualisierungstoken und ein Zugriffstoken. Das Aktualisierungstoken wird verwendet, um neue Zugriffs-/Aktualisierungstoken-Paare abzurufen, wenn das aktuelle Zugriffstoken abläuft. Aktualisierungstoken sind an die Kombination aus Benutzer und Client gebunden. Sie können widerrufen werden, und ihre Gültigkeit wird bei jeder Verwendung überprüft.

Es ist wichtig, zwischen vertraulichen und öffentlichen Clients zu unterscheiden. Vertrauliche Clients sind Anwendungen, die ein Clientkennwort sicher speichern können, was es ihnen ermöglicht nachzuweisen, dass Anforderungen von der Clientanwendung und nicht von einem böswilligen Akteur stammen. Da derartige Flüsse sicherer sind, ist die Standardgültigkeitsdauer von Aktualisierungstoken, die für diese Flüsse ausgestellt werden, höher und kann nicht mithilfe einer Richtlinie geändert werden.

Aufgrund der Einschränkungen der Umgebungen, in denen die Anwendungen ausgeführt werden, können öffentliche Clients ein Clientkennwort nicht sicher speichern. Für Ressourcen können Richtlinien festgelegt werden, um zu verhindern, dass Aktualisierungstoken aus öffentlichen Clients, deren Alter einen festgelegten Wert überschritten hat, ein neues Zugriffs-/Aktualisierungstoken-Paar abrufen können (maximale Zeit der Inaktivität für Aktualisierungstoken).  Darüber hinaus können Richtlinien verwendet werden, um eine bestimmte Zeitspanne festzulegen, nach der die Aktualisierungstoken nicht mehr akzeptiert werden (maximales Alter für Aktualisierungstoken).  Durch das Anpassen der Gültigkeitsdauer von Aktualisierungstoken können Sie steuern, wann und wie oft der Benutzer erneut Anmeldeinformationen eingeben muss, anstatt automatisch erneut authentifiziert zu werden, wenn er eine öffentliche Clientanwendung verwendet.

### <a name="id-tokens"></a>ID-Token
ID-Token werden an Websites und native Clients weitergegeben und enthalten Profilinformationen zu einem Benutzer. Ein ID-Token ist an eine bestimmte Kombination von Benutzer und Client gebunden. ID-Token werden bis zum Ablaufdatum als gültig betrachtet.  In der Regel passt eine Webanwendung die Gültigkeitsdauer der Sitzung eines Benutzers in der Anwendung an die Gültigkeitsdauer des für den Benutzer ausgegebenen ID-Tokens an.  Durch das Anpassen der Gültigkeitsdauer des ID-Tokens können Sie steuern, wie oft die Sitzung der Webanwendung abläuft und der Benutzer erneut (im Hintergrund oder interaktiv) bei Azure AD authentifiziert werden muss.

### <a name="single-sign-on-session-token"></a>Sitzungstoken für die einmalige Anmeldung
Wenn ein Benutzer eine Authentifizierung bei Azure AD vornimmt und das Kontrollkästchen „Angemeldet bleiben“ aktiviert, wird eine Sitzung für einmaliges Anmelden beim Browser des Benutzers und bei Azure AD hergestellt.  Ein Sitzungstoken für die einmalige Anmeldung hat die Form eines Cookies und repräsentiert diese Sitzung. Dabei ist zu beachten, dass das SSO-Sitzungstoken nicht an eine bestimmte Ressource/Clientanwendung gebunden ist. SSO-Sitzungstoken können widerrufen werden, und ihre Gültigkeit wird bei jeder Verwendung überprüft.

Es gibt zwei Arten von SSO-Sitzungstoken. Permanente Sitzungstoken werden vom Browser als permanente Cookies gespeichert, während nicht-permanente Sitzungstoken als Sitzungscookies gespeichert werden (diese gehen verloren, wenn der Browser geschlossen wird).

Nicht-permanente Sitzungstoken haben eine Gültigkeit von 24 Stunden, wohingegen permanente Token eine Gültigkeit von 180 Tagen haben. Jedes Mal, wenn das SSO-Sitzungstoken innerhalb seiner Gültigkeitsdauer verwendet wird, verlängert sich die Gültigkeitsdauer um weitere 24 Stunden bzw. 180 Tage. Wenn das SSO-Sitzungstoken innerhalb seiner Gültigkeitsdauer nicht verwendet wird, wird es als abgelaufen erachtet und nicht mehr akzeptiert. 

Es können Richtlinien verwendet werden, um einen Zeitraum ab der Ausstellung des ersten Sitzungstokens festzulegen, nach dem keine Sitzungstoken mehr akzeptiert werden (maximales Alter Sitzungstoken).  Durch das Anpassen der Gültigkeitsdauer von Sitzungstoken können Sie steuern, wann und wie oft der Benutzer erneut Anmeldeinformationen eingeben muss, anstatt automatisch authentifiziert zu werden, wenn er eine Webanwendung verwendet.

### <a name="token-lifetime-policy-properties"></a>Eigenschaften von Tokengültigkeitsdauer-Richtlinien
Eine Tokengültigkeitsdauer-Richtlinie ist ein Richtlinienobjekt, das Regeln für die Tokengültigkeitsdauer enthält.  Die Eigenschaften der Richtlinie werden verwendet, um angegebene Tokengültigkeitsdauern zu steuern.  Wenn keine Richtlinie festgelegt ist, erzwingt das System den Standardwert für die Gültigkeitsdauer.

### <a name="configurable-token-lifetime-properties"></a>Konfigurierbare Eigenschaften der Tokengültigkeitsdauer
| Eigenschaft | Richtlinien-Eigenschaftszeichenfolge | Betrifft | Standard | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Gültigkeitsdauer Zugriffstoken |AccessTokenLifetime |Zugriffstoken, ID-Token, SAML2-Token |1 Stunde |10 Minuten |1 Tag |
| Max. Zeit der Inaktivität für Aktualisierungstoken |MaxInactiveTime |Aktualisierungstoken |14 Tage |10 Minuten |90 Tage |
| Max. Alter Single-Factor-Aktualisierungstoken |MaxAgeSingleFactor |Aktualisierungstoken (für alle Benutzer) |90 Tage |10 Minuten |Bis zum Widerruf* |
| Max. Alter Multi-Factor-Aktualisierungstoken |MaxAgeMultiFactor |Aktualisierungstoken (für alle Benutzer) |90 Tage |10 Minuten |Bis zum Widerruf* |
| Max. Alter Single-Factor-Sitzungstoken |MaxAgeSessionSingleFactor** |Sitzungstoken (permanent oder nicht-permanent) |Bis zum Widerruf |10 Minuten |Bis zum Widerruf* |
| Max. Alter Multi-Factor-Sitzungstoken |MaxAgeSessionMultiFactor*** |Sitzungstoken (permanent oder nicht-permanent) |Bis zum Widerruf |10 Minuten |Bis zum Widerruf* |

* *365 Tage ist die explizite Maximallänge, die für diese Attribute festgelegt werden kann.
* **Wenn MaxAgeSessionSingleFactor nicht festgelegt ist, wird der Wert MaxAgeSingleFactor angenommen. Wenn keiner der Parameter festgelegt ist, nimmt die Eigenschaft den Standardwert (Bis zum Widerruf) an.
* ***Wenn MaxAgeSessionMultiFactor nicht festgelegt ist, wird der Wert MaxAgeMultiFactor angenommen. Wenn keiner der Parameter festgelegt ist, nimmt die Eigenschaft den Standardwert (Bis zum Widerruf) an.

### <a name="exceptions"></a>Ausnahmen
| Eigenschaft | Betrifft | Standard |
| --- | --- | --- |
| Max. Zeit der Inaktivität für Aktualisierungstoken (Verbundbenutzer mit unzureichenden Widerrufsinformationen) |Aktualisierungstoken (für Verbundbenutzer mit unzureichenden Widerrufsinformationen ausgestellt) |12 Stunden |
| Max. Zeit der Inaktivität für Aktualisierungstoken (vertrauliche Clients) |Aktualisierungstoken (für vertrauliche Clients ausgestellt) |90 Tage |
| Max. Alter Aktualisierungstoken (für vertrauliche Clients ausgestellt) |Aktualisierungstoken (für vertrauliche Clients ausgestellt) |Bis zum Widerruf |

### <a name="priority-and-evaluation-of-policies"></a>Priorität und Auswertung von Richtlinien
Richtlinien für die Tokengültigkeitsdauer können erstellt und bestimmten Anwendungen, Mandanten und Dienstprinzipalen zugewiesen werden. Dies bedeutet, dass mehrere Richtlinien auf eine bestimmte Anwendung angewandt werden können. Folgende Regeln bestimmen, welche Tokengültigkeitsdauer-Richtlinie wirksam wird:

* Wenn eine Richtlinie explizit dem Dienstprinzipal zugewiesen ist, wird sie erzwungen. 
* Wenn keine Richtlinie explizit dem Dienstprinzipal zugewiesen ist, wird eine Richtlinie erzwungen, die explizit dem übergeordneten-Mandanten des Dienstprinzipals zugewiesen ist. 
* Wenn keine Richtlinie explizit dem Dienstprinzipal oder dem Mandanten zugewiesen ist, wird die Richtlinie erzwungen, die der Anwendung zugewiesen ist. 
* Wenn dem Dienstprinzipal, Mandanten oder Anwendungsobjekt keine Richtlinie zugewiesen ist, werden die Standardwerte erzwungen (siehe obige Tabelle).

Ausführliche Informationen zu den Beziehungen zwischen Anwendungsobjekten und Dienstprinzipalobjekten in Azure AD finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](active-directory-application-objects.md).

Die Gültigkeit des Tokens wird zum Zeitpunkt seiner Verwendung überprüft. Die Richtlinie mit der höchsten Priorität für die Anwendung, auf die zugegriffen wird, wird wirksam.

> [!NOTE]
> Beispiel
> 
> Ein Benutzer möchte auf die beiden Webanwendungen A und B zugreifen. 
> 
> * Beide Anwendungen gehören zu demselben übergeordneten Mandanten. 
> * Als Standard für den übergeordneten Mandanten wird die Tokengültigkeitsdauer-Richtlinie 1 mit einem maximalen Alter für Sitzungstoken von 8 Stunden festgelegt.
> * Webanwendung A ist eine regelmäßig verwendete Webanwendung, die nicht mit Richtlinien verknüpft ist. 
> * Webanwendung B wird für hochsensible Prozesse verwendet, und ihr Dienstprinzipal ist mit der Tokengültigkeitsdauer-Richtlinie 2 mit einem maximalen Alter für Sitzungstoken von 30 Minuten verknüpft.
> 
> Um 12 Uhr öffnet der Benutzer eine neue Browsersitzung und versucht, auf die Webanwendung A zuzugreifen. Der Benutzer wird an Azure AD umgeleitet und aufgefordert, sich anzumelden. Hierdurch wird im Browser ein Cookie mit einem Sitzungstoken gespeichert. Der Benutzer wird mit einem ID-Token an die Webanwendung A zurückgeleitet. Dieses ID-Token erlaubt ihm den Zugriff auf die Anwendung.
> 
> Um 12:15 Uhr versucht der Benutzer, auf Webanwendung B zuzugreifen. Der Browser nimmt die Umleitung an Azure AD vor, welches das Sitzungscookie erkennt. Der Dienstprinzipal von Webanwendung B ist mit Richtlinie 2 verknüpft, ist aber zugleich Teil des übergeordneten Mandanten mit der Standardrichtlinie 1. Richtlinie 2 wird wirksam, da mit Dienstprinzipalen verknüpfte Richtlinien eine höhere Priorität haben als Mandantenstandardrichtlinien. Das Sitzungstoken wurde innerhalb der letzten 30 Minuten erstmalig ausgegeben, weshalb es als gültig erachtet wird. Der Benutzer wird mit einem ID-Token, das ihm Zugriff gewährt, an die Webanwendung B zurückgeleitet.
> 
> Um 13:00 Uhr versucht der Benutzer, zur Webanwendung A zu navigieren. Der Benutzer wird an Azure AD umgeleitet. Webanwendung A ist nicht mit Richtlinien verknüpft, aber da sie sich in einem Mandanten mit Standardrichtlinie 1 befindet, wird diese Richtlinie wirksam. Das innerhalb der letzten acht Stunden erstmalig ausgestellte Sitzungscookie wird erkannt, und der Benutzer wird im Hintergrund mit einem neuen ID-Token an die Webanwendung A zurückgeleitet, ohne dass er sich authentifizieren muss.
> 
> Der Benutzer versucht unmittelbar danach, auf Webanwendung B zuzugreifen und wird an Azure AD umgeleitet. Wie bereits zuvor tritt Richtlinie 2 in Kraft. Da die Ausstellung des Tokens mehr als 30 Minuten zurückliegt, wird der Benutzer aufgefordert, seine Anmeldeinformationen erneut eingeben, und es werden völlig neue Sitzungs- und ID-Token ausgestellt. Nun kann der Benutzer auf Webanwendung B zugreifen.
> 
> 

## <a name="configurable-policy-properties-in-depth"></a>Konfigurierbare Richtlinieneigenschaften: ausführliche Informationen
### <a name="access-token-lifetime"></a>Gültigkeitsdauer Zugriffstoken
**Zeichenfolge:** AccessTokenLifetime

**Betrifft:** Zugriffstoken, ID-Token

**Zusammenfassung:** Diese Richtlinie steuert, wie lange Zugriffstoken und ID-Token für diese Ressource als gültig angesehen werden. Das Reduzieren der Gültigkeitsdauer eines Zugriffstokens verringert das Risiko, dass ein Zugriffstoken oder ein ID-Token für längere Zeit von einem böswilligen Akteur verwendet wird (da das Token nicht widerrufen werden kann). Allerdings wirkt sich dies nachteilig auf die Leistung aus, da die Token häufiger ersetzt werden müssen.

### <a name="refresh-token-max-inactive-time"></a>Max. Zeit der Inaktivität für Aktualisierungstoken
**Zeichenfolge:** MaxInactiveTime

**Betrifft:** Aktualisierungstoken

**Zusammenfassung:** Diese Richtlinie steuert, wie alt ein Aktualisierungstoken sein darf, bevor ein Client es nicht mehr verwenden kann, um ein neues Zugriffs-/Aktualisierungstoken-Paar abzurufen, wenn versucht wird, auf diese Ressource zuzugreifen. Da bei Verwendung eines Aktualisierungstokens üblicherweise ein neues Aktualisierungstoken zurückgegeben wird, darf der Client im festgelegten Zeitraum, bis diese Richtlinie den Zugriff verhindern würde, nicht mit dem aktuellen Aktualisierungstoken versucht haben, auf eine Ressource zuzugreifen. 

Durch diese Richtlinie werden Benutzer, die nicht auf ihrem Client aktiv waren, gezwungen, sich erneut zu authentifizieren, um ein neues Aktualisierungstoken abzurufen. 

Hierbei ist zu beachten, dass das Token „Maximale Inaktivitätszeit Aktualisierungstoken“ auf einen niedrigeren Wert gesetzt sein muss als die Token „Maximales Alter Single-Factor-Token“ und „Maximales Alter Multi-Factor-Aktualisierungstoken“.

### <a name="single-factor-refresh-token-max-age"></a>Max. Alter Single-Factor-Aktualisierungstoken
**Zeichenfolge:** MaxAgeSingleFactor

**Betrifft:** Aktualisierungstoken

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer weiterhin Aktualisierungstoken verwenden kann, um neue Zugriffs-/Aktualisierungtoken-Paare zu erhalten, nachdem er sich letztmalig erfolgreich mit nur einem Faktor authentifiziert hat. Sobald sich ein Benutzer authentifiziert und ein neues Aktualisierungstoken erhält, kann er den Aktualisierungstokenfluss für den angegebenen Zeitraum verwenden (solange das aktuelle Aktualisierungstoken nicht widerrufen wird und nicht über die Inaktivitätszeit hinaus ungenutzt bleibt). An diesem Punkt werden Benutzer gezwungen, sich erneut zu authentifizieren, um ein neues Aktualisierungstoken zu erhalten. 

Durch die Reduzierung des maximalen Alters müssen sich Benutzer öfter authentifizieren. Da die Single-Factor Authentication als weniger sicher erachtet wird als die Multi-Factor Authentication, wird empfohlen, diese Richtlinie auf einen Wert gleich oder kleiner des Wertes für die Richtlinie „Max. Alter Multi-Factor-Aktualisierungstoken“ festzulegen.

### <a name="multi-factor-refresh-token-max-age"></a>Max. Alter Multi-Factor-Aktualisierungstoken
**Zeichenfolge:** MaxAgeMultiFactor

**Betrifft:** Aktualisierungstoken

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer weiterhin Aktualisierungstoken verwenden kann, um neue Zugriffs-/Aktualisierungtoken-Paare zu erhalten, nachdem er sich letztmalig erfolgreich mit mehreren Faktoren authentifiziert hat. Sobald sich ein Benutzer authentifiziert und ein neues Aktualisierungstoken erhält, kann er den Aktualisierungstokenfluss für den angegebenen Zeitraum verwenden (solange das aktuelle Aktualisierungstoken nicht widerrufen wird und nicht über die Inaktivitätszeit hinaus ungenutzt bleibt). An diesem Punkt werden Benutzer gezwungen, sich erneut zu authentifizieren, um ein neues Aktualisierungstoken zu erhalten. 

Durch die Reduzierung des maximalen Alters müssen sich Benutzer öfter authentifizieren. Da die Single-Factor Authentication als weniger sicher erachtet wird als die Multi-Factor Authentication, wird empfohlen, diese Richtlinie auf einen Wert gleich oder größer des Wertes für die Richtlinie „Max. Alter Single-Factor-Aktualisierungstoken“ festzulegen.

### <a name="single-factor-session-token-max-age"></a>Max. Alter Single-Factor-Sitzungstoken
**Zeichenfolge:** MaxAgeSessionSingleFactor

**Betrifft:** Sitzungstoken (permanent oder nicht-permanent)

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer weiterhin Sitzungstoken verwenden kann, um neue ID- und Sitzungstoken zu erhalten, nachdem er sich letztmalig erfolgreich mit nur einem Faktor authentifiziert hat. Sobald sich ein Benutzer authentifiziert und ein neues Sitzungstoken erhält, kann er den Sitzungstokenfluss für den angegebenen Zeitraum verwenden (solange das aktuelle Sitzungstoken nicht widerrufen wurde oder abgelaufen ist). An diesem Punkt werden Benutzer gezwungen, sich erneut zu authentifizieren, um ein neues Sitzungstoken zu erhalten. 

Durch die Reduzierung des maximalen Alters müssen sich Benutzer öfter authentifizieren. Da die Single-Factor Authentication als weniger sicher erachtet wird als die Multi-Factor Authentication, wird empfohlen, diese Richtlinie auf einen Wert gleich oder kleiner des Wertes für die Richtlinie „Max. Alter Multi-Factor-Sitzungstoken“ festzulegen.

### <a name="multi-factor-session-token-max-age"></a>Max. Alter Multi-Factor-Sitzungstoken
**Zeichenfolge:** MaxAgeSessionMultiFactor

**Betrifft:** Sitzungstoken (permanent oder nicht-permanent)

**Zusammenfassung:** Diese Richtlinie steuert, wie lange ein Benutzer weiterhin Sitzungstoken verwenden kann, um neue ID- und Sitzungstoken zu erhalten, nachdem er sich letztmalig erfolgreich mit mehreren Faktoren authentifiziert hat. Sobald sich ein Benutzer authentifiziert und ein neues Sitzungstoken erhält, kann er den Sitzungstokenfluss für den angegebenen Zeitraum verwenden (solange das aktuelle Sitzungstoken nicht widerrufen wurde oder abgelaufen ist). An diesem Punkt werden Benutzer gezwungen, sich erneut zu authentifizieren, um ein neues Sitzungstoken zu erhalten. 

Durch die Reduzierung des maximalen Alters müssen sich Benutzer öfter authentifizieren. Da die Single-Factor Authentication als weniger sicher erachtet wird als die Multi-Factor Authentication, wird empfohlen, diese Richtlinie auf einen Wert gleich oder größer des Wertes für die Richtlinie „Max. Alter Single-Factor-Sitzungstoken“ festzulegen.

## <a name="sample-token-lifetime-policies"></a>Beispiel: Tokengültigkeitsdauer-Richtlinien
Die Möglichkeit zum Erstellen und Verwalten von Tokengültigkeitsdauern für Apps, Dienstprinzipale und Ihren gesamten Mandanten eröffnet unterschiedlichste neue Szenarios in Azure AD.  Wir werden einige allgemeine Richtlinienszenarios durchgehen, die es ermöglichen, neue Regeln für folgende Festlegungen vorzugeben:

* Tokengültigkeitsdauer
* Max. Inaktivitätszeit von Token
* Max. Alter von Token

Wir werden einige Szenarios durchgehen, beispielsweise:

* Verwalten der Standardrichtlinie eines Mandanten
* Erstellen einer Richtlinie für die Webanmeldung
* Erstellen einer Richtlinie für native Apps, die eine Web-API aufrufen
* Verwalten einer erweiterten Richtlinie 

### <a name="prerequisites"></a>Voraussetzungen
In den Beispielszenarios werden wir Richtlinien für Apps, Dienstprinzipale und Ihren gesamten Mandanten erstellen, aktualisieren, verknüpfen und löschen.  Lesen Sie [diesen Artikel](active-directory-howto-tenant.md), wenn Sie noch nicht mit Azure AD vertraut sind. Dort erhalten Sie Hilfestellung bei der Einrichtung, bevor Sie sich den nachstehenden Beispielen zuwenden.  

1. Laden Sie zunächst das aktuelle [Azure AD PowerShell-Cmdlet (Vorschau)](https://www.powershellgallery.com/packages/AzureADPreview) herunter. 
2. Nach dem Herunterladen des Azure AD PowerShell-Cmdlets führen Sie den Befehl „Connect“ aus, um sich bei Ihrem Azure AD-Administratorkonto anzumelden. Sie müssen dies jedes Mal tun, wenn Sie eine neue Sitzung starten.
   
     Connect-AzureAD -Confirm
3. Führen Sie den folgenden Befehl aus, um alle Richtlinien anzuzeigen, die in Ihrem Mandanten erstellt wurden.  Dieser Befehl sollte nach den meisten Vorgängen in den folgenden Szenarios verwendet werden.  Außerdem erhalten Sie so die **Objekt-ID** Ihrer Richtlinien. 
   
     Get-AzureADPolicy

### <a name="sample-managing-a-tenants-default-policy"></a>Beispiel: Verwalten der Standardrichtlinie eines Mandanten
In diesem Beispiel wird eine Richtlinie erstellt, die es den Benutzern ermöglicht, sich weniger häufig bei Ihrem gesamten Mandanten anzumelden. 

Hierzu erstellen wir eine Richtlinie für die Tokengültigkeitsdauer für Single-Factor-Aktualisierungstoken, die auf Ihren gesamten Mandanten angewandt wird. Diese Richtlinie wird auf jede Anwendung in Ihrem Mandanten und jeden Dienstprinzipal angewandt, für die/den noch keine Richtlinie festgelegt wurde. 

1. **Legen Sie eine Tokengültigkeitsdauer-Richtlinie an.** 

Setzen Sie das Single-Factor-Aktualisierungstoken auf „Bis zum Widerruf“, was bedeutet, es läuft erst ab, wenn der Zugriff widerrufen wird.  Wir werden die untenstehende Richtliniendefinition erstellen:

        @("{
          `"TokenLifetimePolicy`":
              {
                 `"Version`":1, 
                 `"MaxAgeSingleFactor`":`"until-revoked`"
              }
        }")

Führen Sie nun den folgenden Befehl zum Erstellen dieser Richtlinie aus. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1, `"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName TenantDefaultPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy

Um Ihre neue Richtlinie und deren ObjectID abzurufen, führen Sie den folgenden Befehl aus.

    Get-AzureADPolicy
&nbsp;&nbsp;2.    **Aktualisieren Sie die Richtlinie.**

Sie haben festgestellt, dass die erste Richtlinie nicht so streng ist, wie es Ihr Dienst erfordert, weshalb Ihre Single-Factor-Aktualisierungstoken nach zwei Tagen ablaufen sollen. Führen Sie den folgenden Befehl aus: 

    Set-AzureADPolicy -ObjectId <ObjectID FROM GET COMMAND> -DisplayName TenantDefaultPolicyUpdatedScenario -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"2.00:00:00`"}}")

&nbsp;&nbsp;3. **Fertig!** 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>Beispiel: Erstellen einer Richtlinie für die Webanmeldung
In diesem Beispiel erstellen wir eine Richtlinie, die öfter eine Authentifizierung von Benutzern in Ihrer Web-App erfordert. Diese Richtlinie wird die Lebensdauer der Zugriffs-/ID-Token und das maximale Alter eines Multi-Factor-Sitzungstokens auf die Werte des Dienstprinzipals Ihrer Web-App setzen.

1. **Legen Sie eine Tokengültigkeitsdauer-Richtlinie an.**

Diese Richtlinie für die Webanmeldung wird die Gültigkeit des Zugriffs-/ID-Tokens und das maximale Alter des Single-Factor-Sitzungstokens auf zwei Stunden festlegen.

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"02:00:00`",`"MaxAgeSessionSingleFactor`":`"02:00:00`"}}") -DisplayName WebPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy

Um Ihre neue Richtlinie und deren ObjectID abzurufen, führen Sie den folgenden Befehl aus.

    Get-AzureADPolicy
&nbsp;&nbsp;2.    **Weisen Sie die Richtlinie Ihrem Dienstprinzipal zu.**

Wir werden diese neue Richtlinie mit einen Dienstprinzipal verknüpfen.  Sie benötigen auch eine Möglichkeit, auf die **ObjectId** Ihres Dienstprinzipals zuzugreifen. Sie können [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) abfragen oder zum [Graph-Tester](https://graphexplorer.cloudapp.net/) wechseln und sich bei Ihrem Azure AD-Konto anmelden, um alle Dienstprinzipale Ihres Mandanten zu sehen. 

Nachdem Sie die **ObjectId** abgerufen haben, führen Sie den folgenden Befehl aus.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
&nbsp;&nbsp;3.    **Fertig!** 

 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>Beispiel: Erstellen einer Richtlinie für native Apps, die eine Web-API aufrufen
> [!NOTE]
> Das Verknüpfen von Richtlinien mit Anwendungen ist derzeit deaktiviert.  Wir arbeiten daran, dass diese Funktion in Kürze bereitgestellt wird.  Diese Seite wird aktualisiert, sobald die Funktion verfügbar ist.
> 
> 

In diesem Beispiel werden wir eine Richtlinie erstellen, mit der sich Benutzer seltener authentifizieren müssen und die die Zeitspanne verlängert, die Benutzer inaktiv sein können, ohne sich erneut authentifizieren zu müssen. Die Richtlinie wird auf die Web-API angewandt. Wenn die native App sie als Ressource anfordert, wird diese Richtlinie somit angewandt.

1. **Legen Sie eine Tokengültigkeitsdauer-Richtlinie an.** 

Mit diesem Befehl wird eine strenge Richtlinie für eine Web-API erstellt. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"30.00:00:00`",`"MaxAgeMultiFactor`":`"until-revoked`",`"MaxAgeSingleFactor`":`"180.00:00:00`"}}") -DisplayName WebApiDefaultPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy

Um Ihre neue Richtlinie und deren ObjectID abzurufen, führen Sie den folgenden Befehl aus.

    Get-AzureADPolicy

&nbsp;&nbsp;2.    **Weisen Sie die Richtlinie Ihrer Web-API zu**.

Wir werden diese neue Richtlinie mit einer Anwendung verknüpfen.  Sie benötigen ferner eine Möglichkeit, auf die **ObjectId** Ihrer Anwendung zuzugreifen. Die **ObjectId** Ihrer App suchen Sie idealerweise über das [Azure-Portal](https://portal.azure.com/). 

Nachdem Sie die **ObjectId** abgerufen haben, führen Sie den folgenden Befehl aus.

    Add-AzureADApplicationPolicy -ObjectId <ObjectID of the App> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.    **Fertig!** 

### <a name="sample-managing-an-advanced-policy"></a>Beispiel: Verwalten einer erweiterten Richtlinie
In diesem Beispiel erstellen wir einige Richtlinien, um zu zeigen, wie das Prioritätssystem funktioniert und wie Sie mehrere Richtlinien verwalten können, die auf mehrere Objekte angewandt werden. Dadurch erhalten Sie Einblick in die Priorität der oben erläuterten Richtlinien und können auch komplexere Szenarios verwalten. 

1. **Legen Sie eine Tokengültigkeitsdauer-Richtlinie an.**

Bisher war es noch ziemlich einfach. Wir haben eine Standardrichtlinie für den Mandanten erstellt, die die Gültigkeitsdauer des Single-Factor-Aktualisierungstokens auf 30 Tage festsetzt. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"30.00:00:00`"}}") -DisplayName ComplexPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy
Um Ihre neue Richtlinie anzuzeigen und deren ObjectID abzurufen, führen Sie den folgenden Befehl aus.

    Get-AzureADPolicy

&nbsp;&nbsp;2.    **Weisen Sie die Richtlinie einem Dienstprinzipal zu.**

Nun verfügen wir über eine Richtlinie für den gesamten Mandanten.  Nehmen wir an, dass diese 30-Tage-Richtlinie für einen bestimmten Dienstprinzipal beibehalten werden soll, die Mandantenstandardrichtlinie jedoch so geändert werden soll, dass sie als Obergrenze für „Bis zum Widerruf“ fungiert. 

Wir werden diese neue Richtlinie zunächst mit unserem Dienstprinzipal verknüpfen.  Sie benötigen auch eine Möglichkeit, auf die **ObjectId** Ihres Dienstprinzipals zuzugreifen. Sie können [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) abfragen oder zum [Graph-Tester](https://graphexplorer.cloudapp.net/) wechseln und sich bei Ihrem Azure AD-Konto anmelden, um alle Dienstprinzipale Ihres Mandanten zu sehen. 

Nachdem Sie die **ObjectId** abgerufen haben, führen Sie den folgenden Befehl aus.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.    **Setzen Sie das IsTenantDefault-Flag mithilfe des folgenden Befehls auf „false“**. 

    Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName ComplexPolicyScenario -IsTenantDefault $false
&nbsp;&nbsp;4.    **Erstellen Sie eine neue Mandantenstandardrichtlinie.**

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName ComplexPolicyScenarioTwo -IsTenantDefault $true -Type TokenLifetimePolicy

&nbsp;&nbsp;5.     **Fertig!** 

Sie haben jetzt die ursprüngliche Richtlinie mit Ihrem Dienstprinzipal verknüpft und die neue Richtlinie als Mandantenstandardrichtlinie festgelegt.  Beachten Sie, dass Richtlinien für Dienstprinzipale Priorität vor Mandantenstandardrichtlinien haben. 

## <a name="cmdlet-reference"></a>Cmdlet-Referenz
### <a name="manage-policies"></a>Verwalten von Richtlinien
Mit den folgenden Cmdlets können Richtlinien verwaltet werden.</br></br>

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy
Erstellt eine neue Richtlinie.

    New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsTenantDefault <boolean> -Type <Policy Type> 

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -Definition |Das JSON-Array, dargestellt als Zeichenfolge, das alle Regeln der Richtlinie enthält. |-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") |
| -DisplayName |Zeichenfolge mit dem Namen der Richtlinie |-DisplayName MyTokenPolicy |
| -IsTenantDefault |Bei TRUE wird die Richtlinie als Standardrichtlinie des Mandanten festgelegt, bei FALSE hat der Wert keine Auswirkung. |-IsTenantDefault $true |
| -Type |Die Art der Richtlinie; verwenden Sie für die Tokengültigkeitsdauer stets „TokenLifetimePolicy“. |-Type TokenLifetimePolicy |
| -AlternativeIdentifier [optional] |Legt eine alternative ID für die Richtlinie fest. |-AlternativeIdentifier myAltId |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Ruft alle Azure AD-Richtlinien oder die angegebene Richtlinie ab. 

    Get-AzureADPolicy 

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -ObjectId [optional] |Die Objekt-ID der Richtlinie, die Sie abrufen möchten |-ObjectId &lt;Objekt-ID der Richtlinie&gt; |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Ruft alle Apps und Dienstprinzipale ab, die mit einer Richtlinie verknüpft sind.

    Get-AzureADPolicyAppliedObject -ObjectId <object id of policy> 

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -ObjectId |Die Objekt-ID der Richtlinie, die Sie abrufen möchten |-ObjectId &lt;Objekt-ID der Richtlinie&gt; |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Aktualisiert eine vorhandene Richtlinie.

    Set-AzureADPolicy -ObjectId <object id of policy> -DisplayName <string> 

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -ObjectId |Die Objekt-ID der Richtlinie, die Sie abrufen möchten |-ObjectId &lt;Objekt-ID der Richtlinie&gt; |
| -DisplayName |Zeichenfolge mit dem Namen der Richtlinie |-DisplayName MyTokenPolicy |
| -Definition [optional] |Das JSON-Array, dargestellt als Zeichenfolge, das alle Regeln der Richtlinie enthält. |-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") |
| -IsTenantDefault [optional] |Bei TRUE wird die Richtlinie als Standardrichtlinie des Mandanten festgelegt, bei FALSE hat der Wert keine Auswirkung. |-IsTenantDefault $true |
| -Type [optional] |Die Art der Richtlinie; verwenden Sie für die Tokengültigkeitsdauer stets „TokenLifetimePolicy“. |-Type TokenLifetimePolicy |
| -AlternativeIdentifier [optional] |Legt eine alternative ID für die Richtlinie fest. |-AlternativeIdentifier myAltId |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Löscht die angegebene Richtlinie

     Remove-AzureADPolicy -ObjectId <object id of policy>

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -ObjectId |Die Objekt-ID der Richtlinie, die Sie abrufen möchten |-ObjectId &lt;Objekt-ID der Richtlinie&gt; |

</br></br>

### <a name="application-policies"></a>Anwendungsrichtlinien
Die folgenden Cmdlets können für Anwendungsrichtlinien verwendet werden.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Verknüpft die angegebene Richtlinie mit einer Anwendung

    Add-AzureADApplicationPolicy -ObjectId <object id of application> -RefObjectId <object id of policy>

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -ObjectId |Die Objekt-ID der Anwendung |-ObjectId &lt;Objekt-ID der Anwendung&gt; |
| -RefObjectId |Die Objekt-ID der Richtlinie |-RefObjectId &lt;Objekt-ID der Richtlinie&gt; |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Ruft die Richtlinie ab, die einer Anwendung zugewiesen ist

    Get-AzureADApplicationPolicy -ObjectId <object id of application>

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -ObjectId |Die Objekt-ID der Anwendung |-ObjectId &lt;Objekt-ID der Anwendung&gt; |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Entfernt eine Richtlinie aus einer Anwendung

    Remove-AzureADApplicationPolicy -ObjectId <object id of application> -PolicyId <object id of policy>

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -ObjectId |Die Objekt-ID der Anwendung |-ObjectId &lt;Objekt-ID der Anwendung&gt; |
| -PolicyId |Die Objekt-ID der Richtlinie |-PolicyId &lt;ObjectID der Richtlinie&gt; |

</br></br>

### <a name="service-principal-policies"></a>Dienstprinzipalrichtlinien
Die folgenden Cmdlets können für Dienstprinzipalrichtlinien verwendet werden.</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Verknüpft die angegebene Richtlinie mit einem Dienstprinzipal

    Add-AzureADServicePrincipalPolicy -ObjectId <object id of service principal> -RefObjectId <object id of policy>

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -ObjectId |Die Objekt-ID der Anwendung |-ObjectId &lt;Objekt-ID der Anwendung&gt; |
| -RefObjectId |Die Objekt-ID der Richtlinie |-RefObjectId &lt;Objekt-ID der Richtlinie&gt; |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Ruft alle Richtlinien ab, die mit dem angegebenen Dienstprinzipal verknüpft sind

    Get-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -ObjectId |Die Objekt-ID der Anwendung |-ObjectId &lt;Objekt-ID der Anwendung&gt; |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Entfernt die Richtlinie aus dem angegebenen Dienstprinzipal

    Remove-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>  -PolicyId <object id of policy>

| Parameter | Beschreibung | Beispiel |
| --- | --- | --- |
| -ObjectId |Die Objekt-ID der Anwendung |-ObjectId &lt;Objekt-ID der Anwendung&gt; |
| -PolicyId |Die Objekt-ID der Richtlinie |-PolicyId &lt;ObjectID der Richtlinie&gt; |




<!--HONumber=Nov16_HO3-->


