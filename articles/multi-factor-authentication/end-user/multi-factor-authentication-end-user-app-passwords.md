---
title: "Verwenden von App-Kennwörtern in Azure MFA | Microsoft-Dokumentation"
description: "Auf dieser Seite finden Benutzer Informationen zu App-Kennwörtern und zu deren Verwendung im Zusammenhang mit Azure MFA."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 55ca5ada0db30440e4599c77b7a6834ef671c7a4
ms.sourcegitcommit: 7d4b3cf1fc9883c945a63270d3af1f86e3bfb22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2018
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Welchen Zweck erfüllen App-Kennwörter bei Azure Multi-Factor Authentication?
Bestimmte Nicht-Browser-Apps, z. B. der systemeigene E-Mail-Client von Apple, der Exchange Active Sync verwendet, unterstützen derzeit keine Multi-Factor Authentication. Die Multi-Factor Authentication wird pro Benutzer aktiviert. Wenn die Multi-Factor Authentication für einen bestimmten Benutzer aktiviert wurde, können die betreffenden Nicht-Browser-Apps von diesem Benutzer nicht verwendet werden. Um dennoch eine Authentifizierung zu ermöglichen, wird ein App-Kennwort festgelegt. Wenn Sie die Multi-Factor Authentication über Richtlinien zum bedingten Zugriff und nicht über die MFA pro Benutzer erzwingen, können Sie keine Anwendungskennwörter erstellen. Anwendungen, die Richtlinien zum bedingten Zugriff für die Zugriffskontrolle verwenden, benötigen keine Anwendungskennwörter.

Sobald ein App-Kennwort erstellt wurde, verwenden Sie in den entsprechenden Nicht-Browser-Apps dieses anstelle Ihres ursprünglichen Kennworts. Der Grund dafür ist wie folgt: Wenn Sie sich für die zweistufige Überprüfung registrieren, weisen Sie Microsoft an, niemand sonst die Anmeldung mit Ihrem Kennwort zu erlauben, sofern nicht auch die zweite Überprüfung erfolgreich durchgeführt wird. Der native E-Mail-Client von Apple auf Ihrem Telefon kann sich nicht als Sie anmelden, da er die zweistufige Überprüfung nicht anfordern kann. Die Lösung besteht darin, ein sichereres App-Kennwort zu erstellen, das Sie nicht täglich, sondern nur für diese Apps verwenden, die die zweistufige Überprüfung nicht unterstützen können. Nutzen Sie das App-Kennwort, damit Apps die mehrstufige Authentifizierung umgehen und weiter funktionieren können.

> [!NOTE]
> Office 2013-Clients (einschließlich Outlook) unterstützen neue Authentifizierungsprotokolle und können für die zweistufige Überprüfung verwendet werden.  Damit sind nach der Aktivierung keine App-Kennwörter mehr für Office 2013-Clients erforderlich.  Weitere Informationen finden Sie unter [Office 2013 modern authentication public preview announced](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/) („Öffentliche Preview für moderne Authentifizierung in Office 2013“, in englischer Sprache).


## <a name="how-to-use-app-passwords"></a>Verwenden von App-Kennwörtern
Im Folgenden werden einige wichtige Punkte bei der Verwendung von App-Kennwörtern aufgeführt.

* Sie erstellen keine eigenen App-Kennwörter. Stattdessen werden sie automatisch generiert. Da Sie pro App nur einmal das App-Kennwort eingeben müssen, ist es sicherer, ein komplexeres, automatisch generiertes Kennwort zu nutzen, anstatt selbst eines festzulegen, das Sie sich merken können.
* Derzeit können pro Benutzer maximal 40 Kennwörter festgelegt werden. Wenn Sie versuchen, über diese Beschränkung hinaus weitere Kennwörter zu erstellen, werden Sie aufgefordert, eines der vorhandenen App-Kennwörter zu löschen, ehe das neue Kennwort erstellt werden kann.
* Verwenden Sie ein App-Kennwort geräte- und nicht anwendungsbezogen. Beispielsweise können Sie ein App-Kennwort für Ihren Laptop erstellen und dieses App-Kennwort für alle Anwendungen auf dem betreffenden Laptop verwenden. Erstellen Sie anschließend ein zweites App-Kennwort für alle Apps auf Ihrem Desktop.
* Sie erhalten ein App-Kennwort, wenn Sie sich erstmals für die zweistufige Überprüfung registrieren.  Wenn Sie weitere Kennwörter benötigen, können Sie diese bei Bedarf erstellen.



## <a name="creating-and-deleting-app-passwords"></a>Erstellen und Löschen von App-Kennwörtern
Während der ersten Anmeldung wird ein App-Kennwort erstellt, das Sie direkt verwenden können.  Darüber hinaus können Sie später zusätzliche App-Kennwörter erstellen und löschen.  Wie Sie dabei vorgehen, hängt davon ab, wie Sie Multi-Factor Authentication verwenden. Beantworten Sie die folgenden Fragen, um zu bestimmen, wo Sie App-Kennwörter verwalten sollten:

1. Verwenden Sie die zweistufige Überprüfung für Ihr persönliches Microsoft-Konto? Falls ja, lesen Sie den Artikel [App-Kennwörter und zweistufige Überprüfung](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification), um Hilfe zu erhalten. Falls nein, fahren Sie mit der 2. Frage fort.

2. Sie verwenden also die zweistufige Überprüfung für Ihr Geschäfts-, Schul- oder Unikonto. Verwenden Sie es zum Anmelden bei Office 365-Apps? Falls ja, lesen Sie [Erstellen eines App-Kennworts für Office 365](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183), um Hilfe zu erhalten. Falls nein, fahren Sie mit der 3. Frage fort.

3. Verwenden Sie die zweistufige Überprüfung mit Microsoft Azure? Falls ja, fahren Sie mit dem Abschnitt [Verwalten von App-Kennwörtern im Azure-Portal](#manage-app-passwords-in-the-Azure-portal) in diesem Artikel fort. Falls nein, fahren Sie mit der 4. Frage fort.

4. Sie sind nicht sicher, wo Sie die zweistufige Überprüfung verwenden? Fahren Sie mit dem Abschnitt [Verwalten von App-Kennwörtern im MyApps-Portal](#manage-app-passwords-with-the-myapps-portal) in diesem Artikel fort.


## <a name="manage-app-passwords-in-the-azure-portal"></a>Verwalten von App-Kennwörtern im Azure-Portal
Wenn Sie die zweistufige Überprüfung mit Azure verwenden, können Sie App-Kennwörter über das Azure-Portal erstellen.



## <a name="manage-app-passwords-with-the-myapps-portal"></a>Verwalten von App-Kennwörter im MyApps-Portal
Wenn Sie nicht sicher sind, wie Sie die mehrstufige Authentifizierung verwenden, können Sie App-Kenwörter stets über das MyApps-Portal erstellen und löschen.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>So erstellen Sie ein App-Kennwort mithilfe des MyApps-Portals
1. Melden Sie sich bei [https://myapps.microsoft.com](https://myapps.microsoft.com) an.
2. Klicken Sie rechts oben auf Ihren Namen, und wählen Sie **Profil**.
3. Wählen Sie **Zusätzliche Sicherheitsüberprüfung** aus.
   ![Auswählen von „Zusätzliche Sicherheitsüberprüfung“, Screenshot](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Wählen Sie **App-Kennwörter** aus.
   ![Auswählen von „App-Kennwörter“, Screenshot](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Klicken Sie auf **Create**.
6. Geben Sie einen Namen für das App-Kennwort an, und klicken Sie auf **Weiter**.
7. Kopieren Sie das App-Kennwort in die Zwischenablage, und fügen Sie es in Ihrer Anwendung hinzu.
   ![Erstellen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>So löschen Sie ein App-Kennwort mithilfe des MyApps-Portals
1. Melden Sie sich bei [https://myapps.microsoft.com](https://myapps.microsoft.com) an.
2. Wählen Sie oben das Profil aus.
3. Wählen Sie **Zusätzliche Sicherheitsüberprüfung** aus.

   ![Auswählen von „Zusätzliche Sicherheitsüberprüfung“, Screenshot](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Wählen Sie **App-Kennwörter** aus.

   ![Auswählen von „App-Kennwörter“, Screenshot](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Klicken Sie neben dem App-Kennwort, das Sie entfernen möchten, auf **Löschen**.

   ![Löschen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Bestätigen Sie, dass Sie dieses Kennwort löschen möchten, indem Sie auf **Ja** klicken.
7. Sobald das App-Kennwort gelöscht wurde, klicken Sie auf **Schließen**.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md)

- Probieren Sie die [Microsoft Authenticator-App](microsoft-authenticator-app-how-to.md) aus, um Ihre Anmeldungen mithilfe von App-Benachrichtigungen zu verifizieren, anstatt SMS oder Anrufe zu erhalten.
