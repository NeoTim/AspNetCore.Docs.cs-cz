---
title: Konfigurace oříznutí pro ASP.NET Core Blazor
author: guardrex
description: Naučte se řídit linkeru jazyka IL (Intermediate Language) (trávník) při sestavování Blazor aplikace.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 2923f76c586465e4e6044763f18527a7d36ad57c
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080862"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>Konfigurace oříznutí pro ASP.NET Core Blazor

Od [Pranav Krishnamoorthy](https://github.com/pranavkm)

Blazor WebAssembly provede oříznutí [mezilehlého jazyka (IL)](/dotnet/standard/managed-code#intermediate-language--execution) , aby se snížila velikost publikovaného výstupu.

Oříznutí aplikace optimalizuje pro velikost, ale může mít škodlivé účinky. Aplikace, které používají reflexi nebo související dynamické funkce, mohou být při oříznutí přerušeny, protože oříznutí neví o dynamickém chování a nelze určit, které typy jsou požadovány pro reflexi za běhu. Aby bylo možné oříznout takové aplikace, musí být ořezávání informovaná o jakýchkoli typech vyžadovaných odrazem v kódu a v balíčcích nebo architekturách, na kterých aplikace závisí.

Abyste zajistili, že bude aplikace po nasazení správně fungovat, je důležité otestovat publikovaný výstup často během vývoje.

Oříznutí pro aplikace .NET lze zakázat nastavením `PublishTrimmed` vlastnosti MSBuild na hodnotu `false` v souboru projektu aplikace:

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```

## <a name="additional-resources"></a>Další zdroje

* [Odebrání nechtěných součástí a zachování pouze samostatně nasaditelných součástí a spustitelných souborů](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
