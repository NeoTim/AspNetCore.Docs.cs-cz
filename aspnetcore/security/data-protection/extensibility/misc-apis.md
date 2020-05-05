---
title: Různá rozhraní API ochrany ASP.NET Core dat
author: rick-anderson
description: Přečtěte si o rozhraní ASP.NET Core Data Protection ISecret.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: a07ccc3645a9a8132fd5290e7c43f353f74aca05
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776978"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="f5aee-103">Různá rozhraní API ochrany ASP.NET Core dat</span><span class="sxs-lookup"><span data-stu-id="f5aee-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="f5aee-104">Typy, které implementují některá z následujících rozhraní, by měly být bezpečné pro přístup z více vláken pro více volajících.</span><span class="sxs-lookup"><span data-stu-id="f5aee-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="f5aee-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="f5aee-105">ISecret</span></span>

<span data-ttu-id="f5aee-106">`ISecret` Rozhraní představuje tajnou hodnotu, jako je například materiál kryptografického klíče.</span><span class="sxs-lookup"><span data-stu-id="f5aee-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="f5aee-107">Obsahuje následující plochu rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="f5aee-107">It contains the following API surface:</span></span>

* <span data-ttu-id="f5aee-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="f5aee-108">`Length`: `int`</span></span>

* <span data-ttu-id="f5aee-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="f5aee-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="f5aee-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="f5aee-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="f5aee-111">`WriteSecretIntoBuffer` Metoda naplní poskytnutou vyrovnávací paměť nepůvodní tajnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="f5aee-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="f5aee-112">Důvod, proč toto rozhraní API přebírá vyrovnávací paměť jako parametr, a `byte[]` nevrací přímo, je to, že volající může využít k připnutí objektu vyrovnávací paměti, což omezuje vystavení tajného kódu spravovanému systému uvolňování paměti.</span><span class="sxs-lookup"><span data-stu-id="f5aee-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="f5aee-113">`Secret` Typ je konkrétní implementace, `ISecret` kde je tajná hodnota uložena v paměti v procesu.</span><span class="sxs-lookup"><span data-stu-id="f5aee-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="f5aee-114">Na platformách systému Windows je tajná hodnota šifrována prostřednictvím [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span><span class="sxs-lookup"><span data-stu-id="f5aee-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
