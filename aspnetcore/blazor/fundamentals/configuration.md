---
title: BlazorKonfigurace ASP.NET Core
author: guardrex
description: Seznamte se s konfigurací Blazor aplikací, včetně nastavení aplikací, ověřování a konfigurace protokolování.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 3e1d265f269e2a2c5ea584e62e80f8a6167f9d3a
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944403"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="234da-103">BlazorKonfigurace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="234da-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="234da-104">Toto téma se týká Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="234da-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="234da-105">Obecné pokyny týkající se konfigurace ASP.NET Core aplikace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="234da-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor WebAssembly<span data-ttu-id="234da-106">Načte konfiguraci z:</span><span class="sxs-lookup"><span data-stu-id="234da-106"> loads configuration from:</span></span>

* <span data-ttu-id="234da-107">Soubory nastavení aplikace ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="234da-107">App settings files by default:</span></span>
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* <span data-ttu-id="234da-108">Další [poskytovatelé konfigurace](xref:fundamentals/configuration/index) zaregistrované aplikací</span><span class="sxs-lookup"><span data-stu-id="234da-108">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="234da-109">Ne všichni poskytovatelé jsou vhodné pro Blazor WebAssembly aplikace.</span><span class="sxs-lookup"><span data-stu-id="234da-109">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="234da-110">Vyjasnění, které zprostředkovatelé podporují, Blazor WebAssembly je sledováno pomocí [vysvětlení poskytovatelé konfigurace pro Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="234da-110">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="234da-111">Konfigurace v Blazor WebAssembly aplikaci je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="234da-111">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="234da-112">**Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="234da-112">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="234da-113">Další informace o poskytovatelích konfigurace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="234da-113">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="234da-114">Konfigurace nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="234da-114">App settings configuration</span></span>

<span data-ttu-id="234da-115">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="234da-115">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="234da-116">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="234da-116">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="234da-117">Konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="234da-117">Provider configuration</span></span>

<span data-ttu-id="234da-118">Následující příklad používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> k poskytnutí další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="234da-118">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="234da-119">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="234da-119">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="234da-120">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="234da-120">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

<span data-ttu-id="234da-121">Chcete-li číst další konfigurační soubory ze `wwwroot` složky do konfigurace, použijte <xref:System.Net.Http.HttpClient> k získání obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="234da-121">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="234da-122">Při použití tohoto přístupu existující <xref:System.Net.Http.HttpClient> Registrace služby může použít místního klienta vytvořeného pro čtení souboru, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="234da-122">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="234da-123">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="234da-123">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="234da-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="234da-124">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="234da-125">Konfigurace ověřování</span><span class="sxs-lookup"><span data-stu-id="234da-125">Authentication configuration</span></span>

<span data-ttu-id="234da-126">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="234da-126">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="234da-127">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="234da-127">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="234da-128">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="234da-128">Logging configuration</span></span>

<span data-ttu-id="234da-129">Přidat odkaz na balíček pro [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) :</span><span class="sxs-lookup"><span data-stu-id="234da-129">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="234da-130">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="234da-130">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="234da-131">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="234da-131">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="234da-132">Konfigurace tvůrce hostitele</span><span class="sxs-lookup"><span data-stu-id="234da-132">Host builder configuration</span></span>

<span data-ttu-id="234da-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="234da-133">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="234da-134">Konfigurace uložená v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="234da-134">Cached configuration</span></span>

<span data-ttu-id="234da-135">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="234da-135">Configuration files are cached for offline use.</span></span> <span data-ttu-id="234da-136">S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="234da-136">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="234da-137">Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="234da-137">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="234da-138">Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.</span><span class="sxs-lookup"><span data-stu-id="234da-138">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="234da-139">`service-worker.js`Soubory PWA a `service-worker-assets.js` musí být znovu sestaveny při kompilaci, který signalizace aplikaci na další stránce uživatele online, že byla aplikace znovu nasazena.</span><span class="sxs-lookup"><span data-stu-id="234da-139">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="234da-140">Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, naleznete v tématu <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="234da-140">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
