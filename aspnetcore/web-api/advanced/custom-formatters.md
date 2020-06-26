---
title: Vlastní formátovací moduly v ASP.NET Core Web API
author: rick-anderson
description: Naučte se vytvářet a používat vlastní formátovací moduly pro webová rozhraní API v ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 27819f77cf86c946ab0415d3583dfbab80a24cf5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408861"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Vlastní formátovací moduly v ASP.NET Core Web API

[Kirka Larkin](https://twitter.com/serpent5) a [Dykstra](https://github.com/tdykstra).

ASP.NET Core MVC podporuje výměnu dat ve webových rozhraních API pomocí formátování vstupu a výstupu. Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding). Výstupní formátovací moduly se používají k [formátování odpovědí](xref:web-api/advanced/formatting).

Rozhraní poskytuje předdefinované vstupní a výstupní formátovací moduly pro JSON a XML. Poskytuje vestavěný výstupní formátovací modul pro prostý text, ale neposkytuje vstupní formátovací modul pro prostý text.

Tento článek ukazuje, jak přidat podporu pro další formáty vytvořením vlastních formátovacích modulů. Příklad vlastního formátovacího modulu zadávání prostého textu najdete v tématu [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) na GitHubu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Kdy použít vlastní formátovací moduly

Pomocí vlastního formátovacího modulu můžete přidat podporu pro typ obsahu, který není zpracován formátovacími moduly Bult.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Přehled způsobu použití vlastního formátovacího modulu

Vytvoření vlastního formátovacího modulu:

* Pro serializaci dat odesílaných klientovi vytvořte výstupní třídu formátování.
* Pro deserialzing data přijatá z klienta vytvořte vstupní třídu formátování Input.
* Přidejte instance formátovacích tříd do `InputFormatters` kolekcí a `OutputFormatters` v [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).

## <a name="how-to-create-a-custom-formatter-class"></a>Jak vytvořit vlastní třídu formátování

Vytvoření formátovacího modulu:

* Odvodit třídu od příslušné základní třídy. Ukázková aplikace je odvozena z <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> a <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Zadejte platné typy médií a kódování v konstruktoru.
* Přepište <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> metody a.
* Přepište <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> `WriteResponseBodyAsync` metody a.

Následující kód ukazuje `VcardOutputFormatter` třídu z [ukázky](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=snippet)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Odvození od příslušné základní třídy

Pro typy textových médií (například vCard) je odvozen ze základní třídy [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) nebo [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) .

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Pro binární typy je odvozeno od základní třídy [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) nebo [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) .

### <a name="specify-valid-media-types-and-encodings"></a>Zadejte platné typy médií a kódování.

V konstruktoru zadejte platné typy médií a kódování přidáním do `SupportedMediaTypes` `SupportedEncodings` kolekce a.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=ctor)]

Třída formátovacího modulu **nemůže použít injektáže** konstruktoru pro jeho závislosti. Například `ILogger<VcardOutputFormatter>` nelze přidat jako parametr do konstruktoru. Chcete-li získat přístup ke službám, použijte kontextový objekt, který se předává do metod. Příklad kódu v tomto článku a [Ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) ukazuje, jak to provést.

### <a name="override-canreadtype-and-canwritetype"></a>Přepsat CanReadType a CanWriteType

Určete typ pro deserializaci nebo serializaci pomocí přepsání `CanReadType` `CanWriteType` metod nebo. Například vytváření textu v souborovém formátu vCard z `Contact` typu a naopak.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a>Metoda CanWriteResult

V některých případech `CanWriteResult` je nutné přepsat místo `CanWriteType` . Použijte `CanWriteResult` , pokud jsou splněné následující podmínky:

* Metoda Action vrátí třídu modelu.
* K dispozici jsou odvozené třídy, které mohou být vráceny za běhu.
* Odvozená třída vrácená akcí musí být známá za běhu.

Předpokládejme například metodu Action:

* Signatura vrátí `Person` typ.
* Může vracet `Student` typ nebo `Instructor` , který je odvozen z `Person` . 

Aby formátovací modul zpracovával pouze `Student` objekty, ověřte typ [objektu](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) v objektu Context, který je k metodě k dispozici `CanWriteResult` . Když metoda akce vrátí `IActionResult` :

* Není nutné používat `CanWriteResult` .
* `CanWriteType`Metoda přijímá typ modulu runtime.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Přepsat ReadRequestBodyAsync a WriteResponseBodyAsync

Deserializace nebo serializace je prováděna v `ReadRequestBodyAsync` nebo `WriteResponseBodyAsync` . Následující příklad ukazuje, jak získat služby z kontejneru injektáže na základě závislostí. Z parametrů konstruktoru nelze získat služby.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=writeresponse)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Jak konfigurovat MVC pro použití vlastního formátovacího modulu

Chcete-li použít vlastní formátovací modul, přidejte instanci formátovací třídy do `InputFormatters` `OutputFormatters` kolekce nebo.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/3.1sample/Startup.cs?name=mvcoptions)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Formátovací moduly jsou vyhodnocovány v pořadí, ve kterém je vložíte. První z nich má přednost.

## <a name="the-completed-vcardinputformatter-class"></a>Dokončená `VcardInputFormatter` Třída

Následující kód ukazuje `VcardInputFormatter` třídu z [ukázky](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardInputFormatter.cs?name=snippet)]

## <a name="test-the-app"></a>Otestování aplikace

[Spusťte ukázkovou aplikaci pro tento článek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), která implementuje základní formátovací a výstupní formátovací moduly vCard. Aplikace čte a zapisuje soubory vCard podobně jako v následujícím příkladu:

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

## <a name="additional-resources"></a>Další zdroje

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
