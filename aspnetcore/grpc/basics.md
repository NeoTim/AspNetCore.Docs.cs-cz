---
title: Služby gRPC s jazykem C#
author: juntaoluo
description: Seznamte se se základními pojmy při psaní C#služeb gRPC pomocí nástroje.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 700fe9463317f9ee30dfe4ebf5201c7b9c0c5ad6
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412469"
---
# <a name="grpc-services-with-c"></a>gRPC Services s využitím C\#

Tento dokument popisuje koncepty potřebné k zápisu aplikací [gRPC](https://grpc.io/docs/guides/) v C#nástroji. Zde uvedená témata platí pro gRPC aplikace založené na jazyce [C-Core](https://grpc.io/blog/grpc-stacks)i ASP.NET Core.

## <a name="proto-file"></a>Proto soubor

gRPC využívá přístup ke vývoji rozhraní API, který je prvním kontraktem. Vyrovnávací paměti protokolu (protobuf) se ve výchozím nastavení používají jako jazyk IDL (Interface design Language). Soubor *... proto* obsahuje:

* Definice služby gRPC
* Zprávy odesílané mezi klienty a servery.

Další informace o syntaxi souborů protobuf naleznete v [oficiální dokumentaci (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Předpokládejme například, že soubor *....* je použit v části Začínáme [se službou gRPC](xref:tutorials/grpc/grpc-start):

* `Greeter` Definuje službu.
* `Greeter` Služba`SayHello` definuje volání.
* `SayHello`odešle zprávu a obdrží `HelloReply`zprávu: `HelloRequest`

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>Přidání souboru... a do aplikace\# v jazyce C

Soubor *....* je obsažen v projektu přidáním do `<Protobuf>` skupiny položek:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>C#Podpora nástrojů pro soubory.

K vygenerování C# prostředků ze souborů *...* je potřeba balíček nástrojů [Grpc. Tools.](https://www.nuget.org/packages/Grpc.Tools/) Vygenerované prostředky (soubory):

* Jsou generovány podle potřeby pokaždé, když je projekt sestaven.
* Nejsou přidány do projektu nebo vráceny se změnami do správy zdrojového kódu.
* Jsou artefaktem sestavení obsažený v adresáři *obj* .

Tento balíček vyžadují projekty serveru i klienta. Metapackage obsahuje odkaz na `Grpc.Tools`. `Grpc.AspNetCore` Serverové projekty lze přidat `Grpc.AspNetCore` pomocí Správce balíčků v aplikaci Visual Studio nebo `<PackageReference>` přidáním do souboru projektu:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Klientské projekty by měly `Grpc.Tools` odkazovat přímo. Balíček nástrojů se za běhu nevyžaduje, takže je tato závislost označená jako `PrivateAssets="All"`:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=1&range=11)]

## <a name="generated-c-assets"></a>Vygenerované C# prostředky

Balíček nástroje generuje C# typy reprezentující zprávy definované v zahrnutých souborech *.*

U prostředků na straně serveru se vygeneruje abstraktní základní typ služby. Základní typ obsahuje definice všech volání gRPC obsažených v souboru *..* . Vytvořte konkrétní implementaci služby, která je odvozena z tohoto základního typu a implementuje logiku pro volání gRPC. Pro, výše popsaný příklad, je vygenerován `GreeterBase` abstraktní typ, který obsahuje `SayHello` virtuální metodu. `greet.proto` Konkrétní implementace `GreeterService` přepisuje metodu a implementuje logiku, která zpracovává volání gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

U prostředků na straně klienta se vygeneruje konkrétní typ klienta. Volání gRPC v souboru *.* proč jsou přeložena do metod pro konkrétní typ, který lze volat. Pro, výše popsaný příklad, je vytvořen konkrétní `GreeterClient` typ. `greet.proto` Zavolejte `GreeterClient.SayHello` k inicializaci volání gRPC na server.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?highlight=3-6&name=snippet)]

Ve výchozím nastavení jsou prostředky serveru a klienta generovány pro každý soubor *. proto* zahrnutý ve `<Protobuf>` skupině položek. Chcete-li zajistit, aby byly v projektu serveru generovány pouze prostředky `GrpcServices` serveru, je atribut `Server`nastaven na hodnotu.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Podobně je atribut nastaven na `Client` hodnotu v klientských projektech.

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
