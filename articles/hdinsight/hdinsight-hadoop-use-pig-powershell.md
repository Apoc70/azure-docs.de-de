---
title: Verwenden von Hadoop Pig mit PowerShell in HDInsight | Microsoft Docs
description: Erfahren Sie, wie Sie mit Azure PowerShell Pig-Aufträge an einen Hadoop-Cluster in HDInsight übermitteln.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2016
ms.author: larryfr

---
# <a name="run-pig-jobs-using-powershell"></a>Ausführen von Pig-Aufträgen mit PowerShell
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dieses Dokument enthält ein Beispiel zur Verwendung von Azure PowerShell zum Übermitteln von Pig-Aufträgen an einen Hadoop-Cluster in HDInsight. Mithilfe von Pig können Sie MapReduce-Aufträge in einer Sprache (Pig Latin) verfassen, die Datentransformationen anstelle von Map- und Reduce-Funktionen modelliert.

> [!NOTE]
> Dieses Dokument bietet keine detaillierten Beschreibungen zu den in diesem Beispiel verwendeten Pig Latin-Anweisungen. Informationen zu den in diesem Beispiel verwendeten Pig Latin-Anweisungen finden Sie unter [Verwenden von Pig Latin mit Hadoop in HDInsight](hdinsight-use-pig.md).
> 
> 

## <a name="<a-id="prereq"></a>prerequisites"></a><a id="prereq"></a>Voraussetzungen
Damit Sie die in dieser Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Eine Arbeitsstation mit Azure PowerShell**.
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="<a-id="powershell"></a>run-pig-jobs-using-powershell"></a><a id="powershell"></a>Ausführen von Pig-Aufträgen mit PowerShell
Azure PowerShell stellt *Cmdlets* bereit, mit denen Sie Pig-Aufträge in HDInsight remote ausführen können. Intern wird dies mithilfe von REST-Aufrufen an [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (früher Templeton) im HDInsight-Cluster realisiert.

Die folgenden Cmdlets werden zum Ausführen der Pig-Aufträge in einem HDInsight-Remotecluster verwendet:

* **Login-AzureRmAccount**: Authentifiziert Azure PowerShell für Ihr Azure-Abonnement
* **New-AzureRmHDInsightPigJobDefinition**: erstellt mithilfe der angegebenen Pig Latin-Anweisungen eine neue *Auftragsdefinition*
* **Start-AzureRmHDInsightJob**: sendet die Auftragsdefinition an HDInsight, startet den Auftrag und gibt ein *Auftrags* -Objekt zurück, mit dem der Status des Auftrags geprüft werden kann
* **Wait-AzureRmHDInsightJob**: verwendet das Auftragsobjekt, um den Status des Auftrags zu prüfen. Es wird gewartet, bis der Auftrag abgeschlossen oder die Wartezeit überschritten ist.
* **Get-AzureRmHDInsightJobOutput**: wird zum Abrufen der Ausgabe des Auftrags verwendet

Die folgenden Schritte veranschaulichen, wie diese Cmdlets zum Ausführen eines Auftrags auf dem HDInsight-Cluster verwendet werden.

1. Speichern Sie den folgenden Code mithilfe eines Editors als **pigjob.ps1**. Ersetzen Sie **CLUSTERNAME** durch den Namen des HDInsight-Clusters.
   
        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential
   
        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
   
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
   
        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"

        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

1. Öffnen Sie eine neue Windows PowerShell-Eingabeaufforderung. Navigieren Sie zum Speicherort der Datei **pigjob.ps1** , und verwenden Sie folgenden Befehl zum Ausführen des Skripts:
   
        .\pigjob.ps1
   
    Sie werden zunächst dazu aufgefordert, sich bei Ihrem Azure-Abonnement anzumelden. Außerdem werden Sie aufgefordert, den HTTPS-/Administratorkontonamen und das Kennwort für den HDInsight-Cluster bereitzustellen.
2. Nachdem der Auftrag abgeschlossen ist, muss er Informationen zurückgeben, die den folgenden ähneln:
   
        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a name="<a-id="troubleshooting"></a>troubleshooting"></a><a id="troubleshooting"></a>Problembehandlung
Wenn nach Abschluss des Auftrags keine Informationen zurückgegeben werden, ist während der Verarbeitung möglicherweise ein Fehler aufgetreten. Um Fehlerinformationen für diesen Auftrag anzuzeigen, fügen Sie folgenden Befehl am Ende der Datei **pigjob.ps1** hinzu. Anschließend speichern Sie die Datei und führen sie erneut aus.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Dadurch werden die beim Ausführen des Auftrags an STDERR auf dem Server geschriebenen Informationen zurückgegeben, die möglicherweise hilfreich sind, um festzustellen, warum der Auftrag fehlgeschlagen ist.

## <a name="<a-id="summary"></a>summary"></a><a id="summary"></a>Zusammenfassung
Wie Sie sehen können, bietet Azure PowerShell eine einfache Möglichkeit, um Pig-Aufträge auf einem HDInsight-Cluster auszuführen, den Auftragsstatus zu überwachen und die Ausgabe abzurufen.

## <a name="<a-id="nextsteps"></a>next-steps"></a><a id="nextsteps"></a>Nächste Schritte
Allgemeine Informationen zu Pig in HDInsight:

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)
* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=Oct16_HO2-->


