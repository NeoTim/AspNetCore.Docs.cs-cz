---
<span data-ttu-id="52dc5-101">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-103">'Blazor'</span></span>
- <span data-ttu-id="52dc5-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-104">'Identity'</span></span>
- <span data-ttu-id="52dc5-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-106">'Razor'</span></span>
- <span data-ttu-id="52dc5-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="52dc5-108">ASP.NET Core Blazor Konfigurace modelu hostování</span><span class="sxs-lookup"><span data-stu-id="52dc5-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="52dc5-109">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="52dc5-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="52dc5-110">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="52dc5-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="52dc5-111">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="52dc5-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="52dc5-112">Prostředí</span><span class="sxs-lookup"><span data-stu-id="52dc5-112">Environment</span></span>

<span data-ttu-id="52dc5-113">Při místním spuštění aplikace je prostředí standardně vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="52dc5-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="52dc5-114">Při publikování aplikace je prostředí standardně v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="52dc5-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="52dc5-115">Hostovaná Blazor aplikace WebAssembly vybírá prostředí ze serveru prostřednictvím middlewaru, který toto prostředí komunikuje do prohlížeče přidáním `blazor-environment` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="52dc5-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="52dc5-116">Hodnota hlavičky je prostředí.</span><span class="sxs-lookup"><span data-stu-id="52dc5-116">The value of the header is the environment.</span></span> <span data-ttu-id="52dc5-117">Hostovaná Blazor aplikace a serverová aplikace sdílejí stejné prostředí.</span><span class="sxs-lookup"><span data-stu-id="52dc5-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="52dc5-118">Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="52dc5-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="52dc5-119">Pro samostatnou spuštěnou aplikaci, která je spuštěna místně, vývojový server přidá `blazor-environment` hlavičku pro určení vývojového prostředí.</span><span class="sxs-lookup"><span data-stu-id="52dc5-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="52dc5-120">Chcete-li určit prostředí pro jiná hostující prostředí, přidejte `blazor-environment` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="52dc5-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="52dc5-121">V následujícím příkladu pro službu IIS přidejte vlastní hlavičku do publikovaného souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="52dc5-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="52dc5-122">Soubor *Web. config* je umístěný ve složce *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="52dc5-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="52dc5-123">Chcete-li použít vlastní soubor *Web. config* pro službu IIS, který není po publikování aplikace ve složce pro *publikování* přepsán, přečtěte si téma <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="52dc5-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="52dc5-124">Získání prostředí aplikace v součásti vložením <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a čtením <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="52dc5-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="52dc5-125">Během spouštění <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> zpřístupňuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> vlastnost prostřednictvím vlastnosti, která vývojářům umožňuje mít v kódu logiku konkrétního prostředí:</span><span class="sxs-lookup"><span data-stu-id="52dc5-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="52dc5-126">Následující rozšiřující metody umožňují kontrolu aktuálního prostředí pro názvy pro vývoj, produkci, přípravu a vlastní prostředí:</span><span class="sxs-lookup"><span data-stu-id="52dc5-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="52dc5-127"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Vlastnost lze použít při spuštění, když <xref:Microsoft.AspNetCore.Components.NavigationManager> není služba k dispozici.</span><span class="sxs-lookup"><span data-stu-id="52dc5-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="52dc5-128">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="52dc5-128">Configuration</span></span>

Blazor<span data-ttu-id="52dc5-129">Sestavení WebAssembly načítá konfiguraci z:</span><span class="sxs-lookup"><span data-stu-id="52dc5-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="52dc5-130">Soubory nastavení aplikace ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="52dc5-130">App settings files by default:</span></span>
  * <span data-ttu-id="52dc5-131">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="52dc5-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="52dc5-132">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="52dc5-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="52dc5-133">Další [poskytovatelé konfigurace](xref:fundamentals/configuration/index) zaregistrované aplikací</span><span class="sxs-lookup"><span data-stu-id="52dc5-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="52dc5-134">Ne všichni poskytovatelé jsou vhodný pro Blazor aplikace pro WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="52dc5-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="52dc5-135">Objasnění, které poskytovatelé podporují pro Blazor WebAssembly, je sledováno pomocí [vysvětlení poskytovatelé konfigurace pro Blazor WASM (dotnet/AspNetCore. Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="52dc5-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="52dc5-136">Konfigurace v Blazor aplikaci WebAssembly je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="52dc5-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="52dc5-137">**Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="52dc5-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="52dc5-138">Další informace o poskytovatelích konfigurace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="52dc5-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="52dc5-139">Konfigurace nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="52dc5-139">App settings configuration</span></span>

<span data-ttu-id="52dc5-140">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="52dc5-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="52dc5-141">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="52dc5-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="52dc5-142">Konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="52dc5-142">Provider configuration</span></span>

<span data-ttu-id="52dc5-143">Následující příklad používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> k poskytnutí další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="52dc5-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="52dc5-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="52dc5-144">`Program.Main`:</span></span>

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

<span data-ttu-id="52dc5-145">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="52dc5-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="52dc5-146">Chcete-li číst další konfigurační soubory ze složky *wwwroot* do konfigurace, použijte <xref:System.Net.Http.HttpClient> k získání obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="52dc5-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="52dc5-147">Při použití tohoto přístupu existující <xref:System.Net.Http.HttpClient> Registrace služby může použít místního klienta vytvořeného pro čtení souboru, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="52dc5-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="52dc5-148">*wwwroot/automobily. JSON*:</span><span class="sxs-lookup"><span data-stu-id="52dc5-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="52dc5-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="52dc5-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="52dc5-150">Konfigurace ověřování</span><span class="sxs-lookup"><span data-stu-id="52dc5-150">Authentication configuration</span></span>

<span data-ttu-id="52dc5-151">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="52dc5-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="52dc5-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="52dc5-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="52dc5-153">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="52dc5-153">Logging configuration</span></span>

<span data-ttu-id="52dc5-154">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="52dc5-154">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="52dc5-155">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="52dc5-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="52dc5-156">Konfigurace tvůrce hostitele</span><span class="sxs-lookup"><span data-stu-id="52dc5-156">Host builder configuration</span></span>

<span data-ttu-id="52dc5-157">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="52dc5-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="52dc5-158">Konfigurace uložená v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="52dc5-158">Cached configuration</span></span>

<span data-ttu-id="52dc5-159">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="52dc5-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="52dc5-160">S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="52dc5-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="52dc5-161">Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="52dc5-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="52dc5-162">Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.</span><span class="sxs-lookup"><span data-stu-id="52dc5-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="52dc5-163">Soubory *Service-Worker. js* a *Service-Worker-assets. js* aplikace PWA je nutné znovu sestavit při kompilaci, který signalizuje aplikaci na další stránce uživatele online, na kterou se aplikace znovu nasadila.</span><span class="sxs-lookup"><span data-stu-id="52dc5-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="52dc5-164">Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, naleznete v tématu <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="52dc5-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="52dc5-165">Protokolování</span><span class="sxs-lookup"><span data-stu-id="52dc5-165">Logging</span></span>

<span data-ttu-id="52dc5-166">Informace o Blazor podpoře protokolování WebAssembly naleznete v tématu <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="52dc5-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="52dc5-167">WebServer</span><span class="sxs-lookup"><span data-stu-id="52dc5-167"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="52dc5-168">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="52dc5-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="52dc5-169">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="52dc5-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="52dc5-170">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="52dc5-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="52dc5-171">Chcete-li přizpůsobit uživatelské rozhraní, definujte element na `id` `components-reconnect-modal` `<body>` stránce *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="52dc5-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="52dc5-172">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="52dc5-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="52dc5-173">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="52dc5-173">CSS class</span></span>                       | <span data-ttu-id="52dc5-174">Označující&hellip;</span><span class="sxs-lookup"><span data-stu-id="52dc5-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="52dc5-175">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-176">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-177">'Blazor'</span></span>
- <span data-ttu-id="52dc5-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-178">'Identity'</span></span>
- <span data-ttu-id="52dc5-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-180">'Razor'</span></span>
- <span data-ttu-id="52dc5-181">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-182">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-183">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-184">'Blazor'</span></span>
- <span data-ttu-id="52dc5-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-185">'Identity'</span></span>
- <span data-ttu-id="52dc5-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-187">'Razor'</span></span>
- <span data-ttu-id="52dc5-188">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-189">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-190">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-191">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-191">'Blazor'</span></span>
- <span data-ttu-id="52dc5-192">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-192">'Identity'</span></span>
- <span data-ttu-id="52dc5-193">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-194">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-194">'Razor'</span></span>
- <span data-ttu-id="52dc5-195">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-196">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-197">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-198">'Blazor'</span></span>
- <span data-ttu-id="52dc5-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-199">'Identity'</span></span>
- <span data-ttu-id="52dc5-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-201">'Razor'</span></span>
- <span data-ttu-id="52dc5-202">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-203">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-204">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-205">'Blazor'</span></span>
- <span data-ttu-id="52dc5-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-206">'Identity'</span></span>
- <span data-ttu-id="52dc5-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-208">'Razor'</span></span>
- <span data-ttu-id="52dc5-209">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-210">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-211">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-212">'Blazor'</span></span>
- <span data-ttu-id="52dc5-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-213">'Identity'</span></span>
- <span data-ttu-id="52dc5-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-215">'Razor'</span></span>
- <span data-ttu-id="52dc5-216">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-217">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-218">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-219">'Blazor'</span></span>
- <span data-ttu-id="52dc5-220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-220">'Identity'</span></span>
- <span data-ttu-id="52dc5-221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-222">'Razor'</span></span>
- <span data-ttu-id="52dc5-223">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-224">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-225">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-226">'Blazor'</span></span>
- <span data-ttu-id="52dc5-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-227">'Identity'</span></span>
- <span data-ttu-id="52dc5-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-229">'Razor'</span></span>
- <span data-ttu-id="52dc5-230">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-231">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-232">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-233">'Blazor'</span></span>
- <span data-ttu-id="52dc5-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-234">'Identity'</span></span>
- <span data-ttu-id="52dc5-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-236">'Razor'</span></span>
- <span data-ttu-id="52dc5-237">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-238">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-239">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-240">'Blazor'</span></span>
- <span data-ttu-id="52dc5-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-241">'Identity'</span></span>
- <span data-ttu-id="52dc5-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-243">'Razor'</span></span>
- <span data-ttu-id="52dc5-244">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-245">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-246">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-247">'Blazor'</span></span>
- <span data-ttu-id="52dc5-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-248">'Identity'</span></span>
- <span data-ttu-id="52dc5-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-250">'Razor'</span></span>
- <span data-ttu-id="52dc5-251">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-252">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-253">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-254">'Blazor'</span></span>
- <span data-ttu-id="52dc5-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-255">'Identity'</span></span>
- <span data-ttu-id="52dc5-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-257">'Razor'</span></span>
- <span data-ttu-id="52dc5-258">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-259">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-260">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-261">'Blazor'</span></span>
- <span data-ttu-id="52dc5-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-262">'Identity'</span></span>
- <span data-ttu-id="52dc5-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-264">'Razor'</span></span>
- <span data-ttu-id="52dc5-265">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-265">'SignalR' uid:</span></span> 

<span data-ttu-id="52dc5-266">---------------- | ---Název: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci modelu hostování, včetně postupu integrace Razor komponent do Razor stránek a aplikací MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-267">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-268">'Blazor'</span></span>
- <span data-ttu-id="52dc5-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-269">'Identity'</span></span>
- <span data-ttu-id="52dc5-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-271">'Razor'</span></span>
- <span data-ttu-id="52dc5-272">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-273">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-274">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-275">'Blazor'</span></span>
- <span data-ttu-id="52dc5-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-276">'Identity'</span></span>
- <span data-ttu-id="52dc5-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-278">'Razor'</span></span>
- <span data-ttu-id="52dc5-279">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-280">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-281">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-282">'Blazor'</span></span>
- <span data-ttu-id="52dc5-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-283">'Identity'</span></span>
- <span data-ttu-id="52dc5-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-285">'Razor'</span></span>
- <span data-ttu-id="52dc5-286">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-287">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-288">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-289">'Blazor'</span></span>
- <span data-ttu-id="52dc5-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-290">'Identity'</span></span>
- <span data-ttu-id="52dc5-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-292">'Razor'</span></span>
- <span data-ttu-id="52dc5-293">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-294">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-295">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-296">'Blazor'</span></span>
- <span data-ttu-id="52dc5-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-297">'Identity'</span></span>
- <span data-ttu-id="52dc5-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-299">'Razor'</span></span>
- <span data-ttu-id="52dc5-300">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52dc5-301">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="52dc5-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="52dc5-302">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="52dc5-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52dc5-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-303">'Blazor'</span></span>
- <span data-ttu-id="52dc5-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52dc5-304">'Identity'</span></span>
- <span data-ttu-id="52dc5-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52dc5-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="52dc5-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52dc5-306">'Razor'</span></span>
- <span data-ttu-id="52dc5-307">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="52dc5-307">'SignalR' uid:</span></span> 

<span data-ttu-id="52dc5-308">--------- | | `components-reconnect-show`     | Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="52dc5-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="52dc5-309">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="52dc5-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="52dc5-310">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="52dc5-310">Show the modal.</span></span> <span data-ttu-id="52dc5-311">| | `components-reconnect-hide`     | K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="52dc5-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="52dc5-312">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="52dc5-312">Hide the modal.</span></span> <span data-ttu-id="52dc5-313">| | `components-reconnect-failed`   | Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="52dc5-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="52dc5-314">Chcete-li se pokusit znovu připojit, zavolejte `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="52dc5-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="52dc5-315">| | `components-reconnect-rejected` | Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="52dc5-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="52dc5-316">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="52dc5-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="52dc5-317">K opětovnému načtení aplikace zavolejte `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="52dc5-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="52dc5-318">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="52dc5-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="52dc5-319">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="52dc5-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="52dc5-320">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="52dc5-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="52dc5-321">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="52dc5-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="52dc5-322">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="52dc5-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="52dc5-323">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="52dc5-323">Render mode</span></span>

Blazor<span data-ttu-id="52dc5-324">Serverové aplikace se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="52dc5-324"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="52dc5-325">To je nastaveno na stránce *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="52dc5-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="52dc5-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="52dc5-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="52dc5-327">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="52dc5-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="52dc5-328">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="52dc5-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="52dc5-329">Description</span><span class="sxs-lookup"><span data-stu-id="52dc5-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="52dc5-330">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="52dc5-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="52dc5-331">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="52dc5-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="52dc5-332">Vykreslí značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="52dc5-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="52dc5-333">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="52dc5-333">Output from the component isn't included.</span></span> <span data-ttu-id="52dc5-334">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="52dc5-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="52dc5-335">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="52dc5-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="52dc5-336">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="52dc5-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="52dc5-337">Konfigurace SignalR klienta pro Blazor serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="52dc5-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="52dc5-338">V některých případech je potřeba nakonfigurovat SignalR klienta používaného Blazor serverovými aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="52dc5-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="52dc5-339">Například můžete chtít nakonfigurovat protokolování na SignalR straně klienta, aby bylo možné diagnostikovat problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="52dc5-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="52dc5-340">Chcete-li nakonfigurovat SignalR klienta v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="52dc5-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="52dc5-341">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="52dc5-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="52dc5-342">Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje SignalR Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="52dc5-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="52dc5-343">Protokolování</span><span class="sxs-lookup"><span data-stu-id="52dc5-343">Logging</span></span>

<span data-ttu-id="52dc5-344">Informace o Blazor podpoře protokolování serveru najdete v tématu <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="52dc5-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
