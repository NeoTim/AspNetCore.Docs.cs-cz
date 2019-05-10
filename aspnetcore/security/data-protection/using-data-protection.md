---
title: Začínáme s rozhraními API ochrany dat v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak používat ochranu dat ASP.NET Core API pro ochranu a zrušení ochrany dat v aplikaci.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 25bf099a3d9edd7e6e0872725cbc3707750314e6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087642"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="42cae-103">Začínáme s rozhraními API ochrany dat v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42cae-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="42cae-104">Ve své nejjednodušší, ochranu dat se skládá z následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="42cae-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="42cae-105">Vytvoření ochrany pomocí dat z zprostředkovatel ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="42cae-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="42cae-106">Volání `Protect` metoda s daty, které chcete chránit.</span><span class="sxs-lookup"><span data-stu-id="42cae-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="42cae-107">Volání `Unprotect` metoda s daty, které chcete změnit zpět na prostý text.</span><span class="sxs-lookup"><span data-stu-id="42cae-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="42cae-108">Většina architektur a modely aplikace, jako je ASP.NET Core nebo SignalR, už konfiguraci systému ochrany dat a přidejte ho do kontejneru služby, ke kterým přistupujete prostřednictvím vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="42cae-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="42cae-109">Následující příklad ukazuje konfiguraci služby kontejneru pro vkládání závislostí a registrace zásobník ochrany dat, příjem zprostředkovatel ochrany dat prostřednictvím DI, vytváření ochrana a ochrana pak odvolanými data.</span><span class="sxs-lookup"><span data-stu-id="42cae-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="42cae-110">Při vytváření ochranného zařízení musí zadat jeden nebo více [účelové řetězce](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="42cae-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="42cae-111">Řetězec účelu poskytuje izolaci mezi příjemci.</span><span class="sxs-lookup"><span data-stu-id="42cae-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="42cae-112">Například ochranného zařízení vytvořené pomocí řetězce "green" účel nemohli zrušení ochrany dat poskytované ochranného zařízení s účelem "nachová".</span><span class="sxs-lookup"><span data-stu-id="42cae-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="42cae-113">Instance `IDataProtectionProvider` a `IDataProtector` jsou bezpečné pro vlákna pro více volání.</span><span class="sxs-lookup"><span data-stu-id="42cae-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="42cae-114">Se předpokládá, který po získá odkaz na komponentu `IDataProtector` prostřednictvím volání `CreateProtector`, použije tento odkaz pro více volání `Protect` a `Unprotect`.</span><span class="sxs-lookup"><span data-stu-id="42cae-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="42cae-115">Volání `Unprotect` cryptographicexception – vyvolá výjimku, pokud nelze ověřit nebo dešifrovat znalosti chráněné datové části.</span><span class="sxs-lookup"><span data-stu-id="42cae-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="42cae-116">Některé součásti staví na Ignorovat chyby během odemknout operace komponenta, která načte ověřovací soubory cookie může zpracovat tuto chybu a zpracovávat žádosti, jako kdyby byla žádný soubor cookie vůbec spíše než nesplní žádost rovnou předplatit.</span><span class="sxs-lookup"><span data-stu-id="42cae-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="42cae-117">Komponenty, které chcete toto chování musí konkrétně zachytit cryptographicexception – místo požití všechny výjimky.</span><span class="sxs-lookup"><span data-stu-id="42cae-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
