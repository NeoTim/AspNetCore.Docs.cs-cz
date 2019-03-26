---
title: Řešení potíží s ASP.NET Core lokalizace
author: hishamco
description: Zjistěte, jak diagnostikovat problémy s lokalizace v aplikacích ASP.NET Core.
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 73405f539c89d79096e7b536407cd9730679d478
ms.sourcegitcommit: 687ffb15ebe65379f75c84739ea851d5a0d788b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58489006"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="fb708-103">Řešení potíží s ASP.NET Core lokalizace</span><span class="sxs-lookup"><span data-stu-id="fb708-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="fb708-104">Podle [Ateya Hisham Bin](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="fb708-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="fb708-105">Tento článek obsahuje pokyny o tom, jak diagnostikovat problémy lokalizace aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fb708-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="fb708-106">Konfigurace lokalizace</span><span class="sxs-lookup"><span data-stu-id="fb708-106">Localization configuration issues</span></span>

<span data-ttu-id="fb708-107">**Lokalizace middleware pořadí**</span><span class="sxs-lookup"><span data-stu-id="fb708-107">**Localization middleware order**</span></span>  
<span data-ttu-id="fb708-108">Aplikace nemusí lokalizují, protože middleware lokalizace není seřazené podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="fb708-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="fb708-109">Chcete-li vyřešit tento problém, zkontrolujte, jestli že tento middleware lokalizace zaregistrován před MVC middlewaru.</span><span class="sxs-lookup"><span data-stu-id="fb708-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="fb708-110">V opačném případě se nepoužije middleware lokalizace.</span><span class="sxs-lookup"><span data-stu-id="fb708-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="fb708-111">**Lokalizace prostředků cesta nebyla nalezena.**</span><span class="sxs-lookup"><span data-stu-id="fb708-111">**Localization resources path not found**</span></span>

<span data-ttu-id="fb708-112">**Podporované jazykové verze v RequestCultureProvider se neshodují s zaregistrovat**</span><span class="sxs-lookup"><span data-stu-id="fb708-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="fb708-113">Problémy pojmenování souboru prostředků</span><span class="sxs-lookup"><span data-stu-id="fb708-113">Resource file naming issues</span></span>

<span data-ttu-id="fb708-114">ASP.NET Core má předdefinovaná pravidla a pokyny pro lokalizaci prostředky souboru vytváření názvů, které jsou podrobně popsány v [tady](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span><span class="sxs-lookup"><span data-stu-id="fb708-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="fb708-115">Chybějící prostředky</span><span class="sxs-lookup"><span data-stu-id="fb708-115">Missing resources</span></span>

<span data-ttu-id="fb708-116">Mezi běžné příčiny prostředků nebyl nalezen patří:</span><span class="sxs-lookup"><span data-stu-id="fb708-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="fb708-117">Názvy prostředků jsou chybně buď `resx` souboru nebo lokalizátora žádost.</span><span class="sxs-lookup"><span data-stu-id="fb708-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="fb708-118">Chybí prostředek `resx` pro některé jazyky, ale existuje v jiné.</span><span class="sxs-lookup"><span data-stu-id="fb708-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="fb708-119">Pokud stále máte potíže, zkontrolujte zprávy protokolu lokalizace (které jsou v `Debug` úrovně protokolování) pro další podrobnosti o chybějících prostředků.</span><span class="sxs-lookup"><span data-stu-id="fb708-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="fb708-120">_**Tip:** Při použití `CookieRequestCultureProvider`, ověřte, jednoduché uvozovky nejsou použity s jazykovými verzemi uvnitř hodnoty souboru cookie lokalizace. Například `c='en-UK'|uic='en-US'` je hodnota neplatný soubor cookie, zatímco `c=en-UK|uic=en-US` je platný._</span><span class="sxs-lookup"><span data-stu-id="fb708-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="fb708-121">Problémy s prostředky a knihovny tříd</span><span class="sxs-lookup"><span data-stu-id="fb708-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="fb708-122">ASP.NET Core ve výchozím nastavení poskytuje způsob, jak povolit knihovny tříd k vyhledání svých souborů prostředků prostřednictvím [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="fb708-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="fb708-123">Běžné problémy s knihovny tříd patří:</span><span class="sxs-lookup"><span data-stu-id="fb708-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="fb708-124">Chybí `ResourceLocationAttribute` ve třídě knihovny zabrání `ResourceManagerStringLocalizerFactory` od zjišťování prostředků.</span><span class="sxs-lookup"><span data-stu-id="fb708-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="fb708-125">Pojmenovávání souborů prostředků.</span><span class="sxs-lookup"><span data-stu-id="fb708-125">Resource file naming.</span></span> <span data-ttu-id="fb708-126">Další informace najdete v tématu [souboru prostředků pojmenování problémy](#resource-file-naming-issues) oddílu.</span><span class="sxs-lookup"><span data-stu-id="fb708-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="fb708-127">Změna kořenového oboru názvů knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="fb708-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="fb708-128">Další informace najdete v tématu [vystavuje kořenové Namespace](#root-namespace-issues) oddílu.</span><span class="sxs-lookup"><span data-stu-id="fb708-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="fb708-129">CustomRequestCultureProvider nebude fungovat podle očekávání</span><span class="sxs-lookup"><span data-stu-id="fb708-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="fb708-130">`RequestLocalizationOptions` Třída má tři výchozí poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="fb708-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="fb708-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) vám umožní přizpůsobit, jak se ve vaší aplikaci poskytuje jazykovou verzi lokalizace.</span><span class="sxs-lookup"><span data-stu-id="fb708-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="fb708-132">`CustomRequestCultureProvider` Se používá, když výchozí poskytovatele nesplňují vaše požadavky.</span><span class="sxs-lookup"><span data-stu-id="fb708-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="fb708-133">Vlastní zprostředkovatel nefungují správně běžným důvodem je, že není prvním poskytovatelem v `RequestCultureProviders` seznamu.</span><span class="sxs-lookup"><span data-stu-id="fb708-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="fb708-134">K vyřešení tohoto problému:</span><span class="sxs-lookup"><span data-stu-id="fb708-134">To resolve this issue:</span></span>

- <span data-ttu-id="fb708-135">Vložení vlastního zprostředkovatele na pozici 0 v `RequestCultureProviders` seznamu následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="fb708-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```

- <span data-ttu-id="fb708-136">Použití `AddInitialRequestCultureProvider` metodu rozšíření k nastavení vlastního zprostředkovatele jako počáteční zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="fb708-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="fb708-137">Hlavní problémy Namespace</span><span class="sxs-lookup"><span data-stu-id="fb708-137">Root Namespace issues</span></span>

<span data-ttu-id="fb708-138">Pokud kořenový obor názvů sestavení se liší od názvu sestavení, lokalizace nefunguje ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="fb708-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="fb708-139">Abyste tento problém, použijte [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), který je podrobně popsaný [zde](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span><span class="sxs-lookup"><span data-stu-id="fb708-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

## <a name="resources--build-action"></a><span data-ttu-id="fb708-140">Prostředky a akci sestavení</span><span class="sxs-lookup"><span data-stu-id="fb708-140">Resources & Build Action</span></span>

<span data-ttu-id="fb708-141">Pokud používáte soubory prostředků pro lokalizaci, je důležité, ke kterým mají příslušná sestavení akci.</span><span class="sxs-lookup"><span data-stu-id="fb708-141">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="fb708-142">By měl být **integrovaný prostředek**, jinak `ResourceStringLocalizer` není schopen najít tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="fb708-142">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
