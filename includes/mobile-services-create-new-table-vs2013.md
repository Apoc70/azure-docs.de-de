

Bevor Sie Daten in Ihrem neuen mobilen Dienst speichern können, müssen Sie im Dienst eine neue Speichertabelle erstellen. Diese Schritte zeigen, wie Visual Studio 2013 zum Erstellen einer neuen Tabelle im mobilen Dienst verwendet wird. Danach aktualisieren Sie die Anwendung, damit sie mobile Dienste zum Speichern von Objekten in Azure anstelle der lokalen Sammlung verwendet.

1. Erweitern Sie im Server-Explorer **Azure Mobile Services**, klicken Sie mit der rechten Maustaste auf den mobilen Dienst, klicken Sie auf **Tabelle erstellen**, und geben Sie `TodoItem` unter **Tabellenname** ein.
   
    ![Tabelle in VS 2013 erstellen](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013.png)
2. Erweitern Sie **Erweitert**, überprüfen Sie, ob die Tabellen-Aktionsberechtigungen standardmäßig auf **Jeder mit Anwendungsschlüssel** festgelegt sind, und klicken Sie dann auf **Erstellen**.
   
    Auf diese Weise wird die TodoItem-Tabelle auf dem Server erstellt, auf dem jeder, der über den Anwendungsschlüssel verfügt, auf Daten in der Tabelle zugreifen und diese ändern kann, ohne sich zuerst authentifizieren zu müssen.
   
   > [!NOTE]
   > Der Anwendungsschlüssel wird mit der Anwendung bereitgestellt. Da dieser Schlüssel nicht sicher bereitgestellt wird, kann er nicht als Sicherheitstoken angesehen werden. Für den sicheren Zugriff auf die Daten in Ihrem mobilen Dienst müssen Sie stattdessen Benutzer vor dem Zugriff authentifizieren. Weitere Informationen finden Sie unter [Berechtigungen](http://msdn.microsoft.com/library/windowsazure/jj193161.aspx).
   > 
   > Neue Tabellen werden mit den Spalten "Id", "\_\_createdAt", "\_\_updatedAt" und "\_\_version" erstellt. Wenn das dynamische Schema aktiviert ist, generieren Mobile Services automatisch neue Spalten auf Grundlage des JSON-Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).
   > 
   > 

<!---HONumber=Oct15_HO3-->