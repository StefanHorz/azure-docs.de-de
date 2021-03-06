---
title: "Entwicklerhandbuch für Azure IoT Hub | Microsoft-Dokumentation"
description: "Das Azure IoT Hub-Entwicklerhandbuch umfasst Erläuterungen zu Endpunkten, Sicherheit, Identitätsregistrierung, Geräteverwaltung, direkten Methoden, Gerätezwillingen, Dateiuploads, Aufträgen, die Abfragesprache von IoT Hub und Messaging."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 37f9da7dcc8dd527fe0bfbf2fbcc40a3ba0e8a1c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-iot-hub-developer-guide"></a>Entwicklungsleitfaden für Azure IoT Hub

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht.

Azure IoT Hub bietet Ihnen Folgendes:

* Eine sichere Kommunikation unter Verwendung von Zugriffssteuerung und Sicherheitsanmeldeinformationen auf Gerätebasis.
* Zahlreiche Optionen für hyperskalierbare Kommunikation zwischen Geräten und Cloud (Device-to-Cloud, D2C) sowie zwischen Cloud und Geräten (Cloud-to-Device, C2D)
* Abfragbarer Speicher mit Zustandsinformationen und Metdadaten auf Gerätebasis
* Eine problemlose Geräteanbindung mithilfe von Gerätebibliotheken für die gängigsten Sprachen und Plattformen

Dieses IoT Hub-Entwicklerhandbuch umfasst die folgenden Artikel:

* Die Informationen unter [Device-to-cloud communication guidance][lnk-d2c-guidance] (Leitfaden zur D2C-Kommunikation) erleichtern die Wahl zwischen D2C-Nachrichten, den gemeldeten Eigenschaften des Gerätezwillings und dem Dateiupload.
* Die Informationen unter [Cloud-to-device communication guidance][lnk-c2d-guidance] (Leitfaden zur C2D-Kommunikation) erleichtern die Wahl zwischen direkten Methoden, den gewünschten Eigenschaften des Gerätezwillings und C2D-Nachrichten.
* Unter [Messaging zwischen Geräten und Cloud mit IoT Hub][devguide-messaging] werden die Messagingfeatures (D2C und C2D) beschrieben, die mit IoT Hub zur Verfügung stehen.
  * [Senden von D2C-Nachrichten an IoT Hub][devguide-messages-d2c]
  * [Lesen von D2C-Nachrichten vom integrierten Endpunkt][devguide-builtin]
  * [Verwenden von benutzerdefinierten Endpunkten und Routingregeln für D2C-Nachrichten][devguide-custom]
  * [Senden von C2D-Nachrichten von IoT Hub][devguide-messages-c2d]
  * [Erstellen und Lesen von IoT Hub-Nachrichten][devguide-format]
* Unter [Upload files from a device][devguide-upload] (Hochladen von Dateien von einem Gerät) wird beschrieben, wie Sie Dateien von einem Gerät hochladen können. Dieser Artikel bietet auch Informationen zu Themen wie Benachrichtigungen, die beim Hochladevorgang gesendet werden können.
* Unter [Grundlegendes zur Identitätsregistrierung in Ihrer IoT Hub-Instanz][devguide-identities] wird beschrieben, welche Informationen in jeder IoT Hub-Identitätsregistrierung gespeichert werden. Zudem wird im Artikel beschrieben, wie Sie darauf zugreifen und diese bearbeiten können.
* Unter [Verwalten des Zugriffs auf IoT Hub][devguide-security] wird das Sicherheitsmodell beschrieben, mit dem sowohl für Geräte als auch für Cloudkomponenten Zugriff auf IoT Hub-Funktionalität gewährt werden. Der Artikel enthält Informationen zum Verwenden von Token und X.509-Zertifikaten sowie Details zu den Berechtigungen, die Sie erteilen können.
* [Verwenden von Gerätezwillingen zum Synchronisieren von Status und Konfigurationen][devguide-device-twins] beschreibt das Konzept der *Gerätezwillinge*. Zudem finden Sie in diesem Artikel auch Informationen zur Funktionalität von Gerätezwillingen, z.B. zur Synchronisierung eines Geräts mit dessen Gerätezwilling. Der Artikel enthält Informationen zu den Daten, die in einem Gerätezwilling gespeichert sind.
* [Aufrufen einer direkten Methode auf einem Gerät][devguide-directmethods] beschreibt den Lebenszyklus einer direkten Methode. Der Artikel beschreibt, wie Sie Methoden für ein Gerät aus Ihrer Back-End-App aufrufen und die direkte Methode für Ihr Gerät anwenden.
* Unter [Schedule jobs on multiple devices][devguide-jobs] (Planen von Aufträgen auf mehreren Geräten) wird beschrieben, wie Sie Aufträge auf mehreren Geräten planen können. Der Artikel erläutert das Übermitteln von Aufträgen zum Erledigen von Aufgaben wie dem Ausführen einer direkten Methode und Aktualisieren eines Geräts mithilfe eines Gerätezwillings. Außerdem wird erklärt, wie der Status eines Auftrags abgefragt wird.
* In [Referenz – Auswählen eines Kommunikationsprotokolls][devguide-protocol] werden die Kommunikationsprotokolle beschrieben, die IoT Hub für die Gerätekommunikation unterstützt. Außerdem werden die Ports aufgeführt, die geöffnet werden müssen.
* Unter [Reference - IoT Hub endpoints][devguide-endpoints] (Referenz – IoT Hub-Endpunkte) werden die verschiedenen Endpunkte beschrieben, die jeder IoT-Hub für Laufzeit- und Verwaltungsvorgänge bereitstellt. Außerdem wird beschrieben, wie Sie zusätzliche Endpunkte in Ihrem IoT-Hub erstellen können, und wie Sie mit einem Feldgateway die Konnektivität mit Ihren IoT Hub-Endpunkten in nicht standardmäßigen Szenarien aktivieren.
* Unter [Referenz – IoT Hub-Abfragesprache für Gerätezwillinge, Aufträge und Nachrichtenrouting][devguide-query] wird die IoT Hub-Abfragesprache beschrieben, mit der Sie von Ihrem Hub Informationen über Gerätezwillinge und Aufträge abrufen können.
* Unter [Referenz: IoT Hub-Kontingente und -Drosselung][devguide-quotas] sind die im IoT Hub-Dienst festgelegten Kontingente und Drosselung zusammengefasst, die beim Überschreiten eines Kontingents auftritt.
* [Referenz – Preise][devguide-pricing] enthält allgemeine Informationen zu verschiedenen SKUs und Preise für IoT Hub sowie Details zur Erfassung der verschiedenen IoT Hub-Funktionen als Nachrichten durch IoT Hub.
* Unter [Referenz – Geräte- und Dienst-SDKs][devguide-sdks] sind die Azure IoT SDKs aufgelistet, die Sie bei der Entwicklung von Geräte- und Dienst-Apps für die Interaktion mit Ihrer IoT Hub-Instanz verwenden können. Der Artikel enthält Links zur online verfügbaren API-Dokumentation.
* [IoT Hub-MQTT-Unterstützung][devguide-mqtt] enthält detaillierte Informationen zur Unterstützung des MQTT-Protokolls durch IoT Hub. Der Artikel beschreibt die Unterstützung des in die Azure IoT SDKs integrierten MQTT-Protokolls und bietet Informationen zur direkten Verwendung des MQTT-Protokolls.
* [Glossar][devguide-glossary]: Eine Liste der allgemeinen auf IoT Hub bezogenen Begriffe.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
