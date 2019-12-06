---
title: Pomocné komponenty značek v ASP.NET Core
author: scottaddie
description: Přečtěte si, co jsou pomocné komponenty značek a jak je používat v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 070cc3aae08664c13d8eb793a066766d0a5569ee
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880977"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="c31bb-103">Pomocné komponenty značek v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c31bb-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="c31bb-104">Autor – [Scott Addie](https://twitter.com/Scott_Addie) a [Fiyaz bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="c31bb-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="c31bb-105">Pomocná komponenta značek je pomocný pomocník značek, který umožňuje podmíněně upravovat nebo přidávat prvky HTML z kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="c31bb-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="c31bb-106">Tato funkce je k dispozici v ASP.NET Core 2,0 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="c31bb-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="c31bb-107">ASP.NET Core obsahuje dvě vestavěné pomocné komponenty značek: `head` a `body`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="c31bb-108">Jsou umístěny v oboru názvů <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> a lze je použít v MVC i Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c31bb-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="c31bb-109">Pomocné komponenty značek nevyžadují registraci u aplikace v *_ViewImports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c31bb-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="c31bb-110">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c31bb-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="c31bb-111">Případy použití</span><span class="sxs-lookup"><span data-stu-id="c31bb-111">Use cases</span></span>

<span data-ttu-id="c31bb-112">Mezi dva běžné případy použití pomocných komponent značek patří:</span><span class="sxs-lookup"><span data-stu-id="c31bb-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="c31bb-113">Vložení `<link>` do `<head>`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="c31bb-114">Vložení `<script>` do `<body>`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="c31bb-115">Následující části popisují tyto případy použití.</span><span class="sxs-lookup"><span data-stu-id="c31bb-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="c31bb-116">Vložit do elementu Head HTML</span><span class="sxs-lookup"><span data-stu-id="c31bb-116">Inject into HTML head element</span></span>

<span data-ttu-id="c31bb-117">Uvnitř elementu `<head>` HTML jsou soubory CSS obvykle importovány pomocí elementu `<link>` HTML.</span><span class="sxs-lookup"><span data-stu-id="c31bb-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="c31bb-118">Následující kód vloží `<link>` element do prvku `<head>` pomocí pomocné komponenty značky `head`:</span><span class="sxs-lookup"><span data-stu-id="c31bb-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="c31bb-119">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="c31bb-119">In the preceding code:</span></span>

* <span data-ttu-id="c31bb-120">`AddressStyleTagHelperComponent` implementuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="c31bb-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="c31bb-121">Abstrakce:</span><span class="sxs-lookup"><span data-stu-id="c31bb-121">The abstraction:</span></span>
  * <span data-ttu-id="c31bb-122">Umožňuje inicializaci třídy s <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="c31bb-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="c31bb-123">Povoluje použití pomocných komponent značek k přidání nebo úpravě prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="c31bb-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="c31bb-124">Vlastnost <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> definuje pořadí, ve kterém se komponenty vykreslují.</span><span class="sxs-lookup"><span data-stu-id="c31bb-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="c31bb-125">`Order` je nutné v případě, že v aplikaci existuje více použití pomocných komponent značek.</span><span class="sxs-lookup"><span data-stu-id="c31bb-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="c31bb-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> porovná hodnotu <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> vlastnosti kontextu spuštění s `head`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="c31bb-127">Pokud se porovnání vyhodnotí jako true, obsah pole `_style` je vložen do prvku `<head>` HTML.</span><span class="sxs-lookup"><span data-stu-id="c31bb-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="c31bb-128">Vložit do prvku textu HTML</span><span class="sxs-lookup"><span data-stu-id="c31bb-128">Inject into HTML body element</span></span>

<span data-ttu-id="c31bb-129">Pomocná komponenta značky `body` může vložit `<script>` prvek do prvku `<body>`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="c31bb-130">Následující kód demonstruje tuto techniku:</span><span class="sxs-lookup"><span data-stu-id="c31bb-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="c31bb-131">K uložení prvku `<script>` slouží samostatný soubor HTML.</span><span class="sxs-lookup"><span data-stu-id="c31bb-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="c31bb-132">Soubor HTML vytváří čisticí kód a je udržovatelnější.</span><span class="sxs-lookup"><span data-stu-id="c31bb-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="c31bb-133">Předchozí kód přečte obsah *TagHelpers/Templates/AddressToolTipScript.html* a připojí ho k výstupu pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="c31bb-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="c31bb-134">Soubor *AddressToolTipScript. html* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="c31bb-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="c31bb-135">Předchozí kód váže [widget s popisem spouštěcího](https://getbootstrap.com/docs/3.3/javascript/#tooltips) prvku na jakýkoli `<address>` element, který obsahuje atribut `printable`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="c31bb-136">Je-li ukazatel myši umístěn nad prvkem, je efekt zobrazen.</span><span class="sxs-lookup"><span data-stu-id="c31bb-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="c31bb-137">Registrace součásti</span><span class="sxs-lookup"><span data-stu-id="c31bb-137">Register a Component</span></span>

<span data-ttu-id="c31bb-138">Komponenta pomocníka značek musí být přidána do kolekce komponent pomocníka značek aplikace.</span><span class="sxs-lookup"><span data-stu-id="c31bb-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="c31bb-139">Existují tři způsoby, jak přidat do kolekce:</span><span class="sxs-lookup"><span data-stu-id="c31bb-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="c31bb-140">Registrace prostřednictvím kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="c31bb-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="c31bb-141">Registrace prostřednictvím souboru Razor</span><span class="sxs-lookup"><span data-stu-id="c31bb-141">Registration via Razor file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="c31bb-142">Registrace přes model stránky nebo kontroler</span><span class="sxs-lookup"><span data-stu-id="c31bb-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="c31bb-143">Registrace prostřednictvím kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="c31bb-143">Registration via services container</span></span>

<span data-ttu-id="c31bb-144">Pokud není třída pomocné komponenty značky spravovaná pomocí <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, musí být zaregistrovaná v systému [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="c31bb-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="c31bb-145">Následující kód `Startup.ConfigureServices` registruje `AddressStyleTagHelperComponent` a třídy `AddressScriptTagHelperComponent` s [přechodným trváním](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span><span class="sxs-lookup"><span data-stu-id="c31bb-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="c31bb-146">Registrace prostřednictvím souboru Razor</span><span class="sxs-lookup"><span data-stu-id="c31bb-146">Registration via Razor file</span></span>

<span data-ttu-id="c31bb-147">Pokud komponenta pomocníka značek není zaregistrovaná v DI, dá se zaregistrovat ze stránky Razor Pages nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="c31bb-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="c31bb-148">Tato technika se používá pro řízení vloženého kódu a pořadí provádění součástí ze souboru Razor.</span><span class="sxs-lookup"><span data-stu-id="c31bb-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="c31bb-149">`ITagHelperComponentManager` slouží k přidání pomocných komponent značek nebo jejich odebrání z aplikace.</span><span class="sxs-lookup"><span data-stu-id="c31bb-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="c31bb-150">Následující kód demonstruje tuto techniku s `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="c31bb-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="c31bb-151">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="c31bb-151">In the preceding code:</span></span>

* <span data-ttu-id="c31bb-152">Direktiva `@inject` poskytuje instanci `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="c31bb-153">Instance je přiřazena k proměnné s názvem `manager` pro přístup pro příjem dat v souboru Razor.</span><span class="sxs-lookup"><span data-stu-id="c31bb-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="c31bb-154">Do kolekce komponent pomocníka značek aplikace se přidá instance `AddressTagHelperComponent`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="c31bb-155">`AddressTagHelperComponent` se upraví tak, aby vyhovoval konstruktoru, který přijímá parametry `markup` a `order`:</span><span class="sxs-lookup"><span data-stu-id="c31bb-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="c31bb-156">Zadaný `markup` parametr se používá v `ProcessAsync` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c31bb-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="c31bb-157">Registrace přes model stránky nebo kontroler</span><span class="sxs-lookup"><span data-stu-id="c31bb-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="c31bb-158">Pokud komponenta pomocníka značek není zaregistrovaná v DI, může být zaregistrovaná z Razor Pagesho modelu nebo kontroleru MVC.</span><span class="sxs-lookup"><span data-stu-id="c31bb-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="c31bb-159">Tato technika je užitečná pro oddělení C# logiky ze souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="c31bb-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="c31bb-160">Injektáže konstruktoru se používá pro přístup k instanci `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="c31bb-161">Komponenta pomocník značek je přidána do kolekce komponent pomocníka značek instance.</span><span class="sxs-lookup"><span data-stu-id="c31bb-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="c31bb-162">Následující Razor Pages model ukazuje tuto techniku s `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="c31bb-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="c31bb-163">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="c31bb-163">In the preceding code:</span></span>

* <span data-ttu-id="c31bb-164">Injektáže konstruktoru se používá pro přístup k instanci `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="c31bb-165">Do kolekce komponent pomocníka značek aplikace se přidá instance `AddressTagHelperComponent`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="c31bb-166">Vytvoření komponenty</span><span class="sxs-lookup"><span data-stu-id="c31bb-166">Create a Component</span></span>

<span data-ttu-id="c31bb-167">Vytvoření vlastní pomocné komponenty značky:</span><span class="sxs-lookup"><span data-stu-id="c31bb-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="c31bb-168">Vytvoří veřejnou třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="c31bb-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="c31bb-169">Použijte atribut [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) pro třídu.</span><span class="sxs-lookup"><span data-stu-id="c31bb-169">Apply an [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="c31bb-170">Zadejte název cílového elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="c31bb-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="c31bb-171">*Volitelné*: použijte atribut [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) pro třídu pro potlačení zobrazení typu v IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="c31bb-171">*Optional*: Apply an [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="c31bb-172">Následující kód vytvoří vlastní pomocnou komponentu značky, která cílí na `<address>` HTML element:</span><span class="sxs-lookup"><span data-stu-id="c31bb-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="c31bb-173">Použijte vlastní komponentu pomocníka značek `address` pro vložení značek HTML následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c31bb-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
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

<span data-ttu-id="c31bb-174">Předchozí metoda `ProcessAsync` vloží poskytnutý kód HTML pro <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> do odpovídajícího elementu `<address>`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="c31bb-175">K injektáže dojde v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="c31bb-175">The injection occurs when:</span></span>

* <span data-ttu-id="c31bb-176">Hodnota vlastnosti `TagName` v kontextu spuštění se rovná `address`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="c31bb-177">Odpovídající prvek `<address>` má atribut `printable`.</span><span class="sxs-lookup"><span data-stu-id="c31bb-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="c31bb-178">Například příkaz `if` se při zpracování následujícího elementu `<address>` vyhodnocuje jako true:</span><span class="sxs-lookup"><span data-stu-id="c31bb-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="c31bb-179">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="c31bb-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
