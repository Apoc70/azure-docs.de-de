---
title: Bereitstellen einer Sails.js-Web-App in Azure App Service
description: Erfahren Sie, wie Sie eine Node.js-Anwendung in Azure App Service bereitstellen. In diesem Tutorial wird gezeigt, wie Sie eine Sails.js-Web-App bereitstellen.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 09/23/2016
ms.author: cephalin

---
# <a name="deploy-a-sails.js-web-app-to-azure-app-service"></a>Bereitstellen einer Sails.js-Web-App in Azure App Service
In diesem Tutorial wird gezeigt, wie Sie eine Sails.js-Web-App in Azure App Service bereitstellen. Bei diesem Vorgang können Sie einige allgemeine Kenntnisse über das Konfigurieren Ihrer Node.js-App für die Ausführung in App Service erwerben. 

Sie sollten über ausreichende Kenntnisse zu Sails.js verfügen. Dieses Tutorial ist nicht dazu gedacht, Ihnen bei Problemen im Zusammenhang mit Sail.js im Allgemeinen zu helfen.

## <a name="prerequisites"></a>Voraussetzungen
* [Node.js](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md)
* Ein Microsoft Azure-Konto. Falls Sie noch kein Konto haben, können Sie sich [für eine kostenlose Testversion registrieren](/pricing/free-trial/?WT.mc_id=A261C142F) oder [Ihre Visual Studio-Abonnentenvorteile aktivieren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Unter [Azure App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751)können Sie Azure App Service in Aktion erleben, bevor Sie sich für ein Azure-Konto registrieren. Dort können Sie sofort eine kurzzeitige Start-App in App Service erstellen – ohne Kreditkarte und weitere Verpflichtungen.
> 
> 

## <a name="step-1:-create-a-sails.js-app-locally"></a>Schritt 1: Lokales Erstellen einer Sails.js-App
Erstellen Sie zuerst schnell eine standardmäßige Sails.js-App in Ihrer Entwicklungsumgebung, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie ein Befehlszeilenterminal Ihrer Wahl, und wechseln Sie mit `CD` in ein Arbeitsverzeichnis.
2. Erstellen Sie eine Sails.js-App, und führen Sie sie aus:
   
        sails new <appname>
        cd <appname>
        sails lift
   
    Stellen Sie sicher, dass Sie zur Standardstartseite unter „http://localhost:1377“ navigieren können.

## <a name="step-2:-create-the-azure-app-resource"></a>Schritt 2: Erstellen der Azure-App-Ressource
Als Nächstes erstellen Sie die App Service-Ressource in Azure. Sie werden ihr später Ihre Sails.js-App bereitstellen.

1. Melden Sie sich wie folgt bei Azure an:
2. Wechseln Sie im gleichen Terminal in den ASM-Modus, und melden Sie sich bei Azure an:
   
        azure config mode asm
        azure login
   
    Befolgen Sie die Aufforderung, um die Anmeldung in einem Browser mit einem Microsoft-Konto fortzusetzen, das über Ihr Azure-Abonnement verfügt.
3. Stellen Sie sicher, dass Sie sich noch im Stammverzeichnis Ihres Sails.js-Projekts befinden. Erstellen Sie die App Service-App-Ressource in Azure mit einem eindeutigen App-Namen mit dem nächsten Befehl. Die URL der Web-App lautet „http://&lt;appname>.azurewebsites.net“.
   
        azure site create --git <appname>
   
    Folgen Sie der Aufforderung zum Auswählen einer Azure-Region für die Bereitstellung. Wenn Sie für Ihr Azure-Abonnement noch nie zuvor Git/FTP-Anmeldeinformationen für die Bereitstellung eingerichtet haben, werden Sie aufgefordert, diese zu erstellen.
   
    Sobald die App Service-App-Ressource erstellt wurde:
   
   * Die Sails.js-App wird für Git initialisiert.
   * Ihr lokales für Git initialisiertes Repository wird mit der neuen App Service-App als ein Git-Remoterepository mit dem passenden Namen „Azure“ verbunden.
   * Die Datei „iisnode.yml“ wird im Stammverzeichnis erstellt. Sie können mit dieser Datei [iisnode](https://github.com/tjanczuk/iisnode) konfigurieren. Damit werden in App Service Node.js-Apps ausgeführt.

## <a name="step-3:-configure-and-deploy-your-sails.js-app"></a>Schritt 3: Konfigurieren und Bereitstellen Ihrer Sails.js-App
 Das Arbeiten mit einer Sails.js-App in App Service umfasst drei Hauptschritte:

* Konfigurieren der App für die Ausführung in App Service
* Bereitstellen der App in App Service
* Überprüfen aller stderr- und stdout-Protokolle zur Behandlung von Bereitstellungsfehlern

Folgen Sie diesen Schritten:

1. Öffnen Sie die neue Datei „iisnode.yml“ im Stammverzeichnis, und fügen Sie die beiden folgenden Zeilen hinzu:
   
        loggingEnabled: true
        logDirectory: iisnode
   
    Die Protokollierung ist jetzt für „iisnode“ aktiviert. Weitere Informationen zur Funktionsweise finden Sie unter  [Abrufen von stdout- und stderr-Protokollen von iisnode](app-service-web-nodejs-get-started.md#iisnodelog).
2. Öffnen Sie „config/env/production.js“, um Ihre Produktionsumgebung zu konfigurieren, und legen Sie `port` und `hookTimeout` fest:
   
        module.exports = {
   
            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,
   
            ...
        };
   
    Sie finden die Dokumentation für diese Konfigurationseinstellungen in der  [Sails.js-Dokumentation](http://sailsjs.org/documentation/reference/configuration/sails-config).
   
    Als Nächstes müssen Sie sicherstellen, dass [Grunt](https://www.npmjs.com/package/grunt) mit den Netzlaufwerken von Azure kompatibel ist. Grunt-Versionen vor 1.0.0 verwenden ein veraltetes [Globpaket](https://www.npmjs.com/package/glob) (vor 5.0.14), das keine Netzlaufwerke unterstützt. 
3. Öffnen Sie „Package.json“, ändern Sie die `grunt`-Version in `1.0.0`, und entfernen Sie alle `grunt-*`-Pakete. Ihre `dependencies` -Eigenschaft sollte wie folgt aussehen:
   
        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },
4. Fügen Sie in „package.json“ die folgende `engines` -Eigenschaft hinzu, um die gewünschte Node.js-Version festzulegen.
   
        "engines": {
            "node": "6.6.0"
        },
5. Speichern und testen Sie Ihre Änderungen, um sicherzustellen, dass Ihre App noch lokal ausgeführt wird. Löschen Sie hierzu den Ordner `node_modules` , und führen Sie dann Folgendes aus:
   
        npm install
        sails lift
6. Verwenden Sie jetzt Git, um Ihre App in Azure bereitzustellen:
   
        git add .
        git commit -m "<your commit message>"
        git push azure master
7. Starten Sie die fertige Azure-App jetzt einfach im Browser:
   
        azure site browse
   
    Sie sollten jetzt die gleiche Sails.js-Startseite sehen.
   
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Problembehandlung bei der Bereitstellung
Wenn Ihre Sails.js-Anwendung aus irgendeinem Grund in App Service ausfällt, suchen Sie in den stderr-Protokollen nach Informationen zur Problembehandlung.
Weitere Informationen finden Sie unter [Abrufen von stdout- und stderr-Protokollen von iisnode](app-service-web-nodejs-sails.md#iisnodelog).
Wenn sie erfolgreich gestartet wurde, sollte das stdout-Protokoll die vertraute Meldung anzeigen:

                .-..-.

    Sails              <|    .-..-.
    v0.12.4             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

Die Granularität der stdout-Protokolle kann in der Datei [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) gesteuert werden. 

## <a name="connect-to-a-database-in-azure"></a>Herstellen einer Verbindung mit einer Datenbank in Azure
Erstellen Sie zum Herstellen einer Verbindung mit einer Datenbank in Azure die gewünschte Datenbank in Azure (Azure SQL-Datenbank, MySQL, MongoDB, Azure [Redis] Cache oder Ähnliches), und verwenden Sie den entsprechenden [Datenspeicheradapter](https://github.com/balderdashy/sails#compatibility) , um die Verbindung herzustellen. Mit den Schritten in diesem Abschnitt wird gezeigt, wie Sie in Azure eine Verbindung mit einer MySQL-Datenbank herstellen.

1. Führen Sie [dieses Tutorial](../store-php-create-mysql-database.md) aus, um eine MySQL-Datenbank in Azure zu erstellen.
2. Installieren Sie den MySQL-Adapter über das Befehlszeilenterminal:
   
        npm install sails-mysql --save
3. Öffnen Sie „config/connections.js“, und fügen Sie der Liste das folgende Verbindungsobjekt hinzu: 
   
        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },
4. Dies muss in App Service für jede Umgebungsvariable (`process.env.*`) festgelegt werden. Führen Sie dazu die folgenden Befehle über das Terminal aus. Alle benötigten Verbindungsinformationen befinden sich im Azure-Portal (siehe [Herstellen einer Verbindung mit einer MySQL-Datenbank](../store-php-create-mysql-database.md#connect)).
   
        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
   
    Durch die Platzierung Ihrer Einstellungen in den Azure-App-Einstellungen bleiben vertrauliche Daten von Ihrer Quellcodeverwaltung (Git) getrennt. Als Nächstes konfigurieren Sie Ihre Entwicklungsumgebung für die Verwendung der gleichen Verbindungsinformationen.
5. Öffnen Sie „config/local.js“, und fügen Sie das folgende Verbindungsobjekt hinzu:
   
        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
   
    Diese Konfiguration überschreibt die Einstellungen in der Datei „config/connections.js“ für die lokale Umgebung. Die Datei wird durch die standardmäßige GITIGNORE-Datei in Ihrem Projekt ausgeschlossen und somit nicht in Git gespeichert. Nun können Sie sowohl über Ihre Azure-Web-App als auch über Ihre lokale Entwicklungsumgebung eine Verbindung mit Ihrer MySQL-Datenbank herstellen.
6. Öffnen Sie „config/env/production.js“, um Ihre Produktionsumgebung zu konfigurieren, und fügen Sie das folgende `models` -Objekt hinzu:
   
        models: {
            connection: 'mySql',
            migrate: 'safe'
        },
7. Öffnen Sie „config/env/development.js“, um Ihre Entwicklungsumgebung zu konfigurieren, und fügen Sie das folgende `models`-Objekt hinzu:
   
        models: {
            connection: 'mySql',
            migrate: 'alter'
        },
   
    `migrate: 'alter'` ermöglicht die Verwendung von Datenbankmigrationsfeatures zum problemlosen Erstellen und Aktualisieren der Datenbanktabellen in Ihrer MySQL-Datenbank. Für die Azure-Umgebung (Produktion) wird allerdings `migrate: 'safe'` verwendet, da „Sails.js“ die Verwendung von `migrate: 'alter'` in einer Produktionsumgebung nicht zulässt. (Weitere Informationen finden Sie in der  [Sails.js-Dokumentation](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).
8. [Generieren](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) Sie über das Terminal wie gewohnt eine Sails.js-[Blaupausen-API](http://sailsjs.org/documentation/concepts/blueprints). Führen Sie dann `sails lift` aus, um die Datenbank mit Sails.js-Datenbankmigration zu erstellen. Beispiel:
   
         sails generate api mywidget
         sails lift
   
    Das durch diesen Befehl erstellte `mywidget` -Modell ist zwar leer, wir können damit jedoch zeigen, dass eine Verbindung mit der Datenbank besteht.
    Wenn Sie `sails lift`ausführen, werden die fehlenden Tabellen für die von Ihrer App verwendeten Modelle erstellt.
9. Greifen Sie über den Browser auf die soeben erstellte Blaupausen-API zu. Beispiel:
   
        http://localhost:1337/mywidget/create
   
    Die API sollte den erstellen Eintrag im Browserfenster zurückgeben. So wissen Sie, dass die Datenbank erfolgreich erstellt wurde.
   
        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. Übertragen Sie Ihre Änderungen mittel Push an Azure, und navigieren Sie zu Ihrer App, um sich zu vergewissern, dass sie weiterhin funktioniert.
    
         git add .
         git commit -m "<your commit message>"
         git push azure master
         azure site browse
11. Greifen Sie auf die Blaupausen-API Ihrer Azure-Web-App zu. Beispiel:
    
         http://<appname>.azurewebsites.net/mywidget/create
    
     Wenn die API einen weiteren neuen Eintrag zurückgibt, kommuniziert Ihre Azure-Web-App mit Ihrer MySQL-Datenbank.

## <a name="more-resources"></a>Weitere Ressourcen
* [Erste Schritte mit Node.js-Web-Apps in Azure App Service](app-service-web-nodejs-get-started.md)
* [Verwenden von Node.js-Modulen mit Azure-Anwendungen](../nodejs-use-node-modules-azure-apps.md)

<!--HONumber=Oct16_HO2-->


