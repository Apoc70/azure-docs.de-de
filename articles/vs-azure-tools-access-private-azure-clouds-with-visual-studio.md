---
title: Zugreifen auf private Azure-Clouds mit Visual Studio | Microsoft Docs
description: Erfahren Sie, wie Sie mithilfe von Visual Studio auf private Cloudressourcen zugreifen.
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: ''

ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 08/15/2016
ms.author: tarcher

---
# Zugreifen auf private Azure-Clouds mit Visual Studio
## Übersicht
Visual Studio unterstützt standardmäßig REST-Endpunkte der öffentlichen Azure-Cloud. Dies kann jedoch ein Problem sein, wenn Sie Visual Studio mit einer privaten Azure-Cloud verwenden. Sie können Zertifikate verwenden, um Visual Studio für den Zugriff auf REST-Endpunkte der privaten Azure-Cloud zu konfigurieren. Sie erhalten diese Zertifikate über Ihre Datei mit den Azure-Veröffentlichungseinstellungen.

## So greifen Sie auf eine private Azure-Cloud in Visual Studio zu
1. Laden Sie im [klassischen Azure-Portal](http://go.microsoft.com/fwlink/?LinkID=213885) für die private Cloud die Datei mit den Veröffentlichungseinstellungen herunter, oder wenden Sie sich an Ihren Administrator, um eine Datei mit Veröffentlichungseinstellungen zu erhalten. Für die öffentliche Version von Azure ist dies der Link zum Herunterladen: [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (Die herunterzuladende Datei muss die Erweiterung ".publishsettings" aufweisen.)
2. Wählen Sie in Visual Studio im **Server-Explorer** den Knoten **Azure** und dann im Kontextmenü den Befehl **Abonnements verwalten** aus.
   
    ![Befehl „Abonnements verwalten“](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)
3. Klicken Sie im Dialogfeld **Microsoft Azure-Abonnements verwalten** auf die Registerkarte **Zertifikate** und dann auf die Schaltfläche **Importieren**.
   
    ![Importieren von Azure-Zertifikaten](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)
4. Navigieren Sie im Dialogfeld **Microsoft Azure-Abonnements importieren** zum Ordner, in dem Sie die Datei mit den Veröffentlichungseinstellungen gespeichert haben, und klicken Sie dann auf die Schaltfläche **Importieren**. Daraufhin werden die Zertifikate in der Datei mit den Veröffentlichungseinstellungen in Visual Studio importiert. Sie sollten jetzt in der Lage sein, mit privaten Cloudressourcen zu interagieren.
   
    ![Importieren von Veröffentlichungseinstellungen](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## Nächste Schritte
[Veröffentlichen eines Clouddiensts mit Azure Tools](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[How to: Download and Import Publish Settings and Subscription Information](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx) (Gewusst wie: Herunterladen und Importieren von Veröffentlichungseinstellungen und Abonnementinformationen)

<!---HONumber=AcomDC_0817_2016-->