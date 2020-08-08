---
title: ASP.NET Core Blazor prostředí
author: guardrex
description: Přečtěte si o prostředích v Blazor , včetně postupu nastavení prostředí Blazor WebAssembly aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 8a672b3d2aff4dd2b80465b0f6dac038d299eaa9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014422"
---
# <a name="aspnet-core-no-locblazor-environments"></a>ASP.NET Core Blazor prostředí

> [!NOTE]
> Toto téma se týká Blazor WebAssembly . Obecné pokyny týkající se konfigurace ASP.NET Core aplikace najdete v tématu <xref:fundamentals/environments> .

Při místním spuštění aplikace je prostředí standardně vyvíjené. Při publikování aplikace je prostředí standardně v produkčním prostředí.

Hostovaná Blazor WebAssembly aplikace vybírá prostředí ze serveru prostřednictvím middleware, který toto prostředí komunikuje do prohlížeče přidáním `blazor-environment` hlavičky. Hodnota hlavičky je prostředí. Hostovaná Blazor aplikace a serverová aplikace sdílejí stejné prostředí. Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments> .

Pro samostatnou spuštěnou aplikaci, která je spuštěna místně, vývojový server přidá `blazor-environment` hlavičku pro určení vývojového prostředí. Chcete-li určit prostředí pro jiná hostující prostředí, přidejte `blazor-environment` hlavičku.

V následujícím příkladu pro IIS přidejte vlastní hlavičku do publikovaného `web.config` souboru. `web.config`Soubor se nachází ve `bin/Release/{TARGET FRAMEWORK}/publish` složce:

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
> Chcete-li použít vlastní `web.config` soubor pro službu IIS, která není přepsána při publikování aplikace do `publish` složky, přečtěte si téma <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .

Získání prostředí aplikace v součásti vložením <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a čtením <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> vlastnosti:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Během spouštění <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> zpřístupňuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> vlastnost prostřednictvím vlastnosti, která vývojářům umožňuje mít v kódu logiku konkrétního prostředí:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Následující rozšiřující metody umožňují kontrolu aktuálního prostředí pro názvy pro vývoj, produkci, přípravu a vlastní prostředí:

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Vlastnost lze použít při spuštění, když <xref:Microsoft.AspNetCore.Components.NavigationManager> není služba k dispozici.

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/environments>
