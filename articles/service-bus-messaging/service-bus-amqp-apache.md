---
title: Installieren von Apache Qpid Proton-C auf einem virtuellen Linux-Computer | Microsoft Docs
description: Sie erfahren, wie Sie einen virtuellen CentOS Linux-Computer mit Azure Virtual Machines erstellen und die Apache Qpid Proton-C-Bibliothek erstellen und installieren.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3158d69f-c409-494d-9a6c-32613c1a3c74
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 226703f9b4a4b295231ecd48d719638321ae55ef


---
# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>Installieren von Apache Qpid Proton-C auf einem virtuellen Azure-Linux-Computer
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

In diesem Abschnitt wird gezeigt, wie Sie einen virtuellen CentOS Linux-Computer mit Azure Virtual Machines erstellen und die Apache Qpid Proton-C-Bibliothek zusammen mit den Python- und PHP-Sprachbindungen herunterladen, erstellen und installieren. Nachdem Sie diese Schritte ausgeführt haben, können Sie die Python- und PHP-Beispiele ausführen, die in diesem Leitfaden enthalten sind.

Der erste Schritt wird im [klassischen Azure-Portal][klassisches Azure-Portal] ausgeführt. Der folgende Screenshot zeigt die Erstellung eines virtuellen CentOS-Computers mit dem Namen „scott-centos“:

![Proton auf einem virtuellen Azure-Linux-Computer][0]

Nach der Bereitstellung wird im Portal Folgendes angezeigt:

![Proton auf einem virtuellen Azure-Linux-Computer][1]

Um sich am Computer anzumelden, müssen Sie den Endpunktport für SSH kennen. Sie können diesen Wert aus dem [klassischen Azure-Portal][klassisches Azure-Portal] abrufen, indem Sie den neu erstellten virtuellen Computer auswählen und auf die Registerkarte **Endpunkte** klicken. Der folgende Screenshot zeigt, dass der öffentliche SSH-Port für diesen Computer 57146 lautet.

![Proton auf einem virtuellen Azure-Linux-Computer][2]

Sie können über SSH jetzt eine Verbindung mit dem Computer herstellen. In diesem Beispiel wird das PuTTY-Tool verwendet, wie im folgenden Screenshot zu sehen:

![Proton auf einem virtuellen Azure-Linux-Computer][3]

Für die Python- und PHP-Apps werden in diesem Beispiel die Proton-Clientbibliotheken von Apache verwendet. Diese Bibliotheken stehen unter [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html) zum Download zur Verfügung. In der Infodatei im Verteilungspaket werden die erforderlichen Schritte zum Installieren der Abhängigkeiten und zum Erstellen von Proton erläutert. Zusammenfassung der Schritte:

1. Bearbeiten Sie die yum-Konfigurationsdatei (/etc/yum.conf), und kommentieren Sie den Ausschluss für Updates von Kernelheadern (\# exclude=kernel\*) aus. Dies ist erforderlich, um den GCC-Compiler zu installieren.
2. Installieren Sie die Pakete mit den erforderlichen Komponenten:
   
   ```
   # required dependencies 
   yum install gcc cmake libuuid-devel
   
   # dependencies needed for ssl support
   yum install openssl-devel
   
   # dependencies needed for bindings
   yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
   
   # dependencies needed for python docs
   yum install epydoc
   ```
3. Laden Sie die Proton-Bibliothek herunter:
   
   ```
   [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
   --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
   Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
   Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
   HTTP request sent, awaiting response... 200 OK
   Length: 868226 (848K) [application/x-gzip]
   Saving to: ‘qpid-proton-0.9.tar.gz’
   
   qpid-proton-0.9.tar.gz                               
   
   100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
   
   2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
   ```
4. Extrahieren Sie den Proton-Code aus dem Verteilungsarchiv:
   
   ```
   tar xvfz qpid-proton-0.9.tar.gz
   ```
5. Erstellen und installieren Sie den Code mit den folgenden Schritten aus der Infodatei:
   
   ```
   From the directory where you found this README file:    
   
   mkdir build cd build
   
   # Set the install prefix. You may need to adjust depending on your        
   # system.        
   cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
   
   # Omit the docs target if you do not wish to build or install        
   # documentation.        
   make all docs
   
   # Note that this step will require root privileges.        
   make install
   ```

Nach Durchführung dieser Schritte ist Proton auf dem Computer installiert und einsatzbereit.

## <a name="next-steps"></a>Nächste Schritte
Möchten Sie mehr erfahren? Nutzen Sie die den folgenden Link:

* [Übersicht über Service Bus AMQP][Übersicht über Service Bus AMQP]

[Übersicht über Service Bus AMQP]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[klassisches Azure-Portal]: http://manage.windowsazure.com





<!--HONumber=Nov16_HO3-->


