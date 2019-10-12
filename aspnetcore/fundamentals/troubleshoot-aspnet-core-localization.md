---
title: Řešení potíží s ASP.NET Core lokalizací
author: hishamco
description: Naučte se diagnostikovat problémy s lokalizací v ASP.NET Corech aplikacích.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 98e06a92af0b6c045095ac803196bf4b1f25e5c5
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2019
ms.locfileid: "72289008"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="1b76a-103">Řešení potíží s ASP.NET Core lokalizací</span><span class="sxs-lookup"><span data-stu-id="1b76a-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="1b76a-104">Podle [Hisham bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="1b76a-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="1b76a-105">Tento článek poskytuje pokyny, jak diagnostikovat problémy s lokalizací ASP.NET Core aplikací.</span><span class="sxs-lookup"><span data-stu-id="1b76a-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="1b76a-106">Problémy s konfigurací lokalizace</span><span class="sxs-lookup"><span data-stu-id="1b76a-106">Localization configuration issues</span></span>

<span data-ttu-id="1b76a-107">**Lokalizace pořadí middlewaru**</span><span class="sxs-lookup"><span data-stu-id="1b76a-107">**Localization middleware order**</span></span>  
<span data-ttu-id="1b76a-108">Aplikace nemusí být lokalizována, protože middleware lokalizace není uspořádána podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="1b76a-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="1b76a-109">Chcete-li tento problém vyřešit, zajistěte, aby middleware lokalizace byl zaregistrován před middlewarem MVC.</span><span class="sxs-lookup"><span data-stu-id="1b76a-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="1b76a-110">V opačném případě se middleware lokalizace nepoužije.</span><span class="sxs-lookup"><span data-stu-id="1b76a-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="1b76a-111">**Cesta k prostředkům lokalizace nebyla nalezena.**</span><span class="sxs-lookup"><span data-stu-id="1b76a-111">**Localization resources path not found**</span></span>

<span data-ttu-id="1b76a-112">**Podporované jazykové verze v RequestCultureProvider se neshodují s registrací.**</span><span class="sxs-lookup"><span data-stu-id="1b76a-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="1b76a-113">Problémy s pojmenovávání souborů prostředků</span><span class="sxs-lookup"><span data-stu-id="1b76a-113">Resource file naming issues</span></span>

<span data-ttu-id="1b76a-114">ASP.NET Core má předdefinovaná pravidla a pokyny pro pojmenování souborů lokalizačních prostředků, které jsou [zde](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)podrobně popsané.</span><span class="sxs-lookup"><span data-stu-id="1b76a-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="1b76a-115">Chybějící prostředky</span><span class="sxs-lookup"><span data-stu-id="1b76a-115">Missing resources</span></span>

<span data-ttu-id="1b76a-116">Mezi běžné příčiny nenalezených prostředků patří:</span><span class="sxs-lookup"><span data-stu-id="1b76a-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="1b76a-117">V souboru `resx` nebo v žádosti lokalizátora jsou nesprávně napsané názvy prostředků.</span><span class="sxs-lookup"><span data-stu-id="1b76a-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="1b76a-118">Prostředek chybí v `resx` pro některé jazyky, ale existuje v jiných.</span><span class="sxs-lookup"><span data-stu-id="1b76a-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="1b76a-119">Pokud pořád máte problémy, podívejte se na zprávy protokolu lokalizace (které jsou na úrovni protokolu `Debug`), kde najdete další podrobnosti o chybějících prostředcích.</span><span class="sxs-lookup"><span data-stu-id="1b76a-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="1b76a-120">_**Pomocný parametr:** Při použití `CookieRequestCultureProvider` se ověří, že jednoduché uvozovky nejsou použity s kulturami v hodnotě souboru cookie lokalizace. Například `c='en-UK'|uic='en-US'` je neplatná hodnota souboru cookie, zatímco `c=en-UK|uic=en-US` je platná._</span><span class="sxs-lookup"><span data-stu-id="1b76a-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="1b76a-121">Zdroje & problémy knihoven tříd</span><span class="sxs-lookup"><span data-stu-id="1b76a-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="1b76a-122">ASP.NET Core ve výchozím nastavení poskytuje způsob, jak umožníte knihovnám tříd najít své soubory prostředků prostřednictvím [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="1b76a-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="1b76a-123">Mezi běžné problémy s knihovnami tříd patří:</span><span class="sxs-lookup"><span data-stu-id="1b76a-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="1b76a-124">Chybějící `ResourceLocationAttribute` v knihovně tříd zabrání `ResourceManagerStringLocalizerFactory` v zjišťování prostředků.</span><span class="sxs-lookup"><span data-stu-id="1b76a-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="1b76a-125">Pojmenovávání zdrojového souboru.</span><span class="sxs-lookup"><span data-stu-id="1b76a-125">Resource file naming.</span></span> <span data-ttu-id="1b76a-126">Další informace najdete v části [problémy s pojmenovávání souborů prostředků](#resource-file-naming-issues) .</span><span class="sxs-lookup"><span data-stu-id="1b76a-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="1b76a-127">Změna kořenového oboru názvů knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="1b76a-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="1b76a-128">Další informace najdete v části [problémy kořenového oboru názvů](#root-namespace-issues) .</span><span class="sxs-lookup"><span data-stu-id="1b76a-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="1b76a-129">CustomRequestCultureProvider nefunguje podle očekávání</span><span class="sxs-lookup"><span data-stu-id="1b76a-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="1b76a-130">Třída `RequestLocalizationOptions` má tři výchozí zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="1b76a-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="1b76a-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) umožňuje přizpůsobit, jak je v aplikaci k dispozici kultura lokalizace.</span><span class="sxs-lookup"><span data-stu-id="1b76a-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="1b76a-132">@No__t-0 se používá, pokud výchozí zprostředkovatelé nesplňují vaše požadavky.</span><span class="sxs-lookup"><span data-stu-id="1b76a-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="1b76a-133">Běžný důvod, který vlastní poskytovatel, nefunguje správně, znamená to, že není prvním poskytovatelem v seznamu `RequestCultureProviders`.</span><span class="sxs-lookup"><span data-stu-id="1b76a-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="1b76a-134">Řešení těchto potíží:</span><span class="sxs-lookup"><span data-stu-id="1b76a-134">To resolve this issue:</span></span>

- <span data-ttu-id="1b76a-135">Vložte vlastního zprostředkovatele na pozici 0 v seznamu `RequestCultureProviders` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="1b76a-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

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

- <span data-ttu-id="1b76a-136">Pomocí metody rozšíření `AddInitialRequestCultureProvider` nastavte vlastního zprostředkovatele jako počátečního poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="1b76a-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="1b76a-137">Problémy s kořenovým oborem názvů</span><span class="sxs-lookup"><span data-stu-id="1b76a-137">Root Namespace issues</span></span>

<span data-ttu-id="1b76a-138">Pokud se kořenový obor názvů sestavení liší od názvu sestavení, lokalizace ve výchozím nastavení nefunguje.</span><span class="sxs-lookup"><span data-stu-id="1b76a-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="1b76a-139">Chcete-li se tomuto problému vyhnout, použijte [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), který je [zde](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) podrobně popsán.</span><span class="sxs-lookup"><span data-stu-id="1b76a-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

## <a name="resources--build-action"></a><span data-ttu-id="1b76a-140">Prostředky & akci sestavení</span><span class="sxs-lookup"><span data-stu-id="1b76a-140">Resources & Build Action</span></span>

<span data-ttu-id="1b76a-141">Pokud používáte soubory prostředků k lokalizaci, je důležité, aby měly odpovídající akci sestavení.</span><span class="sxs-lookup"><span data-stu-id="1b76a-141">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="1b76a-142">Měly by být **vložené prostředky**, jinak `ResourceStringLocalizer` nedokáže najít tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="1b76a-142">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
