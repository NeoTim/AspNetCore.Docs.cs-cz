---
title: Poradce při potížích s lokalizací jádra ASP.NET
author: hishamco
description: Přečtěte si, jak diagnostikovat problémy s lokalizací v aplikacích ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660375"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="e1b98-103">Poradce při potížích s lokalizací jádra ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e1b98-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="e1b98-104">Podle [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="e1b98-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="e1b98-105">Tento článek obsahuje pokyny, jak diagnostikovat ASP.NET problémy s lokalizací aplikace Core.</span><span class="sxs-lookup"><span data-stu-id="e1b98-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="e1b98-106">Problémy s konfigurací lokalizace</span><span class="sxs-lookup"><span data-stu-id="e1b98-106">Localization configuration issues</span></span>

<span data-ttu-id="e1b98-107">**Pořadí middlewaru lokalizace**</span><span class="sxs-lookup"><span data-stu-id="e1b98-107">**Localization middleware order**</span></span>  
<span data-ttu-id="e1b98-108">Aplikace nemusí lokalizovat, protože middleware lokalizace není objednané podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="e1b98-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="e1b98-109">Chcete-li tento problém vyřešit, ujistěte se, že middleware lokalizace je registrovánpřed MVC middleware.</span><span class="sxs-lookup"><span data-stu-id="e1b98-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="e1b98-110">V opačném případě není použit middleware lokalizace.</span><span class="sxs-lookup"><span data-stu-id="e1b98-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="e1b98-111">**Cesta prostředků lokalizace nebyla nalezena.**</span><span class="sxs-lookup"><span data-stu-id="e1b98-111">**Localization resources path not found**</span></span>

<span data-ttu-id="e1b98-112">**Podporované jazykové verze v RequestCultureProvider neodpovídají registrované jednou**</span><span class="sxs-lookup"><span data-stu-id="e1b98-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="e1b98-113">Problémy s pojmenováním souborů prostředků</span><span class="sxs-lookup"><span data-stu-id="e1b98-113">Resource file naming issues</span></span>

<span data-ttu-id="e1b98-114">ASP.NET Core má předdefinovaná pravidla a pokyny pro názvy souborů lokalizačních prostředků, které jsou podrobně popsány [zde](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span><span class="sxs-lookup"><span data-stu-id="e1b98-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="e1b98-115">Chybějící zdroje</span><span class="sxs-lookup"><span data-stu-id="e1b98-115">Missing resources</span></span>

<span data-ttu-id="e1b98-116">Mezi běžné příčiny nenalezených prostředků patří:</span><span class="sxs-lookup"><span data-stu-id="e1b98-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="e1b98-117">Názvy prostředků jsou chybně `resx` napsané v souboru nebo v požadavku lokalizačního systému.</span><span class="sxs-lookup"><span data-stu-id="e1b98-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="e1b98-118">Prostředek chybí pro `resx` některé jazyky, ale existuje v jiných jazycích.</span><span class="sxs-lookup"><span data-stu-id="e1b98-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="e1b98-119">Pokud máte stále potíže, zkontrolujte zprávy protokolu lokalizace (které jsou na `Debug` úrovni protokolu) další podrobnosti o chybějící prostředky.</span><span class="sxs-lookup"><span data-stu-id="e1b98-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="e1b98-120">_**Tip:** Při `CookieRequestCultureProvider`použití ověřte, zda se jednoduché uvozovky nepoužívají s jazyky uvnitř hodnoty souboru cookie lokalizace. Například `c='en-UK'|uic='en-US'` je neplatná hodnota `c=en-UK|uic=en-US` cookie, zatímco je platná._</span><span class="sxs-lookup"><span data-stu-id="e1b98-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="e1b98-121">Zdroje & problémy s knihovnami tříd</span><span class="sxs-lookup"><span data-stu-id="e1b98-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="e1b98-122">ASP.NET Core ve výchozím nastavení poskytuje způsob, jak knihovnám tříd umožnit, aby nalezly své soubory prostředků prostřednictvím [resourcelocationattribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="e1b98-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="e1b98-123">Mezi běžné problémy s knihovnami tříd patří:</span><span class="sxs-lookup"><span data-stu-id="e1b98-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="e1b98-124">Chybějící `ResourceLocationAttribute` v knihovně tříd `ResourceManagerStringLocalizerFactory` zabrání zjišťování prostředků.</span><span class="sxs-lookup"><span data-stu-id="e1b98-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="e1b98-125">Pojmenování souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="e1b98-125">Resource file naming.</span></span> <span data-ttu-id="e1b98-126">Další informace naleznete v části [Problémy s pojmenováním souborů prostředků.](#resource-file-naming-issues)</span><span class="sxs-lookup"><span data-stu-id="e1b98-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="e1b98-127">Změna kořenového oboru názvů knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="e1b98-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="e1b98-128">Další informace naleznete v části [Problémy kořenového oboru názvů.](#root-namespace-issues)</span><span class="sxs-lookup"><span data-stu-id="e1b98-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="e1b98-129">CustomRequestCultureProvider nefunguje podle očekávání</span><span class="sxs-lookup"><span data-stu-id="e1b98-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="e1b98-130">Třída `RequestLocalizationOptions` má tři výchozí zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="e1b98-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="e1b98-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) umožňuje přizpůsobit, jak je ve vaší aplikaci poskytována jazyková verze lokalizace.</span><span class="sxs-lookup"><span data-stu-id="e1b98-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="e1b98-132">Používá `CustomRequestCultureProvider` se v případě, že výchozí zprostředkovatelé nesplňují vaše požadavky.</span><span class="sxs-lookup"><span data-stu-id="e1b98-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="e1b98-133">Běžným důvodem, proč vlastní zprostředkovatel nefunguje správně, je `RequestCultureProviders` to, že není prvním poskytovatelem v seznamu.</span><span class="sxs-lookup"><span data-stu-id="e1b98-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="e1b98-134">Řešení tohoto problému:</span><span class="sxs-lookup"><span data-stu-id="e1b98-134">To resolve this issue:</span></span>

- <span data-ttu-id="e1b98-135">Vložte vlastního zprostředkovatele na `RequestCultureProviders` pozici 0 v seznamu takto:</span><span class="sxs-lookup"><span data-stu-id="e1b98-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- <span data-ttu-id="e1b98-136">Pomocí `AddInitialRequestCultureProvider` metody rozšíření nastavte vlastního zprostředkovatele jako počátečního zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="e1b98-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="e1b98-137">Problémy s kořenovým oborem názvů</span><span class="sxs-lookup"><span data-stu-id="e1b98-137">Root Namespace issues</span></span>

<span data-ttu-id="e1b98-138">Pokud kořenový obor názvů sestavení se liší od názvu sestavení, lokalizace nefunguje ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="e1b98-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="e1b98-139">Chcete-li se tomuto problému vyhnout, použijte [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), který je podrobně popsán [zde](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span><span class="sxs-lookup"><span data-stu-id="e1b98-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="e1b98-140">Tato situace může nastat, pokud název projektu není platný identifikátor .NET.</span><span class="sxs-lookup"><span data-stu-id="e1b98-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="e1b98-141">Například `my-project-name.csproj` bude používat kořenový obor `my_project_name` `my-project-name` názvů a název sestavení vedoucí k této chybě.</span><span class="sxs-lookup"><span data-stu-id="e1b98-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="e1b98-142">Zdroje & build akce</span><span class="sxs-lookup"><span data-stu-id="e1b98-142">Resources & Build Action</span></span>

<span data-ttu-id="e1b98-143">Pokud používáte soubory prostředků pro lokalizaci, je důležité, aby měly odpovídající akci sestavení.</span><span class="sxs-lookup"><span data-stu-id="e1b98-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="e1b98-144">Měly by být **vložené zdroje**, jinak `ResourceStringLocalizer` není schopen najít tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="e1b98-144">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
