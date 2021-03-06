---
title: Verbindung und Kommunikation mit Diensten in Azure Service Fabric | Microsoft Docs
description: Hier erfahren Sie, wie Sie Dienste in Service Fabric auflösen, eine Verbindung herstellen und die Kommunikation mit den Diensten einrichten.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: msfussell

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/05/2016
ms.author: vturecek

---
# Herstellung einer Verbindung mit Diensten in Service Fabric und die Kommunikation mit diesen Diensten
In Service Fabric wird ein Dienst an irgendeinem Ort in einem Service Fabric-Cluster ausgeführt, der sich in der Regel auf mehreren virtuellen Computern befindet. Er kann entweder vom Dienstbesitzer oder automatisch von Service Fabric von einem Standort an einen anderen verschoben werden. Dienste sind nicht statisch an einen bestimmten Computer oder eine bestimmte Adresse gefunden.

Eine Service Fabric-Anwendung besteht in der Regel aus vielen verschiedenen Diensten, die jeweils eine bestimmte Aufgabe ausführen. Diese Dienste können miteinander kommunizieren und so eine umfassende Funktion bilden, wie etwa das Rendern verschiedener Teile einer Webanwendung. Darüber hinaus verbinden sich Clientanwendungen zur Kommunikation mit den Diensten. Dieses Dokument erklärt, wie die Kommunikation mit und zwischen Ihren Diensten in Service Fabric eingerichtet wird.

## Nutzen Sie Ihr eigenes Protokoll
Service Fabric ist beim Verwalten des Lebenszyklus Ihrer Dienste behilflich, trifft aber keine Entscheidung in Bezug darauf, was Ihre Dienste tun. Dies umfasst auch die Kommunikation. Wenn Ihr Dienst von Service Fabric geöffnet wird, kann der Dienst einen Endpunkt für eingehende Anforderungen einrichten und dabei das von Ihnen bevorzugte Protokoll bzw. den gewünschten Kommunikationsstapel verwenden. Ihr Dienst lauscht an einer normalen **IP:port**-Adresse mithilfe eines beliebigen Adressierungsschemas wie z.B. URI. Mehrere Dienstinstanzen oder Replikate nutzen unter Umständen einen gemeinsamen Hostprozess. Dabei müssen entweder verschiedene Ports oder eine Portfreigabemethode wie etwa der http.sys-Kerneltreiber in Windows verwendet werden. In beiden Fällen muss jede Dienstinstanz bzw. jedes Replikat in einem Hostprozess eindeutig aufrufbar sein.

![Dienstendpunkte][1]

## Dienstermittlung und Auflösung
In einem verteilten System können Dienste im Laufe der Zeit auf einen anderen Computer übergehen. Dafür gibt es verschiedene Gründe, wie z.B. Ressourcenausgleich, Upgrades, Failover oder horizontale Skalierung. Daher ändern sich die Dienstendpunktadressen, wenn der Dienst auf Knoten mit anderen IP-Adressen verschoben wird, und werden auf anderen Ports geöffnet, wenn der Dienst einen dynamisch ausgewählten Port verwendet.

![Verteilung von Diensten][7]

Service Fabric stellt einen Ermittlungs- und Auflösungsdienst namens „Naming Service“ bereit. Naming Service verwaltet eine Tabelle, die benannte Dienstinstanzen den Endpunktadressen zuordnet, an denen diese lauschen. Alle benannten Dienstinstanzen in Service Fabric verfügen über eindeutige Namen als URIs, wie z.B. `"fabric:/MyApplication/MyService"`. Der Dienstname ändert sich während der Lebensdauer des Diensts nicht, lediglich die Endpunktadressen ändern sich, wenn ein Dienst verschoben wird. Dies ist mit Websites vergleichbar, die zwar konstante URLs verwenden, deren IP-Adresse sich jedoch ändern kann. Ähnlich wie das DNS (Domain Name System) im Web, das Website-URLs zu IP-Adressen auflöst, verfügt auch Service Fabric über eine Registrierungsstelle, die Servicenamen den entsprechenden Endpunktadressen zuordnet.

![Dienstendpunkte][2]

Beim Auflösen und Verbinden mit Diensten werden die folgenden Schritte in einer Schleife ausgeführt:

* **Auflösen**: Der Endpunkt, den ein Dienst von Naming Service veröffentlicht hat, wird abgerufen.
* **Verbinden**: Eine Verbindung mit dem Dienst wird über das auf diesem Endpunkt verwendete Protokoll hergestellt.
* **Wiederholen**: Ein Verbindungsversuch kann aus verschiedenen Gründen fehlschlagen, z.B. wenn der Dienst seit der letzten Auflösung der Endpunktadresse verschoben wurde. In diesem Fall müssen die obigen Schritte zum Auflösen und Verbinden solange wiederholt werden, bis die Verbindung hergestellt werden kann.

## Verbindungen von externen Clients
Dienste, die innerhalb eines Clusters miteinander verbunden sind, können im Allgemeinen direkt auf die Endpunkte anderer Dienste zugreifen, da sich die Knoten in einem Cluster in der Regel im gleichen Netzwerk befinden. In einigen Umgebungen kann sich ein Cluster jedoch hinter einem Load Balancer befinden, der den externen eingehenden Datenverkehr durch eine begrenzte Anzahl von Ports umleitet. In diesen Fällen können Dienste weiterhin miteinander kommunizieren und Adressen mithilfe von Naming Service auflösen, allerdings sind zusätzliche Schritte erforderlich, damit externe Clients eine Verbindung mit den Diensten herstellen können.

## Service Fabric in Azure
Ein Service Fabric-Cluster in Azure befindet sich hinter einem Azure Load Balancer. Der gesamte externe Datenverkehr zum Cluster muss den Load Balancer durchlaufen. Der Load Balancer leitet auf einem bestimmten Port eingehenden Datenverkehr automatisch an einen beliebigen *Knoten* weiter, der den gleichen Port geöffnet hat. Der Azure Load Balancer weiß nur, welche Ports auf den *Knoten* geöffnet sind, aber nicht, welche Ports von einzelnen *Diensten* geöffnet wurden.

![Azure Load Balancer und Service Fabric-Topologie][3]

Damit externer Datenverkehr auf Port **80** zulässig ist, müssen die folgenden Punkte konfiguriert werden:

1. Schreiben Sie einen Dienst, der an Port 80 lauscht. Konfigurieren Sie Port 80 in „ServiceManifest.xml“ für den Dienst, und öffnen Sie einen Listener im Dienst, z.B. einem selbst gehosteten Webserver.
   
    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...
   
            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint = 
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");
   
                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";
   
                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();
   
                string uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
   
                return Task.FromResult(this.publishUri);
            }
   
            ...
        }
   
        class WebService : StatelessService
        {
            ...
   
            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] {new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }
   
            ...
        }
    ```
2. Erstellen Sie einen Service Fabric-Cluster in Azure, und geben Sie Port **80** als benutzerdefinierten Endpunktport für den Knotentyp an, der den Dienst hostet. Wenn Sie über mehrere Knotentypen verfügen, können Sie für den Dienst eine *Platzierungseinschränkung* festlegen, um sicherzustellen, dass er nur auf dem Knotentyp ausgeführt wird, für den der benutzerdefinierte Endpunktport geöffnet ist.
   
    ![Öffnen Sie einen Port auf einem Knotentyp][4]
3. Wenn der Cluster erstellt wurde, konfigurieren Sie den Azure Load Balancer in der Ressourcengruppe des Cluster, um Datenverkehr an Port 80 weiterzuleiten. Wenn Sie einen Cluster über das Azure-Portal erstellen, wird dieser automatisch für jeden benutzerdefinierten Endpunktport eingerichtet, der konfiguriert wurde.
   
    ![Weiterleitung von Datenverkehr im Azure Load Balancer][5]
4. Der Azure Load Balancer stellt mithilfe eines Tests fest, ob Datenverkehr an einen bestimmten Knoten gesendet wird. Dieser Test überprüft in regelmäßigen Abständen einen Endpunkt auf jedem Knoten, um festzustellen, ob der Knoten reagiert. Wenn nach einer bestimmten Anzahl von Versuchen keine Antwort eingeht, sendet der Load Balancer keinen Datenverkehr mehr an diesen Knoten. Wenn Sie einen Cluster über das Azure-Portal erstellen, wird automatisch ein Test für jeden konfigurierten benutzerdefinierten Endpunktport eingerichtet.
   
    ![Weiterleitung von Datenverkehr im Azure Load Balancer][8]

Bitte beachten Sie, dass der Azure Load Balancer und der Test nur die *Knoten* kennen, nicht aber die *Dienste*, die auf den Knoten ausgeführt werden. Der Azure Load Balancer sendet Datenverkehr an die Knoten, die auf den Test reagieren. Stellen Sie daher sicher, dass die Dienste auf den Knoten verfügbar sind, die auf den Test reagieren können.

## Integrierte Optionen für Kommunikations-APIs
Das Reliable Services-Framework wird mit mehreren vorab erstellten Kommunikationsoptionen ausgeliefert. Welche Option in Ihrem Fall am besten geeignet ist, hängt vom Programmiermodell, dem Kommunikationsframework und der Programmiersprache Ihrer Dienste ab.

* **Kein bestimmtes Protokoll**: Wenn Sie kein bestimmtes Kommunikationsframework verwenden und schnell eine Lösung implementieren möchten, ist das [Dienstremoting](service-fabric-reliable-services-communication-remoting.md) die ideale Lösung, da es stark typisierte Remoteprozeduraufrufe für Reliable Services und Reliable Actors zulässt. Dies ist die einfachste und schnellste Methode für den Einstieg in die Dienstkommunikation. Das Dienstremoting verarbeitet die Auflösung von Dienstadressen, die Verbindungsherstellung, Wiederholungsversuche sowie die Problembehandlung. Beachten Sie, dass das Dienstremoting nur für C#-Anwendungen verfügbar ist.
* **HTTP**: Für die sprachunabhängige Kommunikation bietet HTTP eine branchenübliche Auswahl von Tools und HTTP-Servern, die in verschiedenen Sprachen verfügbar sind und alle von Service Fabric unterstützt werden. Dienste können alle verfügbaren HTTP-Stapel verwenden, einschließlich [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md). In C# geschriebene Clients können [die Klassen `ICommunicationClient` und `ServicePartitionClient`](service-fabric-reliable-services-communication.md) für die Dienstauflösung, HTTP-Verbindungen und Wiederholungsschleifen nutzen.
* **WCF**: Wenn der vorhandene Code das WCF-Kommunikationsframework verwendet, können Sie `WcfCommunicationListener` für den Server und die Klassen `WcfCommunicationClient` und `ServicePartitionClient` für den Client verwenden. Weitere Informationen finden Sie in folgenden Artikel: [WCF-basierter Kommunikationsstapel für Reliable Services](service-fabric-reliable-services-communication-wcf.md).

## Verwenden benutzerdefinierter Protokolle und anderer Kommunikationsframeworks
Dienste können beliebige Protokolle oder Frameworks für die Kommunikation nutzen, unabhängig davon, ob es sich um ein benutzerdefiniertes binäres Protokoll über TCP-Sockets oder Streamingereignisse über [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) oder [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) handelt. Service Fabric bietet Kommunikations-APIs, an die Sie Ihren Kommunikationsstapel anschließen können, wobei Sie sich nicht um das Ermitteln und Verbinden kümmern müssen. Weitere Informationen finden Sie im folgenden Artikel: [Das Reliable Service-Kommunikationsmodell](service-fabric-reliable-services-communication.md).

## Nächste Schritte
Einen Überblick über die Konzepte und APIs, die im [Reliable Services-Kommunkationsmodell](service-fabric-reliable-services-communication.md) zur Verfügung stehen, finden Sie im Bereich [Dienstremoting](service-fabric-reliable-services-communication-remoting.md). Sie können aber auch ins Detail gehen und lernen, einen Kommunikationslistener mithilfe der [Web-API-Dienste mit selbstgehostetem OWIN](service-fabric-reliable-services-communication-webapi.md) selbst zu erstellen

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png

<!---HONumber=AcomDC_0713_2016-->