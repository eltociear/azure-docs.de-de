---
title: Von Media Services empfohlene Livestreaming-Encoder – Azure | Microsoft-Dokumentation
description: Erfahren Sie etwas über die von Media Services empfohlenen lokalen Livestreaming-Encoder.
services: media-services
keywords: Codierung; Encoder; Medien
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 5e16f1fb948ddb435c5002c16125b36fa61d50a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336242"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>Getestete lokale Livestreamingencoder

In Azure Media Services stellt ein [Liveereignis](https://docs.microsoft.com/rest/api/media/liveevents) (Kanal) eine Pipeline zum Verarbeiten von Livestreaminginhalten dar. Es gibt zwei Arten, auf die Live-Eingabestreams vom Liveereignis empfangen werden können.

* Von einem lokalen Liveencoder wird ein RTMP- oder Smooth Streaming-Datenstrom (fragmentiertes MP4) mit Mehrfachbitrate an das Liveereignis gesendet, das nicht für die Livecodierung mit Media Services aktiviert ist. Die erfassten Streams durchlaufen Liveereignisse ohne weitere Verarbeitung. Diese Methode wird als **Pass-Through-Methode** bezeichnet. Um auf Clientseite ein Streaming mit adaptiver Bitrate zu ermöglichen, sollte der Liveencoder anstelle eines Einzelbitraten-Datenstroms Mehrfachbitraten-Datenströme an ein Pass-Through-Liveereignis senden. 

    Bei Verwendung von Mehrfachbitraten-Datenströmen für das Pass-Through-Liveereignis müssen die Video-GOP-Größe und die Videofragmente unterschiedlicher Bitraten synchronisiert werden, um ein unerwartetes Verhalten bei der Wiedergabe zu vermeiden.

  > [!TIP]
  > Die Verwendung der Pass-Through-Methode ist die wirtschaftlichste Form des Livestreamings.
 
* Ein lokaler Liveencoder sendet einen Single-Bitrate-Datenstrom an das Liveereignis, das zum Ausführen der Livecodierung mit Media Services in einem der folgenden Formate aktiviert wurde: RTMP oder Smooth Streaming (fragmentiertes MP4). Vom Liveereignis wird dann eine Livecodierung des Eingabestreams mit Einzelbitrate in einen (adaptiven) Videostream mit Mehrfachbitrate ausgeführt.

In diesem Artikel werden getestete lokale Livestreamingencoder behandelt. Anweisungen zum Überprüfen Ihres lokalen Liveencoders finden Sie unter [Überprüfen Ihres lokalen Encoders](become-on-premises-encoder-partner.md).

Ausführliche Informationen zur Livecodierung mit Media Services finden Sie unter [Livestreaming mit Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Anforderungen für Encoder

Encoder müssen TLS 1.2 unterstützen, wenn HTTPS- oder RTMPS-Protokolle verwendet werden.

## <a name="live-encoders-that-output-rtmp"></a>Liveencoder mit RTMP-Ausgabe

Media Services empfiehlt die Verwendung eines der nachfolgenden Liveencoder mit RTMP-Ausgabe. Die unterstützten URL-Schemas sind `rtmp://` und `rtmps://`.

Überprüfen Sie beim Streamen per RTMP in den Firewall- und/oder Proxyeinstellungen, ob die ausgehenden TCP-Ports 1935 und 1936 geöffnet sind.<br/><br/>
Überprüfen Sie beim Streamen per RTMPS in den Firewall- und/oder Proxyeinstellungen, ob die ausgehenden TCP-Ports 2935 und 2936 geöffnet sind.

> [!NOTE]
> Bei Verwendung von RTMPS-Protokollen müssen Encoder TLS 1.2 unterstützen.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (Version 2.14.15 und höher)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (Version 13.0.2 oder höher aufgrund der TLS 1.2-Anforderung)
- Telestream Wirecast S (nur RTMP wird unterstützt)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 und Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Liveencoder mit Ausgabe im fragmentierten MP4-Format

Media Services empfiehlt den Einsatz eines der nachfolgenden Liveencoder, der Smooth Streaming mit Mehrfachbitrate (fragmentiertes MP4) ausgibt. Die unterstützten URL-Schemas sind `http://` und `https://`.

> [!NOTE]
> Bei Verwendung von HTTPS-Protokollen müssen Encoder TLS 1.2 unterstützen.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (Version 2.14.15 und höher aufgrund der TLS 1.2-Anforderung)
- Envivio-4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live und Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Wenn Sie Liveveranstaltungen in mehreren Sprachen streamen (z.B. eine englische Audiospur und eine spanische Audiospur), können Sie dies mit dem Media Excel Live Encoder erreichen, der so konfiguriert ist, dass er den Livefeed an ein Pass-Through-Liveereignis sendet.

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurieren von lokalen Liveencodereinstellungen

Informationen über die gültigen Einstellungen für Ihren Liveereignistyp finden Sie unter [Vergleich von Liveereignistypen](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Wiedergabeanforderungen

Damit Inhalte wiedergegeben werden können, müssen sowohl ein Audio- als auch ein Videostream vorhanden sein. Die Wiedergabe nur des Videostreams wird nicht unterstützt.

### <a name="configuration-tips"></a>Konfigurationstipps

- Verwenden Sie nach Möglichkeit eine Kabelverbindung zum Internet.
- Zum Bestimmen der erforderlichen Bandbreite müssen die Streamingbitraten verdoppelt werden. Wenngleich nicht bindend, ist diese einfache Regel hilfreich, um Netzwerküberlastungen zu verhindern.
- Bei der Verwendung softwarebasierter Encoder schließen Sie alle nicht benötigten Programme.
- Das Ändern der Encoderkonfiguration, nachdem die Pushübertragung begonnen hat, führt zu negativen Auswirkungen auf das Ereignis. Durch Konfigurationsänderungen kann das Ereignis instabil werden. 
- Nehmen Sie sich zum Einrichten Ihres Ereignisses unbedingt ausreichend Zeit. Für Großereignisse empfiehlt es sich, eine Stunde vor dem Ereignis mit dem Setup zu beginnen.
- Verwenden Sie die H.264-Video- und die AAC-Audiocodecausgabe.
- Stellen Sie sicher, dass eine zeitliche Ausrichtung anhand von Keyframe oder GOP übergreifend über Videoqualitäten hinweg vorliegt.
- Vergewissern Sie sich, dass jede Videoqualität durch einen eindeutigen Streamnamen gekennzeichnet ist.
- Verwenden Sie strenge CBR-Codierung, die für eine optimale Leistung bei adaptiver Bitrate empfohlen wird.

> [!IMPORTANT]
> Beobachten Sie die Hardwareressourcen des Computers (CPU, Arbeitsspeicher usw.), da das Hochladen von Fragmenten in die Cloud CPU- und E/A-Vorgänge mit sich bringt. Wenn Sie Einstellungen im Encoder ändern, achten Sie darauf, die Kanäle/Liveereignisse zurückzusetzen, damit die Änderung wirksam wird.

## <a name="see-also"></a>Weitere Informationen

[Livestreaming mit Media Services v3](live-streaming-overview.md)

## <a name="next-steps"></a>Nächste Schritte

[Überprüfen des Encoders](become-on-premises-encoder-partner.md)
