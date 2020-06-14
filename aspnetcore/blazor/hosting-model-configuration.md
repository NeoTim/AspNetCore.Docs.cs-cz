---
title: ASP.NET Core Blazor Konfigurace modelu hostování
author: guardrex
description: Přečtěte si o Blazor konfiguraci modelu hostování, včetně postupu při integraci Razor komponent do Razor stránek a aplikací MVC.
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
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 74501c70df5ad33d5a2478b2ec359713e29292d8
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755778"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="f804a-103">ASP.NET Core Blazor Konfigurace modelu hostování</span><span class="sxs-lookup"><span data-stu-id="f804a-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="f804a-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f804a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f804a-105">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="f804a-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="f804a-106">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f804a-106"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="f804a-107">Prostředí</span><span class="sxs-lookup"><span data-stu-id="f804a-107">Environment</span></span>

<span data-ttu-id="f804a-108">Při místním spuštění aplikace je prostředí standardně vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="f804a-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="f804a-109">Při publikování aplikace je prostředí standardně v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="f804a-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="f804a-110">Hostovaná Blazor aplikace WebAssembly vybírá prostředí ze serveru prostřednictvím middlewaru, který toto prostředí komunikuje do prohlížeče přidáním `blazor-environment` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="f804a-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="f804a-111">Hodnota hlavičky je prostředí.</span><span class="sxs-lookup"><span data-stu-id="f804a-111">The value of the header is the environment.</span></span> <span data-ttu-id="f804a-112">Hostovaná Blazor aplikace a serverová aplikace sdílejí stejné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f804a-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="f804a-113">Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="f804a-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f804a-114">Pro samostatnou spuštěnou aplikaci, která je spuštěna místně, vývojový server přidá `blazor-environment` hlavičku pro určení vývojového prostředí.</span><span class="sxs-lookup"><span data-stu-id="f804a-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="f804a-115">Chcete-li určit prostředí pro jiná hostující prostředí, přidejte `blazor-environment` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="f804a-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="f804a-116">V následujícím příkladu pro IIS přidejte vlastní hlavičku do publikovaného *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="f804a-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="f804a-117">Soubor *web.config* je umístěný ve složce *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="f804a-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="f804a-118">Chcete-li použít vlastní soubor *web.config* pro službu IIS, která není přepsána při publikování aplikace do složky pro *publikování* , přečtěte si téma <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="f804a-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="f804a-119">Získání prostředí aplikace v součásti vložením <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a čtením <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f804a-119">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="f804a-120">Během spouštění <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> zpřístupňuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> vlastnost prostřednictvím vlastnosti, která vývojářům umožňuje mít v kódu logiku konkrétního prostředí:</span><span class="sxs-lookup"><span data-stu-id="f804a-120">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="f804a-121">Následující rozšiřující metody umožňují kontrolu aktuálního prostředí pro názvy pro vývoj, produkci, přípravu a vlastní prostředí:</span><span class="sxs-lookup"><span data-stu-id="f804a-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

<span data-ttu-id="f804a-122"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Vlastnost lze použít při spuštění, když <xref:Microsoft.AspNetCore.Components.NavigationManager> není služba k dispozici.</span><span class="sxs-lookup"><span data-stu-id="f804a-122">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="f804a-123">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="f804a-123">Configuration</span></span>

Blazor<span data-ttu-id="f804a-124">Sestavení WebAssembly načítá konfiguraci z:</span><span class="sxs-lookup"><span data-stu-id="f804a-124"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="f804a-125">Soubory nastavení aplikace ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="f804a-125">App settings files by default:</span></span>
  * <span data-ttu-id="f804a-126">*wwwroot/appsettings.jsv*</span><span class="sxs-lookup"><span data-stu-id="f804a-126">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="f804a-127">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="f804a-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="f804a-128">Další [poskytovatelé konfigurace](xref:fundamentals/configuration/index) zaregistrované aplikací</span><span class="sxs-lookup"><span data-stu-id="f804a-128">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="f804a-129">Ne všichni poskytovatelé jsou vhodný pro Blazor aplikace pro WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="f804a-129">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="f804a-130">Vyjasnění, které poskytovatele jsou podporovány pro Blazor WebAssembly, je sledováno pomocí [vysvětlení poskytovatelé konfigurace pro Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="f804a-130">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="f804a-131">Konfigurace v Blazor aplikaci WebAssembly je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="f804a-131">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="f804a-132">**Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="f804a-132">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="f804a-133">Další informace o poskytovatelích konfigurace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="f804a-133">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="f804a-134">Konfigurace nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="f804a-134">App settings configuration</span></span>

<span data-ttu-id="f804a-135">*wwwroot/appsettings.jsv*:</span><span class="sxs-lookup"><span data-stu-id="f804a-135">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="f804a-136">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f804a-136">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="f804a-137">Konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="f804a-137">Provider configuration</span></span>

<span data-ttu-id="f804a-138">Následující příklad používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> k poskytnutí další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f804a-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="f804a-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f804a-139">`Program.Main`:</span></span>

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

<span data-ttu-id="f804a-140">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f804a-140">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="f804a-141">Chcete-li číst další konfigurační soubory ze složky *wwwroot* do konfigurace, použijte <xref:System.Net.Http.HttpClient> k získání obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="f804a-141">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="f804a-142">Při použití tohoto přístupu existující <xref:System.Net.Http.HttpClient> Registrace služby může použít místního klienta vytvořeného pro čtení souboru, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="f804a-142">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="f804a-143">*wwwroot/cars.jsv*:</span><span class="sxs-lookup"><span data-stu-id="f804a-143">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="f804a-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f804a-144">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="f804a-145">Konfigurace ověřování</span><span class="sxs-lookup"><span data-stu-id="f804a-145">Authentication configuration</span></span>

<span data-ttu-id="f804a-146">*wwwroot/appsettings.jsv*:</span><span class="sxs-lookup"><span data-stu-id="f804a-146">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="f804a-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f804a-147">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="f804a-148">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="f804a-148">Logging configuration</span></span>

<span data-ttu-id="f804a-149">Přidat odkaz na balíček pro [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="f804a-149">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="f804a-150">*wwwroot/appsettings.jsv*:</span><span class="sxs-lookup"><span data-stu-id="f804a-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="f804a-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f804a-151">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="f804a-152">Konfigurace tvůrce hostitele</span><span class="sxs-lookup"><span data-stu-id="f804a-152">Host builder configuration</span></span>

<span data-ttu-id="f804a-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f804a-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="f804a-154">Konfigurace uložená v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="f804a-154">Cached configuration</span></span>

<span data-ttu-id="f804a-155">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="f804a-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="f804a-156">S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="f804a-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="f804a-157">Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="f804a-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="f804a-158">Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.</span><span class="sxs-lookup"><span data-stu-id="f804a-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="f804a-159">Soubory *service-worker.js* a *service-worker-assets.js* aplikace PWA musí být znovu sestaveny při kompilaci, který signalizace aplikaci na další stránce uživatele v online režimu, že byla aplikace znovu nasazena.</span><span class="sxs-lookup"><span data-stu-id="f804a-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="f804a-160">Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, naleznete v tématu <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="f804a-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="f804a-161">protokolování</span><span class="sxs-lookup"><span data-stu-id="f804a-161">Logging</span></span>

<span data-ttu-id="f804a-162">Informace o Blazor podpoře protokolování WebAssembly naleznete v tématu <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="f804a-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="f804a-163">vyjednávání mezi zdroji pro ověřování</span><span class="sxs-lookup"><span data-stu-id="f804a-163"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="f804a-164">Konfigurace SignalR základního klienta pro odesílání přihlašovacích údajů, jako jsou soubory cookie nebo hlavičky ověřování http:</span><span class="sxs-lookup"><span data-stu-id="f804a-164">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="f804a-165">Použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> k nastavení <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> žádostí o [načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) mezi zdroji:</span><span class="sxs-lookup"><span data-stu-id="f804a-165">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="f804a-166">Přiřaďte <xref:System.Net.Http.HttpMessageHandler> k <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Možnosti:</span><span class="sxs-lookup"><span data-stu-id="f804a-166">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="f804a-167">Další informace naleznete v tématu <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="f804a-167">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="f804a-168">WebServer</span><span class="sxs-lookup"><span data-stu-id="f804a-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="f804a-169">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="f804a-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="f804a-170">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="f804a-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="f804a-171">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="f804a-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="f804a-172">Chcete-li přizpůsobit uživatelské rozhraní, definujte element na `id` `components-reconnect-modal` `<body>` stránce *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="f804a-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="f804a-173">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="f804a-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="f804a-174">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="f804a-174">CSS class</span></span>                       | <span data-ttu-id="f804a-175">Označující&hellip;</span><span class="sxs-lookup"><span data-stu-id="f804a-175">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="f804a-176">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="f804a-176">A lost connection.</span></span> <span data-ttu-id="f804a-177">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="f804a-177">The client is attempting to reconnect.</span></span> <span data-ttu-id="f804a-178">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="f804a-178">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="f804a-179">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="f804a-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="f804a-180">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="f804a-180">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="f804a-181">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="f804a-181">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="f804a-182">Chcete-li se pokusit znovu připojit, zavolejte `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="f804a-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="f804a-183">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="f804a-183">Reconnection rejected.</span></span> <span data-ttu-id="f804a-184">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="f804a-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="f804a-185">K opětovnému načtení aplikace zavolejte `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="f804a-185">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="f804a-186">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="f804a-186">This connection state may result when:</span></span><ul><li><span data-ttu-id="f804a-187">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="f804a-187">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="f804a-188">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="f804a-188">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="f804a-189">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="f804a-189">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="f804a-190">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="f804a-190">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="f804a-191">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="f804a-191">Render mode</span></span>

Blazor<span data-ttu-id="f804a-192">Serverové aplikace se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="f804a-192"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="f804a-193">To je nastaveno na stránce *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="f804a-193">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="f804a-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="f804a-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="f804a-195">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="f804a-195">Is prerendered into the page.</span></span>
* <span data-ttu-id="f804a-196">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="f804a-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="f804a-197">Description</span><span class="sxs-lookup"><span data-stu-id="f804a-197">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="f804a-198">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f804a-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f804a-199">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="f804a-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="f804a-200">Vykreslí značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f804a-200">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f804a-201">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="f804a-201">Output from the component isn't included.</span></span> <span data-ttu-id="f804a-202">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="f804a-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="f804a-203">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="f804a-203">Renders the component into static HTML.</span></span> |

<span data-ttu-id="f804a-204">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="f804a-204">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="f804a-205">Konfigurace SignalR klienta pro Blazor serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="f804a-205">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="f804a-206">V některých případech je potřeba nakonfigurovat SignalR klienta používaného Blazor serverovými aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="f804a-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="f804a-207">Například můžete chtít nakonfigurovat protokolování na SignalR straně klienta, aby bylo možné diagnostikovat problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="f804a-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="f804a-208">Chcete-li nakonfigurovat SignalR klienta v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f804a-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="f804a-209">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="f804a-209">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="f804a-210">Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje SignalR Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="f804a-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="f804a-211">protokolování</span><span class="sxs-lookup"><span data-stu-id="f804a-211">Logging</span></span>

<span data-ttu-id="f804a-212">Informace o Blazor podpoře protokolování serveru najdete v tématu <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="f804a-212">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
