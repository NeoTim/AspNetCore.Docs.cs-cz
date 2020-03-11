---
title: Vazba vlastního modelu v ASP.NET Core
author: ardalis
description: Přečtěte si, jak vazba modelu umožňuje akcím kontroleru pracovat přímo s typy modelů v ASP.NET Core.
ms.author: riande
ms.date: 01/06/2020
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 511cf39bfedfc55d2f75842daf4445d2aaf4872d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659864"
---
# <a name="custom-model-binding-in-aspnet-core"></a>Vazba vlastního modelu v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Steve Smith](https://ardalis.com/) a [Kirka Larkin](https://twitter.com/serpent5)

Vazba modelu umožňuje akcím kontroleru pracovat přímo s typy modelů (předanými jako argumenty metody), nikoli požadavky HTTP. Mapování mezi příchozími daty požadavků a aplikačními modely je zpracováváno pomocí vazeb modelů. Vývojáři mohou pomocí implementace vlastních modelů modelu integrovat integrované funkce vazeb modelů (obvykle ale nemusíte psát vlastního poskytovatele).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Výchozí omezení pořadače modelu

Výchozí pořadače modelů podporují většinu běžných datových typů .NET Core a musí splňovat většinu potřeb vývojářů. Očekávají, že se vstupní text na základě požadavku váže přímo na typy modelů. Před vytvořením vazby může být nutné transformovat vstup. Například když máte klíč, který lze použít k vyhledání dat modelu. Pomocí vlastního pořadače modelu můžete načíst data založená na klíči.

## <a name="model-binding-review"></a>Kontrola vazeb modelů

Vazba modelu používá specifické definice pro typy, na kterých pracuje. *Jednoduchý typ* je převeden z jednoho řetězce ve vstupu. *Komplexní typ* je převeden z více vstupních hodnot. Rozhraní určuje rozdíl na základě existence `TypeConverter`. Pokud máte jednoduché `string` -> `SomeType` mapování, které nevyžaduje externí prostředky, doporučujeme vytvořit konvertor typu.

Před vytvořením vlastního pořadače modelu je potřeba zkontrolovat, jak jsou naimplementované existující vazby modelů. Vezměte v úvahu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, který se dá použít k převedení řetězců kódovaných v kódování Base64 na pole bajtů. Pole bajtů jsou často ukládána jako soubory nebo databáze objektů BLOB databáze.

### <a name="working-with-the-bytearraymodelbinder"></a>Práce s ByteArrayModelBinder

K reprezentaci binárních dat lze použít řetězce kódované v kódování Base64. Například obrázek může být kódovaný jako řetězec. Ukázka obsahuje obrázek jako řetězec kódovaný v kódování Base64 v souboru [Base64String. txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).

ASP.NET Core MVC může použít řetězec kódovaný v kódování Base64 a pomocí `ByteArrayModelBinder` ho převést na pole bajtů. <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> mapuje `byte[]` argumenty `ByteArrayModelBinder`:

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

Při vytváření vlastního pořadače modelů můžete implementovat vlastní typ `IModelBinderProvider` nebo použít <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.

Následující příklad ukazuje, jak použít `ByteArrayModelBinder` k převedení řetězce zakódovaného ve formátu base64 na `byte[]` a uložení výsledku do souboru:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Řetězec s kódováním base64 můžete do této metody rozhraní API poslat pomocí nástroje, jako je například [Poster](https://www.getpostman.com/):

![Postman](custom-model-binding/images/postman.png "Postman")

Pokud může pořadač svázat data požadavku s odpovídajícími pojmenovanými vlastnostmi nebo argumenty, vazba modelu bude úspěšná. Následující příklad ukazuje, jak použít `ByteArrayModelBinder` s modelem zobrazení:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a>Ukázka pořadače vlastního modelu

V této části implementujeme vlastní pořadač modelů, který:

- Převede příchozí data požadavku na klíčové argumenty silného typu.
- Pomocí Entity Framework Core načíst přidruženou entitu.
- Předá přidruženou entitu jako argument metodě Action.

Následující příklad používá atribut `ModelBinder` v modelu `Author`:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

V předchozím kódu atribut `ModelBinder` určuje typ `IModelBinder`, který by měl být použit k navázání `Author` parametrů akce.

Následující třída `AuthorEntityBinder` váže `Author` parametr tím, že načte entitu ze zdroje dat pomocí Entity Framework Core a `authorId`:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> Předchozí `AuthorEntityBinder` třída je určena k ilustraci vlastního pořadače modelů. Třída není určena k ilustraci osvědčených postupů pro scénář vyhledávání. Pro vyhledávání navažte `authorId` a Dotazujte databázi v metodě akce. Tento přístup odděluje neúspěšné vazby mezi modely z `NotFound`ch případů.

Následující kód ukazuje, jak použít `AuthorEntityBinder` v metodě akce:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

Atribut `ModelBinder` lze použít k použití `AuthorEntityBinder` na parametry, které nepoužívají výchozí konvence:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

V tomto příkladu, protože název argumentu není výchozí `authorId`, je zadán v parametru pomocí atributu `ModelBinder`. Metoda kontroleru i akce je zjednodušená v porovnání s vyhledáním entity v metodě Action. Logika pro načtení autora pomocí Entity Framework Core je přesunuta do pořadače modelů. To může být značné zjednodušení, pokud máte několik metod, které se vážou na model `Author`.

Atribut `ModelBinder` můžete použít pro jednotlivé vlastnosti modelu (například na ViewModel) nebo na parametry metody pro určení určitého modelu pořadače nebo názvu modelu pouze pro tento typ nebo akci.

### <a name="implementing-a-modelbinderprovider"></a>Implementace ModelBinderProvider

Místo použití atributu můžete implementovat `IModelBinderProvider`. Tímto způsobem jsou implementovány předdefinované vazby rozhraní. Když zadáte typ, na kterém bude pořadač fungovat, zadáte typ argumentu, který vytváří, **nikoli** vstup, který váš pořadač akceptuje. Následující poskytovatel pořadače spolupracuje s `AuthorEntityBinder`. Když je přidána do kolekce poskytovatelů MVC, nemusíte používat atribut `ModelBinder` u parametrů `Author` nebo `Author`.

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Poznámka: předchozí kód vrátí `BinderTypeModelBinder`. `BinderTypeModelBinder` slouží jako továrna pro modelové vazby a poskytuje vkládání závislostí (DI). `AuthorEntityBinder` vyžaduje DI pro přístup k EF Core. Pokud pořadač modelů vyžaduje služby od DI, použijte `BinderTypeModelBinder`.

Pokud chcete použít vlastního poskytovatele pořadače modelů, přidejte ho do `ConfigureServices`:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

Při vyhodnocování pořadačů modelů se kolekce zprostředkovatelů zkoumá v daném pořadí. Použije se první zprostředkovatel, který vrací Binder. Pokud přidáte poskytovatele na konec kolekce, může to mít za následek volání předdefinovaného pořadače modelu předtím, než má vlastní pořadač možnost. V tomto příkladu je vlastní zprostředkovatel přidán na začátek kolekce, aby se zajistilo jeho použití pro `Author` argumenty akce.

### <a name="polymorphic-model-binding"></a>Polymorfní vazba modelu

Vazba na různé modely odvozených typů se označuje jako polymorfní vazba modelu. Polymorfní vazba vlastního modelu je požadována v případě, že hodnota požadavku musí být vázána na konkrétní typ odvozeného modelu. Polymorfní vazba modelu:

* Není typický pro REST API, která je navržená tak, aby spolupracovala se všemi jazyky.
* To je obtížné kvůli vázaným modelům.

Nicméně pokud aplikace vyžaduje vazbu polymorfního modelu, implementace může vypadat jako v následujícím kódu:

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Doporučení a osvědčené postupy

Vlastní pořadače modelů:

- Neměl by se pokoušet nastavit stavové kódy nebo vracet výsledky (například 404 se nenašly). Pokud vazba modelu selže, měla by být chyba zpracována [filtrem akcí](xref:mvc/controllers/filters) nebo logikou v rámci samotné metody akce.
- Jsou nejužitečnější pro odstranění opakujícího se kódu a přeshraničních otázek z metod akcí.
- Obvykle by neměl být použit k převodu řetězce na vlastní typ, <xref:System.ComponentModel.TypeConverter> je obvykle lepší volbou.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

[Steve Smith](https://ardalis.com/)

Vazba modelu umožňuje akcím kontroleru pracovat přímo s typy modelů (předanými jako argumenty metody), nikoli požadavky HTTP. Mapování mezi příchozími daty požadavků a aplikačními modely je zpracováváno pomocí vazeb modelů. Vývojáři mohou pomocí implementace vlastních modelů modelu integrovat integrované funkce vazeb modelů (obvykle ale nemusíte psát vlastního poskytovatele).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Výchozí omezení pořadače modelu

Výchozí pořadače modelů podporují většinu běžných datových typů .NET Core a musí splňovat většinu potřeb vývojářů. Očekávají, že se vstupní text na základě požadavku váže přímo na typy modelů. Před vytvořením vazby může být nutné transformovat vstup. Například když máte klíč, který lze použít k vyhledání dat modelu. Pomocí vlastního pořadače modelu můžete načíst data založená na klíči.

## <a name="model-binding-review"></a>Kontrola vazeb modelů

Vazba modelu používá specifické definice pro typy, na kterých pracuje. *Jednoduchý typ* je převeden z jednoho řetězce ve vstupu. *Komplexní typ* je převeden z více vstupních hodnot. Rozhraní určuje rozdíl na základě existence `TypeConverter`. Pokud máte jednoduché `string` -> `SomeType` mapování, které nevyžaduje externí prostředky, doporučujeme vytvořit konvertor typu.

Před vytvořením vlastního pořadače modelu je potřeba zkontrolovat, jak jsou naimplementované existující vazby modelů. Vezměte v úvahu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, který se dá použít k převedení řetězců kódovaných v kódování Base64 na pole bajtů. Pole bajtů jsou často ukládána jako soubory nebo databáze objektů BLOB databáze.

### <a name="working-with-the-bytearraymodelbinder"></a>Práce s ByteArrayModelBinder

K reprezentaci binárních dat lze použít řetězce kódované v kódování Base64. Například obrázek může být kódovaný jako řetězec. Ukázka obsahuje obrázek jako řetězec kódovaný v kódování Base64 v souboru [Base64String. txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).

ASP.NET Core MVC může použít řetězec kódovaný v kódování Base64 a pomocí `ByteArrayModelBinder` ho převést na pole bajtů. <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> mapuje `byte[]` argumenty `ByteArrayModelBinder`:

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

Při vytváření vlastního pořadače modelů můžete implementovat vlastní typ `IModelBinderProvider` nebo použít <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.

Následující příklad ukazuje, jak použít `ByteArrayModelBinder` k převedení řetězce zakódovaného ve formátu base64 na `byte[]` a uložení výsledku do souboru:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

Řetězec s kódováním base64 můžete do této metody rozhraní API poslat pomocí nástroje, jako je například [Poster](https://www.getpostman.com/):

![Postman](custom-model-binding/images/postman.png "Postman")

Pokud může pořadač svázat data požadavku s odpovídajícími pojmenovanými vlastnostmi nebo argumenty, vazba modelu bude úspěšná. Následující příklad ukazuje, jak použít `ByteArrayModelBinder` s modelem zobrazení:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Ukázka pořadače vlastního modelu

V této části implementujeme vlastní pořadač modelů, který:

- Převede příchozí data požadavku na klíčové argumenty silného typu.
- Pomocí Entity Framework Core načíst přidruženou entitu.
- Předá přidruženou entitu jako argument metodě Action.

Následující příklad používá atribut `ModelBinder` v modelu `Author`:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

V předchozím kódu atribut `ModelBinder` určuje typ `IModelBinder`, který by měl být použit k navázání `Author` parametrů akce.

Následující třída `AuthorEntityBinder` váže `Author` parametr tím, že načte entitu ze zdroje dat pomocí Entity Framework Core a `authorId`:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> Předchozí `AuthorEntityBinder` třída je určena k ilustraci vlastního pořadače modelů. Třída není určena k ilustraci osvědčených postupů pro scénář vyhledávání. Pro vyhledávání navažte `authorId` a Dotazujte databázi v metodě akce. Tento přístup odděluje neúspěšné vazby mezi modely z `NotFound`ch případů.

Následující kód ukazuje, jak použít `AuthorEntityBinder` v metodě akce:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

Atribut `ModelBinder` lze použít k použití `AuthorEntityBinder` na parametry, které nepoužívají výchozí konvence:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

V tomto příkladu, protože název argumentu není výchozí `authorId`, je zadán v parametru pomocí atributu `ModelBinder`. Metoda kontroleru i akce je zjednodušená v porovnání s vyhledáním entity v metodě Action. Logika pro načtení autora pomocí Entity Framework Core je přesunuta do pořadače modelů. To může být značné zjednodušení, pokud máte několik metod, které se vážou na model `Author`.

Atribut `ModelBinder` můžete použít pro jednotlivé vlastnosti modelu (například na ViewModel) nebo na parametry metody pro určení určitého modelu pořadače nebo názvu modelu pouze pro tento typ nebo akci.

### <a name="implementing-a-modelbinderprovider"></a>Implementace ModelBinderProvider

Místo použití atributu můžete implementovat `IModelBinderProvider`. Tímto způsobem jsou implementovány předdefinované vazby rozhraní. Když zadáte typ, na kterém bude pořadač fungovat, zadáte typ argumentu, který vytváří, **nikoli** vstup, který váš pořadač akceptuje. Následující poskytovatel pořadače spolupracuje s `AuthorEntityBinder`. Když je přidána do kolekce poskytovatelů MVC, nemusíte používat atribut `ModelBinder` u parametrů `Author` nebo `Author`.

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Poznámka: předchozí kód vrátí `BinderTypeModelBinder`. `BinderTypeModelBinder` slouží jako továrna pro modelové vazby a poskytuje vkládání závislostí (DI). `AuthorEntityBinder` vyžaduje DI pro přístup k EF Core. Pokud pořadač modelů vyžaduje služby od DI, použijte `BinderTypeModelBinder`.

Pokud chcete použít vlastního poskytovatele pořadače modelů, přidejte ho do `ConfigureServices`:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

Při vyhodnocování pořadačů modelů se kolekce zprostředkovatelů zkoumá v daném pořadí. Použije se první zprostředkovatel, který vrací Binder. Pokud přidáte poskytovatele na konec kolekce, může to mít za následek volání předdefinovaného pořadače modelu předtím, než má vlastní pořadač možnost. V tomto příkladu je vlastní zprostředkovatel přidán na začátek kolekce, aby se zajistilo jeho použití pro `Author` argumenty akce.

### <a name="polymorphic-model-binding"></a>Polymorfní vazba modelu

Vazba na různé modely odvozených typů se označuje jako polymorfní vazba modelu. Polymorfní vazba vlastního modelu je požadována v případě, že hodnota požadavku musí být vázána na konkrétní typ odvozeného modelu. Polymorfní vazba modelu:

* Není typický pro REST API, která je navržená tak, aby spolupracovala se všemi jazyky.
* To je obtížné kvůli vázaným modelům.

Nicméně pokud aplikace vyžaduje vazbu polymorfního modelu, implementace může vypadat jako v následujícím kódu:

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Doporučení a osvědčené postupy

Vlastní pořadače modelů:

- Neměl by se pokoušet nastavit stavové kódy nebo vracet výsledky (například 404 se nenašly). Pokud vazba modelu selže, měla by být chyba zpracována [filtrem akcí](xref:mvc/controllers/filters) nebo logikou v rámci samotné metody akce.
- Jsou nejužitečnější pro odstranění opakujícího se kódu a přeshraničních otázek z metod akcí.
- Obvykle by neměl být použit k převodu řetězce na vlastní typ, <xref:System.ComponentModel.TypeConverter> je obvykle lepší volbou.

::: moniker-end
