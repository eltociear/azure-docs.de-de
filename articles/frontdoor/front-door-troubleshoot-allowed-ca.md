---
title: Zulässige Zertifizierungsstelle für die Aktivierung von benutzerdefiniertem HTTPS für Azure Front Door Service
description: Wenn Sie Ihr eigenes Zertifikat zur Aktivierung von HTTPS in einer benutzerdefinierten Domäne verwenden, müssen Sie für die Erstellung eine zulässige Zertifizierungsstelle (CA) verwenden.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 62420889d9a4cb1e9d1c570a0845c704fca56cb3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184581"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Zulässige Zertifizierungsstellen für die Aktivierung von benutzerdefiniertem HTTPS für Azure Front Door Service

Wenn Sie für eine benutzerdefinierte Azure Front Door Service-Domäne [die HTTPS-Funktion durch Verwendung Ihres eigenen Zertifikats aktivieren](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), müssen Sie für die Erstellung Ihres SSL-Zertifikats eine zulässige Zertifizierungsstelle (CA) verwenden. Bei der Verwendung einer unzulässigen Zertifizierungsstelle oder eines selbstsignierten Zertifikats wird Ihre Anforderung abgelehnt.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
