---
title: Aktivieren der Offlinesynchronisierung für Ihre Azure Mobile App (Xamarin iOS)
description: Erfahren Sie, wie Sie mobile App Service-Apps verwenden, um Offlinedaten in Ihrer Xamarin iOS-Anwendung zwischenzuspeichern und zu synchronisieren.
documentationcenter: xamarin
author: wesmc7777
manager: dwrede
editor: ''
services: app-service\mobile

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: wesmc

---
# Aktivieren der Offlinesynchronisierung für Ihre mobile Xamarin.iOS-App
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Übersicht
In diesem Lernprogramm wird die Funktion zur Offlinesynchronisierung von Azure Mobile Apps für Xamarin.iOS eingeführt. Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App – Anzeigen, Hinzufügen und Ändern von Daten –, auch wenn keine Netzwerkverbindung vorhanden ist. Änderungen werden in einer lokalen Datenbank gespeichert. Sobald das Gerät wieder online ist, werden diese Änderungen mit dem Remotedienst synchronisiert.

In diesem Tutorial aktualisieren Sie das Xamarin.iOS-App-Projekt aus [Erstellen einer Xamarin iOS-App] zur Unterstützung der Offlinefunktionen von Azure Mobile Apps. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt die Datenzugriffs-Erweiterungspakete hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

Weitere Informationen über die Funktion der Offlineynchronisierung finden Sie im Thema [Offlinedatensynchronisierung in Azure Mobile Apps].

## Überprüfen des Clientcodes für die Synchronisierung
Das Xamarin-Clientprojekt, das Sie heruntergeladen haben, nachdem Sie das Lernprogramm [Erstellen einer Xamarin iOS-App] abgeschlossen haben, enthält bereits Code zur Unterstützung der Offlinesynchronisierung mithilfe einer lokalen SQLite-Datenbank. Dies ist eine kurze Übersicht darüber, was bereits im Code des Lernprogramms enthalten ist. Eine grundlegende Übersicht über die Funktion finden Sie unter [Offlinedatensynchronisierung in Azure Mobile Apps].

* Bevor Tabellenvorgänge durchgeführt werden können, muss der lokale Speicher initialisiert werden. Die lokale Datenbank wird initialisiert, wenn `QSTodoService.InitializeStoreAsync()` von `QSTodoListViewController.ViewDidLoad()` ausgeführt wird. Dadurch wird eine neue lokale SQLite-Datenbank mit der `MobileServiceSQLiteStore`-Klasse erstellt, die vom Azure Mobile Apps-Client-SDK bereitgestellt wird.
  
    Die `DefineTable`-Methode erstellt eine Tabelle im lokalen Speicher, die mit den Feldern im bereitgestellten Typ übereinstimmt (in diesem Fall `ToDoItem`). Der Typ muss nicht alle Spalten der Remotedatenbank enthalten. Es ist möglich, nur eine Teilmenge der Spalten zu speichern.
  
        // QSTodoService.cs
  
        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();
  
            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Das `todoTable`-Mitglied von `QSTodoService` ist vom Typ `IMobileServiceSyncTable` und nicht vom Typ `IMobileServiceTable`. Dies leitet alle Erstellen-, Lesen-, Aktualisieren- und Löschtabellenvorgänge (CRUD) an die lokale Datenbank.
  
    Sie legen fest, wann diese Änderungen per Pushvorgang an das Azure Mobile App-Back-End durch Aufrufen von `IMobileServiceSyncContext.PushAsync()` mit dem Synchronisierungskontext für die Clientverbindung übertragen werden. Der Synchronisierungskontext hilft dabei, Tabellenbeziehungen durch das Nachverfolgen und Übertragen von Änderungen in allen Tabellen beizubehalten, die eine Clientanwendung geändert hat, wenn `PushAsync` aufgerufen wird.
  
    Der bereitgestellte Code ruft `QSTodoService.SyncAsync()` zum Synchronisieren ab, wenn die TodoItem-Liste aktualisiert wird oder ein TodoItem hinzugefügt wird oder abgeschlossen ist. So synchronisiert er nach jeder lokalen Änderung durch Ausführen eines Pushvorgangs auf den Synchronisierungskontext und eines Pullvorgangs auf der Synchronisierungstabelle. Es ist jedoch zu beachten, dass bei einem Pullvorgang auf einer Tabelle mit ausstehenden lokalen Updates, die durch den Kontext verfolgt werden, dieser Pullvorgang zunächst automatisch einen Kontextpush ausführt. So können Sie in diesen Fällen (Aktualisieren, Hinzufügen und Abschließen von Elementen) den expliziten `PushAsync`-Aufruf auslassen. Er ist überflüssig.
  
    Im bereitgestellten Code werden alle Datensätze in der `TodoItem`-Remotetabelle abgefragt, es ist aber auch möglich, Datensätze durch Übergeben einer Abfrage-ID und Abfrage an `PushAsync` zu filtern. Weitere Informationen finden Sie im Abschnitt *Inkrementelle Synchronisierung* in [Offlinedatensynchronisierung in Azure Mobile Apps].
  
    <!-- Need updated conflict handling info : `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
    -->

        // QSTodoService.cs

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }


## Ausführen der Client-App
Führen Sie die Clientanwendung mindestens einmal aus, um die lokale Datenbank zu füllen. Im nächsten Abschnitt simulieren Sie ein Offlineszenario und ändern die Daten im lokalen Speicher, während die App offline ist.

## Aktualisieren des Synchronisierungsverhaltens der Client-App
In diesem Abschnitt ändern Sie das Clientprojekt, um ein Offlineszenario mithilfe einer ungültigen Anwendungs-URL für Ihr Back-End zu simulieren. Beim Hinzufügen oder Ändern von Datenelementen werden diese Änderungen im lokalen Speicher gespeichert, aber nicht mit dem Back-End-Datenspeicher synchronisiert, bis die Verbindung wiederhergestellt ist.

1. Ändern Sie am Anfang von `QSTodoService.cs` die Initialisierung von `applicationURL`, um auf eine ungültige URL zu verweisen:
   
        const string applicationURL = @"https://your-service.azurewebsites.xxx/";
2. Fügen Sie einen zusätzlichen `catch` für die `Exception`-Klasse in `QSTodoService.SyncAsync` hinzu, der die Ausnahmemeldung für die Konsole schreibt.
   
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }
            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
        }
3. Erstellen Sie die Client-App und führen Sie sie aus. Fügen Sie einige neue "todo"-Elemente hinzu und beachten Sie, wie eine Ausnahme für jeden Versuch protokolliert wird, sich mit dem mobilen App-Back-End zu synchronisieren. Diese neuen Elemente sind nur im lokalen Speicher vorhanden, bis sie per Push an das mobile Back-End übertragen werden können. Die Client-App verhält sich so, als ob eine Verbindung mit dem mobilen Back-End vorhanden wäre, und unterstützt alle Operationen zum Erstellen, Lesen, Aktualisieren und Löschen (CRUD-Vorgänge).
4. Schließen Sie die App, und starten Sie sie neu, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.
5. (Optional) Verwenden Sie Visual Studio zum Anzeigen der Azure SQL-Datenbanktabelle, um festzustellen, ob sich die Daten in der Back-End-Datenbank nicht geändert haben.
   
    Öffnen Sie den **Server-Explorer** in Visual Studio. Navigieren Sie zu der Datenbank in **Azure** -> **SQL-Datenbanken**. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen** aus. Jetzt können nach Ihrer SQL-Datenbanktabelle und seinen Inhalten suchen.

## Aktualisieren der Client-App, um erneut eine Verbindung mit dem mobilen Back-End herzustellen
In diesem Abschnitt stellen Sie erneut eine Verbindung zwischen der App und dem mobilen Back-End her, um zu simulieren, dass die App wieder im Onlinezustand ist. Wenn Sie die Aktualisierungsbewegung durchführen, werden Daten mit Ihrem mobilen Back-End synchronisiert.

1. Öffnen Sie `QSTodoService.cs`. Korrigieren Sie die `applicationURL`, sodass sie auf die richtige URL verweist.
2. Erstellen Sie die Client-App erneut, und führen Sie sie aus. Die App versucht, sich nach dem Start mit dem Azure Mobile App-Back-End zu synchronisieren. Vergewissern Sie sich, dass in der Debugkonsole keine Ausnahmen protokolliert sind.
3. (Optional) Zeigen Sie die aktualisierten Daten mithilfe von SQL Server-Objekt-Explorer oder einem REST-Tool wie Fiddler an. Beachten Sie, dass die Daten zwischen der Azure Mobile App-Back-End-Datenbank und dem lokalen Speicher synchronisiert wurden.
   
    Beachten Sie, dass die Daten zwischen der Datenbank und dem lokalen Speicher synchronisiert wurden und die Elemente enthalten, die Sie hinzugefügt haben, während Ihre App von der Verbindung getrennt war.

## Weitere Ressourcen
* [Offlinedatensynchronisierung in Azure Mobile Apps]
* [Cloud Cover: Offlinesynchronisierung in Azure Mobile Services] \(Hinweis: Im Video geht es zwar um Mobile Services, aber die Offlinesynchronisierung in Azure Mobile Apps funktioniert auf ähnliche Weise.\)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Erstellen einer Xamarin iOS-App]: app-service-mobile-xamarin-ios-get-started.md
[Offlinedatensynchronisierung in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[How to use the Xamarin Component client for Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md
[Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=AcomDC_0824_2016-->