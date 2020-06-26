---
title: Začínáme s rozhraními API pro ochranu dat v ASP.NET Core
author: rick-anderson
description: Naučte se používat ASP.NET Core rozhraní API ochrany dat pro ochranu a ochranu dat v aplikaci.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1b0dc6756de55d9ce35eb08ca037e4d4b1fede75
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405611"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="4d68a-103">Začínáme s rozhraními API pro ochranu dat v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d68a-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="4d68a-104">V nejjednodušším případě ochrana dat sestává z následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="4d68a-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="4d68a-105">Vytvořte ochranu dat od poskytovatele ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="4d68a-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="4d68a-106">Zavolejte `Protect` metodu s daty, která chcete chránit.</span><span class="sxs-lookup"><span data-stu-id="4d68a-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="4d68a-107">Zavolejte `Unprotect` metodu s daty, která chcete převést zpět na prostý text.</span><span class="sxs-lookup"><span data-stu-id="4d68a-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="4d68a-108">Většina architektur a modelů aplikací, jako je například ASP.NET Core nebo SignalR , již konfigurují systém ochrany dat a přidávají ho do kontejneru služby, ke kterému přistupujete prostřednictvím injektáže závislosti.</span><span class="sxs-lookup"><span data-stu-id="4d68a-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="4d68a-109">Následující příklad znázorňuje konfiguraci kontejneru služby pro vkládání závislostí a registraci zásobníku ochrany dat, příjem poskytovatele ochrany dat přes DI, vytvoření ochrany a ochrana před tím, než se data odeberou.</span><span class="sxs-lookup"><span data-stu-id="4d68a-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="4d68a-110">Při vytváření ochrany musíte zadat jeden nebo více [řetězců účelu](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="4d68a-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="4d68a-111">Řetězec účelu poskytuje izolaci mezi spotřebiteli.</span><span class="sxs-lookup"><span data-stu-id="4d68a-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="4d68a-112">Například ochrana vytvořená pomocí řetězce "zelená" by nedokázala zrušit ochranu dat poskytovaných ochranou s účelem "fialové".</span><span class="sxs-lookup"><span data-stu-id="4d68a-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="4d68a-113">Instance `IDataProtectionProvider` a `IDataProtector` jsou bezpečné pro přístup z více vláken pro více volajících.</span><span class="sxs-lookup"><span data-stu-id="4d68a-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="4d68a-114">Je určeno, že jakmile komponenta získá odkaz na `IDataProtector` volání `CreateProtector` , použije tento odkaz na více volání `Protect` a `Unprotect` .</span><span class="sxs-lookup"><span data-stu-id="4d68a-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="4d68a-115">Volání `Unprotect` bude vyvolat CryptographicException –, pokud nelze ověřit nebo dešifrovat chráněnou datovou část.</span><span class="sxs-lookup"><span data-stu-id="4d68a-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="4d68a-116">Některé součásti můžou během operace zrušení ochrany ignorovat chyby; komponenta, která čte soubory cookie ověřování, může tuto chybu zpracovat a považovat požadavek za neúspěšného uložení souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="4d68a-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="4d68a-117">Součásti, které chtějí toto chování by měly specificky zachytit CryptographicException – místo požití všech výjimek.</span><span class="sxs-lookup"><span data-stu-id="4d68a-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
