---
title: "Aufgabenvoreinstellungen für MES (Media Encoder Standard) | Microsoft Docs"
description: "Das Thema bietet eine Übersicht über die Aufgabenvoreinstellungen für MES (Media Encoder Standard)."
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 0124c884d3d805a407041e8ecbc5961209a9653d
ms.openlocfilehash: 708444a1b2569cf794ce1f6d8d6c97c7d9e339b7


---

# <a name="task-presets-for-mes-media-encoder-standard"></a>Aufgabenvoreinstellungen für MES (Media Encoder Standard)

`Media Encoder Standard` definiert eine Reihe von Codierungsvoreinstellungen, die Sie beim Erstellen von Codierungsaufträgen verwenden können.  
  
 Die vorhandenen XML- oder JSON-Zeichenfolgen sollten auf den Voreinstellungen basieren, die in den Dateien unten gezeigt werden. Sie können die Voreinstellungen mit benutzerdefinierten (gültigen) Werten an den Encoder übergeben. Erläuterungen zur Bedeutung der einzelnen Elemente in diesen Voreinstellungen sowie gültige Werte für jedes Element finden Sie im Thema [Media Encoder Standard schema](media-services-mes-schema.md) (Media Encoder Standard-Schema).  
  
> [!NOTE]
>  Wenn Sie eine Voreinstellung für 4k-Codierungen verwenden, sollten Sie den reservierten Einheitentyp `S3` erhalten. Weitere Informationen finden Sie unter [Skalieren der Codierung](https://azure.microsoft.com/en-us/documentation/articles/media-services-portal-encoding-units).  
  
 Bei der Arbeit mit Media Encoder Standard ist Drehung standardmäßig aktiviert. Wenn Ihr Video auf einem Smartphone oder anderen mobilen Gerät im Hochformat aufgezeichnet wurde, wird es durch diese Voreinstellungen standardmäßig vor der Codierung ins Querformat gedreht (im Gegensatz zur Arbeit mit Azure Media Encoder, wo die Drehung des Videos ein manueller Vorgang ist, wie in [diesem](http://azure.microsoft.com/blog/2014/08/21/advanced-encoding-features-in-azure-media-encoder/) Blog unter „Video Rotation“ dokumentiert).  
  
 Die Voreinstellungsnamen sind Voreinstellungen zugeordnet, die in den folgenden Themen vorgestellt werden.  
  
 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) erzeugt einen Satz von acht MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 6.000KBit/s und 400KBit/s sowie AAC 5.1-Audio.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) erzeugt einen Satz von acht MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 6.000KBit/s und 400KBit/s sowie Stereo-AAC-Audio.  
  
 [H264 Multiple Bitrate 16x9 für iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) erzeugt einen Satz von acht MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 8.500KBit/s und 200KBit/s sowie Stereo-AAC-Audio.  
  
 [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) erzeugt einen Satz von fünf MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 1.900KBit/s und 400KBit/s sowie AAC 5.1-Audio.  
  
 [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) erzeugt einen Satz von fünf MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 1.900KBit/s und 400KBit/s sowie Stereo-AAC-Audio.  
  
 [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) erzeugt einen Satz von zwölf MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 20.000KBit/s und 1.000KBit/s sowie AAC 5.1-Audio.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) erzeugt einen Satz von 12 MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 20.000KBit/s und 1.000KBit/s sowie Stereo-AAC-Audio.  
  
 [H264 Multiple Bitrate 4x3 für iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) erzeugt einen Satz von acht MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 8.500KBit/s und 200KBit/s sowie Stereo-AAC-Audio.  
  
 [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) erzeugt einen Satz von fünf MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 1.600KBit/s und 400KBit/s sowie AAC 5.1-Audio.  
  
 [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) erzeugt einen Satz von fünf MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 1.600KBit/s und 400KBit/s sowie Stereo-AAC-Audio.  
  
 [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) erzeugt einen Satz von sechs MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 3.400KBit/s und 400KBit/s sowie AAC 5.1-Audio.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) erzeugt einen Satz von sechs MP4-Dateien mit GOP-Ausrichtung mit Werten zwischen 3.400KBit/s und 400KBit/s sowie Stereo-AAC-Audio.  
  
 [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 6.750KBit/s und AAC 5.1-Audio.  
  
 [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 6.750KBit/s und Stereo-AAC-Audio.  
  
 [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 18.000KBit/s und AAC 5.1-Audio.  
  
 [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 18.000KBit/s und Stereo-AAC-Audio.  
  
 [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 18.000KBit/s und AAC 5.1-Audio.  
  
 [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 18.000KBit/s und Stereo-AAC-Audio.  
  
 [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 2.200KBit/s und AAC 5.1-Audio.  
  
 [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 2.200KBit/s und Stereo-AAC-Audio.  
  
 [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 4.500KBit/s und AAC 5.1-Audio.  
  
 [H264 Single Bitrate 720p für Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 2.000KBit/s und Stereo-AAC.  
  
 [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 4.500KBit/s und Stereo-AAC-Audio.  
  
 [H264 Single Bitrate High Quality SD für Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 500KBit/s und Stereo-AAC-Audio.  
  
 [H264 Single Bitrate Low Quality SD für Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) erzeugt eine einzelne MP4-Datei mit einer Bitrate von 56KBit/s und Stereo-AAC-Audio.  
  
 Weitere Informationen im Zusammenhang mit Media Services-Encodern finden Sie unter [Azure On-Demand Media Encoder – Überblick und Vergleich](https://azure.microsoft.com/en-us/documentation/articles/media-services-encode-asset/).


<!--HONumber=Nov16_HO5-->


