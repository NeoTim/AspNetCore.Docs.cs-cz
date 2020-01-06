---
title: Vazba vlastního modelu v ASP.NET Core
author: ardalis
description: Přečtěte si, jak vazba modelu umožňuje akcím kontroleru pracovat přímo s typy modelů v ASP.NET Core.
ms.author: riande
ms.date: 01/01/2020
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 565858ff3471253f2975d73cc8a3aa85360eb227
ms.sourcegitcommit: e7d4fe6727d423f905faaeaa312f6c25ef844047
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2020
ms.locfileid: "75608077"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="9ea5a-103">Vazba vlastního modelu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ea5a-103">Custom Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="9ea5a-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9ea5a-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9ea5a-105">Vazba modelu umožňuje akcím kontroleru pracovat přímo s typy modelů (předanými jako argumenty metody), nikoli požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="9ea5a-106">Mapování mezi příchozími daty požadavků a aplikačními modely je zpracováváno pomocí vazeb modelů.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="9ea5a-107">Vývojáři mohou pomocí implementace vlastních modelů modelu integrovat integrované funkce vazeb modelů (obvykle ale nemusíte psát vlastního poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="9ea5a-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="9ea5a-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ea5a-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="9ea5a-109">Výchozí omezení pořadače modelu</span><span class="sxs-lookup"><span data-stu-id="9ea5a-109">Default model binder limitations</span></span>

<span data-ttu-id="9ea5a-110">Výchozí pořadače modelů podporují většinu běžných datových typů .NET Core a musí splňovat většinu potřeb vývojářů.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="9ea5a-111">Očekávají, že se vstupní text na základě požadavku váže přímo na typy modelů.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="9ea5a-112">Před vytvořením vazby může být nutné transformovat vstup.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="9ea5a-113">Například když máte klíč, který lze použít k vyhledání dat modelu.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="9ea5a-114">Pomocí vlastního pořadače modelu můžete načíst data založená na klíči.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="9ea5a-115">Kontrola vazeb modelů</span><span class="sxs-lookup"><span data-stu-id="9ea5a-115">Model binding review</span></span>

<span data-ttu-id="9ea5a-116">Vazba modelu používá specifické definice pro typy, na kterých pracuje.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="9ea5a-117">*Jednoduchý typ* je převeden z jednoho řetězce ve vstupu.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="9ea5a-118">*Komplexní typ* je převeden z více vstupních hodnot.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="9ea5a-119">Rozhraní určuje rozdíl na základě existence `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="9ea5a-120">Pokud máte jednoduché `string` -> `SomeType` mapování, které nevyžaduje externí prostředky, doporučujeme vytvořit konvertor typu.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="9ea5a-121">Před vytvořením vlastního pořadače modelu je potřeba zkontrolovat, jak jsou naimplementované existující vazby modelů.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="9ea5a-122">Vezměte v úvahu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, který se dá použít k převedení řetězců kódovaných v kódování Base64 na pole bajtů.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="9ea5a-123">Pole bajtů jsou často ukládána jako soubory nebo databáze objektů BLOB databáze.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="9ea5a-124">Práce s ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="9ea5a-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="9ea5a-125">K reprezentaci binárních dat lze použít řetězce kódované v kódování Base64.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="9ea5a-126">Například obrázek může být kódovaný jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="9ea5a-127">Postupujte podle pokynů v [souboru Readme ukázky](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/README.md) a převeďte řetězec kódovaný v kódování Base64 na soubor.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-127">Follow the instructions in the [sample's README](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/README.md) to convert a base64-encoded string into a file.</span></span>

<span data-ttu-id="9ea5a-128">ASP.NET Core MVC může použít řetězec kódovaný v kódování Base64 a pomocí `ByteArrayModelBinder` ho převést na pole bajtů.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="9ea5a-129"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> mapuje `byte[]` argumenty `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

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

<span data-ttu-id="9ea5a-130">Při vytváření vlastního pořadače modelů můžete implementovat vlastní typ `IModelBinderProvider` nebo použít <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="9ea5a-131">Následující příklad ukazuje, jak použít `ByteArrayModelBinder` k převedení řetězce zakódovaného ve formátu base64 na `byte[]` a uložení výsledku do souboru:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="9ea5a-132">Řetězec s kódováním base64 můžete do této metody rozhraní API poslat pomocí nástroje, jako je například [Poster](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="9ea5a-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="9ea5a-133">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="9ea5a-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="9ea5a-134">Pokud může pořadač svázat data požadavku s odpovídajícími pojmenovanými vlastnostmi nebo argumenty, vazba modelu bude úspěšná.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="9ea5a-135">Následující příklad ukazuje, jak použít `ByteArrayModelBinder` s modelem zobrazení:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="9ea5a-136">Ukázka pořadače vlastního modelu</span><span class="sxs-lookup"><span data-stu-id="9ea5a-136">Custom model binder sample</span></span>

<span data-ttu-id="9ea5a-137">V této části implementujeme vlastní pořadač modelů, který:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="9ea5a-138">Převede příchozí data požadavku na klíčové argumenty silného typu.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="9ea5a-139">Pomocí Entity Framework Core načíst přidruženou entitu.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="9ea5a-140">Předá přidruženou entitu jako argument metodě Action.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="9ea5a-141">Následující příklad používá atribut `ModelBinder` v modelu `Author`:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="9ea5a-142">V předchozím kódu atribut `ModelBinder` určuje typ `IModelBinder`, který by měl být použit k navázání `Author` parametrů akce.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="9ea5a-143">Následující třída `AuthorEntityBinder` váže `Author` parametr tím, že načte entitu ze zdroje dat pomocí Entity Framework Core a `authorId`:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="9ea5a-144">Předchozí `AuthorEntityBinder` třída je určena k ilustraci vlastního pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="9ea5a-145">Třída není určena k ilustraci osvědčených postupů pro scénář vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="9ea5a-146">Pro vyhledávání navažte `authorId` a Dotazujte databázi v metodě akce.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="9ea5a-147">Tento přístup odděluje neúspěšné vazby mezi modely z `NotFound`ch případů.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="9ea5a-148">Následující kód ukazuje, jak použít `AuthorEntityBinder` v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="9ea5a-149">Atribut `ModelBinder` lze použít k použití `AuthorEntityBinder` na parametry, které nepoužívají výchozí konvence:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="9ea5a-150">V tomto příkladu, protože název argumentu není výchozí `authorId`, je zadán v parametru pomocí atributu `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="9ea5a-151">Metoda kontroleru i akce je zjednodušená v porovnání s vyhledáním entity v metodě Action.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="9ea5a-152">Logika pro načtení autora pomocí Entity Framework Core je přesunuta do pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="9ea5a-153">To může být značné zjednodušení, pokud máte několik metod, které se vážou na model `Author`.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="9ea5a-154">Atribut `ModelBinder` můžete použít pro jednotlivé vlastnosti modelu (například na ViewModel) nebo na parametry metody pro určení určitého modelu pořadače nebo názvu modelu pouze pro tento typ nebo akci.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="9ea5a-155">Implementace ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="9ea5a-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="9ea5a-156">Místo použití atributu můžete implementovat `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="9ea5a-157">Tímto způsobem jsou implementovány předdefinované vazby rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="9ea5a-158">Když zadáte typ, na kterém bude pořadač fungovat, zadáte typ argumentu, který vytváří, **nikoli** vstup, který váš pořadač akceptuje.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="9ea5a-159">Následující poskytovatel pořadače spolupracuje s `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="9ea5a-160">Když je přidána do kolekce poskytovatelů MVC, nemusíte používat atribut `ModelBinder` u parametrů `Author` nebo `Author`.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="9ea5a-161">Poznámka: předchozí kód vrátí `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="9ea5a-162">`BinderTypeModelBinder` slouží jako továrna pro modelové vazby a poskytuje vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="9ea5a-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="9ea5a-163">`AuthorEntityBinder` vyžaduje DI pro přístup k EF Core.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="9ea5a-164">Pokud pořadač modelů vyžaduje služby od DI, použijte `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="9ea5a-165">Pokud chcete použít vlastního poskytovatele pořadače modelů, přidejte ho do `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="9ea5a-166">Při vyhodnocování pořadačů modelů se kolekce zprostředkovatelů zkoumá v daném pořadí.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="9ea5a-167">Použije se první zprostředkovatel, který vrací Binder.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-167">The first provider that returns a binder is used.</span></span> <span data-ttu-id="9ea5a-168">Pokud přidáte poskytovatele na konec kolekce, může to mít za následek volání předdefinovaného pořadače modelu předtím, než má vlastní pořadač možnost.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-168">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="9ea5a-169">V tomto příkladu je vlastní zprostředkovatel přidán na začátek kolekce, aby se zajistilo jeho použití pro `Author` argumenty akce.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-169">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="9ea5a-170">Polymorfní vazba modelu</span><span class="sxs-lookup"><span data-stu-id="9ea5a-170">Polymorphic model binding</span></span>

<span data-ttu-id="9ea5a-171">Vazba na různé modely odvozených typů se označuje jako polymorfní vazba modelu.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="9ea5a-172">Polymorfní vazba vlastního modelu je požadována v případě, že hodnota požadavku musí být vázána na konkrétní typ odvozeného modelu.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="9ea5a-173">Polymorfní vazba modelu:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="9ea5a-174">Není typický pro REST API, která je navržená tak, aby spolupracovala se všemi jazyky.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="9ea5a-175">To je obtížné kvůli vázaným modelům.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="9ea5a-176">Nicméně pokud aplikace vyžaduje vazbu polymorfního modelu, implementace může vypadat jako v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/3.0sample/PolymorphicModelBinding/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="9ea5a-177">Doporučení a osvědčené postupy</span><span class="sxs-lookup"><span data-stu-id="9ea5a-177">Recommendations and best practices</span></span>

<span data-ttu-id="9ea5a-178">Vlastní pořadače modelů:</span><span class="sxs-lookup"><span data-stu-id="9ea5a-178">Custom model binders:</span></span>

- <span data-ttu-id="9ea5a-179">Neměl by se pokoušet nastavit stavové kódy nebo vracet výsledky (například 404 se nenašly).</span><span class="sxs-lookup"><span data-stu-id="9ea5a-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="9ea5a-180">Pokud vazba modelu selže, měla by být chyba zpracována [filtrem akcí](xref:mvc/controllers/filters) nebo logikou v rámci samotné metody akce.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="9ea5a-181">Jsou nejužitečnější pro odstranění opakujícího se kódu a přeshraničních otázek z metod akcí.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="9ea5a-182">Obvykle by neměl být použit k převodu řetězce na vlastní typ, <xref:System.ComponentModel.TypeConverter> je obvykle lepší volbou.</span><span class="sxs-lookup"><span data-stu-id="9ea5a-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>
