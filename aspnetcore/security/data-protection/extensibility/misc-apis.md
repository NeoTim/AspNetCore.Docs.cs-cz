---
title: Různá rozhraní API ochrany ASP.NET Core dat
author: rick-anderson
description: Přečtěte si o rozhraní ASP.NET Core Data Protection ISecret.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 2e319cdcec1e005682555c4e03c52632e6d8521a
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913803"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="ba261-103">Různá rozhraní API ochrany ASP.NET Core dat</span><span class="sxs-lookup"><span data-stu-id="ba261-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="ba261-104">Typy, které implementují některá z následujících rozhraní, by měly být bezpečné pro přístup z více vláken pro více volajících.</span><span class="sxs-lookup"><span data-stu-id="ba261-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="ba261-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="ba261-105">ISecret</span></span>

<span data-ttu-id="ba261-106">`ISecret`Rozhraní představuje tajnou hodnotu, jako je například materiál kryptografického klíče.</span><span class="sxs-lookup"><span data-stu-id="ba261-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="ba261-107">Obsahuje následující plochu rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="ba261-107">It contains the following API surface:</span></span>

* <span data-ttu-id="ba261-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="ba261-108">`Length`: `int`</span></span>

* <span data-ttu-id="ba261-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="ba261-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="ba261-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="ba261-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="ba261-111">`WriteSecretIntoBuffer`Metoda naplní poskytnutou vyrovnávací paměť nepůvodní tajnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="ba261-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="ba261-112">Důvod, proč toto rozhraní API přebírá vyrovnávací paměť jako parametr, a nevrací `byte[]` přímo, je to, že volající může využít k připnutí objektu vyrovnávací paměti, což omezuje vystavení tajného kódu spravovanému systému uvolňování paměti.</span><span class="sxs-lookup"><span data-stu-id="ba261-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="ba261-113">`Secret`Typ je konkrétní implementace, `ISecret` kde je tajná hodnota uložena v paměti v procesu.</span><span class="sxs-lookup"><span data-stu-id="ba261-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="ba261-114">Na platformách systému Windows je tajná hodnota šifrována prostřednictvím [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span><span class="sxs-lookup"><span data-stu-id="ba261-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
