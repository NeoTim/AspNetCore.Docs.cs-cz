---
title: BlazorKonfigurace ASP.NET Core
author: guardrex
description: Seznamte se s konfigurací Blazor aplikací, včetně nastavení aplikací, ověřování a konfigurace protokolování.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 437e7be6b805ad836df60e831f5e0dc0bda4f5a5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014448"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="86385-103">BlazorKonfigurace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="86385-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="86385-104">Toto téma se týká Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="86385-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="86385-105">Obecné pokyny týkající se konfigurace ASP.NET Core aplikace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="86385-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="86385-106">Blazor WebAssemblyNačte konfiguraci ze souborů nastavení aplikace ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="86385-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="86385-107">Konfigurace můžou taky poskytovat další poskytovatelé konfigurace, které tato aplikace zaregistruje.</span><span class="sxs-lookup"><span data-stu-id="86385-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="86385-108">Ne všichni poskytovatelé nebo funkce poskytovatele jsou vhodné pro Blazor WebAssembly aplikace:</span><span class="sxs-lookup"><span data-stu-id="86385-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="86385-109">[Zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration): zprostředkovatel není podporován pro spravovanou IDENTITU a ID aplikace (ID klienta) s použitím scénářů tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="86385-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="86385-110">ID aplikace s tajným klíčem klienta se nedoporučuje pro žádnou aplikaci ASP.NET Core, zejména pro Blazor WebAssembly aplikace, protože tajný klíč klienta nemůže být zabezpečený na straně klienta pro přístup ke službě.</span><span class="sxs-lookup"><span data-stu-id="86385-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="86385-111">[Poskytovatel konfigurace aplikace Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): poskytovatel není vhodný pro Blazor WebAssembly aplikace, protože Blazor WebAssembly aplikace neběží na serveru v Azure.</span><span class="sxs-lookup"><span data-stu-id="86385-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="86385-112">Konfigurace v Blazor WebAssembly aplikaci je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="86385-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="86385-113">**Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="86385-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="86385-114">Další informace o poskytovatelích konfigurace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="86385-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="86385-115">Konfigurace nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="86385-115">App settings configuration</span></span>

<span data-ttu-id="86385-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="86385-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="86385-117">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="86385-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="86385-118">Vlastní poskytovatel konfigurace s EF Core</span><span class="sxs-lookup"><span data-stu-id="86385-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="86385-119">Vlastní poskytovatel konfigurace s EF Core ukázal v <xref:fundamentals/configuration/index#custom-configuration-provider> práci s Blazor WebAssembly aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="86385-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="86385-120">Přidejte poskytovatele konfigurace tohoto příkladu k následujícímu kódu v `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="86385-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="86385-121">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="86385-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="86385-122">Zdroj konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="86385-122">Memory Configuration Source</span></span>

<span data-ttu-id="86385-123">Následující příklad používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> k poskytnutí další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="86385-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="86385-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="86385-124">`Program.Main`:</span></span>

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

<span data-ttu-id="86385-125">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="86385-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="86385-126">Chcete-li číst další konfigurační soubory ze `wwwroot` složky do konfigurace, použijte <xref:System.Net.Http.HttpClient> k získání obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="86385-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="86385-127">Při použití tohoto přístupu existující <xref:System.Net.Http.HttpClient> Registrace služby může použít místního klienta vytvořeného pro čtení souboru, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="86385-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="86385-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="86385-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="86385-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="86385-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="86385-130">Konfigurace ověřování</span><span class="sxs-lookup"><span data-stu-id="86385-130">Authentication configuration</span></span>

<span data-ttu-id="86385-131">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="86385-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="86385-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="86385-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="86385-133">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="86385-133">Logging configuration</span></span>

<span data-ttu-id="86385-134">Přidat odkaz na balíček pro [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) :</span><span class="sxs-lookup"><span data-stu-id="86385-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="86385-135">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="86385-135">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="86385-136">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="86385-136">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="86385-137">Konfigurace tvůrce hostitele</span><span class="sxs-lookup"><span data-stu-id="86385-137">Host builder configuration</span></span>

<span data-ttu-id="86385-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="86385-138">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="86385-139">Konfigurace uložená v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="86385-139">Cached configuration</span></span>

<span data-ttu-id="86385-140">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="86385-140">Configuration files are cached for offline use.</span></span> <span data-ttu-id="86385-141">S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="86385-141">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="86385-142">Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="86385-142">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="86385-143">Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.</span><span class="sxs-lookup"><span data-stu-id="86385-143">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="86385-144">`service-worker.js`Soubory PWA a `service-worker-assets.js` musí být znovu sestaveny při kompilaci, který signalizace aplikaci na další stránce uživatele online, že byla aplikace znovu nasazena.</span><span class="sxs-lookup"><span data-stu-id="86385-144">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="86385-145">Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, naleznete v tématu <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="86385-145">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
