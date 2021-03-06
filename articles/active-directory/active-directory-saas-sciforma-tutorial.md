---
title: 'Tutorial: Azure Active Directory-Integration mit Sciforma | Microsoft Docs'
description: "Hier erfahren Sie, wie Sie Sciforma mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: abbfb5ac-7687-4153-b263-8090102dae37
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a61552f3275aa58aca9c014d24ae69d91ce59d5


---
# <a name="tutorial-azure-ad-integration-with-sciforma"></a>Tutorial: Azure AD-Integration mit Sciforma
In diesem Tutorial wird die Integration von Azure und Sciforma erläutert.  
Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Ein gültiges Azure-Abonnement
* Ein Sciforma-Mandant

Nach Abschluss dieses Lernprogramms können sich die Sciforma zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer Sciforma-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1. Aktivieren der Anwendungsintegration für Sciforma
2. Konfigurieren der einmaligen Anmeldung
3. Konfigurieren der Benutzerbereitstellung
4. Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-sciforma-tutorial/IC777369.png "Scenario")

## <a name="enabling-the-application-integration-for-sciforma"></a>Aktivieren der Anwendungsintegration für Sciforma
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Sciforma aktivieren.

### <a name="to-enable-the-application-integration-for-sciforma-perform-the-following-steps"></a>So aktivieren Sie die Anwendungsintegration für Sciforma:
1. Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")
2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.
3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen** .
   
   ![Anwendungen](./media/active-directory-saas-sciforma-tutorial/IC700994.png "Applications")
4. Klicken Sie unten auf der Seite auf **Hinzufügen** .
   
   ![Anwendung hinzufügen](./media/active-directory-saas-sciforma-tutorial/IC749321.png "Add application")
5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.
   
   ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-sciforma-tutorial/IC749322.png "Add an application from gallerry")
6. Geben Sie im **Suchfeld** das Wort **Sciforma** ein.
   
   ![Anwendungskatalog](./media/active-directory-saas-sciforma-tutorial/IC777370.png "Application gallery")
7. Wählen Sie im Ergebnisbereich **Sciforma** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.
   
   ![Sciforma](./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
   
   ## <a name="configuring-single-sign-on"></a>Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Sciforma zu authentifizieren.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>So konfigurieren Sie einmaliges Anmelden
1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Sciforma** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.
   
   ![Configure single sign-on](./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configure single sign-on")
2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Sciforma anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.
   
   ![Configure single sign-on](./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configure single sign-on")
3. Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Sciforma-Anmelde-URL** die URL im Format *https://\<Mandantenname\>.Sciforma.com* ein, und klicken Sie dann auf **Weiter**.
   
   ![App-URL konfigurieren](./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configure app URL")
4. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Sciforma** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal unter **C:\\SciformaMetaData.xml**.
   
   ![Configure single sign-on](./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configure single sign-on")
5. Leiten Sie die Metadatendatei an das Supportteam von Sciforma weiter. Das Supportteam muss ein einmaliges Anmelden für Sie konfigurieren.
6. Bestätigen Sie die Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.
   
   ![Configure single sign-on](./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Konfigurieren der Benutzerbereitstellung

Für das Konfigurieren der Benutzerbereitstellung in Sciforma steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Sciforma anzumelden, überprüft Sciforma, ob der Benutzer vorhanden ist.  
Ist noch kein Benutzerkonto verfügbar, wird es von Sciforma automatisch erstellt.

## <a name="assigning-users"></a>Zuweisen von Benutzern
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

### <a name="to-assign-users-to-sciforma-perform-the-following-steps"></a>So weisen Sie Sciforma Benutzer zu:
1. Erstellen Sie im klassischen Azure-Portal ein Testkonto.
2. Klicken Sie auf der Anwendungsintegrationsseite für **Sciforma** auf **Benutzer zuweisen**.
   
   ![Benutzer zuweisen](./media/active-directory-saas-sciforma-tutorial/IC777377.png "Assign users")
3. Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.
   
   ![Ja](./media/active-directory-saas-sciforma-tutorial/IC767830.png "Yes")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


