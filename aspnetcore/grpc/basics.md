---
title: Služby gRPC s jazykem C#
author: juntaoluo
description: Naučte se základní pojmy při psaní služeb gRPC s C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664204"
---
# <a name="grpc-services-with-c"></a>gRPC služby s C\#

Tento dokument popisuje koncepty potřebné k zápisu [gRPC](https://grpc.io/docs/guides/) aplikací v C#. Témata, která jsou zde popsána, se vztahují jak na aplikace gRPC založené na [jádru C,](https://grpc.io/blog/grpc-stacks)tak ASP.NET na gRPC založené na jádru.

## <a name="proto-file"></a>proto soubor

gRPC používá přístup k vývoji rozhraní API založený na smlouvě. Vyrovnávací paměti protokolu (protobuf) se ve výchozím nastavení používají jako návrhový jazyk rozhraní (IDL). Soubor * \*.proto* obsahuje:

* Definice služby gRPC.
* Zprávy odeslané mezi klienty a servery.

Další informace o syntaxi souborů protobufů naleznete v [oficiální dokumentaci (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Zvažte například soubor *greet.proto* použitý v [začínáme se službou gRPC](xref:tutorials/grpc/grpc-start):

* Definuje službu. `Greeter`
* Služba `Greeter` definuje `SayHello` volání.
* `SayHello`odešle `HelloRequest` zprávu a `HelloReply` obdrží zprávu:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Přidání souboru .proto\# do aplikace C

Soubor * \*.proto* je součástí projektu přidáním `<Protobuf>` do skupiny položek:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>Podpora nástrojů jazyka C# pro soubory .proto

Nástrojový balíček [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) je nutné generovat c# prostředky ze * \*souborů .proto.* Generované datové zdroje (soubory):

* Jsou generovány podle potřeby při každém vytvoření projektu.
* Nejsou přidány do projektu nebo se změnami do správy zdrojového kódu.
* Jsou artefakt sestavení obsažené v adresáři *obj.*

Tento balíček je vyžadován serverovými i klientskými projekty. Metabalíček `Grpc.AspNetCore` obsahuje odkaz `Grpc.Tools`na . Serverové projekty můžete přidat `Grpc.AspNetCore` pomocí Správce balíčků `<PackageReference>` v sadě Visual Studio nebo přidáním souboru projektu:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Klientské projekty `Grpc.Tools` by měly odkazovat přímo vedle ostatních balíčků potřebných k použití klienta gRPC. Balíček nástrojů není vyžadován za běhu, takže závislost je `PrivateAssets="All"`označena :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Generované datové zdroje jazyka C#

Balíček nástrojů generuje c# typy představující zprávy definované v zahrnuté * \*.proto* soubory.

Pro prostředky na straně serveru je generován abstraktní typ základny služby. Základní typ obsahuje definice všech volání gRPC obsažených v souboru *.proto.* Vytvořte konkrétní implementaci služby, která je odvozena z tohoto základního typu a implementuje logiku pro volání gRPC. Pro `greet.proto`, příklad popsaný výše, `GreeterBase` abstraktní typ, `SayHello` který obsahuje virtuální metodu je generován. Konkrétní implementace `GreeterService` přepíše metodu a implementuje logiku zpracování volání gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Pro prostředky na straně klienta je generován konkrétní typ klienta. Volání gRPC v souboru *.proto* jsou přeložena do metod na konkrétní typ, který lze volat. Pro `greet.proto`, příklad popsaný výše, `GreeterClient` je generován konkrétní typ. Volání `GreeterClient.SayHelloAsync` zahájit volání gRPC na server.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Ve výchozím nastavení jsou pro každý `<Protobuf>` * \** soubor .proto zahrnutý do skupiny položek generovány datové zdroje serveru a klienta. Chcete-li zajistit, aby byly v projektu `GrpcServices` serveru generovány `Server`pouze datové zdroje serveru, je atribut nastaven na .

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Podobně atribut je nastaven `Client` na v klientských projektech.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
