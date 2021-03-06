

Für Azure-Infrastrukturdienste kann Lastenausgleich auf zwei Ebenen genutzt werden:

* **DNS-Ebene**: Lastenausgleich für Datenverkehr an unterschiedliche Clouddienste in unterschiedlichen Rechenzentren, an unterschiedliche Azure-Websites in unterschiedlichen Rechenzentren oder an externe Endpunkte. Für diese Form des Lastenausgleichs werden Azure Traffic Manager und die Roundrobin-Methode verwendet.
* **Netzwerkebene**: Lastenausgleich für eingehenden Internetdatenverkehr an unterschiedliche virtuelle Computer eines Clouddiensts oder Lastenausgleich für den Datenverkehr zwischen virtuellen Computern in einem Clouddienst oder virtuellen Netzwerk. Für diese Aufgaben wird das Azure-Lastenausgleichsmodul verwendet.

## <a name="traffic-manager-load-balancing-for-cloud-services-and-websites"></a>Traffic Manager-Lastenausgleich für Clouddienste und Websites
Mit Traffic Manager können Sie die Verteilung des Benutzerdatenverkehrs an Endpunkte steuern, z. B. an Clouddienste, Websites externe Websites und andere Traffic Manager-Profile. Die Funktionsweise von Traffic Manager basiert darauf, dass Sie ein intelligentes Richtlinienmodul auf DNS-Abfragen (Domain Name System) von Domänennamen Ihrer Internetressourcen anwenden. Die Clouddienste oder Websites können in verschiedenen Rechenzentren auf der ganzen Welt ausgeführt werden.

Sie müssen entweder REST oder Windows PowerShell zur Konfiguration externer Endpunkte oder Traffic Manager-Profile als Endpunkte verwenden.

Traffic Manager verwendet drei Lastenausgleichsmethoden, um den Datenverkehr zu verteilen:

* **Failover**: Verwenden Sie diese Methode, wenn Sie einen primären Endpunkt für den gesamten Datenverkehr verwenden, aber Sicherungskopien bereitstellen möchten, falls der primäre Endpunkt nicht mehr verfügbar sein sollte.
* **Leistung**: Verwenden Sie diese Methode, wenn sich die Endpunkte an unterschiedlichen geografischen Standorten befinden und anfordernde Clients den nächstgelegenen Endpunkt (im Hinblick auf die geringste Latenzzeit) verwenden sollen.
* **Roundrobin**: Verwenden Sie diese Methode, wenn Sie die Last auf eine Reihe von Clouddiensten im gleichen Rechenzentrum oder auf Clouddienste oder Websites in verschiedenen Rechenzentren verteilen möchten.

Weitere Informationen finden Sie unter [Traffic Manager-Lastenausgleichsmethoden](../articles/traffic-manager/traffic-manager-routing-methods.md).

Das folgende Diagramm zeigt ein Beispiel für die Roundrobin-Lastenausgleichsmethode, bei der der Datenverkehr zwischen unterschiedlichen Clouddiensten verteilt wird.

![loadbalancing](./media/virtual-machines-common-load-balance/TMSummary.png)

Im Folgenden die grundlegende Vorgehensweise:

1. Ein Internetclient fragt einen Domänennamen ab, der einem Webdienst entspricht.
2. DNS leitet die Namensabfrage an den Traffic Manager weiter.
3. Traffic Manager wählt den nächsten Clouddienst in der Round-Robin-Liste und sendet den DNS-Namen zurück. Der DNS-Server des Internetclients löst den Namen in eine IP-Adresse auf und sendet diese an den Internetclient.
4. Der Internetclient wählt eine Verbindung mit dem Clouddienst von Traffic Manager aus.

Weitere Informationen finden Sie unter [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md).

## <a name="azure-load-balancing-for-virtual-machines"></a>Azure-Lastenausgleich für virtuelle Computer
Virtuelle Computer im selben Clouddienst oder virtuellen Netzwerk können über ihre privaten IP-Adressen direkt miteinander kommunizieren. Computer und Dienste außerhalb des Clouddiensts oder virtuellen Netzwerks können nur mit virtuellen Computern in einem Clouddienst oder virtuellen Netzwerk mit einem konfigurierten Endpunkt kommunizieren. Ein Endpunkt ist eine Zuordnung einer öffentlichen IP-Adresse und eines Ports zu der privaten IP-Adresse und dem Port eines virtuellen Computers oder einer Webrolle innerhalb eines Azure-Clouddiensts.

Das Azure-Lastenausgleichsmodul verteilt bestimmte eingehende Datenverkehrsdaten nach dem Zufallsprinzip auf mehrere virtuelle Computer oder Dienste. Diese Konfiguration wird als Gruppe mit Lastenausgleich bezeichnet. Sie können zum Beispiel die Netzwerklast von Webanfragen auf mehrere Webserver oder Webrollen verteilen.

Das folgende Diagramm zeigt einen Endpunkt für Standard-Datenverkehr (unverschlüsselt) mit Lastenausgleich, der von drei virtuellen Computern für den öffentlichen und privaten TCP-Port 80 genutzt wird. Diese drei virtuellen Computer bilden einen Satz mit Lastenausgleich.

![loadbalancing](./media/virtual-machines-common-load-balance/LoadBalancing.png)

Weitere Informationen finden Sie unter [Azure-Lastenausgleichsmodul](../articles/load-balancer/load-balancer-overview.md). Die Schritte zum Erstellen einer Gruppe mit Lastenausgleich finden Sie unter [Konfigurieren einer Gruppe mit Lastenausgleich](../articles/load-balancer/load-balancer-get-started-internet-arm-ps.md).

Azure ist auch in der Lage, Lasten innerhalb eines Clouddiensts oder virtuellen Netzwerks auszugleichen. Diese Methode wird als interner Lastenausgleich bezeichnet und kann wie folgt verwendet werden:

* Zum Lastenausgleich zwischen Servern auf unterschiedlichen Ebenen einer Multi-Tier-Anwendung (beispielsweise zwischen der Web- und Datenbankebene).
* Zum Lastenausgleich für Branchenanwendungen (LOB-Anwendungen), die in Azure gehostet werden, ohne dass zusätzliche Hardware oder Software für den Lastenausgleich erforderlich ist.
* Zum Einbeziehen von lokalen Servern in die Gruppe der Computer, für deren Datenverkehr Lastenausgleich stattfindet.

Vergleichbar mit dem Azure-Lastenausgleich ist auch ein interner Lastenausgleich möglich, indem eine interne Gruppe mit Lastenausgleich konfiguriert wird.

Das folgende Diagramm zeigt ein Beispiel eines internen Endpunkts mit Lastenausgleich für eine Branchenanwendung (LOB-Anwendung), die in einem standortübergreifenden virtuellen Netzwerk von drei virtuellen Computern gemeinsam genutzt wird.

![loadbalancing](./media/virtual-machines-common-load-balance/LOBServers.png)

## <a name="load-balancer-considerations"></a>Überlegungen zu Load Balancer
Ein Load Balancer ist standardmäßig so konfiguriert, dass ein Timeout erfolgt, wenn sich eine Sitzung 4 Minuten im Leerlauf befindet. Wenn Ihre Anwendung hinter einem Load Balancer eine Verbindung länger als 4 Minuten im Leerlauf lässt und keine Keep-Alive-Konfiguration aufweist, wird die Verbindung getrennt. Sie können das Load Balancer-Verhalten so ändern, dass eine [längere Timeouteinstellung für Azure Load Balancer](../articles/load-balancer/load-balancer-tcp-idle-timeout.md)zugelassen wird.

Eine weitere Überlegung betrifft den Typ des Verteilungsmodus, der von Azure Load Balancer unterstützt wird. Sie können die Quell-IP-Affinität (Quell-IP, Ziel-IP) oder das Quell-IP-Protokoll (Quell-IP, Ziel-IP und Protokoll) konfigurieren. Weitere Informationen finden Sie unter [Azure Load Balancer-Verteilungsmodus (Quell-IP-Affinität)](../articles/load-balancer/load-balancer-distribution-mode.md) .

## <a name="next-steps"></a>Nächste Schritte
Die Schritte zum Erstellen einer Gruppe mit Lastenausgleich finden Sie unter [Konfigurieren einer internen Gruppe mit Lastenausgleich](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md).

Weitere Informationen zum Lastenausgleich finden Sie unter [Interner Lastenausgleich](../articles/load-balancer/load-balancer-internal-overview.md).



<!--HONumber=Nov16_HO3-->


