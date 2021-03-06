---
title: Verwendung der BizTalk-Transformation in Logik-Apps in Azure App Service | Microsoft Docs
description: Erfahren Sie, wie XML-Dokumente von einem Schema in ein anderes transformiert werden.
author: anuragdalmia
manager: erikre
editor: ''
services: logic-apps
documentationcenter: ''

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2016
ms.author: anuragdalmia

---
# BizTalk-Transformation
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

## Übersicht
Die BizTalk-Transformations-API-App konvertiert Daten von einem Format in ein anderes Format. Sie können z. B. Versand- und Rechnungsanschrift einer Bestellung entnehmen und in ein Rechnungsdokument einfügen. Oder Sie haben eine eingehende Nachricht, die das aktuelle Datum im *YearMonthDay*-Format enthält. Sie möchten das Datum im *MonthDayYear*-Format neu formatieren.

Dazu können Sie die Transformations-API-App in Microsoft Azure App Service verwenden. Eine Transformation, auch als Zuordnung bezeichnet, besteht aus einem XML-Quellschema (der Eingabe) und einem XML-Zielschema (der Ausgabe). Sie können verschiedene integrierte Funktionen verwenden, um Daten zu ändern oder zu steuern, einschließlich Zeichenfolgenbearbeitungen, bedingter Zuordnungen, arithmetischer Ausdrücke, Datum-Uhrzeit-Formatierungen und sogar Schleifenkonstrukten.

Zuordnungen werden in Visual Studio mithilfe des [Microsoft Azure BizTalk Services SDK](http://www.microsoft.com/download/details.aspx?id=39087) erstellt. Wenn Sie mit dem Erstellen und Testen der Zuordnung fertig sind, laden Sie die Zuordnung (.trfm) in die BizTalk-Transformations-API-App hoch.

Zusätzliche Features:

* Die in einer Zuordnung erstellte Transformation kann einfach sein, beispielsweise das Kopieren eines Namen und einer Adresse aus einem Dokument in ein anderes. Oder Sie können mithilfe der standardmäßigen Zuordnungsvorgänge komplexere Transformationen erstellen.
* Mehrere Zuordnungsvorgänge oder Funktionen sind verfügbar, einschließlich Zeichenfolgen, Datum-Uhrzeit-Funktionen usw.
* Direkte Kopieren von Daten zwischen den Schemas. Im BizTalk-Mapper ist dies so einfach wie das Zeichnen einer Linie, die die Elemente im Quellschema mit ihren Gegenstücken im Zielschema verbindet.
* Wenn Sie eine Zuordnung erstellen, zeigen Sie eine grafische Darstellung der Zuordnung einschließlich aller erstellten Beziehungen und Links an.
* Verwenden Sie das Feature **Testzuordnung** zum Hinzufügen einer XML-Beispielnachricht. Mit einem einfachen Mausklick können Sie die erstellte Zuordnung testen und die generierte Ausgabe anzeigen.
* Laden Sie vorhandene Azure BizTalk Services-Zuordnungen (.trfm) hoch, und nutzen Sie alle Vorteile der Transformations-API-App.
* Wenn Sie die Zuordnung erstellen, müssen Sie kein Schema hinzuzufügen. Wenn die Zuordnung bereit ist, fügen Sie sie zur Transformations-API-App hinzu, und schon kann es losgehen.
* Unterstützung für das XML-Format.

## Herunterladen von Schemas aus Connector-API-Apps
Sie können die XML-Schemas für Connectors, z. B. SQL, SAP und SharePoint, von der API-App-Seite "Zusammenfassung" herunterladen. Wenn Sie z. B. XML-Schemas für eine bestimmte SAP-Connector-API-App herunterladen möchten, wechseln Sie zur API-App, und öffnen Sie die Seite "Zusammenfassung". Klicken Sie auf **Schemas herunterladen**. Eine ZIP-Datei mit den Schemas, die den SAP-Aktionen entsprechend, wird auf Ihren Computer heruntergeladen. Anhand der Schemas können Sie in Visual Studio eine Zuordnung (.trfm) erstellen.

## Erstellen und Hinzufügen der Zuordnung
Transformationen und Zuordnungen werden in Visual Studio mithilfe des [Microsoft Azure BizTalk Services SDK](http://www.microsoft.com/download/details.aspx?id=39087) erstellt, das kostenlos heruntergeladen werden kann.

Hilfe zum Erstellen einer Zuordnung finden Sie in [Erstellen einer Zuordnung in Visual Studio](http://aka.ms/createamapinvs). Nachdem die Zuordnung erstellt wurde und für die Produktion bereit ist, können Sie die Zuordnung (TRFM-Datei) der BizTalk-Transformations-API-App hinzufügen, die Sie im Azure-Portal erstellt haben.

Wenn sich die Zuordnung ändert oder nach dem Hochladen geändert wird, können Sie die aktualisierte Zuordnung hochladen, die die vorhandene Zuordnung in der Transformations-API-App ersetzt.

1. Klicken Sie im Azure-Portal (links auf dem Bildschirm) auf **Durchsuchen**, und wählen Sie **API-Apps** aus. Wenn **API-Apps** nicht angezeigt wird, wählen Sie **Alles** und anschließend in der Liste "Verfügbar" **API-Apps** aus:
   
   ![][7]
2. Die Liste aller **API-Apps**, die in Ihren Azure-Abonnement erstellt wurden, wird angezeigt:
   
   ![][8]
3. Wählen Sie die BizTalk-Transformations-API-App aus, die Sie im vorherigen Abschnitt erstellt haben.
4. Das Blatt "Konfiguration" für die API-App wird geöffnet. Im Abschnitt "Komponenten" werden **Zuordnungen** angezeigt:
   
   ![][9]
5. Wählen Sie **Zuordnungen** aus, um das neue Blatt mit der Liste der Zuordnungen zu öffnen.
6. Klicken Sie oben auf das Symbol **Zuordnung hinzufügen**, um das Blatt **Zuordnung hinzufügen** zu öffnen:
   
   ![][10]
7. Klicken Sie auf das Dateisymbol, und suchen Sie auf dem lokalen Computer eine Zuordnungsdatei (.trfm).
8. Klicken Sie auf **OK**, woraufhin eine neue Zuordnung erstellt wird. Sie wird in der Liste der Zuordnungen angezeigt.

## Verwenden einer BizTalk-Transformations-API-App in einer Logik-App
Nachdem die Zuordnung erstellt und getestet wurde, steht sie jetzt zur Nutzung bereit.

1. Innerhalb der Logik-App ist "BizTalk-Transformation" im Katalog rechts verfügbar. Wählen Sie im Katalog **BizTalk Transform Service** aus. Die Transformation wird dem Datenfluss hinzugefügt:
   
    ![][11]
2. Wählen Sie die Aktion **Transformation** aus. Die Eingabeparameter werden angezeigt:
   
    ![][12]
3. Geben Sie die folgenden Parameter zum Abschließen der Konfiguration der Aktion **Transformation** ein:
   
   * Eingabe-XML
     
     * Geben Sie den gültigen XML-Inhalt ein, der dem Quellschema einer Zuordnung in der Transformations-API-App entspricht. Dies kann eine Ausgabe einer vorherigen Aktion in der Logik-App sein z. B. "RFC aufrufen – SAP" oder "In Tabelle einfügen – SQL".
   * Zuordnungsname (optional)
     
     * Geben Sie einen gültigen Namen einer Zuordnung ein, die bereits in die Transformations-API-App hochgeladen wurde. Wenn keine Zuordnung eingegeben wird, wird die Zuordnung automatisch basierend auf dem Quellschema ausgewählt, dem die XML-Eingabedatei entspricht.
     
     ![][13]
4. Die Ausgabe der Aktion "Ausgabe-XML" kann in nachfolgenden Aktionen in Ihren Logik-Apps verwendet werden.

<!--Image references-->
[1]: ./media/app-service-logic-transform-xml-documents/Create_Everything.png
[2]: ./media/app-service-logic-transform-xml-documents/Create_Marketplace.png
[4]: ./media/app-service-logic-transform-xml-documents/Search_TransformAPIApp.png
[5]: ./media/app-service-logic-transform-xml-documents/Transform_APIApp_Landing_Page.png
[6]: ./media/app-service-logic-transform-xml-documents/New_TransformAPIApp_Blade.png
[7]: ./media/app-service-logic-transform-xml-documents/Browse_APIApps.png
[8]: ./media/app-service-logic-transform-xml-documents/Select_APIApp_List.png
[9]: ./media/app-service-logic-transform-xml-documents/Configure_Transform_APIApp.png
[10]: ./media/app-service-logic-transform-xml-documents/Add_Map.png
[11]: ./media/app-service-logic-transform-xml-documents/Transform_action_flow.png
[12]: ./media/app-service-logic-transform-xml-documents/Transform_Inputs.png
[13]: ./media/app-service-logic-transform-xml-documents/Transform_configured.png
[14]: ./media/app-service-logic-transform-xml-documents/Download_Schemas.png





<!---HONumber=AcomDC_0803_2016-->