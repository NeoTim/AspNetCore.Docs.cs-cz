---
title: Vazba vlastního modelu v ASP.NET Core
author: ardalis
description: Přečtěte si, jak vazba modelu umožňuje akcím kontroleru pracovat přímo s typy modelů v ASP.NET Core.
ms.author: riande
ms.date: 11/13/2018
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: b2fbe6a9f11315d1fb8863fbf62e8929c7ff3fc2
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186880"
---
# <a name="custom-model-binding-in-aspnet-core"></a>Vazba vlastního modelu v ASP.NET Core

[Steve Smith](https://ardalis.com/)

Vazba modelu umožňuje akcím kontroleru pracovat přímo s typy modelů (předanými jako argumenty metody), nikoli požadavky HTTP. Mapování mezi příchozími daty požadavků a aplikačními modely je zpracováváno pomocí vazeb modelů. Vývojáři mohou pomocí implementace vlastních modelů modelu integrovat integrované funkce vazeb modelů (obvykle ale nemusíte psát vlastního poskytovatele).

[Zobrazení nebo stažení ukázky z GitHubu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a>Výchozí omezení pořadače modelu

Výchozí pořadače modelů podporují většinu běžných datových typů .NET Core a musí splňovat většinu potřeb vývojářů. Očekávají, že se vstupní text na základě požadavku váže přímo na typy modelů. Před vytvořením vazby může být nutné transformovat vstup. Například když máte klíč, který lze použít k vyhledání dat modelu. Pomocí vlastního pořadače modelu můžete načíst data založená na klíči.

## <a name="model-binding-review"></a>Kontrola vazeb modelů

Vazba modelu používá specifické definice pro typy, na kterých pracuje. *Jednoduchý typ* je převeden z jednoho řetězce ve vstupu. *Komplexní typ* je převeden z více vstupních hodnot. Rozhraní určuje rozdíl na základě existence `TypeConverter`. Pokud máte jednoduché `string`  ->  mapování,kterénevyžadujeexterníprostředky,doporučujemevytvořitkonvertortypu.`SomeType`

Před vytvořením vlastního pořadače modelu je potřeba zkontrolovat, jak jsou naimplementované existující vazby modelů. Vezměte v úvahu [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) , který se dá použít k převedení řetězců kódovaných v kódování Base64 na pole bajtů. Pole bajtů jsou často ukládána jako soubory nebo databáze objektů BLOB databáze.

### <a name="working-with-the-bytearraymodelbinder"></a>Práce s ByteArrayModelBinder

K reprezentaci binárních dat lze použít řetězce kódované v kódování Base64. Například následující obrázek může být kódovaný jako řetězec.

![dotnet – robot](custom-model-binding/images/bot.png "dotnet – robot")

Malá část kódovaného řetězce je znázorněna na následujícím obrázku:

![dotnet – kódování bot](custom-model-binding/images/encoded-bot.png "dotnet – kódování bot")

Postupujte podle pokynů v [souboru Readme ukázky](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) a převeďte řetězec kódovaný v kódování Base64 na soubor.

ASP.NET Core MVC může přebírat řetězec kódovaný v kódování Base64 a použít `ByteArrayModelBinder` ho k převedení na pole bajtů. [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) , který implementuje argumenty mapování `byte[]` [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) na `ByteArrayModelBinder`:

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

Při vytváření vlastního pořadače modelu můžete implementovat vlastní `IModelBinderProvider` typ nebo použít [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).

Následující příklad ukazuje, jak použít `ByteArrayModelBinder` pro převod řetězce kódovaného ve formátu base64 `byte[]` na a a výsledek do souboru:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

Řetězec s kódováním base64 můžete do této metody rozhraní API poslat pomocí nástroje, jako je například [Poster](https://www.getpostman.com/):

![postman](custom-model-binding/images/postman.png "postman")

Pokud může pořadač svázat data požadavku s odpovídajícími pojmenovanými vlastnostmi nebo argumenty, vazba modelu bude úspěšná. Následující příklad ukazuje, jak použít `ByteArrayModelBinder` s modelem zobrazení:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Ukázka pořadače vlastního modelu

V této části implementujeme vlastní pořadač modelů, který:

- Převede příchozí data požadavku na klíčové argumenty silného typu.
- Pomocí Entity Framework Core načíst přidruženou entitu.
- Předá přidruženou entitu jako argument metodě Action.

Následující příklad používá `ModelBinder` atribut `Author` v modelu:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

V předchozím kódu `ModelBinder` atribut určuje `IModelBinder` typ, který má být použit k navázání `Author` parametrů akce.

Následující `AuthorEntityBinder` třída váže `Author` parametr tím, že načte entitu ze `authorId`zdroje dat pomocí Entity Framework Core a:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> Předchozí `AuthorEntityBinder` třída je určena k ilustraci vlastního pořadače modelů. Třída není určena k ilustraci osvědčených postupů pro scénář vyhledávání. Pro vyhledávání navažte `authorId` a Dotazujte databázi v metodě Action. Tento přístup odděluje selhání vazeb modelu z `NotFound` případů.

Následující kód ukazuje, jak použít `AuthorEntityBinder` metodu akce:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

Atribut lze použít k `AuthorEntityBinder` aplikování parametrů, které nepoužívají výchozí konvence: `ModelBinder`

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

V tomto příkladu, protože název argumentu není výchozí `authorId`, je zadán v parametru `ModelBinder` pomocí atributu. Metoda kontroleru i akce je zjednodušená v porovnání s vyhledáním entity v metodě Action. Logika pro načtení autora pomocí Entity Framework Core je přesunuta do pořadače modelů. To může být značné zjednodušení, pokud máte několik metod, které se vážou k `Author` modelu.

Můžete použít `ModelBinder` atribut na vlastnosti jednotlivých modelů (například na ViewModel) nebo na parametry metody pro určení určitého modelu pořadače nebo názvu modelu pouze pro tento typ nebo akci.

### <a name="implementing-a-modelbinderprovider"></a>Implementace ModelBinderProvider

Místo použití atributu můžete implementovat `IModelBinderProvider`. Tímto způsobem jsou implementovány předdefinované vazby rozhraní. Když zadáte typ, na kterém bude pořadač fungovat, zadáte typ argumentu, který vytváří, **nikoli** vstup, který váš pořadač akceptuje. Následující poskytovatel pořadače spolupracuje s `AuthorEntityBinder`. Když je přidána do kolekce zprostředkovatelů MVC, nemusíte používat `ModelBinder` atribut u `Author` parametrů nebo `Author`typu.

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Poznámka: Předchozí kód vrátí `BinderTypeModelBinder`. `BinderTypeModelBinder`slouží jako továrna pro pojiva modelů a poskytuje vkládání závislostí (DI). Pro přístup k EF Core vyžadujedi.`AuthorEntityBinder` Použijte `BinderTypeModelBinder` , pokud pořadač modelů vyžaduje služby od di.

Pokud chcete použít vlastního poskytovatele pořadače modelů, přidejte ho `ConfigureServices`do:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

Při vyhodnocování pořadačů modelů se kolekce zprostředkovatelů zkoumá v daném pořadí. Použije se první zprostředkovatel, který vrací Binder.

Následující obrázek ukazuje výchozí pojiva modelu z ladicího programu.

![výchozí modely pojiv](custom-model-binding/images/default-model-binders.png "výchozí modely pojiv")

Pokud přidáte poskytovatele na konec kolekce, může to mít za následek volání předdefinovaného pořadače modelu předtím, než má vlastní pořadač možnost. V tomto příkladu je vlastní zprostředkovatel přidán na začátek kolekce, aby se zajistilo jeho použití pro `Author` argumenty akce.

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

### <a name="polymorphic-model-binding"></a>Polymorfní vazba modelu

Vazba na různé modely odvozených typů se označuje jako polymorfní vazba modelu. Polymorfní vazba vlastního modelu je požadována v případě, že hodnota požadavku musí být vázána na konkrétní typ odvozeného modelu. Polymorfní vazba modelu:

* Není typický pro REST API, která je navržená tak, aby spolupracovala se všemi jazyky.
* To je obtížné kvůli vázaným modelům.

Nicméně pokud aplikace vyžaduje vazbu polymorfního modelu, implementace může vypadat jako v následujícím kódu:

[!code-csharp[](custom-model-binding/3.0sample/PolymorphicModelBinding/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Doporučení a osvědčené postupy

Vlastní pořadače modelů:

- Neměl by se pokoušet nastavit stavové kódy nebo vracet výsledky (například 404 se nenašly). Pokud vazba modelu selže, měla by být chyba zpracována [filtrem akcí](xref:mvc/controllers/filters) nebo logikou v rámci samotné metody akce.
- Jsou nejužitečnější pro odstranění opakujícího se kódu a přeshraničních otázek z metod akcí.
- Obvykle by neměl být použit k převodu řetězce na vlastní typ, [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) je obvykle lepší volbou.
