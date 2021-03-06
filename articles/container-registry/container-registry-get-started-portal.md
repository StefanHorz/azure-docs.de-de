---
title: "Schnellstartanleitung: Erstellen einer privaten Docker-Registrierung in Azure über das Azure-Portal"
description: "Hier lernen Sie, wie Sie ganz schnell eine private Docker-Containerregistrierung über das Azure-Portal erstellen."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/06/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: eaf935c1060e53673351936111083d8bb44f05e7
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Erstellen einer Containerregistrierung mit dem Azure-Portal

Eine Azure Container Registry-Instanz ist eine private Docker-Registrierung in Azure, in der Sie Ihre privaten Docker-Containerimages speichern und verwalten können. In dieser Schnellstartanleitung erstellen Sie eine Containerregistrierung über das Azure-Portal.

Diese Schnellstartanleitung setzt eine lokale Docker-Installation voraus. Für Docker sind Pakete erhältlich, mit denen Docker problemlos auf einem [Mac-][docker-mac], [Windows-][docker-windows] oder [Linux-][docker-linux]System konfiguriert werden kann.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter „https://portal.azure.com“ am Azure-Portal an.

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

Klicken Sie auf **Neu** > **Container** > **Azure Container Registry**.

![Erstellen einer Containerregistrierung über das Azure-Portal][qs-portal-01]

Geben Sie Werte für **Registrierungsname** und **Ressourcengruppe** ein. Der Registrierungsname muss innerhalb von Azure eindeutig sein und zwischen 5 und 50 alphanumerische Zeichen enthalten. Erstellen Sie eine neue Ressourcengruppe namens `myResourceGroup`, und wählen Sie für **SKU** die Option „Basic“ aus. Klicken Sie auf **Erstellen**, um die ACR-Instanz bereitzustellen.

![Erstellen einer Containerregistrierung über das Azure-Portal][qs-portal-03]

In dieser Schnellstartanleitung erstellen wir eine *Basic*-Registrierung. Azure Container Registry steht in mehreren unterschiedlichen SKUs zur Verfügung, die in der folgenden Tabelle kurz beschrieben werden. Ausführliche Details zu den einzelnen Einträgen finden Sie unter [Containerregistrierungs-SKUs][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Wenn die Meldung **Bereitstellung erfolgreich** erscheint, wählen Sie die Containerregistrierung im Portal aus, und klicken Sie anschließend auf **Zugriffsschlüssel**.

![Erstellen einer Containerregistrierung über das Azure-Portal][qs-portal-05]

Klicken Sie unter **Administratorbenutzer** auf **Aktivieren**. Notieren Sie sich die folgenden Werte:

* Anmeldeserver
* Username
* password

Diese Werte benötigen Sie in den folgenden Schritten, wenn Sie über die Docker-Befehlszeilenschnittstelle Aktionen für Ihre Registrierung ausführen.

![Erstellen einer Containerregistrierung über das Azure-Portal][qs-portal-06]

## <a name="log-in-to-acr"></a>Anmelden bei ACR

Bevor Sie Push- und Pullvorgänge für Containerimages ausführen können, müssen Sie sich bei der ACR-Instanz anmelden. Verwenden Sie hierzu den Befehl [docker login][docker-login]. Ersetzen Sie *username* (Benutzername), *password* (Kennwort) und *login server* (Anmeldeserver) durch die Werte, die Sie sich im vorherigen Schritt notiert haben.

```bash
docker login --username <username> --password <password> <login server>
```

Der Befehl gibt nach Abschluss des Vorgangs `Login Succeeded` zurück. Möglicherweise wird auch eine Sicherheitswarnung angezeigt, in der die Verwendung des `--password-stdin`-Parameters empfohlen wird. Obwohl in diesem Artikel nicht auf dessen Verwendung eingegangen werden kann, wird empfohlen, diese bewährte Methode anzuwenden. Weitere Informationen zum Befehl [docker login][docker-login] finden Sie in der entsprechenden Referenzdokumentation.

## <a name="push-image-to-acr"></a>Übertragen eines Images an ACR mithilfe von Push

Um ein Image mithilfe von Push an Ihre Azure Container Registry-Instanz übertragen zu können, benötigen Sie zunächst ein Image. Führen Sie bei Bedarf den folgenden Befehl aus, um ein vorhandenes Image von Docker Hub abzurufen:

```bash
docker pull microsoft/aci-helloworld
```

Vor dem Übertragen an die Registrierung muss das Image mit dem ARC-Anmeldeservernamen markiert werden. Markieren Sie das Image mithilfe des Befehls [docker tag][docker-tag]. Ersetzen Sie *login server* durch den Anmeldeservernamen, den Sie sich zuvor notiert haben.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Nun können Sie das Image mit [docker push][docker-push] per Pushvorgang an die ACR-Instanz übertragen. Ersetzen Sie *login server* durch den Anmeldeservernamen Ihrer ACR-Instanz.

```
docker push <login server>/aci-helloworld:v1
```

Beispielausgabe nach erfolgreicher Ausführung des Befehls `docker push`:

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Auflisten von Containerimages

Navigieren Sie zum Auflisten der Images Ihrer ACR-Instanz im Portal zu Ihrer Registrierung, klicken Sie auf **Repositorys**, und wählen Sie das Repository aus, das Sie mit `docker push` erstellt haben.

In diesem Beispiel wählen wir das Repository **aci-helloworld** aus. Das mit `v1` markierte Image wird unter **TAGS** angezeigt.

![Erstellen einer Containerregistrierung über das Azure-Portal][qs-portal-09]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe **myResourceGroup**, wenn Sie sie nicht mehr benötigen. Dadurch werden die Ressourcengruppe, die ACR-Instanz und alle Containerimages gelöscht.

![Erstellen einer Containerregistrierung über das Azure-Portal][qs-portal-08]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Azure Container Registry-Instanz über das Azure-Portal erstellt. Wenn Sie Azure Container Registry mit Azure Container Instances verwenden möchten, fahren Sie mit dem Tutorial zu Azure Container Instances fort.

> [!div class="nextstepaction"]
> [Azure Container Instances-Tutorial][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
