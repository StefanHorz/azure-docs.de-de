---
title: Grundlegendes zum Azure IoT Hub-C2D-Messaging | Microsoft-Dokumentation
description: "Entwicklerhandbuch: Verwenden des C2D-Messaging mit IoT Hub. Enthält Informationen über den Lebenszyklus von Nachrichten und die Konfigurationsoptionen."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2017
ms.author: dobett
ms.openlocfilehash: 1b34e579f2ba40f4d77f7a3ba1841f59f795d292
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2018
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Senden von C2D-Nachrichten von IoT Hub

Für das Senden von unidirektionalen Benachrichtigungen von Ihrem Lösungs-Back-End an die Geräte-App senden Sie C2D-Nachrichten von Ihrer IoT Hub-Instanz an Ihr Gerät. Eine Erläuterung anderer C2D-Optionen, die von IoT Hub unterstützt werden, finden Sie im [Leitfaden zur C2D-Kommunikation][lnk-c2d-guidance].

Sie senden Cloud-zu-Gerät-Nachrichten (Cloud-to-Device, C2D) über einen dienstseitigen Endpunkt (**/messages/devicebound**). Ein Gerät empfängt die Nachrichten dann über einen gerätespezifischen Endpunkt (**/devices/{Geräte-ID}/messages/devicebound**).

Um jede C2D-Nachricht einem bestimmten Gerät zuzuordnen, legt IoT Hub die **to**-Eigenschaft auf **/devices/{Geräte-ID}/messages/devicebound** fest.

Jede Gerätewarteschlange kann maximal 50 C2D-Nachrichten enthalten. Der Versuch, eine größere Anzahl von Nachrichten an das gleiche Gerät zu senden, führt zu einem Fehler.

## <a name="the-cloud-to-device-message-lifecycle"></a>Der Lebenszyklus von C2D-Nachrichten

Um die Nachrichtenübermittlung mindestens einmal zu gewährleisten, werden C2D-Nachrichten in IoT Hub in Warteschlangen auf Gerätebasis beibehalten. Geräte müssen explizit den *Abschluss* bestätigen, damit IoT Hub sie aus der Warteschlange entfernen kann. Auf diese Weise wird Resilienz bei Verbindungs- und Gerätefehlern gewährleistet.

Die folgende Abbildung zeigt den Lebenszyklus einer C2D-Nachricht und ihren jeweiligen Status in IoT Hub.

![Lebenszyklus von C2D-Nachrichten][img-lifecycle]

Wenn der IoT Hub-Dienst eine Nachricht an ein Gerät sendet, legt der Dienst den Nachrichtenstatus auf **In Warteschlange eingereiht** fest. Wenn ein Gerät eine Nachricht *empfangen* möchte, *sperrt* IoT Hub die Nachricht (Status wird auf **Nicht sichtbar** gesetzt), um anderen Threads auf dem gleichen Gerät das Empfangen anderer Nachrichten zu ermöglichen. Wenn ein Gerätethread die Verarbeitung einer Nachricht abschließt, wird IoT Hub hierüber durch den *Abschluss* der Nachricht benachrichtigt. IoT Hub legt dann den Status auf **Abgeschlossen** fest.

Ein Gerät kann auch Folgendes durchführen:

* *Ablehnen* der Nachricht: IoT Hub versetzt sie in den Status **Unzustellbar**. Geräte, die über das MQTT-Protokoll eine Verbindung herstellen, können C2D-Nachrichten nicht ablehnen.
* *Verwerfen* der Nachricht: IoT Hub platziert die Nachricht wieder in der Warteschlange mit dem Status **Zur Warteschlange hinzugefügt**. Geräte, die über das MQTT-Protokoll eine Verbindung herstellen, können C2D-Nachrichten nicht ablehnen.

Bei der Nachrichtenverarbeitung durch den Thread könnte ein Fehler auftreten, ohne dass IoT Hub hierüber benachrichtigt wird. In diesem Fall werden Nachrichten automatisch vom Status **Nicht sichtbar** zurück in den Status **Enqueued** (Zur Warteschlange hinzugefügt) versetzt, wenn ein *Timeout für die Sichtbarkeit (oder Sperrung)* abgelaufen ist. Der Standardwert dieses Timeouts ist eine Minute.

Die Eigenschaft **Anzahl maximaler Zustellungen** in IoT Hub bestimmt, wie oft eine Nachricht zwischen den Statuswerten **In Warteschlange eingereiht** und **Nicht sichtbar** wechseln kann. Nachdem diese Anzahl überschritten wurde, legt IoT Hub den Status der Nachricht auf **Unzustellbar** fest. Ebenso kennzeichnet IoT Hub eine Nachricht nach deren Ablaufzeitpunkt als **Unzustellbar** (siehe [Gültigkeitsdauer][lnk-ttl]).

In [Senden von C2D-Nachrichten mit IoT Hub][lnk-c2d-tutorial] erfahren Sie, wie Sie C2D-Nachrichten über IoT Hub aus der Cloud senden und auf einem Gerät empfangen.

Typischerweise werden C2D-Nachrichten immer dann vom Gerät abgeschlossen, wenn der Verlust der Nachricht keine Auswirkung auf die Anwendungslogik hat. Dies trifft z.B. zu, wenn das Gerät den Nachrichteninhalt lokal gespeichert hat oder ein Vorgang erfolgreich ausgeführt wurde. Die Nachricht könnte auch vorübergehende Informationen übermitteln, deren Verlust die Funktionalität der Anwendung nicht beeinträchtigen würde. Für Aufgaben mit langer Ausführungszeit können Sie:

* Die C2D-Nachricht nach Beibehalten der Aufgabenbeschreibung im lokalen Speicher abschließen.
* Das Lösungs-Back-End mithilfe von D2C-Nachrichten in verschiedenen Phasen des Aufgabenverlaufs benachrichtigen.

## <a name="message-expiration-time-to-live"></a>Nachrichtenablauf (Gültigkeitsdauer)

Jede C2D-Nachricht verfügt über eine Gültigkeitsdauer. Dieser Zeitraum wird mit einer der folgenden Alternativen festgelegt:

* Der **ExpiryTimeUtc**-Eigenschaft im Dienst.
* IoT Hub verwendet die als IoT Hub-Eigenschaft angegebene standardmäßige *Gültigkeitsdauer*.

Siehe [Optionen für die C2D-Konfiguration][lnk-c2d-configuration].

Eine gängige Methode, den Vorteil des Nachrichtenablaufs zu nutzen und zu vermeiden, dass Nachrichten an getrennte Geräte gesendet werden, ist die Festlegung einer kurzen Gültigkeitsdauer für Werte. Bei diesem Ansatz wird das gleiche Ergebnis erzielt wie beim Aufrechterhalten des Geräteverbindungsstatus, er ist aber effizienter. Wenn Sie Nachrichtenbestätigungen anfordern, teilt IoT Hub Ihnen mit, für welche Geräte Folgendes zutrifft:

* In der Lage, Nachrichten zu empfangen.
* Nicht online oder fehlerhaft.

## <a name="message-feedback"></a>Nachrichtenfeedback

Beim Senden einer C2D-Nachricht kann der Dienst das Übermitteln von Feedback auf Nachrichtenbasis anfordern, um über den finalen Status dieser Nachricht informiert zu werden.

| Ack-Eigenschaft | Verhalten |
| ------------ | -------- |
| **positive** | Wenn die Cloud-zu-Gerät-Nachricht den Status **Abgeschlossen** erreicht, generiert IoT Hub eine Feedbacknachricht. |
| **negative** | Wenn die Cloud-zu-Gerät-Nachricht den Status **Unzustellbar** erreicht, generiert IoT Hub eine Feedbacknachricht. |
| **full**     | IoT Hub generiert in beiden Fällen eine Feedbacknachricht. |

Wenn **Ack** auf **Voll** festgelegt ist und Sie keine Feedbacknachricht erhalten, bedeutet dies, dass die Feedbacknachricht abgelaufen ist. Der Dienst kann nicht wissen, was mit der ursprünglichen Nachricht geschehen ist. In der Praxis sollte ein Dienst sicherstellen, dass Feedback verarbeitet werden kann, bevor es abläuft. Die maximale Ablaufzeit beträgt zwei Tage, sodass ausreichend Zeit verbleibt, um den Dienst wieder zu starten, wenn ein Fehler auftritt.

Wie im Abschnitt [Endpunkte][lnk-endpoints] erläutert, übermittelt IoT Hub Feedback in Form von Nachrichten über einen dienstseitigen Endpunkt (**/messages/servicebound/feedback**). Die Semantik für den Empfang von Feedback stimmt mit der für C2D-Nachrichten überein und weist den gleichen [Nachrichtenlebenszyklus][lnk-lifecycle] auf. Nachrichtenfeedback wird nach Möglichkeit in einer einzigen Nachricht zusammengefasst, die das folgende Format aufweist:

| Eigenschaft     | BESCHREIBUNG |
| ------------ | ----------- |
| EnqueuedTime | Zeitstempel, der die Erstellung der Nachricht angibt. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Der Nachrichtenkörper ist ein serialisiertes JSON-Array aus Datensätzen, von denen jeder die folgenden Eigenschaften aufweist:

| Eigenschaft           | BESCHREIBUNG |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Zeitstempel, der den Zeitpunkt des Nachrichtenergebnisses angibt. Diese Eigenschaft kann beispielsweise angeben, wann das Gerät abgeschlossen wurde oder die Nachricht abgelaufen ist. |
| OriginalMessageId  | **MessageId** der C2D-Nachricht, auf die sich das Feedback bezieht. |
| StatusCode         | Erforderliche Zeichenfolge. In von IoT Hub erzeugten Feedbacknachrichten verwendet. <br/> 'Success' <br/> 'Expired' <br/> 'DeliveryCountExceeded' <br/> 'Rejected' <br/> 'Purged' |
| BESCHREIBUNG        | Zeichenfolgenwerte für **StatusCode**. |
| deviceId           | **DeviceId** des Zielgeräts für die C2D-Nachricht, auf die sich das Feedback bezieht. |
| DeviceGenerationId | **DeviceGenerationId** des Zielgeräts für die C2D-Nachricht, auf die sich das Feedback bezieht. |

Der Dienst muss eine **MessageId** für die C2D-Nachricht angeben, damit das Feedback der ursprünglichen Nachricht zugeordnet werden kann.

Das folgende Beispiel zeigt den Text einer Feedbacknachricht.

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Optionen für die C2D-Konfiguration

Jede IoT Hub-Instanz legt die folgenden Konfigurationsoptionen für das C2D-Messaging offen:

| Eigenschaft                  | BESCHREIBUNG | Bereich und Standardwert |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standardmäßige Gültigkeitsdauer für C2D-Nachrichten. | ISO_8601-Intervall bis 2D (mindestens 1 Minute). Standardwert: 1 Stunde. |
| maxDeliveryCount          | Maximale Zustellungsanzahl für C2D-Gerätewarteschlangen pro Gerät. | 1 bis 100. Standardwert: 10 |
| feedback.ttlAsIso8601     | Aufbewahrungsdauer für dienstgebundene Feedbacknachrichten. | ISO_8601-Intervall bis 2D (mindestens 1 Minute). Standardwert: 1 Stunde. |
| feedback.maxDeliveryCount |Maximale Zustellungsanzahl für Feedbackwarteschlangen. | 1 bis 100. Standardwert: 100. |

Weitere Informationen zum Festlegen dieser Konfigurationsoptionen finden Sie unter [Erstellen von IoT Hub-Instanzen][lnk-portal].

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den SDKs, die Sie für den Empfang von C2D-Nachrichten verwenden können, finden Sie unter [Azure IoT-SDKs][lnk-sdks].

Wenn Sie den Empfang von C2D-Nachrichten testen möchten, absolvieren Sie das Tutorial [Senden von C2D-Nachrichten][lnk-c2d-tutorial].

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
