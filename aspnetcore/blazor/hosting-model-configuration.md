---
title: ASP.NET Core Blazor konfigurace modelu hostování
author: guardrex
description: Přečtěte Blazor si o konfiguraci modelu hostování, včetně postupu Razor při integraci Razor komponent do stránek a aplikací MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 17ed43a12643f067da73658bec72400acbe1be43
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772071"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="6315f-103">Konfigurace modelu hostování ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6315f-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="6315f-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6315f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6315f-105">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="6315f-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="6315f-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6315f-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="6315f-107">Prostředí</span><span class="sxs-lookup"><span data-stu-id="6315f-107">Environment</span></span>

<span data-ttu-id="6315f-108">Při místním spuštění aplikace je prostředí standardně vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="6315f-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="6315f-109">Při publikování aplikace je prostředí standardně v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="6315f-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="6315f-110">Hostovaná aplikace WebAssembly Blazor vybírá prostředí ze serveru prostřednictvím middlewaru, který toto prostředí komunikuje do prohlížeče přidáním `blazor-environment` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="6315f-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="6315f-111">Hodnota hlavičky je prostředí.</span><span class="sxs-lookup"><span data-stu-id="6315f-111">The value of the header is the environment.</span></span> <span data-ttu-id="6315f-112">Hostovaná aplikace Blazor a serverová aplikace sdílejí stejné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6315f-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="6315f-113">Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="6315f-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="6315f-114">Pro samostatnou spuštěnou aplikaci, která je spuštěna místně, `blazor-environment` vývojový server přidá hlavičku pro určení vývojového prostředí.</span><span class="sxs-lookup"><span data-stu-id="6315f-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="6315f-115">Chcete-li určit prostředí pro jiná hostující prostředí, přidejte `blazor-environment` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="6315f-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="6315f-116">V následujícím příkladu pro službu IIS přidejte vlastní hlavičku do publikovaného souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="6315f-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="6315f-117">Soubor *Web. config* je umístěný ve složce *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="6315f-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="6315f-118">Chcete-li použít vlastní soubor *Web. config* pro službu IIS, který není po publikování aplikace ve složce pro *publikování* přepsán, <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>Přečtěte si téma.</span><span class="sxs-lookup"><span data-stu-id="6315f-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="6315f-119">Získání prostředí aplikace v součásti vložením `IWebAssemblyHostEnvironment` a čtením `Environment` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6315f-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="6315f-120">Během spouštění `WebAssemblyHostBuilder` zpřístupňuje `IWebAssemblyHostEnvironment` `HostEnvironment` vlastnost prostřednictvím vlastnosti, která vývojářům umožňuje mít v kódu logiku konkrétního prostředí:</span><span class="sxs-lookup"><span data-stu-id="6315f-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="6315f-121">Následující rozšiřující metody umožňují kontrolu aktuálního prostředí pro názvy pro vývoj, produkci, přípravu a vlastní prostředí:</span><span class="sxs-lookup"><span data-stu-id="6315f-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="6315f-122">"Prostředí" ("{název prostředí}")</span><span class="sxs-lookup"><span data-stu-id="6315f-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="6315f-123">`IWebAssemblyHostEnvironment.BaseAddress` Vlastnost lze použít při spuštění, když není `NavigationManager` služba k dispozici.</span><span class="sxs-lookup"><span data-stu-id="6315f-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="6315f-124">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="6315f-124">Configuration</span></span>

<span data-ttu-id="6315f-125">Blazor WebAssembly načítá konfiguraci z:</span><span class="sxs-lookup"><span data-stu-id="6315f-125">Blazor WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="6315f-126">Soubory nastavení aplikace ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="6315f-126">App settings files by default:</span></span>
  * <span data-ttu-id="6315f-127">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="6315f-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="6315f-128">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="6315f-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="6315f-129">Další [poskytovatelé konfigurace](xref:fundamentals/configuration/index) zaregistrované aplikací</span><span class="sxs-lookup"><span data-stu-id="6315f-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="6315f-130">Ne všichni poskytovatelé jsou vhodné pro aplikace Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="6315f-130">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="6315f-131">Vyjasnění, které poskytovatele se podporují pro Blazor WebAssembly, je sledováno pomocí [vysvětlení poskytovatelé konfigurace pro BLAZOR WASM (dotnet/AspNetCore. Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="6315f-131">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="6315f-132">Konfigurace v aplikaci WebAssembly v Blazor je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="6315f-132">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="6315f-133">**Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="6315f-133">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="6315f-134">Další informace o poskytovatelích konfigurace najdete v <xref:fundamentals/configuration/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="6315f-134">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="6315f-135">Konfigurace nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="6315f-135">App settings configuration</span></span>

<span data-ttu-id="6315f-136">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6315f-136">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="6315f-137">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="6315f-137">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="6315f-138">Konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="6315f-138">Provider configuration</span></span>

<span data-ttu-id="6315f-139">Následující příklad používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> k poskytnutí další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="6315f-139">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="6315f-140">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6315f-140">`Program.Main`:</span></span>

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

<span data-ttu-id="6315f-141">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="6315f-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="6315f-142">Chcete-li číst další konfigurační soubory ze složky *wwwroot* do konfigurace, použijte `HttpClient` k získání obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="6315f-142">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="6315f-143">Při použití tohoto přístupu existující `HttpClient` registrace služby může použít místního klienta vytvořeného pro čtení souboru, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="6315f-143">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="6315f-144">*wwwroot/automobily. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6315f-144">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="6315f-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6315f-145">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="6315f-146">Konfigurace ověřování</span><span class="sxs-lookup"><span data-stu-id="6315f-146">Authentication configuration</span></span>

<span data-ttu-id="6315f-147">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6315f-147">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="6315f-148">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6315f-148">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="6315f-149">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="6315f-149">Logging configuration</span></span>

<span data-ttu-id="6315f-150">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6315f-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="6315f-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6315f-151">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="6315f-152">Konfigurace tvůrce hostitele</span><span class="sxs-lookup"><span data-stu-id="6315f-152">Host builder configuration</span></span>

<span data-ttu-id="6315f-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6315f-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="6315f-154">Konfigurace uložená v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="6315f-154">Cached configuration</span></span>

<span data-ttu-id="6315f-155">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="6315f-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="6315f-156">S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="6315f-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="6315f-157">Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="6315f-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="6315f-158">Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.</span><span class="sxs-lookup"><span data-stu-id="6315f-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="6315f-159">Soubory *Service-Worker. js* a *Service-Worker-assets. js* aplikace PWA je nutné znovu sestavit při kompilaci, který signalizuje aplikaci na další stránce uživatele online, na kterou se aplikace znovu nasadila.</span><span class="sxs-lookup"><span data-stu-id="6315f-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="6315f-160">Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, <xref:blazor/progressive-web-app#background-updates>naleznete v tématu.</span><span class="sxs-lookup"><span data-stu-id="6315f-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="6315f-161">protokolování</span><span class="sxs-lookup"><span data-stu-id="6315f-161">Logging</span></span>

<span data-ttu-id="6315f-162">Informace o podpoře protokolování WebAssembly v Blazor naleznete v <xref:fundamentals/logging/index#create-logs-in-blazor>tématu.</span><span class="sxs-lookup"><span data-stu-id="6315f-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="6315f-163">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="6315f-163">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="6315f-164">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="6315f-164">Reflect the connection state in the UI</span></span>

<span data-ttu-id="6315f-165">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="6315f-165">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="6315f-166">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="6315f-166">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="6315f-167">Chcete-li přizpůsobit uživatelské rozhraní, `id` definujte element `components-reconnect-modal` `<body>` na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="6315f-167">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="6315f-168">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="6315f-168">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="6315f-169">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="6315f-169">CSS class</span></span>                       | <span data-ttu-id="6315f-170">Označující&hellip;</span><span class="sxs-lookup"><span data-stu-id="6315f-170">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="6315f-171">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="6315f-171">A lost connection.</span></span> <span data-ttu-id="6315f-172">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="6315f-172">The client is attempting to reconnect.</span></span> <span data-ttu-id="6315f-173">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="6315f-173">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="6315f-174">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="6315f-174">An active connection is re-established to the server.</span></span> <span data-ttu-id="6315f-175">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="6315f-175">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="6315f-176">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="6315f-176">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="6315f-177">Chcete-li se pokusit `window.Blazor.reconnect()`znovu připojit, zavolejte.</span><span class="sxs-lookup"><span data-stu-id="6315f-177">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="6315f-178">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="6315f-178">Reconnection rejected.</span></span> <span data-ttu-id="6315f-179">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="6315f-179">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="6315f-180">K opětovnému načtení aplikace zavolejte `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="6315f-180">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="6315f-181">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="6315f-181">This connection state may result when:</span></span><ul><li><span data-ttu-id="6315f-182">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="6315f-182">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="6315f-183">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="6315f-183">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="6315f-184">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="6315f-184">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="6315f-185">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="6315f-185">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="6315f-186">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="6315f-186">Render mode</span></span>

<span data-ttu-id="6315f-187">Aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="6315f-187">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="6315f-188">To je nastaveno na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="6315f-188">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="6315f-189">`RenderMode`nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="6315f-189">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="6315f-190">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="6315f-190">Is prerendered into the page.</span></span>
* <span data-ttu-id="6315f-191">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="6315f-191">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="6315f-192">Popis</span><span class="sxs-lookup"><span data-stu-id="6315f-192">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="6315f-193">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6315f-193">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="6315f-194">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="6315f-194">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="6315f-195">Vykreslí značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6315f-195">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="6315f-196">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="6315f-196">Output from the component isn't included.</span></span> <span data-ttu-id="6315f-197">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="6315f-197">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="6315f-198">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="6315f-198">Renders the component into static HTML.</span></span> |

<span data-ttu-id="6315f-199">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="6315f-199">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="6315f-200">Konfigurace SignalR klienta pro Blazor serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="6315f-200">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="6315f-201">V SignalR některých případech je potřeba nakonfigurovat klienta používaného Blazor serverovými aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="6315f-201">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="6315f-202">Například můžete chtít nakonfigurovat protokolování na SignalR straně klienta, aby bylo možné diagnostikovat problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="6315f-202">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="6315f-203">Chcete-li SignalR nakonfigurovat klienta v souboru *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="6315f-203">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="6315f-204">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="6315f-204">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="6315f-205">Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje SignalR tvůrce.</span><span class="sxs-lookup"><span data-stu-id="6315f-205">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a><span data-ttu-id="6315f-206">protokolování</span><span class="sxs-lookup"><span data-stu-id="6315f-206">Logging</span></span>

<span data-ttu-id="6315f-207">Informace o Blazor podpoře protokolování serveru najdete v tématu <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="6315f-207">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
