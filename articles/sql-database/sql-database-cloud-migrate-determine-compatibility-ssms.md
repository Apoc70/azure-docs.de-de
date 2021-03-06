---
title: "Verwenden von SQL Server Management Studio zum Ermitteln der Kompatibilität mit SQL-Datenbank vor der Migration zu Azure SQL-Datenbank | Microsoft Docs"
description: "Microsoft Azure SQL-Datenbank, Datenbankmigration, SQL-Datenbankkompatibilität, Assistent „Datenebenenanwendung exportieren“"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: c9ead868-aa1e-4a92-a099-6baf7c0dda32
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/29/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 15d0bf86d6094b6a8c0d6aaf1e1bb59382a81ffb


---
# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Verwenden Sie SQL Server Management Studio zum Ermitteln der Kompatibilität von SQL-Datenbank vor der Migration zu Azure SQL-Datenbank.
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

In diesem Artikel erfahren Sie, wie Sie ermitteln können, ob eine SQL Server-Datenbank für die Migration zur SQL-Datenbank geeignet ist, indem Sie den Assistenten „Datenebenenanwendung exportieren“ in SQL Server Management Studio verwenden.

## <a name="using-sql-server-management-studio"></a>Verwenden von SQL Server Management Studio
1. Stellen Sie sicher, dass Sie über die neueste Version von SQL Server Management Studio verfügen. Neue Versionen von Management Studio werden monatlich aktualisiert, damit sie mit Updates des Azure-Portals synchron sind.
   
   > [!IMPORTANT]
   > Es wird empfohlen, immer die neueste Version von Management Studio zu verwenden, damit Sie mit Updates von Microsoft Azure und SQL-Datenbank synchron sind. [Aktualisieren Sie SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Öffnen Sie Management Studio, und stellen Sie eine Verbindung mit Ihrer Quelldatenbank im Objekt-Explorer her.
3. Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf die Quelldatenbank, zeigen Sie auf **Aufgaben**, und klicken Sie auf **Datenebenenanwendung exportieren...**.
   
    ![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. Klicken Sie im Export-Assistenten auf **Weiter**, und konfigurieren Sie dann auf der Registerkarte **Einstellungen** den Export für das Speichern der BACPAC-Datei entweder an einem lokalen Speicherort auf dem Datenträger oder in einem Azure-Blob. Eine BACPAC-Datei wird gespeichert, wenn keine Probleme mit der Datenbankkompatibilität vorliegen. Wenn Kompatibilitätsprobleme auftreten, werden sie auf der Konsole angezeigt.
   
    ![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)
5. Um das Exportieren von Daten zu überspringen, klicken Sie auf die **Registerkarte „Erweitert“**, und deaktivieren Sie das Kontrollkästchen **Alles markieren**. An diesem Punkt möchten wir nur die Kompatibilität testen.
   
    ![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)
6. Klicken Sie auf **Weiter** und anschließend auf **Fertig stellen**. Probleme mit der Datenbankkompatibilität werden, falls vorhanden, angezeigt, nachdem der Assistent das Schema überprüft hat.
   
    ![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)
7. Wenn keine Fehler angezeigt werden, ist Ihre Datenbank kompatibel, und Sie können die Migration durchführen. Falls Fehler auftreten, müssen Sie sie beheben. Klicken Sie zum Anzeigen der Fehler für **Schema wird verifiziert** auf **Fehler**. 
    ![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)
8. Wenn die BACPAC-Datei erfolgreich generiert wurde, ist Ihre Datenbank mit der SQL-Datenbank kompatibel, und Sie können die Migration durchführen.

## <a name="next-steps"></a>Nächste Schritte
* [Neueste Version von SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Neueste Version von SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Beheben von Kompatibilitätsproblemen in Bezug auf die Datenbankmigration](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [Migrieren einer kompatiblen SQL Server-Datenbank zur SQL-Datenbank](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Azure SQL Database features (Features der Azure SQL-Datenbank)](sql-database-features.md)
* [Teilweise oder vollständig unterstützte Transact-SQL-Funktionen](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Migrieren von Nicht-SQL Server-Datenbanken mithilfe des SQL Server-Migrations-Assistenten)](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


