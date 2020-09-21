---
title: Testování gRPC služeb pomocí gRPCurl v ASP.NET Core
author: jamesnk
description: Naučte se testovat služby pomocí nástrojů gRPC. gRPCurl nástroj příkazového řádku pro interakci s gRPC službami. gRPCui je interaktivní webové uživatelské rozhraní.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: 800b320413552e73f05e0359e67eeb2caf4e0e2a
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770165"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>Testování gRPC služeb pomocí gRPCurl v ASP.NET Core

Od [James Newton – král](https://twitter.com/jamesnk)

K dispozici jsou nástroje pro gRPC, které vývojářům umožňují testovat služby bez vytváření klientských aplikací:

* [gRPCurl](https://github.com/fullstorydev/grpcurl) je nástroj příkazového řádku, který poskytuje interakce se službami gRPC.
* [gRPCui](https://github.com/fullstorydev/grpcui) sestaví gRPCurl a přidá interaktivní webové uživatelské rozhraní pro gRPC, podobně jako pro nástroj post a Swagger.

Tento článek popisuje, jak:

* Stáhněte a nainstalujte gRPCurl a gRPCui.
* Nastavte reflexi gRPC pomocí aplikace gRPC ASP.NET Core.
* Zjišťování a testování gRPC služeb pomocí `grpcurl` .
* Interakce s gRPC službami přes prohlížeč pomocí `grpcui` .

## <a name="about-grpcurl"></a>O gRPCurl

gRPCurl je nástroj příkazového řádku vytvořený komunitou gRPC. Mezi tyto funkce patří:

* Volání služeb gRPC Services, včetně služeb streamování.
* Zjišťování služby pomocí [reflexe gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).
* Seznamte se s popisem služeb gRPC.
* Pracuje se zabezpečeným (TLS) a nezabezpečeným (prostým) serverem.

Informace o stažení a instalaci `grpcurl` najdete na [domovské stránce GitHubu gRPCurl](https://github.com/fullstorydev/grpcurl#installation).

![příkazový řádek gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>Nastavení reflexe gRPC

`grpcurl` před voláním služby musíte znát Protobuf kontrakt služeb. Toto lze provést dvěma způsoby:

* Nastavte [reflexi gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) na serveru. gRPCurl automaticky zjišťuje kontrakty služby.
* Zadejte `.proto` soubory v argumentech příkazového řádku pro gRPCurl.

Použití gRPCurl s reflexí gRPC je jednodušší. reflexe gRPC přidá do aplikace novou službu gRPC, kterou klienti můžou volat pro zjišťování služeb.

gRPC ASP.NET Core obsahuje integrovanou podporu pro reflexi gRPC s [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) balíčkem. Konfigurace reflexe v aplikaci:

* Přidejte `Grpc.AspNetCore.Server.Reflection` odkaz na balíček.
* Registrovat reflexi v `Startup.cs` :
  * `AddGrpcReflection` k registraci služeb, které povolují reflexi.
  * `MapGrpcReflectionService` pro přidání koncového bodu služby Reflection.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

Když je nastavená reflexe gRPC:

* Do serverové aplikace se přidá služba reflexe gRPC.
* Klientské aplikace, které podporují reflexi gRPC, mohou volat službu reflexe za účelem zjišťování služeb hostovaných serverem.
* služby gRPC Services se pořád volají z klienta. Reflexe umožňuje jenom zjišťování služby a obchází zabezpečení na straně serveru. Koncové body chráněné [ověřováním a autorizací](xref:grpc/authn-and-authz) vyžadují volajícímu předání přihlašovacích údajů pro koncový bod, který se má úspěšně volat.

## <a name="use-grpcurl"></a>Použití `grpcurl`

`-help`Argument vysvětluje `grpcurl` Možnosti příkazového řádku:

```console
$ grpcurl -help
```

### <a name="discover-services"></a>Vyhledat služby

Pomocí `describe` příkazu Zobrazte služby definované serverem:

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

Předchozí příklad:

* Spustí `describe` operaci na serveru `localhost:5001` .
* Vytiskne služby a metody vrácené gRPC reflexe.
  * `Greeter` je služba implementovaná aplikací.
  * `ServerReflection` je služba přidaná `Grpc.AspNetCore.Server.Reflection` balíčkem.

`describe`V kombinaci s názvem služby, metody nebo zprávy zobrazíte její podrobnosti:

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Volání služeb gRPC Services

Zavolejte službu gRPC zadáním názvu služby a metody společně s argumentem JSON, který představuje zprávu požadavku. KÓD JSON se převede na Protobuf a pošle se do služby.

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

V předchozím příkladu:

* `-d`Argument určuje zprávu požadavku s JSON. Tento argument musí být před názvem adresy serveru a metody.
* Volá `SayHello` metodu `greeter.Greeter` služby.
* Vytiskne zprávu odpovědi jako JSON.

## <a name="about-grpcui"></a>O gRPCui

gRPCui je interaktivní webové uživatelské rozhraní pro gRPC. Sestavuje se na gRPCurl a nabízí grafické rozhraní pro zjišťování a testování gRPCch služeb, podobně jako prostřednictvím nástroje HTTP, jako je například post nebo Swagger UI.

Informace o stažení a instalaci `grpcui` najdete na [domovské stránce GitHubu gRPCui](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Používání akce `grpcui`

Spusťte `grpcui` s adresou serveru pro interakci s jako argument:

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Nástroj spustí okno prohlížeče s interaktivním webovým uživatelským rozhraním. služby gRPC se automaticky zjišťují pomocí reflexe gRPC.

![webové uživatelské rozhraní gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Další zdroje

* [Domovská stránka GitHubu gRPCurl](https://github.com/fullstorydev/grpcurl)
* [Domovská stránka GitHubu gRPCui](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
