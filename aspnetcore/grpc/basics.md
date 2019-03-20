---
title: Služby gRPC s jazykem C#
author: juntaoluo
description: Informace o základních konceptech při zápisu gRPC služby s C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/basics
ms.openlocfilehash: 936561a3ad04183aff4c3ba1c9b0e8ab20dcbe12
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264774"
---
# <a name="grpc-services-with-c"></a>gRPC služby pomocí jazyka C\#

Tento dokument popisuje základní koncepty jsou potřeba k zápisu [gRPC](https://grpc.io/docs/guides/) aplikací v C#. Tady probíraná témata platit pro oboje [C – jádro](https://grpc.io/blog/grpc-stacks) podle a gRPC aplikace ASP.NET Core.

## <a name="proto-file"></a>proto soubor

gRPC používá kontraktem přístup pro vývoj rozhraní API. Protocol buffers (protobuf) se používají jako návrh jazyka IDL (Interface) ve výchozím nastavení. *.Proto* soubor obsahuje:

* Definice služby gRPC.
* Zprávy odeslané mezi klienty a servery.

Další informace o syntaxi protobuf soubory, najdete v článku [oficiální dokumentaci (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Představme si třeba, *greet.proto* souboru použitého v [začít používat gRPC service](xref:tutorials/grpc/grpc-start):

* Definuje `Greeter` služby.
* `Greeter` Služba definuje `SayHello` volání.
* `SayHello` odešle `HelloRequest` zprávy a přijímá `HelloResponse` zpráva:

[!code-proto[](~/tutorials/grpc/grpc-start/samples/GrpcStart/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>Přidat soubor .proto C# aplikace

*.Proto* soubor zahrnut v projektu tak, že ji přidáte `<Protobuf>` skupiny položek:

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

## <a name="c-tooling-support-for-proto-files"></a>C#Podpora nástrojů pro soubory .proto

Balíček nástroje [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) vyžadovaných ke generování C# assety z *.proto* soubory. Vygenerovaných prostředků (soubory):

* Jsou generovány na základě potřeby pokaždé, když sestavení projektu.
* Nejsou přidány do projektu nebo zapsány do správy zdrojového kódu.
* Jsou obsaženy v artefaktu sestavení *obj* adresáře.

Tento balíček je vyžadován server i klient projekty. `Grpc.Tools` lze přidat pomocí Správce balíčků v sadě Visual Studio nebo přidáním `<PackageReference>` do souboru projektu:

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=1&range=16)]

Balíček nástroje není vyžadováno za běhu, proto by měly být označené závislost `PrivateAssets="All"`.

## <a name="generated-c-assets"></a>Vygeneruje C# prostředky

Bude generovat balíček nástroje C# typy představující zpráv definovaný v zahrnutou *.proto* soubory.

Pro prostředky na straně serveru je vygenerována základního typu abstraktní služby. Základní typ obsahuje definice všech součástí gRPC volání *.proto* souboru. Pak vytvoříte konkrétní implementace služby je odvozena z tohoto základního typu a implementuje logiku pro gRPC volání. Pro `greet.proto` příklad je popsáno výše, abstraktní `GreeterBase` typ, který obsahuje virtuální `SayHello` generované metody. Konkrétní implementaci `GreeterService` přepisuje metodu a implementuje logiku zpracování gRPC volání.

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/Services/GreeterService.cs?name=snippet)]

Pro prostředky na straně klienta je vygenerována typ konkrétní klienta. GRPC zavolá v *.proto* souboru jsou přeloženy na metody na konkrétní typ, který může být volána. Pro `greet.proto` příklad je popsáno výše, konkrétní `GreeterClient` typ generován. `GreeterClient` Obsahuje typ `SayHello` metodu, která lze volat pro zahájení gRPC volání serveru.

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Client/Program.cs?highlight=9-11&name=snippet)]

Ve výchozím nastavení, prostředky serveru a klienta jsou generovány pro každou *.proto* zahrnuté v souboru `<Protobuf>` skupiny položek. Aby se serverové prostředky jsou generovány v projektu serveru, `GrpcServices` atribut je nastaven na `Server`.

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

Podobně platí, atribut je nastaven na `Client` v klientské projekty.

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
