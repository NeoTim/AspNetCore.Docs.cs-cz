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
# <a name="aspnet-core-blazor-environments"></a>ASP.NET Core Blazor prostředí

> [!NOTE]
> Toto téma se vztahuje na Blazor WebAssembly. Obecné pokyny týkající se konfigurace ASP.NET Core aplikace najdete v tématu <xref:fundamentals/environments> .

Při místním spuštění aplikace je prostředí standardně vyvíjené. Při publikování aplikace je prostředí standardně v produkčním prostředí.

Hostovaná Blazor aplikace WebAssembly vybírá prostředí ze serveru prostřednictvím middlewaru, který toto prostředí komunikuje do prohlížeče přidáním `blazor-environment` hlavičky. Hodnota hlavičky je prostředí. Hostovaná Blazor aplikace a serverová aplikace sdílejí stejné prostředí. Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments> .

Pro samostatnou spuštěnou aplikaci, která je spuštěna místně, vývojový server přidá `blazor-environment` hlavičku pro určení vývojového prostředí. Chcete-li určit prostředí pro jiná hostující prostředí, přidejte `blazor-environment` hlavičku.

V následujícím příkladu pro IIS přidejte vlastní hlavičku do publikovaného *web.config* souboru. Soubor *web.config* je umístěný ve složce *bin/Release/{Target Framework}/Publish* :

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
> Chcete-li použít vlastní soubor *web.config* pro službu IIS, která není přepsána při publikování aplikace do složky pro *publikování* , přečtěte si téma <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .

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

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/environments>
