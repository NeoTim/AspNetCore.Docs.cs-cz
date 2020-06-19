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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: b43eae03c71cabbaafa2bc0d704765e89f743279
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103700"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="ff120-103">BlazorKonfigurace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff120-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="ff120-104">Toto téma se vztahuje na Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ff120-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="ff120-105">Obecné pokyny týkající se konfigurace ASP.NET Core aplikace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="ff120-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="ff120-106">Sestavení WebAssembly načítá konfiguraci z:</span><span class="sxs-lookup"><span data-stu-id="ff120-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="ff120-107">Soubory nastavení aplikace ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="ff120-107">App settings files by default:</span></span>
  * <span data-ttu-id="ff120-108">*wwwroot/appsettings.jsv*</span><span class="sxs-lookup"><span data-stu-id="ff120-108">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="ff120-109">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="ff120-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="ff120-110">Další [poskytovatelé konfigurace](xref:fundamentals/configuration/index) zaregistrované aplikací</span><span class="sxs-lookup"><span data-stu-id="ff120-110">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="ff120-111">Ne všichni poskytovatelé jsou vhodný pro Blazor aplikace pro WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ff120-111">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="ff120-112">Vyjasnění, které poskytovatele jsou podporovány pro Blazor WebAssembly, je sledováno pomocí [vysvětlení poskytovatelé konfigurace pro Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="ff120-112">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="ff120-113">Konfigurace v Blazor aplikaci WebAssembly je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="ff120-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="ff120-114">**Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="ff120-114">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="ff120-115">Další informace o poskytovatelích konfigurace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="ff120-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="ff120-116">Konfigurace nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="ff120-116">App settings configuration</span></span>

<span data-ttu-id="ff120-117">*wwwroot/appsettings.jsv*:</span><span class="sxs-lookup"><span data-stu-id="ff120-117">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="ff120-118">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="ff120-118">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="ff120-119">Konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="ff120-119">Provider configuration</span></span>

<span data-ttu-id="ff120-120">Následující příklad používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> k poskytnutí další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="ff120-120">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="ff120-121">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ff120-121">`Program.Main`:</span></span>

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

<span data-ttu-id="ff120-122">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="ff120-122">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="ff120-123">Chcete-li číst další konfigurační soubory ze složky *wwwroot* do konfigurace, použijte <xref:System.Net.Http.HttpClient> k získání obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="ff120-123">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="ff120-124">Při použití tohoto přístupu existující <xref:System.Net.Http.HttpClient> Registrace služby může použít místního klienta vytvořeného pro čtení souboru, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="ff120-124">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="ff120-125">*wwwroot/cars.jsv*:</span><span class="sxs-lookup"><span data-stu-id="ff120-125">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="ff120-126">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ff120-126">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="ff120-127">Konfigurace ověřování</span><span class="sxs-lookup"><span data-stu-id="ff120-127">Authentication configuration</span></span>

<span data-ttu-id="ff120-128">*wwwroot/appsettings.jsv*:</span><span class="sxs-lookup"><span data-stu-id="ff120-128">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="ff120-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ff120-129">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="ff120-130">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="ff120-130">Logging configuration</span></span>

<span data-ttu-id="ff120-131">Přidat odkaz na balíček pro [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="ff120-131">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="ff120-132">*wwwroot/appsettings.jsv*:</span><span class="sxs-lookup"><span data-stu-id="ff120-132">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="ff120-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ff120-133">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="ff120-134">Konfigurace tvůrce hostitele</span><span class="sxs-lookup"><span data-stu-id="ff120-134">Host builder configuration</span></span>

<span data-ttu-id="ff120-135">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ff120-135">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="ff120-136">Konfigurace uložená v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="ff120-136">Cached configuration</span></span>

<span data-ttu-id="ff120-137">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="ff120-137">Configuration files are cached for offline use.</span></span> <span data-ttu-id="ff120-138">S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="ff120-138">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="ff120-139">Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="ff120-139">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="ff120-140">Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.</span><span class="sxs-lookup"><span data-stu-id="ff120-140">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="ff120-141">Soubory *service-worker.js* a *service-worker-assets.js* aplikace PWA musí být znovu sestaveny při kompilaci, který signalizace aplikaci na další stránce uživatele v online režimu, že byla aplikace znovu nasazena.</span><span class="sxs-lookup"><span data-stu-id="ff120-141">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="ff120-142">Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, naleznete v tématu <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="ff120-142">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
