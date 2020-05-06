---
title: Služby gRPC s jazykem C#
author: juntaoluo
description: Seznamte se se základními koncepty při psaní služeb gRPC pomocí jazyka C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: a55ed90e7c854d1475b1f5d95347505fad0813ab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774753"
---
# <a name="grpc-services-with-c"></a>gRPC Services s využitím C\#

Tento dokument popisuje koncepty potřebné k psaní aplikací [gRPC](https://grpc.io/docs/guides/) v jazyce C#. Zde uvedená témata platí pro gRPC aplikace založené na jazyce [C-Core](https://grpc.io/blog/grpc-stacks)i ASP.NET Core.

## <a name="proto-file"></a>Proto soubor

gRPC využívá přístup ke vývoji rozhraní API, který je prvním kontraktem. Vyrovnávací paměti protokolu (protobuf) se ve výchozím nastavení používají jako jazyk IDL (Interface design Language). Soubor * \*... proto* obsahuje:

* Definice služby gRPC
* Zprávy odesílané mezi klienty a servery.

Další informace o syntaxi souborů protobuf naleznete v [oficiální dokumentaci (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Předpokládejme například, že soubor *....* je použit v části Začínáme [se službou gRPC](xref:tutorials/grpc/grpc-start):

* Definuje `Greeter` službu.
* `Greeter` Služba definuje `SayHello` volání.
* `SayHello`odešle `HelloRequest` zprávu a obdrží `HelloReply` zprávu:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Přidání souboru... a do aplikace\# v jazyce C

Soubor * \*....* je obsažen v projektu přidáním do skupiny `<Protobuf>` položek:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>Podpora nástrojů C# pro soubory.

K vygenerování prostředků jazyka C# ze souborů * \*.* infroms se vyžaduje balíček nástrojů [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) . Vygenerované prostředky (soubory):

* Jsou generovány podle potřeby pokaždé, když je projekt sestaven.
* Nejsou přidány do projektu nebo vráceny se změnami do správy zdrojového kódu.
* Jsou artefaktem sestavení obsažený v adresáři *obj* .

Tento balíček vyžadují projekty serveru i klienta. `Grpc.AspNetCore` Metapackage obsahuje odkaz na `Grpc.Tools`. Serverové projekty lze přidat `Grpc.AspNetCore` pomocí Správce balíčků v aplikaci Visual Studio nebo přidáním `<PackageReference>` do souboru projektu:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Klientské projekty by se měly `Grpc.Tools` přímo odkazovat vedle ostatních balíčků vyžadovaných k použití klienta gRPC. Balíček nástrojů se za běhu nevyžaduje, takže je tato závislost označená jako `PrivateAssets="All"`:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Generované prostředky C#

Balíček nástrojů generuje typy jazyka C# reprezentující zprávy definované v zahrnutých * \*souborech.*

U prostředků na straně serveru se vygeneruje abstraktní základní typ služby. Základní typ obsahuje definice všech volání gRPC obsažených v souboru *..* . Vytvořte konkrétní implementaci služby, která je odvozena z tohoto základního typu a implementuje logiku pro volání gRPC. Pro `greet.proto`, výše popsaný příklad, je vygenerován `GreeterBase` abstraktní typ, který obsahuje `SayHello` virtuální metodu. Konkrétní implementace `GreeterService` přepisuje metodu a implementuje logiku, která zpracovává volání gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

U prostředků na straně klienta se vygeneruje konkrétní typ klienta. Volání gRPC v souboru *.* proč jsou přeložena do metod pro konkrétní typ, který lze volat. Pro `greet.proto`, výše popsaný příklad, je vytvořen konkrétní `GreeterClient` typ. Zavolejte `GreeterClient.SayHelloAsync` k inicializaci volání gRPC na server.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Ve výchozím nastavení jsou prostředky serveru a klienta generovány pro každý `<Protobuf>` * \*soubor. proto* zahrnutý ve skupině položek. Chcete-li zajistit, aby byly v projektu serveru generovány pouze prostředky `GrpcServices` serveru, je atribut `Server`nastaven na hodnotu.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Podobně je atribut nastaven na `Client` hodnotu v klientských projektech.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
