---
title: Vlastní formátovací moduly v ASP.NET Core Web API
author: rick-anderson
description: Naučte se vytvářet a používat vlastní formátovací moduly pro webová rozhraní API v ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 122edfd4ccd06ed62e071691f421d2aeef8002b4
ms.sourcegitcommit: 488cc779fc71377d9371e7a14356113e9c7eff17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70913504"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Vlastní formátovací moduly v ASP.NET Core Web API

Tím, že [Dykstra](https://github.com/tdykstra)

ASP.NET Core MVC podporuje výměnu dat ve webových rozhraních API pomocí formátování vstupu a výstupu. Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding). Výstupní formátovací moduly se používají k [formátování odpovědí](xref:web-api/advanced/formatting).

Rozhraní poskytuje předdefinované vstupní a výstupní formátovací moduly pro JSON a XML. Poskytuje vestavěný výstupní formátovací modul pro prostý text, ale neposkytuje vstupní formátovací modul pro prostý text.

Tento článek ukazuje, jak přidat podporu pro další formáty vytvořením vlastních formátovacích modulů. Příklad vlastního vstupního formátovacího modulu pro prostý text najdete v tématu [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) na GitHubu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Kdy použít vlastní formátovací moduly

Vlastní formátovací modul použijte v případě, že chcete, aby proces [vyjednávání obsahu](xref:web-api/advanced/formatting#content-negotiation) podporoval typ obsahu, který není podporován vestavěnými formátovacími moduly.

Pokud například někteří klienti webového rozhraní API mohou zpracovat formát [Protobuf](https://github.com/google/protobuf) , můžete chtít použít Protobuf u těchto klientů, protože je efektivnější. Nebo můžete chtít, aby vaše webové rozhraní API odesílalo jména kontaktů a adresy ve formátu [vCard](https://wikipedia.org/wiki/VCard) , což je běžně používaný formát pro výměnu dat kontaktů. Ukázková aplikace, která je součástí tohoto článku, implementuje jednoduchý formátovací modul vCard.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Přehled způsobu použití vlastního formátovacího modulu

Tady je postup vytvoření a použití vlastního formátovacího modulu:

* Vytvořte výstupní třídu formátování výstupu, pokud chcete serializovat data pro odeslání klientovi.
* Vytvořte vstupní třídu formátování, pokud chcete deserializovat data přijatá z klienta.
* Přidejte instance formátovacích modulů do `InputFormatters` kolekcí a `OutputFormatters` v [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).

Následující části obsahují pokyny a příklady kódu pro každý z těchto kroků.

## <a name="how-to-create-a-custom-formatter-class"></a>Jak vytvořit vlastní třídu formátování

Vytvoření formátovacího modulu:

* Odvodit třídu od příslušné základní třídy.
* Zadejte platné typy médií a kódování v konstruktoru.
* Metody `CanReadType` přepsání / `CanWriteType`
* Metody `ReadRequestBodyAsync` přepsání / `WriteResponseBodyAsync`
  
### <a name="derive-from-the-appropriate-base-class"></a>Odvození od příslušné základní třídy

Pro typy textových médií (například vCard) je odvozen ze základní třídy [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) nebo [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) .

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

Pro binární typy je odvozeno od základní třídy [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) nebo [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) .

### <a name="specify-valid-media-types-and-encodings"></a>Zadejte platné typy médií a kódování.

V konstruktoru zadejte platné typy médií a kódování přidáním do `SupportedMediaTypes` kolekce a. `SupportedEncodings`

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

> [!NOTE]
> Vložení závislosti konstruktoru nelze provést ve třídě formátovacího modulu. Například nemůžete získat protokolovací nástroj přidáním parametru protokolovacího nástroje do konstruktoru. Pro přístup ke službám je nutné použít objekt kontextu, který se předává do vašich metod. [Následující](#read-write) příklad kódu ukazuje, jak to provést.

### <a name="override-canreadtypecanwritetype"></a>Přepsat CanReadType/CanWriteType

Zadejte typ, který lze deserializovat nebo serializovat, přepsáním `CanReadType` metod nebo. `CanWriteType` Například může být možné pouze vytvořit text v vCard z `Contact` typu a naopak.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

#### <a name="the-canwriteresult-method"></a>Metoda CanWriteResult

V některých případech je nutné přepsat `CanWriteResult` `CanWriteType`místo. Použijte `CanWriteResult` , pokud jsou splněné následující podmínky:

* Vaše metoda akce vrátí třídu modelu.
* K dispozici jsou odvozené třídy, které mohou být vráceny za běhu.
* Je potřeba znát za běhu, který tato akce vrátila odvozenou třídu.

Předpokládejme například, že váš podpis metody akce `Person` vrací typ, ale může `Student` vracet typ nebo `Instructor` , který je odvozen z `Person`. Chcete-li, aby formátovací modul zpracovával `Student` pouze objekty, ověřte typ [objektu](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) v objektu Context, který je `CanWriteResult` k metodě k dispozici. Všimněte si, že není nutné použít `CanWriteResult` při návratu `IActionResult`do metody Action; `CanWriteType` v takovém případě metoda přijímá typ modulu runtime.

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>Override ReadRequestBodyAsync/WriteResponseBodyAsync

Provedete skutečnou práci deserializace nebo serializace v `ReadRequestBodyAsync` nebo. `WriteResponseBodyAsync` Zvýrazněné řádky v následujícím příkladu ukazují, jak získat služby z kontejneru vkládání závislostí (nemůžete je získat z parametrů konstruktoru).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Jak konfigurovat MVC pro použití vlastního formátovacího modulu

Chcete-li použít vlastní formátovací modul, přidejte instanci formátovací třídy do `InputFormatters` kolekce nebo. `OutputFormatters`

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

Formátovací moduly jsou vyhodnocovány v pořadí, ve kterém je vložíte. První z nich má přednost.

## <a name="next-steps"></a>Další postup

* [Ukázková aplikace pro tento dokument](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), která implementuje jednoduché formátovací a výstupní formátovací moduly vCard Aplikace čte a zapisuje soubory vCard, které vypadají jako v následujícím příkladu:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

Chcete-li zobrazit výstup v programu vCard, spusťte aplikaci a odešlete požadavek GET s hlavičkou Accept "text `http://localhost:63313/api/contacts/` /vCard" do (při spuštění ze `http://localhost:5000/api/contacts/` sady Visual Studio) nebo (při spuštění z příkazového řádku).

Chcete-li přidat soubor vCard do kolekce kontaktů v paměti, odešlete požadavek post na stejnou adresu URL s hlavičkou Content-Type "text/vCard" a textem vCard v těle, který je formátován jako příklad výše.
