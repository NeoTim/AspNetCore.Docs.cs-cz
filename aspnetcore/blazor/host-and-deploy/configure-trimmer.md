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
ms.openlocfilehash: 57d8f069c79b558020253968d736f350bc8a6f03
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721733"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="e06a3-103">Konfigurace oříznutí pro ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e06a3-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="e06a3-104">Od [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="e06a3-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="e06a3-105">Blazor WebAssembly provede oříznutí [mezilehlého jazyka (IL)](/dotnet/standard/managed-code#intermediate-language--execution) , aby se snížila velikost publikovaného výstupu.</span><span class="sxs-lookup"><span data-stu-id="e06a3-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="e06a3-106">Oříznutí aplikace optimalizuje pro velikost, ale může mít škodlivé účinky.</span><span class="sxs-lookup"><span data-stu-id="e06a3-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="e06a3-107">Aplikace, které používají reflexi nebo související dynamické funkce, mohou být při oříznutí přerušeny, protože oříznutí neví o dynamickém chování a nelze určit, které typy jsou požadovány pro reflexi za běhu.</span><span class="sxs-lookup"><span data-stu-id="e06a3-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="e06a3-108">Aby bylo možné oříznout takové aplikace, musí být ořezávání informovaná o jakýchkoli typech vyžadovaných odrazem v kódu a v balíčcích nebo architekturách, na kterých aplikace závisí.</span><span class="sxs-lookup"><span data-stu-id="e06a3-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="e06a3-109">Abyste zajistili, že bude aplikace po nasazení správně fungovat, je důležité otestovat publikovaný výstup často během vývoje.</span><span class="sxs-lookup"><span data-stu-id="e06a3-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="e06a3-110">Oříznutí pro aplikace .NET lze zakázat nastavením `PublishTrimmed` vlastnosti MSBuild na hodnotu `false` v souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="e06a3-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
<span data-ttu-id="e06a3-111">Další možnosti konfigurace ořezávání najdete v části [Možnosti ořezávání](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="e06a3-111">Additional options to configure the trimmer can be found at [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e06a3-112">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e06a3-112">Additional resources</span></span>

* [<span data-ttu-id="e06a3-113">Odebrání nechtěných součástí a zachování pouze samostatně nasaditelných součástí a spustitelných souborů</span><span class="sxs-lookup"><span data-stu-id="e06a3-113">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
