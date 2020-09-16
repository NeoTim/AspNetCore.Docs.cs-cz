---
title: Testování služeb pomocí nástrojů gRPC
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
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594416"
---
# <a name="test-services-with-grpc-tools"></a>Testování služeb pomocí nástrojů gRPC

Od [James Newton – král](https://twitter.com/jamesnk)

K dispozici jsou nástroje pro gRPC, které vývojářům umožňují testovat služby bez vytváření klientských aplikací. [gRPCurl](https://github.com/fullstorydev/grpcurl) je nástroj příkazového řádku, který poskytuje interakce se službami gRPC. [gRPCui](https://github.com/fullstorydev/grpcui) přidá interaktivní webové uživatelské rozhraní pro gRPC.

Tento článek popisuje, jak:

* Stáhněte a nainstalujte gRPCurl a gRPCui.
* Nastavení reflexe gRPC pomocí aplikace v gRPC ASP.NET Core.
* Zjišťování a testování gRPC služeb pomocí `grpcurl` .
* Interakce s gRPC službami přes prohlížeč pomocí `grpcui` .

## <a name="about-grpcurl"></a>O gRPCurl

gRPCurl je nástroj příkazového řádku vytvořený komunitou gRPC. Mezi tyto funkce patří:

* Volání služeb gRPC Services, včetně služeb streamování.
* Zjišťování služby pomocí [reflexe gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).
* Seznamte se s popisem služeb gRPC.
* Pracuje se zabezpečeným (TLS) a nezabezpečeným (prostým) serverem.

Informace o stažení a instalaci `grpcurl` najdete na [domovské stránce GitHubu gRPCurl](https://github.com/fullstorydev/grpcurl#installation).

## <a name="setup-grpc-reflection"></a>Nastavit reflexi gRPC

`grpcurl` aby je bylo možné volat, musí znát Protobuf kontrakt služeb. Toto lze provést dvěma způsoby:

* Použijte reflexi gRPC k zjišťování kontraktů služeb.
* Zadejte soubory *.* a v argumentech příkazového řádku.

Použití gRPCurl s gRPC reflexe a Service Discovery je jednodušší. gRPC ASP.NET Core obsahuje integrovanou podporu gRPC reflexe s balíčkem [gRPC. AspNetCore. Server. reflexe](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) . Konfigurace reflexe v aplikaci:

* Přidat `Grpc.AspNetCore.Server.Reflection` odkaz na balíček
* Zaregistrujte reflexi v *Startup.cs*:
  * `AddGrpcReflection` k registraci služeb, které povolují reflexi.
  * `MapGrpcReflectionService` pro přidání koncového bodu služby reflexe.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a>Použití `grpcurl`

`-help`Argument vysvětluje `grpcurl` Možnosti příkazového řádku:

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a>Vyhledat služby

Pomocí `describe` příkazu Zobrazte služby definované serverem:

```powershell
> grpcurl.exe localhost:5001 describe
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

Kombinovat `describe` s názvem služby, metody nebo zprávy pro zobrazení jejich podrobností:

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Volání služeb gRPC Services

Zavolejte službu gRPC zadáním názvu služby a metody společně s argumentem JSON, který představuje zprávu požadavku. KÓD JSON se převede na Protobuf a pošle se do služby.

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

Předchozí příklad:

* `-d` argument určuje zprávu požadavku s JSON. Tento argument musí být před názvem adresy serveru a metody.
* Volá `SayHello` metodu `greeter.Greeter` služby.
* Vytiskne zprávu odpovědi jako JSON.

## <a name="about-grpcui"></a>O gRPCui

gRPCui je interaktivní webové uživatelské rozhraní pro gRPC. Sestavuje se na gRPCurl a nabízí grafické rozhraní pro zjišťování a testování gRPCch služeb, podobně jako prostřednictvím nástrojů HTTP, jako je post.

Informace o stažení a instalaci `grpcui` najdete na [domovské stránce GitHubu gRPCui](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Používání akce `grpcui`

Spusťte `grpcui` s adresou serveru pro interakci s jako argument.

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Nástroj spustí okno prohlížeče s interaktivním webovým uživatelským rozhraním. služby gRPC se automaticky zjišťují pomocí reflexe gRPC.

![webové uživatelské rozhraní gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Další zdroje

* [Domovská stránka GitHubu gRPCurl](https://github.com/fullstorydev/grpcurl)
* [Domovská stránka GitHubu gRPCui](https://github.com/fullstorydev/grpcui)
* [Grpc. AspNetCore. Server. Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
