---
title: Vlastní vazba modelu v ASP.NET Core
author: ardalis
description: Zjistěte, jak vazby modelu umožňuje akce kontroleru pracovat přímo s typy modelů v ASP.NET Core.
ms.author: riande
ms.date: 11/13/2018
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 33551c9fc22561b992b4a09a4c7187ade136c09c
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410242"
---
# <a name="custom-model-binding-in-aspnet-core"></a>Vlastní vazba modelu v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/)

Vazby modelu umožňuje akce kontroleru pracovat přímo s typy modelů (předané jako argumenty metody), spíše než požadavky HTTP. Mapování mezi příchozí žádosti o datové a aplikační modely zařizuje služba vazače modelů. Vývojáři mohou rozšířit funkci vazby modelu předdefinovaných implementací vazače modelů vlastní (i když obvykle není nutné napsat vlastního zprostředkovatele).

[Zobrazit nebo stáhnout ukázky z Githubu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a>Výchozí omezení vazače modelu

Vazače modelů výchozí podporu většiny běžných typů dat .NET Core a by měl splňovat potřeby Většina vývojářů. Očekávají se vytvořit vazbu textový vstup z požadavku přímo s typy modelů. Můžete potřebovat, který umožňuje transformovat vstupní před jeho vazbu. Například pokud máte klíč, který slouží k vyhledání dat modelu. Můžete použít vlastní vazač modelu pro načtení dat na základě klíče.

## <a name="model-binding-review"></a>Kontrola vazby modelu

Vazby modelu používá konkrétní definice pro typy, které se pracuje. A *jednoduchý typ* převést z jednoho řetězce ve vstupu. A *komplexní typ* převést z více vstupních hodnot. Rozhraní určuje rozdíl založené na existenci `TypeConverter`. Doporučujeme vytvořit konvertor typu, pokud máte jednoduchou `string`  ->  `SomeType` mapování, které nevyžaduje externí prostředky.

Před vytvořením vlastní vlastní vazač modelu, je vhodné si předtím prostudovali jak existující model jsou implementovány vazače. Vezměte v úvahu [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) kterých jde převést pole bajtů řetězce s kódováním base64. Bajtová pole jsou často uložené jako soubory nebo pole objektů BLOB v databázi.

### <a name="working-with-the-bytearraymodelbinder"></a>Práce s ByteArrayModelBinder

Řetězce s kódováním base64 slouží k reprezentaci binární data. Na následujícím obrázku například může být zakódován jako řetězec.

![DotNet bot](custom-model-binding/images/bot.png "dotnet bot")

Malou část řetězce s kódováním je vidět na následujícím obrázku:

![DotNet bot kódovaný](custom-model-binding/images/encoded-bot.png "dotnet bot kódování")

Postupujte podle pokynů [vzorku README](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) pro převod řetězce s kódováním base64 do souboru.

Můžete provést řetězec s kódováním base64 a používat ASP.NET Core MVC `ByteArrayModelBinder` převést do bajtového pole. [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) která implementuje [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) mapuje `byte[]` argumenty `ByteArrayModelBinder`:

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

Při vytváření vlastní vlastní vazač modelu, můžete implementovat vlastní `IModelBinderProvider` typ nebo použít [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).

Následující příklad ukazuje, jak používat `ByteArrayModelBinder` převést na řetězec s kódováním base64 `byte[]` a uložte výsledky do souboru:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

Můžete vytvořit řetězec s kódováním base64 do této metody rozhraní api pomocí některého nástroje, například [Postman](https://www.getpostman.com/):

![postman](custom-model-binding/images/postman.png "postman")

Tak dlouho, dokud vazač svázat data požadavku na příslušně pojmenovaných vlastnosti nebo argumenty, proběhne úspěšně vazby modelu. Následující příklad ukazuje, jak používat `ByteArrayModelBinder` pomocí zobrazení modelu:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Ukázka vlastního modelu vazače

V této části jsme budete implementovat vlastní vazač modelu, který:

- Převede příchozí žádosti o data silného typu klíče argumenty.
- Používá Entity Framework Core se načíst související entity.
- Související entita se předává jako argument do metody akce.

Následující ukázkový používá `ModelBinder` atribut na `Author` modelu:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

V předchozím kódu `ModelBinder` atribut určuje typ `IModelBinder` , který má použít k vytvoření vazby `Author` parametry akce.

Následující `AuthorEntityBinder` třídy vytvoří vazbu `Author` parametru načtení entity ze zdroje dat pomocí Entity Framework Core a `authorId`:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> Předchozí `AuthorEntityBinder` třídy je určený k objasnění vlastní vazač modelu. Třída není určený k objasnění osvědčené postupy pro scénář vyhledávání. Vyhledávání, vytvořit vazbu `authorId` a dotazování databáze v metodě akce. Tento přístup odděluje selhání vazby modelu z `NotFound` případy.

Následující kód ukazuje způsob použití `AuthorEntityBinder` v metodě akce:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

`ModelBinder` Atribut je možné použít `AuthorEntityBinder` na parametry, které nepoužívají výchozí konvence:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

V tomto příkladu, protože název argumentu není výchozí `authorId`, je zadán pomocí parametru `ModelBinder` atribut. Všimněte si, že metoda kontroleru a akce jsou zjednodušené ve srovnání s vyhledávání entit v metodě akce. Logika pro načtení Autor pomocí Entity Framework Core se přesune do vazače modelu. To může být značné zjednodušení, pokud máte několik metod, které svázat `Author` modelu.

Můžete použít `ModelBinder` atribut vlastnosti jednotlivých modelu (například na viewmodel) nebo na parametry metod akce k určení vazač modelu nebo název modelu pro právě tento typ nebo akce.

### <a name="implementing-a-modelbinderprovider"></a>Implementace ModelBinderProvider

Místo použití atributu, můžete implementovat `IModelBinderProvider`. To je, jak se implementují vestavěnou architekturou vazače. Když zadáte typ pracuje vaše vazač, zadejte typ argumentu vytvoří, **není** přijímá vstup vaše vazače. Následující zprostředkovatele vazače spolupracuje `AuthorEntityBinder`. Když se přidá do kolekce MVC od poskytovatelů, není nutné použít `ModelBinder` atribut na `Author` nebo `Author`-silné parametry.

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Poznámka: Vrátí předchozí kód `BinderTypeModelBinder`. `BinderTypeModelBinder` funguje jako objekt pro vytváření pro vazače modelů a poskytuje injektáž závislostí (DI). `AuthorEntityBinder` Vyžaduje DI pro přístup k EF Core. Použití `BinderTypeModelBinder` Pokud vyžaduje služby DI vaše vazače modelu.

Použití zprostředkovatele vazače modelu vlastní, přidejte ji kliknutím na `ConfigureServices`:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

Při vyhodnocování vazače modelů, je kolekce zprostředkovatelů zkoumány podle pořadí. Prvním poskytovatelem, který vrací vazač se používá.

Následující obrázek ukazuje výchozí vazače modelů z ladicího programu.

![Výchozí vazače modelů](custom-model-binding/images/default-model-binders.png "výchozí vazače modelů")

Vazač modelu integrované volána před vlastní vázací objekt má možnost můžou výsledkem přidání poskytovatele na konec kolekce. V tomto příkladu vlastního zprostředkovatele se přidá na začátek kolekce, kterou chcete zajistit, že se používá pro `Author` argumentů.

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

## <a name="recommendations-and-best-practices"></a>Doporučení a osvědčené postupy

Vazače modelů vlastní:

- By se neměly pokoušet stavové kódy nebo vrátit výsledky (například 404 Nenalezeno). Pokud vazba modelu selže, [filtr akce](xref:mvc/controllers/filters) nebo by měla tuto chybu řešit logiky v rámci samotného metody akce.
- Jsou zvláště užitečná pro odstranění opakování kódu a převeďte společné aspekty z metody akce.
- Obvykle neměl by se převod řetězce na vlastní typ, [ `TypeConverter` ](/dotnet/api/system.componentmodel.typeconverter) je obvykle vhodnější.
