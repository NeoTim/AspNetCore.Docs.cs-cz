---
title: Použití ASP.NET Core s HTTP/2 na IIS
author: rick-anderson
description: Naučte se používat funkce HTTP/2 ve službě IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 1c3748eeead1b8cccdb6112150cf90fb5597b689
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654367"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>Použití ASP.NET Core s HTTP/2 na IIS

Od [Justin Kotalik](https://github.com/jkotalik)

[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:

* Windows Server 2016 nebo novější/Windows 10 nebo novější
* IIS 10 nebo novější
* Připojení TLS 1,2 nebo novější
* Při [hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model): připojení hraničního serveru s přístupem k serveru používá protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.

Pro nasazení v rámci procesu, když je vytvořeno připojení HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2` . Pro nasazení mimo procesy, když je navázáno připojení HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1` .

Další informace o modelech hostování v procesu a mimoprocesové procesy naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .

Protokol HTTP/2 je ve výchozím nastavení povolen pro připojení pomocí protokolu HTTPS/TLS. Pokud není navázáno připojení HTTP/2, připojení se vrátí k HTTP/1.1. Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="advanced-http2-features-to-support-grpc"></a>Rozšířené funkce protokolu HTTP/2 pro podporu gRPC

Další funkce HTTP/2 ve službě IIS podporují gRPC, včetně podpory pro přívěsy odpovědí a odesílání snímků pro resetování.

Požadavky na spuštění gRPC ve službě IIS:

* Hostování v procesu.
* Windows 10, Build operačního systému 20300,1000 nebo novější. Může vyžadovat použití buildů Windows Insider.
* Připojení TLS 1,2 nebo novější

### <a name="trailers"></a>Karoseri

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Resetovat

[!INCLUDE[](~/includes/reset.md)]
