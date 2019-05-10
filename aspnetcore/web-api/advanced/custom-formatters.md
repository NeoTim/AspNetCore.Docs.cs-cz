---
title: Vlastní formátování v rozhraní Web API ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit a použít vlastní formátovací moduly pro webová rozhraní API v ASP.NET Core.
ms.author: tdykstra
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: ece60b47f345235a084faacf8e59b792856614d2
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900663"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Vlastní formátování v rozhraní Web API ASP.NET Core

podle [Petr Dykstra](https://github.com/tdykstra)

ASP.NET Core MVC obsahuje integrovanou podporu pro výměnu dat ve webovém rozhraní API pomocí XML nebo JSON. Tento článek ukazuje, jak přidat podporu pro další formáty tak, že vytvoříte vlastní formátovací moduly.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Kdy použít vlastní formátovací moduly

Pokud chcete použít vlastní formátovací modul [vyjednávání obsahu](xref:web-api/advanced/formatting#content-negotiation) proces pro podporu typu obsahu, který není podporován předdefinované formátování (JSON a XML).

Například, pokud některé z klientů pro vaše webové rozhraní API může zpracovat [Protobuf](https://github.com/google/protobuf) formátu, můžete chtít použít Protobuf s těmito klienty, protože je mnohem efektivnější. Nebo můžete chtít vaše webové rozhraní API k odeslání jména kontaktů a adresy v [vCard](https://wikipedia.org/wiki/VCard) formát, běžně používaný formát pro výměnu kontaktní údaje. Ukázkovou aplikaci k dispozici v tomto článku implementuje jednoduchou vCard formátování.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Přehled o tom, jak používat vlastní formátování

Tady jsou kroky k vytvoření a použití vlastní formátovací modul:

* Vytvořte třídu formátování výstupu, pokud chcete serializovat data k odeslání do klienta.
* Vytvořte třídu vstupní formátovací modul, pokud chcete zrušit serializaci dat přijatých z klienta.
* Doplnit dodatečné instance vaší formátovací moduly, `InputFormatters` a `OutputFormatters` kolekce v [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).

Následující části obsahují pokyny a příklady kódu pro každý z těchto kroků.

## <a name="how-to-create-a-custom-formatter-class"></a>Jak vytvořit třídu vlastního formátování

Chcete-li vytvořit formátování:

* Třídy odvozen od příslušné základní třídy.
* Zadejte platné médium typy a kódování v konstruktoru.
* Přepsat `CanReadType` / `CanWriteType` metody
* Přepsat `ReadRequestBodyAsync` / `WriteResponseBodyAsync` metody
  
### <a name="derive-from-the-appropriate-base-class"></a>Odvozen od příslušné základní třídy

Typ média textu (například soubor vCard), jsou odvozeny z [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) nebo [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) základní třídy.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Příklad vstupní formátovací modul, najdete v článku [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

Pro binární typy jsou odvozeny z [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) nebo [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) základní třídy.

### <a name="specify-valid-media-types-and-encodings"></a>Zadejte platné médium typy a kódování

V konstruktoru, určete přidáním do platné médium typy a kódování `SupportedMediaTypes` a `SupportedEncodings` kolekce.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

Příklad vstupní formátovací modul, najdete v článku [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

> [!NOTE]
> Injektáž závislostí konstruktoru ve třídě formátovacího modulu nelze provést. Například nelze získat protokolovač tak, že přidáte parametr protokolovač konstruktoru. Pro přístup ke službám, budete muset použít objekt kontextu, který získá předán do metody. Příklad kódu [níže](#read-write) ukazuje, jak to provést.

### <a name="override-canreadtypecanwritetype"></a>Přepsat CanReadType/CanWriteType

Určení typu lze deserializovat do nebo z serializovat tak, že přepíšete `CanReadType` nebo `CanWriteType` metody. Například může být pouze budete moci vytvořit vCard text z `Contact` typu a naopak.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

Příklad vstupní formátovací modul, najdete v článku [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

#### <a name="the-canwriteresult-method"></a>CanWriteResult – metoda

V některých scénářích je nutné přepsat `CanWriteResult` místo `CanWriteType`. Použití `CanWriteResult` Pokud jsou splněny následující podmínky:

* Vaše metoda akce vrací třídu modelu.
* Existují odvozených tříd, které může být vrácena za běhu.
* Musíte znát za běhu, který odvozené třídy vrátil akce.

Předpokládejme například, vrátí podpis metody akce `Person` typu, ale může vrátit `Student` nebo `Instructor` typ, který je odvozen od `Person`. Pokud chcete, aby vaše formátovací modul pro zpracování pouze `Student` objekty, zkontrolujte typ [objekt](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) v objektu kontextu k dispozici na `CanWriteResult` metody. Všimněte si, že není nutné používat `CanWriteResult` při vrácení metody akce `IActionResult`; v takovém případě `CanWriteType` metoda přijímá typ modulu runtime.

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>Override ReadRequestBodyAsync/WriteResponseBodyAsync

Vykonávají samotnou práci rušení serializace nebo serializace v `ReadRequestBodyAsync` nebo `WriteResponseBodyAsync`. Zvýrazněné řádky v následujícím příkladu ukazují, jak získat služby z kontejneru pro vkládání závislosti (je již nelze získat z parametrů konstruktoru).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

Příklad vstupní formátovací modul, najdete v článku [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Jak nakonfigurovat MVC pomocí vlastního formátovacího modulu

Pokud chcete použít vlastní formátovací modul, přidat instanci formátovacího modulu třídy, která se `InputFormatters` nebo `OutputFormatters` kolekce.

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

Formátovací moduly jsou vyhodnocovány v pořadí, v jakém že je vkládat v případě potřeby. První z nich má přednost.

## <a name="next-steps"></a>Další kroky

* [Prostý text formátovací modul ukázkového kódu na Githubu.](https://github.com/aspnet/Entropy/tree/master/samples/Mvc.Formatters)
* [Ukázková aplikace pro tento dokument](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), které implementuje jednoduché vCard Vstupní a výstupní formátování. Aplikace čte a zapisuje vCard, která vypadají, jako v následujícím příkladu:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

Chcete-li zobrazit vCard výstup, spusťte aplikaci a odeslat požadavek Get s přijmout záhlaví "text/vcard" k `http://localhost:63313/api/contacts/` (při spuštění ze sady Visual Studio) nebo `http://localhost:5000/api/contacts/` (při spuštění z příkazového řádku).

Přidat do kolekce v paměti kontaktů vizitku, odešlete požadavek Post na stejnou adresu URL, s hlavičkou Content-Type "text/vcard" a textem vCard v textu ve formátu jako v příkladu výše.
