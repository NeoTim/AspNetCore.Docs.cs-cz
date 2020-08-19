---
title: Omezte dobu života chráněných datových částí v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak omezit životnost chráněné datové části pomocí ASP.NET Core rozhraní API ochrany dat.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: f76aca460c293b5f814ba10ee6c8ac68b3d147bb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634420"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="6eb19-103">Omezte dobu života chráněných datových částí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6eb19-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="6eb19-104">V některých scénářích chce vývojář aplikace vytvořit chráněnou datovou část, jejíž platnost vyprší po nastaveném časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="6eb19-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="6eb19-105">Chráněná datová část například může představovat token pro resetování hesla, který by měl platit jenom pro jednu hodinu.</span><span class="sxs-lookup"><span data-stu-id="6eb19-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="6eb19-106">Je možné, že vývojář vytvoří vlastní formát datové části, který obsahuje vložené datum vypršení platnosti, a zkušení vývojáři ho můžou chtít udělat, ale většina vývojářů, kteří spravují tyto vypršení platnosti, může růst zdlouhavě.</span><span class="sxs-lookup"><span data-stu-id="6eb19-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="6eb19-107">Aby bylo snazší pro naši cílovou skupinu pro vývojáře, balíček [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) obsahuje rozhraní API pro vytváření datových částí, jejichž platnost automaticky vyprší po nastaveném časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="6eb19-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="6eb19-108">Tato rozhraní API zablokují `ITimeLimitedDataProtector` typ.</span><span class="sxs-lookup"><span data-stu-id="6eb19-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="6eb19-109">Použití rozhraní API</span><span class="sxs-lookup"><span data-stu-id="6eb19-109">API usage</span></span>

<span data-ttu-id="6eb19-110">`ITimeLimitedDataProtector`Rozhraní je základní rozhraní pro ochranu a odemknutí datové části s časovým limitem pro časově omezená data.</span><span class="sxs-lookup"><span data-stu-id="6eb19-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="6eb19-111">Chcete-li vytvořit instanci `ITimeLimitedDataProtector` , budete nejprve potřebovat instanci regulárního [IDataProtector](xref:security/data-protection/consumer-apis/overview) vytvořenou s konkrétním účelem.</span><span class="sxs-lookup"><span data-stu-id="6eb19-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="6eb19-112">Jakmile `IDataProtector` je instance k dispozici, zavolejte `IDataProtector.ToTimeLimitedDataProtector` metodu rozšíření a vraťte ochranu s integrovanými možnostmi vypršení platnosti.</span><span class="sxs-lookup"><span data-stu-id="6eb19-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="6eb19-113">`ITimeLimitedDataProtector` zpřístupňuje následující plochu rozhraní API a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6eb19-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="6eb19-114">CreateProtector (řetězec pro účely): ITimeLimitedDataProtector – toto rozhraní API je podobné jako stávající `IDataProtectionProvider.CreateProtector` v tom, že je možné ho použít k vytvoření [řetězů účelu](xref:security/data-protection/consumer-apis/purpose-strings) z kořenového ochrany s časovým omezením.</span><span class="sxs-lookup"><span data-stu-id="6eb19-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="6eb19-115">Chránit (Byte [] prostý text, DateTimeOffset vypršení): Byte []</span><span class="sxs-lookup"><span data-stu-id="6eb19-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="6eb19-116">Ochrana (Byte [] prostého textu, životnost TimeSpan): Byte []</span><span class="sxs-lookup"><span data-stu-id="6eb19-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="6eb19-117">Chránit (Byte [] ve formátu prostého textu): Byte []</span><span class="sxs-lookup"><span data-stu-id="6eb19-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="6eb19-118">Chránit (řetězec ve formátu prostého textu, DateTimeOffset vypršení): řetězec</span><span class="sxs-lookup"><span data-stu-id="6eb19-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="6eb19-119">Chránit (textový řetězec ve formátu prostého textu, životnost TimeSpan): řetězec</span><span class="sxs-lookup"><span data-stu-id="6eb19-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="6eb19-120">Chránit (řetězec ve formátu prostého textu): řetězec</span><span class="sxs-lookup"><span data-stu-id="6eb19-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="6eb19-121">Kromě základních `Protect` metod, které přebírají pouze prostý text, existují nová přetížení, která umožňují zadat datum vypršení platnosti datové části.</span><span class="sxs-lookup"><span data-stu-id="6eb19-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="6eb19-122">Datum vypršení platnosti lze zadat jako absolutní datum (prostřednictvím `DateTimeOffset` ) nebo jako relativní čas (od aktuálního systémového času, přes a `TimeSpan` ).</span><span class="sxs-lookup"><span data-stu-id="6eb19-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="6eb19-123">Pokud se zavolá přetížení, které nepřijímá vypršení platnosti, předpokládá se, že datová část nebude nikdy vypršet.</span><span class="sxs-lookup"><span data-stu-id="6eb19-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="6eb19-124">Odemknout (Byte [] protectedData, konec platnosti datového typu DateTimeOffset): Byte []</span><span class="sxs-lookup"><span data-stu-id="6eb19-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="6eb19-125">Zrušit ochranu (Byte [] protectedData): Byte []</span><span class="sxs-lookup"><span data-stu-id="6eb19-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="6eb19-126">Zrušit ochranu (řetězcová protectedData, odchozí datový čas DateTimeOffset): řetězec</span><span class="sxs-lookup"><span data-stu-id="6eb19-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="6eb19-127">Unprotect (String protectedData): řetězec</span><span class="sxs-lookup"><span data-stu-id="6eb19-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="6eb19-128">`Unprotect`Metody vrátí původní nechráněná data.</span><span class="sxs-lookup"><span data-stu-id="6eb19-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="6eb19-129">Pokud v datové části ještě nevypršela platnost, vrátí se absolutní doba vypršení platnosti jako volitelný parametr out spolu s původní nechráněnými daty.</span><span class="sxs-lookup"><span data-stu-id="6eb19-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="6eb19-130">Pokud vypršela platnost datové části, všechna přetížení metody Unprotect vyvolá CryptographicException –.</span><span class="sxs-lookup"><span data-stu-id="6eb19-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="6eb19-131">Nedoporučuje se používat tato rozhraní API k ochraně datových částí, které vyžadují dlouhodobou nebo neomezenou perzistenci.</span><span class="sxs-lookup"><span data-stu-id="6eb19-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="6eb19-132">"Můžu si dovolit, aby se chráněná datová část trvale po měsíci neobnovila?"</span><span class="sxs-lookup"><span data-stu-id="6eb19-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="6eb19-133">může sloužit jako dobré pravidlo pro palec. Pokud odpověď není, vývojáři by si měli vzít v úvahu alternativní rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6eb19-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="6eb19-134">Následující ukázka používá [nedi cestu kódu](xref:security/data-protection/configuration/non-di-scenarios) pro vytvoření instance systému ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="6eb19-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="6eb19-135">Chcete-li spustit tuto ukázku, ujistěte se, že jste nejprve přidali odkaz na balíček Microsoft. AspNetCore. DataProtection. Extensions.</span><span class="sxs-lookup"><span data-stu-id="6eb19-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
