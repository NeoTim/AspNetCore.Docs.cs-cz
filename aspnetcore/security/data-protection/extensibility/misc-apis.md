---
title: Různá rozhraní API ochrany ASP.NET Core dat
author: rick-anderson
description: Přečtěte si o rozhraní ASP.NET Core Data Protection ISecret.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666080"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="1cdeb-103">Různá rozhraní API ochrany ASP.NET Core dat</span><span class="sxs-lookup"><span data-stu-id="1cdeb-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="1cdeb-104">Typy, které implementují některý z následujících rozhraní by měly být bezpečné pro vlákna pro více volání.</span><span class="sxs-lookup"><span data-stu-id="1cdeb-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="1cdeb-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="1cdeb-105">ISecret</span></span>

<span data-ttu-id="1cdeb-106">Rozhraní `ISecret` představuje tajnou hodnotu, jako je například materiál kryptografického klíče.</span><span class="sxs-lookup"><span data-stu-id="1cdeb-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="1cdeb-107">Obsahuje následující plochu rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="1cdeb-107">It contains the following API surface:</span></span>

* <span data-ttu-id="1cdeb-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="1cdeb-108">`Length`: `int`</span></span>

* <span data-ttu-id="1cdeb-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="1cdeb-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="1cdeb-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="1cdeb-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="1cdeb-111">Metoda `WriteSecretIntoBuffer` naplní poskytnutou vyrovnávací paměť nepůvodní tajnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="1cdeb-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="1cdeb-112">Důvod, proč toto rozhraní API přebírá vyrovnávací paměť jako parametr, a nevrací `byte[]` přímo, je to, že volající bude chtít připnout objekt vyrovnávací paměti a omezit vystavení tajného přístupu spravovanému systému uvolňování paměti.</span><span class="sxs-lookup"><span data-stu-id="1cdeb-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="1cdeb-113">`Secret` typ je konkrétní implementace `ISecret`, kde tajná hodnota je uložena v paměti v procesu.</span><span class="sxs-lookup"><span data-stu-id="1cdeb-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="1cdeb-114">Na platformách systému Windows je tajná hodnota šifrována prostřednictvím [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span><span class="sxs-lookup"><span data-stu-id="1cdeb-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
