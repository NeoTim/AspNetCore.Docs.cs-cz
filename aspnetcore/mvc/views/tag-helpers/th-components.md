---
title: Pomocné komponenty značek v ASP.NET Core
author: scottaddie
description: Přečtěte si, co jsou pomocné komponenty značek a jak je používat v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 23e244649350b41e4112d10df63139864e5b4381
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022221"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="9c1ab-103">Pomocné komponenty značek v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c1ab-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="9c1ab-104">Autor – [Scott Addie](https://twitter.com/Scott_Addie) a [Fiyaz bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="9c1ab-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="9c1ab-105">Pomocná komponenta značek je pomocný pomocník značek, který umožňuje podmíněně upravovat nebo přidávat prvky HTML z kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="9c1ab-106">Tato funkce je k dispozici v ASP.NET Core 2,0 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="9c1ab-107">ASP.NET Core obsahuje dvě vestavěné pomocné komponenty značek: `head` a. `body`</span><span class="sxs-lookup"><span data-stu-id="9c1ab-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="9c1ab-108">Jsou umístěny v <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> oboru názvů a lze je použít v MVC i Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="9c1ab-109">Pomocné komponenty značek nevyžadují registraci u aplikace v *_ViewImports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="9c1ab-110">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9c1ab-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="9c1ab-111">Případy použití</span><span class="sxs-lookup"><span data-stu-id="9c1ab-111">Use cases</span></span>

<span data-ttu-id="9c1ab-112">Mezi dva běžné případy použití pomocných komponent značek patří:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="9c1ab-113">`<link>` Vložení`<head>`do.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="9c1ab-114">`<script>` Vložení`<body>`do.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="9c1ab-115">Následující části popisují tyto případy použití.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="9c1ab-116">Vložit do elementu Head HTML</span><span class="sxs-lookup"><span data-stu-id="9c1ab-116">Inject into HTML head element</span></span>

<span data-ttu-id="9c1ab-117">V rámci elementu `<head>` HTML se soubory CSS běžně importují pomocí elementu HTML. `<link>`</span><span class="sxs-lookup"><span data-stu-id="9c1ab-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="9c1ab-118">Následující kód vloží `<link>` element `<head>` do prvku pomocí `head` pomocné komponenty Tag:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="9c1ab-119">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-119">In the preceding code:</span></span>

* <span data-ttu-id="9c1ab-120">`AddressStyleTagHelperComponent`implementuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="9c1ab-121">Abstrakce:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-121">The abstraction:</span></span>
  * <span data-ttu-id="9c1ab-122">Umožňuje inicializaci třídy s <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="9c1ab-123">Povoluje použití pomocných komponent značek k přidání nebo úpravě prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="9c1ab-124"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> Vlastnost definuje pořadí, ve kterém se komponenty vykreslují.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="9c1ab-125">`Order`je nutné v případě, že je v aplikaci více použití komponent pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="9c1ab-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*>Porovná hodnotu <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> vlastnosti kontextu spuštění s `head`hodnotou.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="9c1ab-127">Pokud se porovnání vyhodnotí jako true, obsah `_style` pole je vložen do elementu HTML. `<head>`</span><span class="sxs-lookup"><span data-stu-id="9c1ab-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="9c1ab-128">Vložit do prvku textu HTML</span><span class="sxs-lookup"><span data-stu-id="9c1ab-128">Inject into HTML body element</span></span>

<span data-ttu-id="9c1ab-129">Komponenta pomocník `<script>`značekmůže vložit prvek do `<body>` prvku. `body`</span><span class="sxs-lookup"><span data-stu-id="9c1ab-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="9c1ab-130">Následující kód demonstruje tuto techniku:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="9c1ab-131">K uložení `<script>` prvku se používá samostatný soubor HTML.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="9c1ab-132">Soubor HTML vytváří čisticí kód a je udržovatelnější.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="9c1ab-133">Předchozí kód přečte obsah *TagHelpers/Templates/AddressToolTipScript.html* a připojí ho k výstupu pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="9c1ab-134">Soubor *AddressToolTipScript. html* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="9c1ab-135">Předchozí kód váže [widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) s popisem spouštěcího `printable` prvku na `<address>` libovolný prvek, který obsahuje atribut.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="9c1ab-136">Je-li ukazatel myši umístěn nad prvkem, je efekt zobrazen.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="9c1ab-137">Registrace součásti</span><span class="sxs-lookup"><span data-stu-id="9c1ab-137">Register a Component</span></span>

<span data-ttu-id="9c1ab-138">Komponenta pomocníka značek musí být přidána do kolekce komponent pomocníka značek aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="9c1ab-139">Existují tři způsoby, jak přidat do kolekce:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="9c1ab-140">Registrace prostřednictvím kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="9c1ab-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="9c1ab-141">Registrace prostřednictvím souboru Razor</span><span class="sxs-lookup"><span data-stu-id="9c1ab-141">Registration via Razor file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="9c1ab-142">Registrace přes model stránky nebo kontroler</span><span class="sxs-lookup"><span data-stu-id="9c1ab-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="9c1ab-143">Registrace prostřednictvím kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="9c1ab-143">Registration via services container</span></span>

<span data-ttu-id="9c1ab-144">Pokud není třída pomocné komponenty značky spravovaná pomocí <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, musí být zaregistrovaná v systému [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="9c1ab-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="9c1ab-145">Následující `Startup.ConfigureServices` kód [](xref:fundamentals/dependency-injection#lifetime-and-registration-options)registruje třídy a `AddressScriptTagHelperComponent`s přechodnou životností: `AddressStyleTagHelperComponent`</span><span class="sxs-lookup"><span data-stu-id="9c1ab-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="9c1ab-146">Registrace prostřednictvím souboru Razor</span><span class="sxs-lookup"><span data-stu-id="9c1ab-146">Registration via Razor file</span></span>

<span data-ttu-id="9c1ab-147">Pokud komponenta pomocníka značek není zaregistrovaná v DI, dá se zaregistrovat ze stránky Razor Pages nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="9c1ab-148">Tato technika se používá pro řízení vloženého kódu a pořadí provádění součástí ze souboru Razor.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="9c1ab-149">`ITagHelperComponentManager`slouží k přidání pomocných komponent značek nebo jejich odebrání z aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="9c1ab-150">Následující kód demonstruje tuto techniku `AddressTagHelperComponent`s:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="9c1ab-151">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-151">In the preceding code:</span></span>

* <span data-ttu-id="9c1ab-152">Direktiva poskytuje `ITagHelperComponentManager`instanci. `@inject`</span><span class="sxs-lookup"><span data-stu-id="9c1ab-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="9c1ab-153">Instance je přiřazena k proměnné s názvem `manager` pro přístup pro příjem dat v souboru Razor.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="9c1ab-154">Instance `AddressTagHelperComponent` je přidána do kolekce komponent pomocníka značek aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="9c1ab-155">`AddressTagHelperComponent`je upraveno tak, aby vyhovovalo konstruktoru `markup` , `order` který přijímá parametry a:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="9c1ab-156">Zadaný `markup` parametr se používá v `ProcessAsync` následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="9c1ab-157">Registrace přes model stránky nebo kontroler</span><span class="sxs-lookup"><span data-stu-id="9c1ab-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="9c1ab-158">Pokud komponenta pomocníka značek není zaregistrovaná v DI, může být zaregistrovaná z Razor Pagesho modelu nebo kontroleru MVC.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="9c1ab-159">Tato technika je užitečná pro oddělení C# logiky ze souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="9c1ab-160">Injektáže konstruktoru se používá pro přístup k instanci `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="9c1ab-161">Komponenta pomocník značek je přidána do kolekce komponent pomocníka značek instance.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="9c1ab-162">Následující Razor Pages model ukazuje tuto techniku s `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="9c1ab-163">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-163">In the preceding code:</span></span>

* <span data-ttu-id="9c1ab-164">Injektáže konstruktoru se používá pro přístup k instanci `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="9c1ab-165">Instance `AddressTagHelperComponent` je přidána do kolekce komponent pomocníka značek aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="9c1ab-166">Vytvoření komponenty</span><span class="sxs-lookup"><span data-stu-id="9c1ab-166">Create a Component</span></span>

<span data-ttu-id="9c1ab-167">Vytvoření vlastní pomocné komponenty značky:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="9c1ab-168">Vytvoří veřejnou třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="9c1ab-169">Použijte pro třídu atribut [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) .</span><span class="sxs-lookup"><span data-stu-id="9c1ab-169">Apply an [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="9c1ab-170">Zadejte název cílového elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="9c1ab-171">*Volitelné*: Použijte atribut [[členy editorbrowsable (EditorBrowsableState. Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) pro třídu pro potlačení zobrazení typu v technologii IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-171">*Optional*: Apply an [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="9c1ab-172">Následující kód vytvoří vlastní pomocnou komponentu značky, která se zaměřuje `<address>` na element jazyka HTML:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="9c1ab-173">Použijte vlastní `address` komponentu pomocníka značek pro vložení značek HTML následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

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

<span data-ttu-id="9c1ab-174">Předchozí `ProcessAsync` metoda vloží do odpovídajícího `<address>` prvku kód HTML, <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> který je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="9c1ab-175">K injektáže dojde v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-175">The injection occurs when:</span></span>

* <span data-ttu-id="9c1ab-176">Hodnota `TagName` vlastnosti kontextu spuštění `address`se rovná.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="9c1ab-177">Odpovídající `<address>` element`printable` má atribut.</span><span class="sxs-lookup"><span data-stu-id="9c1ab-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="9c1ab-178">Například příkaz je `if` vyhodnocen jako true při zpracování následujícího `<address>` elementu:</span><span class="sxs-lookup"><span data-stu-id="9c1ab-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="9c1ab-179">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9c1ab-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
