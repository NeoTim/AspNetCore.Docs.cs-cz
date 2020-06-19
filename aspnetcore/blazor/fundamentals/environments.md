---
title: ASP.NET Core Blazor prostředí
author: guardrex
description: Přečtěte si o prostředích v Blazor , včetně postupu nastavení prostředí Blazor aplikace pro WebAssembly.
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 203f29ce606a313463e416b068177ce02acd6231
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103695"
---
# <a name="aspnet-core-blazor-environments"></a><span data-ttu-id="d1be1-103">ASP.NET Core Blazor prostředí</span><span class="sxs-lookup"><span data-stu-id="d1be1-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="d1be1-104">Toto téma se vztahuje na Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="d1be1-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="d1be1-105">Obecné pokyny týkající se konfigurace ASP.NET Core aplikace najdete v tématu <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="d1be1-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d1be1-106">Při místním spuštění aplikace je prostředí standardně vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="d1be1-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="d1be1-107">Při publikování aplikace je prostředí standardně v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="d1be1-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="d1be1-108">Hostovaná Blazor aplikace WebAssembly vybírá prostředí ze serveru prostřednictvím middlewaru, který toto prostředí komunikuje do prohlížeče přidáním `blazor-environment` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="d1be1-108">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="d1be1-109">Hodnota hlavičky je prostředí.</span><span class="sxs-lookup"><span data-stu-id="d1be1-109">The value of the header is the environment.</span></span> <span data-ttu-id="d1be1-110">Hostovaná Blazor aplikace a serverová aplikace sdílejí stejné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d1be1-110">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="d1be1-111">Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="d1be1-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d1be1-112">Pro samostatnou spuštěnou aplikaci, která je spuštěna místně, vývojový server přidá `blazor-environment` hlavičku pro určení vývojového prostředí.</span><span class="sxs-lookup"><span data-stu-id="d1be1-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="d1be1-113">Chcete-li určit prostředí pro jiná hostující prostředí, přidejte `blazor-environment` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="d1be1-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="d1be1-114">V následujícím příkladu pro IIS přidejte vlastní hlavičku do publikovaného *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="d1be1-114">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="d1be1-115">Soubor *web.config* je umístěný ve složce *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="d1be1-115">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="d1be1-116">Chcete-li použít vlastní soubor *web.config* pro službu IIS, která není přepsána při publikování aplikace do složky pro *publikování* , přečtěte si téma <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="d1be1-116">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="d1be1-117">Získání prostředí aplikace v součásti vložením <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a čtením <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="d1be1-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="d1be1-118">Během spouštění <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> zpřístupňuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> vlastnost prostřednictvím vlastnosti, která vývojářům umožňuje mít v kódu logiku konkrétního prostředí:</span><span class="sxs-lookup"><span data-stu-id="d1be1-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="d1be1-119">Následující rozšiřující metody umožňují kontrolu aktuálního prostředí pro názvy pro vývoj, produkci, přípravu a vlastní prostředí:</span><span class="sxs-lookup"><span data-stu-id="d1be1-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="d1be1-120"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Vlastnost lze použít při spuštění, když <xref:Microsoft.AspNetCore.Components.NavigationManager> není služba k dispozici.</span><span class="sxs-lookup"><span data-stu-id="d1be1-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d1be1-121">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d1be1-121">Additional resources</span></span>

* <xref:fundamentals/environments>
