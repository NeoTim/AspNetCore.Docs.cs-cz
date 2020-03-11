---
title: Služby gRPC s jazykem C#
author: juntaoluo
description: Seznamte se se základními pojmy při psaní C#služeb gRPC pomocí nástroje.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664204"
---
# <a name="grpc-services-with-c"></a>gRPC Services s využitím C\#

Tento dokument popisuje koncepty potřebné k zápisu aplikací [gRPC](https://grpc.io/docs/guides/) v C#nástroji. Zde uvedená témata platí pro gRPC aplikace založené na jazyce [C-Core](https://grpc.io/blog/grpc-stacks)i ASP.NET Core.

## <a name="proto-file"></a>Proto soubor

gRPC využívá přístup ke vývoji rozhraní API, který je prvním kontraktem. Vyrovnávací paměti protokolu (protobuf) se ve výchozím nastavení používají jako jazyk IDL (Interface design Language). Soubor *\*.* proč obsahuje:

* Definice služby gRPC
* Zprávy odesílané mezi klienty a servery.

Další informace o syntaxi souborů protobuf naleznete v [oficiální dokumentaci (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Předpokládejme například, že soubor *....* je použit v části Začínáme [se službou gRPC](xref:tutorials/grpc/grpc-start):

* Definuje službu `Greeter`.
* Služba `Greeter` definuje volání `SayHello`.
* `SayHello` odesílá zprávu `HelloRequest` a obdrží zprávu `HelloReply`:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Přidání souboru... a do aplikace\# v jazyce C

Soubor *\*....* je obsažen v projektu přidáním do skupiny položek `<Protobuf>`:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>C#Podpora nástrojů pro soubory.

Aby se vygenerovaly C# prostředky z *\*.* . jsou potřebné nástroje pro [vytváření balíčků.](https://www.nuget.org/packages/Grpc.Tools/) Vygenerované prostředky (soubory):

* Jsou generovány podle potřeby pokaždé, když je projekt sestaven.
* Nejsou přidány do projektu nebo vráceny se změnami do správy zdrojového kódu.
* Jsou artefaktem sestavení obsažený v adresáři *obj* .

Tento balíček vyžadují projekty serveru i klienta. `Grpc.AspNetCore` Metapackage obsahuje odkaz na `Grpc.Tools`. Serverové projekty mohou přidat `Grpc.AspNetCore` pomocí Správce balíčků v aplikaci Visual Studio nebo přidáním `<PackageReference>` do souboru projektu:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Klientské projekty by měly přímo odkazovat `Grpc.Tools` společně s ostatními balíčky potřebnými k použití klienta gRPC. Balíček nástrojů se za běhu nevyžaduje, takže je tato závislost označená jako `PrivateAssets="All"`:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Vygenerované C# prostředky

Balíček nástroje generuje C# typy reprezentující zprávy definované v zahrnutých *\*.* . soubory.

U prostředků na straně serveru se vygeneruje abstraktní základní typ služby. Základní typ obsahuje definice všech volání gRPC obsažených v souboru *.* . Vytvořte konkrétní implementaci služby, která je odvozena z tohoto základního typu a implementuje logiku pro volání gRPC. Pro `greet.proto`, výše popsaný příklad, je vygenerován abstraktní typ `GreeterBase` obsahující metodu Virtual `SayHello`. Konkrétní implementace `GreeterService` přepisuje metodu a implementuje logiku, která zpracovává volání gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

U prostředků na straně klienta se vygeneruje konkrétní typ klienta. Volání gRPC v souboru *.* proč jsou přeložena do metod pro konkrétní typ, který lze volat. Pro `greet.proto`výše popsaného příkladu je vygenerován konkrétní typ `GreeterClient`. Zavolejte `GreeterClient.SayHelloAsync` pro zahájení volání gRPC na server.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Ve výchozím nastavení jsou prostředky serveru a klienta generovány pro každý *\*.* soubor je součástí skupiny položek `<Protobuf>`. Aby se zajistilo, že se v serverovém projektu generují jenom prostředky serveru, `GrpcServices` atribut je nastaven na `Server`.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Podobně je atribut nastaven na `Client` v klientských projektech.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
