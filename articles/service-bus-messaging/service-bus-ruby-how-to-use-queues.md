---
title: Verwenden von Service Bus-Warteschlangen mit Ruby | Microsoft Docs
description: Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure. Die Codebeispiele wurden in Ruby geschrieben.
services: service-bus
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 10/04/2016
ms.author: sethm

---
# <a name="how-to-use-service-bus-queues"></a>Verwenden von Service Bus-Warteschlangen
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In diesem Leitfaden wird beschrieben, wie Sie Service Bus-Warteschlangen verwenden. Die Beispiele sind in Ruby geschrieben und verwenden das Azure-Gem. Die Szenarios behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von Nachrichten** und **Löschen von Warteschlangen**. Weitere Informationen zu Service Bus-Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

## <a name="what-are-service-bus-queues?"></a>Was sind Service Bus-Warteschlangen?
Service Bus-Warteschlangen unterstützen ein Kommunikationsmodell namens *Brokermessaging*. Bei der Verwendung von Warteschlangen kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über eine Warteschlange aus, die als Zwischenstufe fungiert. Ein Nachrichtenproducer (Absender) übergibt eine Nachricht an die Warteschlange und setzt seine Funktion fort.
Ein Nachrichtenconsumer (Empfänger) ruft die Nachricht asynchron aus der Warteschlange ab und verarbeitet sie. Der Producer muss nicht auf eine Antwort vom Consumer warten, um seine Funktion fortzusetzen und weitere Nachrichten zu schicken. Warteschlangen liefern die Nachrichten im **First In, First Out (FIFO)** -Verfahren an einen oder mehrere Consumer. Die Nachrichten werden also normalerweise in der gleichen Reihenfolge von den Consumern empfangen und verarbeitet, wie sie in die Warteschlange übergeben wurden, und jede Nachricht wird nur von einem Consumer verarbeitet.

![Konzepte für Warteschlangen](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Service Bus-Warteschlangen sind eine Allzwecktechnologie für viele unterschiedliche Szenarien:

* Kommunikation zwischen Web- und Workerrollen in [Azure-Anwendungen mit mehreren Ebenen](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
* Kommunikation zwischen lokalen Apps und von Azure gehosteten Apps in einer [Hybridlösung](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md)
* Kommunikation zwischen Komponenten einer verteilten lokalen Anwendung, die in verschiedenen Organisationen oder Abteilungen einer Organisation laufen

Warteschlangen helfen bei der Skalierung Ihrer Anwendungen und führen zu einer robusteren Architektur.

## <a name="create-a-namespace"></a>Erstellen eines Namespace
Um mit der Verwendung von Service Bus-Warteschlangen in Azure beginnen zu können, müssen Sie zuerst einen Namespace erstellen. Ein Namespace ist ein Bereichscontainer für die Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung. Da das Azure-Portal den Namespace nicht mit einer ACS-Verbindung erstellt, müssen Sie den Namespace über die Befehlszeilenschnittstelle erstellen.

So erstellen Sie einen Namespace

1. Öffnen Sie eine Azure PowerShell-Konsole.
2. Geben Sie den folgenden Befehl ein, um einen Service Bus-Namespace zu erstellen. Geben Sie einen eigenen Namespacewert und dieselbe Region wie für Ihre Anwendung an.
   
    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
   
    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
    ```

## <a name="obtain-management-credentials-for-the-namespace"></a>Abrufen von Anmeldeinformationen zur Verwaltung des Namespace
Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung einer Warteschlange im neuen Namespace, müssen Sie die Anmeldeinformationen für den Namespace abrufen.

Das PowerShell-Cmdlet, das Sie zum Erstellen des Azure Service Bus-Namespaces ausgeführt haben, gibt den Schlüssel an, mit dem Sie den Namespace verwalten können. Kopieren Sie den Wert **DefaultKey**. Sie verwenden diesen Wert später in diesem Tutorial in Ihrem Code.

![Kopieren des Schlüssels](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [!NOTE]
> Sie können diesen Wert auch ermitteln, indem Sie sich beim [Azure-Portal](https://portal.azure.com/) anmelden und zu den Verbindungsinformationen für Ihren Service Bus-Namespace navigieren.
> 
> 

## <a name="create-a-ruby-application"></a>Erstellen einer Ruby-Anwendung
Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie unter [Ruby on Rails-Webanwendung auf Azure VM](/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
Um Azure Service Bus zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### <a name="use-rubygems-to-obtain-the-package"></a>Verwenden von RubyGems zum Abrufen des Pakets
1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).
2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### <a name="import-the-package"></a>Importieren des Pakets
Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>Einrichten einer Azure Service Bus-Verbindung
Das Azure-Modul liest die Umgebungsvariablen **AZURE\_SERVICEBUS\_NAMESPACE** und **AZURE\_SERVICEBUS\_ACCESS_KEY** nach Informationen aus, die für eine Verbindung zu Ihrem Service Bus-Namespace benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::ServiceBusService** mit dem folgenden Code angeben:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Legen Sie den Wert für den Namespace auf den von Ihnen erstellten Wert statt auf die gesamte URL fest. Verwenden Sie beispielsweise **Beispielnamespace**, anstelle von „Beispielnamespace.servicebus.windows.net“.

## <a name="how-to-create-a-queue"></a>Erstellen von Warteschlangen
Das **Azure::ServiceBusService**-Objekt ermöglicht Ihnen, mit Warteschlangen zu arbeiten. Verwenden Sie die Methode **create_queue()**, um eine Warteschlange zu erstellen. Im folgenden Beispiel werden eine Warteschlange erstellt oder gegebenenfalls Fehler ausgegeben.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Sie können außerdem ein **Azure::ServiceBus::Queue**-Objekt mit weiteren Optionen übergeben, mit denen Sie die Standardeinstellungen wie z.B. Nachrichtenlebensdauer oder maximale Warteschlangengröße überschreiben können. Das folgende Beispiel zeigt, wie Sie die maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute festlegen:

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die Methode **send\_queue\_message()** für das **Azure::ServiceBusService**-Objekt auf. Nachrichten, die an die Service Bus-Warteschlangen gesendet werden (und von diesen empfangen werden), sind **Azure::ServiceBus::BrokeredMessage**-Objekte und verfügen über einen Satz von Standardeigenschaften (z.B. **label** und **time\_to\_live**), ein Wörterbuch, in dem benutzerdefinierte anwendungsspezifische Eigenschaften enthalten sind, sowie einen Bestand beliebiger Anwendungsdaten. Eine Anwendung kann den Hauptteil einer Nachricht festlegen, indem sie einen Zeichenkettenwert als Nachricht weitergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie eine Testnachricht an die Warteschlange „test-queue“ mithilfe von **send\_queue\_message()** gesendet wird:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB für den [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB für den [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange
Nachrichten aus einer Warteschlange werden mithilfe der Methode **receive\_queue\_message()** über das **Azure::ServiceBusService**-Objekt empfangen. Standardmäßig werden Nachrichten gelesen und gesperrt, ohne aus der Warteschlange gelöscht zu werden. Sie können die Nachricht jedoch beim Lesen aus der Warteschlange löschen, indem Sie die Option **:peek_lock** auf **FALSE** setzen.

Im Standardverhalten sind Empfang und Löschung von Nachrichten zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf der Methode **delete\_queue\_message()** und Bereitstellen der Nachricht durch, die als Parameter gelöscht wird. Die Methode **delete\_queue\_message()** markiert die Nachricht als verarbeitet, und entfernt sie aus der Warteschlange.

Wenn der **:peek\_lock**-Parameter auf **FALSE** festgelegt ist, wird zum Lesen und Löschen der Nachricht das einfachste Modell verwendet. Dieses sollte für Anwendungen eingesetzt werden, die damit umgehen können, wenn Nachrichten bei Auftreten eines Fehlers nicht verarbeitet werden. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Im folgenden Beispiel wird veranschaulicht, wie Nachrichten mithilfe von **receive\_queue\_message()** empfangen und verarbeitet werden. In diesem Beispiel wird zuerst eine Nachricht mit **:peek\_lock** gleich **FALSE** empfangen und gelöscht, und anschließend eine Nachricht empfangen und mit **delete\_queue\_message()** gelöscht:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten
Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die Methode **unlock\_queue\_message()** für das **Azure::ServiceBusService**-Objekt aufrufen. Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Mit einer innerhalb der Warteschlange gesperrten Nachricht ist außerdem eine Zeitlimitüberschreitung verknüpft. Wenn die Anwendung die Nachricht nicht verarbeiten kann, bevor die Zeitlimitüberschreitung der Sperre abläuft (z. B. falls die Anwendung abstürzt), so entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, sodass sie wieder empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der Methode **delete\_queue\_message()** abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing**(Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der Eigenschaft **message\_id** der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen der Servicebus-Warteschlangen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

* Übersicht über [Warteschlangen, Themen und Abonnements](service-bus-queues-topics-subscriptions.md).
* Besuchen Sie das [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby)-Repository auf GitHub.

Einen Vergleich zwischen den in diesem Artikel besprochenen Azure Service Bus-Warteschlangen und den unter [Verwenden des Warteschlangenspeichers mit Ruby](../storage/storage-ruby-how-to-use-queue-storage.md) erörterten Azure-Warteschlangen finden Sie unter [Azure-Warteschlangen und Azure Service Bus-Warteschlangen – Vergleich und Gegenüberstellung](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

<!--HONumber=Oct16_HO2-->


