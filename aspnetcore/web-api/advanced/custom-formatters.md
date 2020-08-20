---
title: Vlastní formátovací moduly v ASP.NET Core Web API
author: rick-anderson
description: Naučte se vytvářet a používat vlastní formátovací moduly pro webová rozhraní API v ASP.NET Core.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 9f87d02dd3abe6dca8db495e482ccf9c440a2469
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627543"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Vlastní formátovací moduly v ASP.NET Core Web API

[Kirka Larkin](https://twitter.com/serpent5) a [Dykstra](https://github.com/tdykstra).

ASP.NET Core MVC podporuje výměnu dat ve webových rozhraních API pomocí formátování vstupu a výstupu. Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding). Výstupní formátovací moduly se používají k [formátování odpovědí](xref:web-api/advanced/formatting).

Rozhraní poskytuje předdefinované vstupní a výstupní formátovací moduly pro JSON a XML. Poskytuje vestavěný výstupní formátovací modul pro prostý text, ale neposkytuje vstupní formátovací modul pro prostý text.

Tento článek ukazuje, jak přidat podporu pro další formáty vytvořením vlastních formátovacích modulů. Příklad vlastního formátovacího modulu zadávání prostého textu najdete v tématu [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) na GitHubu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Kdy použít vlastní formátovací moduly

Pomocí vlastního formátovacího modulu můžete přidat podporu pro typ obsahu, který není zpracován vestavěnými formátovacími moduly.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Přehled způsobu použití vlastního formátovacího modulu

Vytvoření vlastního formátovacího modulu:

* Pro serializaci dat odesílaných klientovi vytvořte výstupní třídu formátování.
* Pro deserializaci dat přijatých z klienta vytvořte vstupní třídu formátování Input.
* Přidejte instance formátovacích tříd do `InputFormatters` kolekcí a `OutputFormatters` v rámci <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

## <a name="how-to-create-a-custom-formatter-class"></a>Jak vytvořit vlastní třídu formátování

Vytvoření formátovacího modulu:

* Odvodit třídu od příslušné základní třídy. Ukázková aplikace je odvozena z <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> a <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Zadejte platné typy médií a kódování v konstruktoru.
* Přepište <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> metody a.
* Přepište <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> `WriteResponseBodyAsync` metody a.

Následující kód ukazuje `VcardOutputFormatter` třídu z [ukázky](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Odvození od příslušné základní třídy

Pro typy textových médií (například vCard) je odvozen ze <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> základní třídy nebo.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

Pro binární typy je odvozen ze <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> základní třídy nebo.

### <a name="specify-valid-media-types-and-encodings"></a>Zadejte platné typy médií a kódování.

V konstruktoru zadejte platné typy médií a kódování přidáním do `SupportedMediaTypes` `SupportedEncodings` kolekce a.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

Třída formátovacího modulu **nemůže použít injektáže** konstruktoru pro jeho závislosti. Například `ILogger<VcardOutputFormatter>` nelze přidat jako parametr do konstruktoru. Chcete-li získat přístup ke službám, použijte kontextový objekt, který se předává do metod. Příklad kódu v tomto článku a [Ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ukazuje, jak to provést.

### <a name="override-canreadtype-and-canwritetype"></a>Přepsat CanReadType a CanWriteType

Určete typ pro deserializaci nebo serializaci pomocí přepsání `CanReadType` `CanWriteType` metod nebo. Například vytváření textu v souborovém formátu vCard z `Contact` typu a naopak.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a>Metoda CanWriteResult

V některých případech `CanWriteResult` je nutné přepsat místo `CanWriteType` . Použijte `CanWriteResult` , pokud jsou splněné následující podmínky:

* Metoda Action vrátí třídu modelu.
* K dispozici jsou odvozené třídy, které mohou být vráceny za běhu.
* Odvozená třída vrácená akcí musí být známá za běhu.

Předpokládejme například metodu Action:

* Signatura vrátí `Person` typ.
* Může vracet `Student` typ nebo `Instructor` , který je odvozen z `Person` . 

Aby formátovací modul zpracovával pouze `Student` objekty, ověřte typ <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> v objektu Context, který je k metodě k dispozici `CanWriteResult` . Když metoda akce vrátí `IActionResult` :

* Není nutné používat `CanWriteResult` .
* `CanWriteType`Metoda přijímá typ modulu runtime.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Přepsat ReadRequestBodyAsync a WriteResponseBodyAsync

Deserializace nebo serializace je prováděna v `ReadRequestBodyAsync` nebo `WriteResponseBodyAsync` . Následující příklad ukazuje, jak získat služby z kontejneru injektáže na základě závislostí. Z parametrů konstruktoru nelze získat služby.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Jak konfigurovat MVC pro použití vlastního formátovacího modulu

Chcete-li použít vlastní formátovací modul, přidejte instanci formátovací třídy do `InputFormatters` `OutputFormatters` kolekce nebo.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Formátovací moduly jsou vyhodnocovány v pořadí, ve kterém je vložíte. První z nich má přednost.

## <a name="the-complete-vcardinputformatter-class"></a>Úplná `VcardInputFormatter` Třída

Následující kód ukazuje `VcardInputFormatter` třídu z [ukázky](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a>Otestování aplikace

[Spusťte ukázkovou aplikaci pro tento článek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), která implementuje základní formátovací a výstupní formátovací moduly vCard. Aplikace čte a zapisuje soubory vCard podobně jako v následujícím příkladu:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

Chcete-li zobrazit výstup v programu vCard, spusťte aplikaci a odešlete požadavek GET s hlavičkou Accept `text/vcard` do `https://localhost:5001/api/contacts` .

Chcete-li přidat soubor vCard do kolekce kontaktů v paměti:

* Odešlete `Post` požadavek na `/api/contacts` nástroj, jako je například post.
* Nastavte `Content-Type` hlavičku na `text/vcard` .
* Nastavte `vCard` text v těle formátovaného jako v předchozím příkladu.

## <a name="additional-resources"></a>Další materiály

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
