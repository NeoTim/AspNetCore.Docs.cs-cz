---
title: Součásti pomocné rutiny značky v ASP.NET Core
author: scottaddie
description: Zjistěte, jaké jsou součásti pomocné rutiny značek a jejich použití v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 777bbcca3d1c2f03f386fa62df2a2e0b00409318
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902082"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="aefee-103">Součásti pomocné rutiny značky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aefee-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="aefee-104">Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Hasan Fiyaz Bin](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="aefee-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="aefee-105">Komponenta pomocné rutiny značky je pomocná rutina značky, který umožňuje podmíněně úprava nebo přidání elementů HTML kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="aefee-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="aefee-106">Tato funkce je dostupná v ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="aefee-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="aefee-107">ASP.NET Core zahrnuje dvě komponenty integrované pomocné rutiny značky: `head` a `body`.</span><span class="sxs-lookup"><span data-stu-id="aefee-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="aefee-108">Nacházejí se v <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> obor názvů a je možné v MVC a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="aefee-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="aefee-109">Součásti pomocné rutiny značky nevyžadují registraci s touto aplikací v *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="aefee-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="aefee-110">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aefee-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="aefee-111">Případy použití</span><span class="sxs-lookup"><span data-stu-id="aefee-111">Use cases</span></span>

<span data-ttu-id="aefee-112">Dvě běžné případy použití komponent pomocné rutiny značky patří:</span><span class="sxs-lookup"><span data-stu-id="aefee-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="aefee-113">Vkládání `<link>` do `<head>`.</span><span class="sxs-lookup"><span data-stu-id="aefee-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="aefee-114">Vkládání `<script>` do `<body>`.</span><span class="sxs-lookup"><span data-stu-id="aefee-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="aefee-115">Následující části popisují tyto případy použití.</span><span class="sxs-lookup"><span data-stu-id="aefee-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="aefee-116">Vložit do hlavního elementu HTML</span><span class="sxs-lookup"><span data-stu-id="aefee-116">Inject into HTML head element</span></span>

<span data-ttu-id="aefee-117">V kódu HTML `<head>` elementu, soubory šablon stylů CSS se obvykle importují s HTML `<link>` elementu.</span><span class="sxs-lookup"><span data-stu-id="aefee-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="aefee-118">Následující kód vkládá `<link>` elementu do `<head>` pomocí elementu `head` komponenty pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="aefee-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="aefee-119">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="aefee-119">In the preceding code:</span></span>

* <span data-ttu-id="aefee-120">`AddressStyleTagHelperComponent` implementuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="aefee-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="aefee-121">Abstrakce:</span><span class="sxs-lookup"><span data-stu-id="aefee-121">The abstraction:</span></span>
  * <span data-ttu-id="aefee-122">Umožňuje inicializaci třídy s vlastností <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="aefee-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="aefee-123">Umožňuje použití komponent pomocné rutiny značky přidat nebo upravit prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="aefee-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="aefee-124"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> Vlastnost definuje pořadí, ve kterém jsou generovány komponenty.</span><span class="sxs-lookup"><span data-stu-id="aefee-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="aefee-125">`Order` je potřeba, pokud existuje více použití komponent pomocné rutiny značky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="aefee-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="aefee-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> Porovná kontextu spuštění <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> hodnoty vlastnosti `head`.</span><span class="sxs-lookup"><span data-stu-id="aefee-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="aefee-127">Pokud porovnání vyhodnocen jako true, obsah `_style` pole se vloží do kódu HTML `<head>` elementu.</span><span class="sxs-lookup"><span data-stu-id="aefee-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="aefee-128">Vložení do prvku text HTML</span><span class="sxs-lookup"><span data-stu-id="aefee-128">Inject into HTML body element</span></span>

<span data-ttu-id="aefee-129">`body` Komponenty pomocné rutiny značky lze vložit `<script>` elementu do `<body>` elementu.</span><span class="sxs-lookup"><span data-stu-id="aefee-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="aefee-130">Následující kód ukazuje tento postup:</span><span class="sxs-lookup"><span data-stu-id="aefee-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="aefee-131">Samostatný soubor HTML se používá k ukládání `<script>` elementu.</span><span class="sxs-lookup"><span data-stu-id="aefee-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="aefee-132">Soubor HTML díky kód přehlednější a jednodušší údržbu.</span><span class="sxs-lookup"><span data-stu-id="aefee-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="aefee-133">Předchozí kód načte obsah *TagHelpers/Templates/AddressToolTipScript.html* a připojí jej s výstupem pomocné rutiny značky.</span><span class="sxs-lookup"><span data-stu-id="aefee-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="aefee-134">*AddressToolTipScript.html* soubor obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="aefee-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="aefee-135">Předchozí kód vytvoří vazbu [Bootstrap popisek widgetu](https://getbootstrap.com/docs/3.3/javascript/#tooltips) k libovolnému `<address>` element, který obsahuje `printable` atribut.</span><span class="sxs-lookup"><span data-stu-id="aefee-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="aefee-136">Efekt je viditelná, když ukazatel myši pohybuje nad prvkem.</span><span class="sxs-lookup"><span data-stu-id="aefee-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="aefee-137">Registrují komponentu</span><span class="sxs-lookup"><span data-stu-id="aefee-137">Register a Component</span></span>

<span data-ttu-id="aefee-138">Komponentu pomocné rutiny značky musí být přidán do kolekce součásti pomocné rutiny značky aplikace.</span><span class="sxs-lookup"><span data-stu-id="aefee-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="aefee-139">Existují tři způsoby, jak přidat do kolekce:</span><span class="sxs-lookup"><span data-stu-id="aefee-139">There are three ways to add to the collection:</span></span>

1. [<span data-ttu-id="aefee-140">Registrace prostřednictvím služby kontejneru</span><span class="sxs-lookup"><span data-stu-id="aefee-140">Registration via services container</span></span>](#registration-via-services-container)
1. [<span data-ttu-id="aefee-141">Registrace prostřednictvím souboru Razor</span><span class="sxs-lookup"><span data-stu-id="aefee-141">Registration via Razor file</span></span>](#registration-via-razor-file)
1. [<span data-ttu-id="aefee-142">Registrace prostřednictvím stránky nebo Model kontroleru</span><span class="sxs-lookup"><span data-stu-id="aefee-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="aefee-143">Registrace prostřednictvím služby kontejneru</span><span class="sxs-lookup"><span data-stu-id="aefee-143">Registration via services container</span></span>

<span data-ttu-id="aefee-144">Pokud třída součástí pomocné rutiny značky není spravován pomocí <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) systému.</span><span class="sxs-lookup"><span data-stu-id="aefee-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="aefee-145">Následující `Startup.ConfigureServices` kódu registrů `AddressStyleTagHelperComponent` a `AddressScriptTagHelperComponent` třídy s [přechodná doba života](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span><span class="sxs-lookup"><span data-stu-id="aefee-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="aefee-146">Registrace prostřednictvím souboru Razor</span><span class="sxs-lookup"><span data-stu-id="aefee-146">Registration via Razor file</span></span>

<span data-ttu-id="aefee-147">Pokud součást pomocné rutiny značky není registrována s DI, lze jej zaregistrovat ze stránky Razor Pages nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="aefee-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="aefee-148">Tento postup slouží k řízení vloženého kódu a pořadí spuštění součásti ze souboru Razor.</span><span class="sxs-lookup"><span data-stu-id="aefee-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="aefee-149">`ITagHelperComponentManager` slouží k přidání komponenty pomocné rutiny značky nebo odebrat z aplikace.</span><span class="sxs-lookup"><span data-stu-id="aefee-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="aefee-150">Následující kód ukazuje tento postup s `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="aefee-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="aefee-151">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="aefee-151">In the preceding code:</span></span>

* <span data-ttu-id="aefee-152">`@inject` Směrnice poskytuje instance `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="aefee-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="aefee-153">Instance je přiřazen do proměnné s názvem `manager` pro přístup k směru server-klient v souboru Razor.</span><span class="sxs-lookup"><span data-stu-id="aefee-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="aefee-154">Instance `AddressTagHelperComponent` se přidá do kolekce součásti pomocné rutiny značky aplikací.</span><span class="sxs-lookup"><span data-stu-id="aefee-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="aefee-155">`AddressTagHelperComponent` upravit tak, aby vyhovovaly konstruktor, který přijímá `markup` a `order` parametry:</span><span class="sxs-lookup"><span data-stu-id="aefee-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="aefee-156">Poskytnuté `markup` parametr se používá v `ProcessAsync` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="aefee-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="aefee-157">Registrace prostřednictvím stránky nebo Model kontroleru</span><span class="sxs-lookup"><span data-stu-id="aefee-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="aefee-158">Pokud součást pomocné rutiny značky není registrována s DI, lze jej zaregistrovat z modelu stránky Razor Pages nebo kontroler MVC.</span><span class="sxs-lookup"><span data-stu-id="aefee-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="aefee-159">Tato technika je užitečná pro oddělení logiky C# v souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="aefee-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="aefee-160">Vkládání konstruktor se používá pro přístup k instanci `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="aefee-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="aefee-161">Komponenta pomocné rutiny značky se přidá do kolekce na instanci komponenty pomocné rutiny značky.</span><span class="sxs-lookup"><span data-stu-id="aefee-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="aefee-162">Tuto techniku s demonstruje následující model stránky Razor Pages `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="aefee-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="aefee-163">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="aefee-163">In the preceding code:</span></span>

* <span data-ttu-id="aefee-164">Vkládání konstruktor se používá pro přístup k instanci `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="aefee-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="aefee-165">Instance `AddressTagHelperComponent` se přidá do kolekce součásti pomocné rutiny značky aplikací.</span><span class="sxs-lookup"><span data-stu-id="aefee-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="aefee-166">Vytvoření komponenty</span><span class="sxs-lookup"><span data-stu-id="aefee-166">Create a Component</span></span>

<span data-ttu-id="aefee-167">Chcete-li vytvořit komponentu vlastní pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="aefee-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="aefee-168">Vytvořit veřejnou třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="aefee-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="aefee-169">Použití [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) atribut třídy.</span><span class="sxs-lookup"><span data-stu-id="aefee-169">Apply an [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="aefee-170">Zadejte název cílového elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="aefee-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="aefee-171">*Volitelné*: Použití [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) atribut třídy pro potlačení tohoto typu zobrazení v IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="aefee-171">*Optional*: Apply an [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="aefee-172">Následující kód vytvoří komponentu vlastní pomocné rutiny značky, který cílí `<address>` prvek HTML:</span><span class="sxs-lookup"><span data-stu-id="aefee-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="aefee-173">Použít vlastní `address` komponenty pomocné rutiny značky se vložit kód HTML následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="aefee-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open("
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

<span data-ttu-id="aefee-174">Předchozí `ProcessAsync` metoda vkládá HTML poskytnuté <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> do odpovídající `<address>` elementu.</span><span class="sxs-lookup"><span data-stu-id="aefee-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="aefee-175">Vyvolá se vkládání při:</span><span class="sxs-lookup"><span data-stu-id="aefee-175">The injection occurs when:</span></span>

* <span data-ttu-id="aefee-176">Kontext spuštění `TagName` vlastnost hodnota se rovná `address`.</span><span class="sxs-lookup"><span data-stu-id="aefee-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="aefee-177">Odpovídající `<address>` obsahuje element `printable` atribut.</span><span class="sxs-lookup"><span data-stu-id="aefee-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="aefee-178">Například `if` vyhodnotí na hodnotu true při zpracovávání následujících `<address>` element:</span><span class="sxs-lookup"><span data-stu-id="aefee-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="aefee-179">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="aefee-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
