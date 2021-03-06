---
title: Installieren von MongoDB auf einem virtuellen Windows-Computer | Microsoft Docs
description: Erfahren Sie, wie Sie MongoDB auf einem virtuellen Azure-Computer installieren, der mit dem klassischen Bereitstellungsmodell unter Windows Server erstellt wurde.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou

---
# <a name="install-mongodb-on-a-windows-vm"></a>Installieren von MongoDB auf einem virtuellen Windows-Computer
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Informationen zum Installieren und Konfigurieren von MongoDB mithilfe des Resource Manager-Bereitstellungsmodells, finden Sie in [diesem Artikel](virtual-machines-windows-classic-install-mongodb.md).

[MongoDB][MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Dieser Artikel führt Sie durch die Schritte zum Erstellen eines neuen virtuellen Windows Server-Computers (Virtual Machine, VM) im [klassischen Azure-Portal][AzurePortal]. Sie erstellen dabei einen Datenträger, fügen ihn an den virtuellen Computer an und installieren und konfigurieren MongoDB. Wenn Sie bereits über einen virtuellen Computer in Azure verfügen, den Sie für MongoDB verwenden möchten, können Sie sofort zum [Installieren und Konfigurieren von MongoDB](#install-and-run-mongodb-on-the-virtual-machine) übergehen.

## <a name="create-a-virtual-machine-running-windows-server"></a>Erstellen eines virtuellen Computers unter Windows Server
Befolgen Sie diese Anweisungen, um einen virtuellen Computer zu erstellen.

[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> Sie können beim Erstellen des virtuellen Computers einen Endpunkt für MongoDB hinzufügen und wie folgt konfigurieren: Vergeben Sie den Namen **Mongo**, verwenden Sie **TCP** als Protokoll, und legen Sie die öffentlichen und privaten Ports auf **27017** fest.
> 
> 

## <a name="attach-a-data-disk"></a>Datenträger anfügen
Fügen Sie einen Datenträger an den virtuellen Computer an, und initialisieren Sie ihn anschließend für die Verwendung durch Windows, damit ein entsprechender Speicher für den virtuellen Computer verfügbar ist. Wenn Sie bereits über einen Datenträger verfügen, können Sie diesen vorhandenen Datenträger oder einen leeren Datenträger anfügen.

[!INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Informationen zum Initialisieren des Datenträgers finden Sie in [Anfügen eines Datenträgers an einen virtuellen Windows-Computer](virtual-machines-windows-classic-attach-disk.md)unter "Initialisieren eines neues Datenträgers unter Windows Server".

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Installieren und Ausführen von MongoDB auf dem virtuellen Computer
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Zusammenfassung
In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen Windows Server-Computer erstellen, eine Remoteverbindung dazu herstellen und anschließend einen Datenträger anfügen.  Sie haben außerdem erfahren, wie Sie MongoDB auf dem Windows-basierten virtuellen Computer installieren und konfigurieren. Sie können nun auf dem Windows-basierten virtuellen Computer auf MongoDB zugreifen, indem Sie die erweiterten Themen in der [MongoDB-Dokumentation][MongoDocs] abrufen.

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[Azure-Portal]: http://manage.windowsazure.com



<!--HONumber=Oct16_HO2-->


