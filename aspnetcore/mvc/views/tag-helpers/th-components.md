---
title: Součásti pomocné rutiny značky v ASP.NET Core
author: scottaddie
description: Zjistěte, jaké jsou součásti pomocné rutiny značek a jejich použití v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: b5b3abea6492cfaa7d6acd0e54073a8db12eb2a5
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034763"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="d8b8c-103">Součásti pomocné rutiny značky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8b8c-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="d8b8c-104">Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Hasan Fiyaz Bin](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="d8b8c-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="d8b8c-105">Komponenta pomocné rutiny značky je pomocná rutina značky, který umožňuje podmíněně úprava nebo přidání elementů HTML kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="d8b8c-106">Tato funkce je dostupná v ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="d8b8c-107">ASP.NET Core zahrnuje dvě komponenty integrované pomocné rutiny značky: `head` a `body`.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="d8b8c-108">Nacházejí se v <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> obor názvů a je možné v MVC a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="d8b8c-109">Součásti pomocné rutiny značky nevyžadují registraci s touto aplikací v *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="d8b8c-110">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8b8c-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="d8b8c-111">Případy použití</span><span class="sxs-lookup"><span data-stu-id="d8b8c-111">Use cases</span></span>

<span data-ttu-id="d8b8c-112">Dvě běžné případy použití komponent pomocné rutiny značky patří:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="d8b8c-113">Vkládání `<link>` do `<head>`.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="d8b8c-114">Vkládání `<script>` do `<body>`.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="d8b8c-115">Následující části popisují tyto případy použití.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="d8b8c-116">Vložit do hlavního elementu HTML</span><span class="sxs-lookup"><span data-stu-id="d8b8c-116">Inject into HTML head element</span></span>

<span data-ttu-id="d8b8c-117">V kódu HTML `<head>` elementu, soubory šablon stylů CSS se obvykle importují s HTML `<link>` elementu.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="d8b8c-118">Následující kód vkládá `<link>` elementu do `<head>` pomocí elementu `head` komponenty pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="d8b8c-119">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-119">In the preceding code:</span></span>

* <span data-ttu-id="d8b8c-120">`AddressStyleTagHelperComponent` implementuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="d8b8c-121">Abstrakce:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-121">The abstraction:</span></span>
  * <span data-ttu-id="d8b8c-122">Umožňuje inicializaci třídy s vlastností <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="d8b8c-123">Umožňuje použití komponent pomocné rutiny značky přidat nebo upravit prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="d8b8c-124"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> Vlastnost definuje pořadí, ve kterém jsou generovány komponenty.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="d8b8c-125">`Order` je potřeba, pokud existuje více použití komponent pomocné rutiny značky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="d8b8c-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> Porovná kontextu spuštění <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> hodnoty vlastnosti `head`.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="d8b8c-127">Pokud porovnání vyhodnocen jako true, obsah `_style` pole se vloží do kódu HTML `<head>` elementu.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="d8b8c-128">Vložení do prvku text HTML</span><span class="sxs-lookup"><span data-stu-id="d8b8c-128">Inject into HTML body element</span></span>

<span data-ttu-id="d8b8c-129">`body` Komponenty pomocné rutiny značky lze vložit `<script>` elementu do `<body>` elementu.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="d8b8c-130">Následující kód ukazuje tento postup:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="d8b8c-131">Samostatný soubor HTML se používá k ukládání `<script>` elementu.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="d8b8c-132">Soubor HTML díky kód přehlednější a jednodušší údržbu.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="d8b8c-133">Předchozí kód načte obsah *TagHelpers/Templates/AddressToolTipScript.html* a připojí jej s výstupem pomocné rutiny značky.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="d8b8c-134">*AddressToolTipScript.html* soubor obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="d8b8c-135">Předchozí kód vytvoří vazbu [Bootstrap popisek widgetu](https://getbootstrap.com/docs/3.3/javascript/#tooltips) k libovolnému `<address>` element, který obsahuje `printable` atribut.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="d8b8c-136">Efekt je viditelná, když ukazatel myši pohybuje nad prvkem.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="d8b8c-137">Registrují komponentu</span><span class="sxs-lookup"><span data-stu-id="d8b8c-137">Register a Component</span></span>

<span data-ttu-id="d8b8c-138">Komponentu pomocné rutiny značky musí být přidán do kolekce součásti pomocné rutiny značky aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="d8b8c-139">Existují tři způsoby, jak přidat do kolekce:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="d8b8c-140">Součásti pomocné rutiny značky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8b8c-140">Tag Helper Components in ASP.NET Core</span></span>](#tag-helper-components-in-aspnet-core)
  * [<span data-ttu-id="d8b8c-141">Případy použití</span><span class="sxs-lookup"><span data-stu-id="d8b8c-141">Use cases</span></span>](#use-cases)
    * [<span data-ttu-id="d8b8c-142">Vložit do hlavního elementu HTML</span><span class="sxs-lookup"><span data-stu-id="d8b8c-142">Inject into HTML head element</span></span>](#inject-into-html-head-element)
    * [<span data-ttu-id="d8b8c-143">Vložení do prvku text HTML</span><span class="sxs-lookup"><span data-stu-id="d8b8c-143">Inject into HTML body element</span></span>](#inject-into-html-body-element)
  * [<span data-ttu-id="d8b8c-144">Registrují komponentu</span><span class="sxs-lookup"><span data-stu-id="d8b8c-144">Register a Component</span></span>](#register-a-component)
    * [<span data-ttu-id="d8b8c-145">Registrace prostřednictvím služby kontejneru</span><span class="sxs-lookup"><span data-stu-id="d8b8c-145">Registration via services container</span></span>](#registration-via-services-container)
    * [<span data-ttu-id="d8b8c-146">Registrace prostřednictvím souboru Razor</span><span class="sxs-lookup"><span data-stu-id="d8b8c-146">Registration via Razor file</span></span>](#registration-via-razor-file)
    * [<span data-ttu-id="d8b8c-147">Registrace prostřednictvím stránky nebo Model kontroleru</span><span class="sxs-lookup"><span data-stu-id="d8b8c-147">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)
  * [<span data-ttu-id="d8b8c-148">Vytvoření komponenty</span><span class="sxs-lookup"><span data-stu-id="d8b8c-148">Create a Component</span></span>](#create-a-component)
  * [<span data-ttu-id="d8b8c-149">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="d8b8c-149">Additional resources</span></span>](#additional-resources)

### <a name="registration-via-services-container"></a><span data-ttu-id="d8b8c-150">Registrace prostřednictvím služby kontejneru</span><span class="sxs-lookup"><span data-stu-id="d8b8c-150">Registration via services container</span></span>

<span data-ttu-id="d8b8c-151">Pokud třída součástí pomocné rutiny značky není spravován pomocí <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) systému.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-151">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="d8b8c-152">Následující `Startup.ConfigureServices` kódu registrů `AddressStyleTagHelperComponent` a `AddressScriptTagHelperComponent` třídy s [přechodná doba života](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span><span class="sxs-lookup"><span data-stu-id="d8b8c-152">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="d8b8c-153">Registrace prostřednictvím souboru Razor</span><span class="sxs-lookup"><span data-stu-id="d8b8c-153">Registration via Razor file</span></span>

<span data-ttu-id="d8b8c-154">Pokud součást pomocné rutiny značky není registrována s DI, lze jej zaregistrovat ze stránky Razor Pages nebo zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-154">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="d8b8c-155">Tento postup slouží k řízení vloženého kódu a pořadí spuštění součásti ze souboru Razor.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-155">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="d8b8c-156">`ITagHelperComponentManager` slouží k přidání komponenty pomocné rutiny značky nebo odebrat z aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-156">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="d8b8c-157">Následující kód ukazuje tento postup s `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-157">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="d8b8c-158">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-158">In the preceding code:</span></span>

* <span data-ttu-id="d8b8c-159">`@inject` Směrnice poskytuje instance `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-159">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="d8b8c-160">Instance je přiřazen do proměnné s názvem `manager` pro přístup k směru server-klient v souboru Razor.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-160">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="d8b8c-161">Instance `AddressTagHelperComponent` se přidá do kolekce součásti pomocné rutiny značky aplikací.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-161">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="d8b8c-162">`AddressTagHelperComponent` upravit tak, aby vyhovovaly konstruktor, který přijímá `markup` a `order` parametry:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-162">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="d8b8c-163">Poskytnuté `markup` parametr se používá v `ProcessAsync` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-163">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="d8b8c-164">Registrace prostřednictvím stránky nebo Model kontroleru</span><span class="sxs-lookup"><span data-stu-id="d8b8c-164">Registration via Page Model or controller</span></span>

<span data-ttu-id="d8b8c-165">Pokud součást pomocné rutiny značky není registrována s DI, lze jej zaregistrovat z modelu stránky Razor Pages nebo kontroler MVC.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-165">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="d8b8c-166">Tato technika je užitečná pro oddělení logiky C# v souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-166">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="d8b8c-167">Vkládání konstruktor se používá pro přístup k instanci `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-167">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="d8b8c-168">Komponenta pomocné rutiny značky se přidá do kolekce na instanci komponenty pomocné rutiny značky.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-168">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="d8b8c-169">Tuto techniku s demonstruje následující model stránky Razor Pages `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-169">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="d8b8c-170">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-170">In the preceding code:</span></span>

* <span data-ttu-id="d8b8c-171">Vkládání konstruktor se používá pro přístup k instanci `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-171">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="d8b8c-172">Instance `AddressTagHelperComponent` se přidá do kolekce součásti pomocné rutiny značky aplikací.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-172">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="d8b8c-173">Vytvoření komponenty</span><span class="sxs-lookup"><span data-stu-id="d8b8c-173">Create a Component</span></span>

<span data-ttu-id="d8b8c-174">Chcete-li vytvořit komponentu vlastní pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-174">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="d8b8c-175">Vytvořit veřejnou třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-175">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="d8b8c-176">Použití [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) atribut třídy.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-176">Apply an [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="d8b8c-177">Zadejte název cílového elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-177">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="d8b8c-178">*Volitelné*: Použití [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) atribut třídy pro potlačení tohoto typu zobrazení v IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-178">*Optional*: Apply an [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="d8b8c-179">Následující kód vytvoří komponentu vlastní pomocné rutiny značky, který cílí `<address>` prvek HTML:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-179">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="d8b8c-180">Použít vlastní `address` komponenty pomocné rutiny značky se vložit kód HTML následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-180">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

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

<span data-ttu-id="d8b8c-181">Předchozí `ProcessAsync` metoda vkládá HTML poskytnuté <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> do odpovídající `<address>` elementu.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-181">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="d8b8c-182">Vyvolá se vkládání při:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-182">The injection occurs when:</span></span>

* <span data-ttu-id="d8b8c-183">Kontext spuštění `TagName` vlastnost hodnota se rovná `address`.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-183">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="d8b8c-184">Odpovídající `<address>` obsahuje element `printable` atribut.</span><span class="sxs-lookup"><span data-stu-id="d8b8c-184">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="d8b8c-185">Například `if` vyhodnotí na hodnotu true při zpracovávání následujících `<address>` element:</span><span class="sxs-lookup"><span data-stu-id="d8b8c-185">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="d8b8c-186">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d8b8c-186">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
