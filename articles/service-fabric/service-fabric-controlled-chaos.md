---
title: "Auslösen von Chaos in Service Fabric-Clustern | Microsoft Docs"
description: Verwenden von Fault Injection und Cluster Analysis Service-APIs zum Verwalten von Chaostests im Cluster.
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2016
ms.author: motanv
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ea8f76d146bd630cc8cb0a417d8c090b656150a4


---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Auslösen von kontrolliertem Chaos in Service Fabric-Clustern
Große verteilte Systeme wie Cloudinfrastrukturen sind grundsätzlich unzuverlässig. Azure Service Fabric ermöglicht Entwicklern, aufbauend auf einer unzuverlässigen Infrastruktur zuverlässige Dienste zu erstellen. Um zuverlässige Dienste programmieren zu können, müssen Entwickler Fehler in eine solche unzuverlässige Infrastruktur einschleusen können, um die Stabilität ihrer Dienste zu testen.

Der Fault Injection and Cluster Analysis Service (auch als Fault Analysis Service (FAS) bezeichnet) gibt Entwicklern die Möglichkeit, Fehleraktionen einzuschleusen, um Dienste zu testen. Doch mit zielgerichteten simulierten Fehlern können Sie nicht alle Eventualitäten abdecken. Um die Tests noch zu verschärfen, können Sie Chaos auslösen.

Mithilfe von Chaos werden im Cluster über längere Zeiträume hinweg fortlaufende verschachtelte Fehler simuliert (sowohl ordnungsgemäß als auch nicht ordnungsgemäß). Nachdem Sie Chaos mit der Rate und Art der Fehler für einen Test konfiguriert haben, können Sie den Vorgang starten oder beenden. Hierzu werden entweder die C#-APIs oder PowerShell genutzt, um Fehler im Cluster und in Ihrem Dienst zu generieren.

Während dieses Chaostests, werden verschiedene Ereignisse erzeugt, die den Status der Ausführung im jeweiligen Moment erfassen. Beispielsweise enthält ein ExecutingFaultsEvent-Element alle Fehler, die im Rahmen der Iteration ausgeführt werden. Ein ValidationFailedEvent-Element enthält die Details eines Fehlers, der während der Clusterüberprüfung gefunden wurde. Sie können die GetChaosReportAsync-API aufrufen, um einen Bericht zum ausgelösten Chaostest zu erhalten.

## <a name="faults-induced-in-chaos"></a>Im Chaostest ausgelöste Fehler
Der Chaostest generiert Fehler im gesamten Service Fabric-Cluster und komprimiert in Monaten oder Jahren auftretende Fehler zu einigen wenigen Stunden. Bei dieser Kombination aus überlappenden Fehlern mit der hohen Fehlerrate werden auch Spezialfälle erkannt, die sonst nicht auffallen. Dieser Chaostest führt zu einer erheblichen Verbesserung der Codequalität des Diensts.

Der Chaostest verursacht Fehler in den folgenden Kategorien:

* Neustart eines Knotens
* Neustart eines bereitgestellten Codepakets
* Entfernung eines Replikats
* Neustart eines Replikats
* Verschiebung eines primären Replikats (konfigurierbar)
* Verschiebung eines sekundären Replikats (konfigurierbar)

Chaos wird in mehreren Iterationen ausgeführt. Jede Iteration besteht aus Fehlern und der Clusterüberprüfung für den angegebenen Zeitraum. Sie können die Dauer der Stabilisierung des Clusters und des erfolgreichen Abschlusses der Überprüfung konfigurieren. Wenn bei der Clusterüberprüfung ein Fehler gefunden wird, wird vom Chaostest ein „ValidationFailedEvent“-Ereignis mit dem UTC-Zeitstempel und den Fehlerdetails generiert und gespeichert.

Nehmen wir beispielsweise an, ein Chaostest soll eine Stunde lang ausgeführt werden und maximal drei gleichzeitige Fehler umfassen. Beim Chaostest werden drei Fehler eingeschleust, und anschließend wird die Clusterintegrität überprüft. Es erfolgt eine Iteration durch den vorherigen Schritt, bis dieser explizit mithilfe der StopChaosAsync-API beendet wird oder eine Stunde vergangen ist. Wenn der Cluster bei einer der Iterationen fehlerhaft wird (also sich nicht innerhalb des konfigurierten Zeitraums stabilisiert), generiert Chaos ein Ereignis vom Typ „ValidationFailedEvent“. Dieses Ereignis bedeutet, dass etwas schiefgelaufen ist und ggf. eine nähere Untersuchung erforderlich ist.

In seiner derzeitigen Form werden von Chaos nur sichere Fehler ausgelöst. Dies bedeutet, dass bei einem Nichtvorhandensein von externen Fehlern niemals ein Quorum- oder Datenverlust auftritt.

## <a name="important-configuration-options"></a>Wichtige Konfigurationsoptionen
* **TimeToRun**: Gesamtdauer der Ausführung des Chaostests, bevor er erfolgreich abgeschlossen wird. Sie können den Chaostest mit der StopChaos-API vor Ablauf des Zeitraums von „TimeToRun“ beenden.
* **MaxClusterStabilizationTimeout**: Maximale Dauer, wie lange auf die Wiederherstellung der Integrität des Clusters gewartet wird, bevor erneut eine Überprüfung durchgeführt wird. Mit diesem Wartezeitraum soll die Auslastung des Clusters während der Wiederherstellung reduziert werden. Die folgenden Prüfungen erfolgen:
  * Ob die Clusterintegrität in Ordnung ist
  * Ob die Dienstintegrität in Ordnung ist
  * Ob die Größe der Zielreplikatgruppe für die Dienstpartition erreicht wurde
  * Ob keine „InBuild“-Replikate vorhanden sind
* **MaxConcurrentFaults**: Maximale Anzahl von gleichzeitigen Fehlern, die bei jeder Iteration ausgelöst werden. Je höher die Anzahl ist, desto aggressiver ist der Chaostest. Dies führt zu komplexeren Failovern und Übergangskombinationen. Der Chaostest garantiert, dass es bei Nichtauftreten externer Fehler nicht zu einem Quorum- oder Datenverlust kommt, und zwar unabhängig von der Höhe des Werts dieser Konfiguration.
* **EnableMoveReplicaFaults**: Aktiviert oder deaktiviert die Fehler, die zur Verschiebung der primären oder sekundären Replikate führen. Diese Fehler sind standardmäßig deaktiviert.
* **WaitTimeBetweenIterations**: Gibt an, wie lange zwischen Iterationen gewartet wird (also nach einer Fehlerrunde und der entsprechenden Überprüfung).
* **WaitTimeBetweenFaults**: Wartezeit zwischen zwei aufeinander folgenden Fehlern in einer Iteration.

## <a name="how-to-run-chaos"></a>Ausführen des Chaostests
**C#:**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell:**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```



<!--HONumber=Nov16_HO3-->


