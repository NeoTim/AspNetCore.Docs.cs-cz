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
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="63bc8-103">Vazba vlastního modelu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63bc8-103">Custom Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="63bc8-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="63bc8-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="63bc8-105">Vazba modelu umožňuje akcím kontroleru pracovat přímo s typy modelů (předanými jako argumenty metody), nikoli požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="63bc8-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="63bc8-106">Mapování mezi příchozími daty požadavků a aplikačními modely je zpracováváno pomocí vazeb modelů.</span><span class="sxs-lookup"><span data-stu-id="63bc8-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="63bc8-107">Vývojáři mohou pomocí implementace vlastních modelů modelu integrovat integrované funkce vazeb modelů (obvykle ale nemusíte psát vlastního poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="63bc8-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

[<span data-ttu-id="63bc8-108">Zobrazení nebo stažení ukázky z GitHubu</span><span class="sxs-lookup"><span data-stu-id="63bc8-108">View or download sample from GitHub</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a><span data-ttu-id="63bc8-109">Výchozí omezení pořadače modelu</span><span class="sxs-lookup"><span data-stu-id="63bc8-109">Default model binder limitations</span></span>

<span data-ttu-id="63bc8-110">Výchozí pořadače modelů podporují většinu běžných datových typů .NET Core a musí splňovat většinu potřeb vývojářů.</span><span class="sxs-lookup"><span data-stu-id="63bc8-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="63bc8-111">Očekávají, že se vstupní text na základě požadavku váže přímo na typy modelů.</span><span class="sxs-lookup"><span data-stu-id="63bc8-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="63bc8-112">Před vytvořením vazby může být nutné transformovat vstup.</span><span class="sxs-lookup"><span data-stu-id="63bc8-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="63bc8-113">Například když máte klíč, který lze použít k vyhledání dat modelu.</span><span class="sxs-lookup"><span data-stu-id="63bc8-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="63bc8-114">Pomocí vlastního pořadače modelu můžete načíst data založená na klíči.</span><span class="sxs-lookup"><span data-stu-id="63bc8-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="63bc8-115">Kontrola vazeb modelů</span><span class="sxs-lookup"><span data-stu-id="63bc8-115">Model binding review</span></span>

<span data-ttu-id="63bc8-116">Vazba modelu používá specifické definice pro typy, na kterých pracuje.</span><span class="sxs-lookup"><span data-stu-id="63bc8-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="63bc8-117">*Jednoduchý typ* je převeden z jednoho řetězce ve vstupu.</span><span class="sxs-lookup"><span data-stu-id="63bc8-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="63bc8-118">*Komplexní typ* je převeden z více vstupních hodnot.</span><span class="sxs-lookup"><span data-stu-id="63bc8-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="63bc8-119">Rozhraní určuje rozdíl na základě existence `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="63bc8-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="63bc8-120">Pokud máte jednoduché `string`  ->  mapování,kterénevyžadujeexterníprostředky,doporučujemevytvořitkonvertortypu.`SomeType`</span><span class="sxs-lookup"><span data-stu-id="63bc8-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="63bc8-121">Před vytvořením vlastního pořadače modelu je potřeba zkontrolovat, jak jsou naimplementované existující vazby modelů.</span><span class="sxs-lookup"><span data-stu-id="63bc8-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="63bc8-122">Vezměte v úvahu [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) , který se dá použít k převedení řetězců kódovaných v kódování Base64 na pole bajtů.</span><span class="sxs-lookup"><span data-stu-id="63bc8-122">Consider the [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="63bc8-123">Pole bajtů jsou často ukládána jako soubory nebo databáze objektů BLOB databáze.</span><span class="sxs-lookup"><span data-stu-id="63bc8-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="63bc8-124">Práce s ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="63bc8-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="63bc8-125">K reprezentaci binárních dat lze použít řetězce kódované v kódování Base64.</span><span class="sxs-lookup"><span data-stu-id="63bc8-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="63bc8-126">Například následující obrázek může být kódovaný jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="63bc8-126">For example, the following image can be encoded as a string.</span></span>

<span data-ttu-id="63bc8-127">![dotnet – robot](custom-model-binding/images/bot.png "dotnet – robot")</span><span class="sxs-lookup"><span data-stu-id="63bc8-127">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span></span>

<span data-ttu-id="63bc8-128">Malá část kódovaného řetězce je znázorněna na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="63bc8-128">A small portion of the encoded string is shown in the following image:</span></span>

<span data-ttu-id="63bc8-129">![dotnet – kódování bot](custom-model-binding/images/encoded-bot.png "dotnet – kódování bot")</span><span class="sxs-lookup"><span data-stu-id="63bc8-129">![dotnet bot encoded](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span></span>

<span data-ttu-id="63bc8-130">Postupujte podle pokynů v [souboru Readme ukázky](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) a převeďte řetězec kódovaný v kódování Base64 na soubor.</span><span class="sxs-lookup"><span data-stu-id="63bc8-130">Follow the instructions in the [sample's README](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) to convert the base64-encoded string into a file.</span></span>

<span data-ttu-id="63bc8-131">ASP.NET Core MVC může přebírat řetězec kódovaný v kódování Base64 a použít `ByteArrayModelBinder` ho k převedení na pole bajtů.</span><span class="sxs-lookup"><span data-stu-id="63bc8-131">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="63bc8-132">[ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) , který implementuje argumenty mapování `byte[]` [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) na `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="63bc8-132">The [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) which implements [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

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

<span data-ttu-id="63bc8-133">Při vytváření vlastního pořadače modelu můžete implementovat vlastní `IModelBinderProvider` typ nebo použít [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span><span class="sxs-lookup"><span data-stu-id="63bc8-133">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span></span>

<span data-ttu-id="63bc8-134">Následující příklad ukazuje, jak použít `ByteArrayModelBinder` pro převod řetězce kódovaného ve formátu base64 `byte[]` na a a výsledek do souboru:</span><span class="sxs-lookup"><span data-stu-id="63bc8-134">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

<span data-ttu-id="63bc8-135">Řetězec s kódováním base64 můžete do této metody rozhraní API poslat pomocí nástroje, jako je například [Poster](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="63bc8-135">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="63bc8-136">![postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="63bc8-136">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="63bc8-137">Pokud může pořadač svázat data požadavku s odpovídajícími pojmenovanými vlastnostmi nebo argumenty, vazba modelu bude úspěšná.</span><span class="sxs-lookup"><span data-stu-id="63bc8-137">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="63bc8-138">Následující příklad ukazuje, jak použít `ByteArrayModelBinder` s modelem zobrazení:</span><span class="sxs-lookup"><span data-stu-id="63bc8-138">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="63bc8-139">Ukázka pořadače vlastního modelu</span><span class="sxs-lookup"><span data-stu-id="63bc8-139">Custom model binder sample</span></span>

<span data-ttu-id="63bc8-140">V této části implementujeme vlastní pořadač modelů, který:</span><span class="sxs-lookup"><span data-stu-id="63bc8-140">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="63bc8-141">Převede příchozí data požadavku na klíčové argumenty silného typu.</span><span class="sxs-lookup"><span data-stu-id="63bc8-141">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="63bc8-142">Pomocí Entity Framework Core načíst přidruženou entitu.</span><span class="sxs-lookup"><span data-stu-id="63bc8-142">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="63bc8-143">Předá přidruženou entitu jako argument metodě Action.</span><span class="sxs-lookup"><span data-stu-id="63bc8-143">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="63bc8-144">Následující příklad používá `ModelBinder` atribut `Author` v modelu:</span><span class="sxs-lookup"><span data-stu-id="63bc8-144">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

<span data-ttu-id="63bc8-145">V předchozím kódu `ModelBinder` atribut určuje `IModelBinder` typ, který má být použit k navázání `Author` parametrů akce.</span><span class="sxs-lookup"><span data-stu-id="63bc8-145">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="63bc8-146">Následující `AuthorEntityBinder` třída váže `Author` parametr tím, že načte entitu ze `authorId`zdroje dat pomocí Entity Framework Core a:</span><span class="sxs-lookup"><span data-stu-id="63bc8-146">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="63bc8-147">Předchozí `AuthorEntityBinder` třída je určena k ilustraci vlastního pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="63bc8-147">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="63bc8-148">Třída není určena k ilustraci osvědčených postupů pro scénář vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="63bc8-148">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="63bc8-149">Pro vyhledávání navažte `authorId` a Dotazujte databázi v metodě Action.</span><span class="sxs-lookup"><span data-stu-id="63bc8-149">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="63bc8-150">Tento přístup odděluje selhání vazeb modelu z `NotFound` případů.</span><span class="sxs-lookup"><span data-stu-id="63bc8-150">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="63bc8-151">Následující kód ukazuje, jak použít `AuthorEntityBinder` metodu akce:</span><span class="sxs-lookup"><span data-stu-id="63bc8-151">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="63bc8-152">Atribut lze použít k `AuthorEntityBinder` aplikování parametrů, které nepoužívají výchozí konvence: `ModelBinder`</span><span class="sxs-lookup"><span data-stu-id="63bc8-152">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="63bc8-153">V tomto příkladu, protože název argumentu není výchozí `authorId`, je zadán v parametru `ModelBinder` pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="63bc8-153">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="63bc8-154">Metoda kontroleru i akce je zjednodušená v porovnání s vyhledáním entity v metodě Action.</span><span class="sxs-lookup"><span data-stu-id="63bc8-154">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="63bc8-155">Logika pro načtení autora pomocí Entity Framework Core je přesunuta do pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="63bc8-155">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="63bc8-156">To může být značné zjednodušení, pokud máte několik metod, které se vážou k `Author` modelu.</span><span class="sxs-lookup"><span data-stu-id="63bc8-156">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="63bc8-157">Můžete použít `ModelBinder` atribut na vlastnosti jednotlivých modelů (například na ViewModel) nebo na parametry metody pro určení určitého modelu pořadače nebo názvu modelu pouze pro tento typ nebo akci.</span><span class="sxs-lookup"><span data-stu-id="63bc8-157">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="63bc8-158">Implementace ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="63bc8-158">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="63bc8-159">Místo použití atributu můžete implementovat `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="63bc8-159">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="63bc8-160">Tímto způsobem jsou implementovány předdefinované vazby rozhraní.</span><span class="sxs-lookup"><span data-stu-id="63bc8-160">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="63bc8-161">Když zadáte typ, na kterém bude pořadač fungovat, zadáte typ argumentu, který vytváří, **nikoli** vstup, který váš pořadač akceptuje.</span><span class="sxs-lookup"><span data-stu-id="63bc8-161">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="63bc8-162">Následující poskytovatel pořadače spolupracuje s `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="63bc8-162">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="63bc8-163">Když je přidána do kolekce zprostředkovatelů MVC, nemusíte používat `ModelBinder` atribut u `Author` parametrů nebo `Author`typu.</span><span class="sxs-lookup"><span data-stu-id="63bc8-163">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="63bc8-164">Poznámka: Předchozí kód vrátí `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="63bc8-164">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="63bc8-165">`BinderTypeModelBinder`slouží jako továrna pro pojiva modelů a poskytuje vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="63bc8-165">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="63bc8-166">Pro přístup k EF Core vyžadujedi.`AuthorEntityBinder`</span><span class="sxs-lookup"><span data-stu-id="63bc8-166">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="63bc8-167">Použijte `BinderTypeModelBinder` , pokud pořadač modelů vyžaduje služby od di.</span><span class="sxs-lookup"><span data-stu-id="63bc8-167">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="63bc8-168">Pokud chcete použít vlastního poskytovatele pořadače modelů, přidejte ho `ConfigureServices`do:</span><span class="sxs-lookup"><span data-stu-id="63bc8-168">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

<span data-ttu-id="63bc8-169">Při vyhodnocování pořadačů modelů se kolekce zprostředkovatelů zkoumá v daném pořadí.</span><span class="sxs-lookup"><span data-stu-id="63bc8-169">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="63bc8-170">Použije se první zprostředkovatel, který vrací Binder.</span><span class="sxs-lookup"><span data-stu-id="63bc8-170">The first provider that returns a binder is used.</span></span>

<span data-ttu-id="63bc8-171">Následující obrázek ukazuje výchozí pojiva modelu z ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="63bc8-171">The following image shows the default model binders from the debugger.</span></span>

<span data-ttu-id="63bc8-172">![výchozí modely pojiv](custom-model-binding/images/default-model-binders.png "výchozí modely pojiv")</span><span class="sxs-lookup"><span data-stu-id="63bc8-172">![default model binders](custom-model-binding/images/default-model-binders.png "default model binders")</span></span>

<span data-ttu-id="63bc8-173">Pokud přidáte poskytovatele na konec kolekce, může to mít za následek volání předdefinovaného pořadače modelu předtím, než má vlastní pořadač možnost.</span><span class="sxs-lookup"><span data-stu-id="63bc8-173">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="63bc8-174">V tomto příkladu je vlastní zprostředkovatel přidán na začátek kolekce, aby se zajistilo jeho použití pro `Author` argumenty akce.</span><span class="sxs-lookup"><span data-stu-id="63bc8-174">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

### <a name="polymorphic-model-binding"></a><span data-ttu-id="63bc8-175">Polymorfní vazba modelu</span><span class="sxs-lookup"><span data-stu-id="63bc8-175">Polymorphic model binding</span></span>

<span data-ttu-id="63bc8-176">Vazba na různé modely odvozených typů se označuje jako polymorfní vazba modelu.</span><span class="sxs-lookup"><span data-stu-id="63bc8-176">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="63bc8-177">Polymorfní vazba vlastního modelu je požadována v případě, že hodnota požadavku musí být vázána na konkrétní typ odvozeného modelu.</span><span class="sxs-lookup"><span data-stu-id="63bc8-177">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="63bc8-178">Polymorfní vazba modelu:</span><span class="sxs-lookup"><span data-stu-id="63bc8-178">Polymorphic model binding:</span></span>

* <span data-ttu-id="63bc8-179">Není typický pro REST API, která je navržená tak, aby spolupracovala se všemi jazyky.</span><span class="sxs-lookup"><span data-stu-id="63bc8-179">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="63bc8-180">To je obtížné kvůli vázaným modelům.</span><span class="sxs-lookup"><span data-stu-id="63bc8-180">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="63bc8-181">Nicméně pokud aplikace vyžaduje vazbu polymorfního modelu, implementace může vypadat jako v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="63bc8-181">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/3.0sample/PolymorphicModelBinding/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="63bc8-182">Doporučení a osvědčené postupy</span><span class="sxs-lookup"><span data-stu-id="63bc8-182">Recommendations and best practices</span></span>

<span data-ttu-id="63bc8-183">Vlastní pořadače modelů:</span><span class="sxs-lookup"><span data-stu-id="63bc8-183">Custom model binders:</span></span>

- <span data-ttu-id="63bc8-184">Neměl by se pokoušet nastavit stavové kódy nebo vracet výsledky (například 404 se nenašly).</span><span class="sxs-lookup"><span data-stu-id="63bc8-184">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="63bc8-185">Pokud vazba modelu selže, měla by být chyba zpracována [filtrem akcí](xref:mvc/controllers/filters) nebo logikou v rámci samotné metody akce.</span><span class="sxs-lookup"><span data-stu-id="63bc8-185">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="63bc8-186">Jsou nejužitečnější pro odstranění opakujícího se kódu a přeshraničních otázek z metod akcí.</span><span class="sxs-lookup"><span data-stu-id="63bc8-186">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="63bc8-187">Obvykle by neměl být použit k převodu řetězce na vlastní typ, [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) je obvykle lepší volbou.</span><span class="sxs-lookup"><span data-stu-id="63bc8-187">Typically shouldn't be used to convert a string into a custom type, a [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) is usually a better option.</span></span>
