---
title: Různé technologie ASP.NET Core rozhraními API na ochranu dat
author: rick-anderson
description: Další informace o rozhraní ASP.NET Core Data Protection ISecret.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902658"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="6560b-103">Různé technologie ASP.NET Core rozhraními API na ochranu dat</span><span class="sxs-lookup"><span data-stu-id="6560b-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="6560b-104">Typy, které implementují některý z následujících rozhraní by měly být bezpečné pro vlákna pro více volání.</span><span class="sxs-lookup"><span data-stu-id="6560b-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="6560b-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="6560b-105">ISecret</span></span>

<span data-ttu-id="6560b-106">`ISecret` Rozhraní představuje hodnotu tajného kódu, jako je například kryptografické klíče.</span><span class="sxs-lookup"><span data-stu-id="6560b-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="6560b-107">Obsahuje následující plochy rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="6560b-107">It contains the following API surface:</span></span>

* <span data-ttu-id="6560b-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="6560b-108">`Length`: `int`</span></span>

* <span data-ttu-id="6560b-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="6560b-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="6560b-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="6560b-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="6560b-111">`WriteSecretIntoBuffer` Metoda naplní poskytnutá vyrovnávací paměť s nezpracované hodnoty tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="6560b-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="6560b-112">Z důvodu tohoto rozhraní API trvá vyrovnávací paměti jako parametr místo vrácení `byte[]` přímo je, že to dává volající možnost připnout vyrovnávací paměti objektu, omezuje jeho vystavení tajných kódů spravované systému uvolňování paměti.</span><span class="sxs-lookup"><span data-stu-id="6560b-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="6560b-113">`Secret` Typ je konkrétní implementaci `ISecret` kde tajná hodnota uložená v paměti v procesu.</span><span class="sxs-lookup"><span data-stu-id="6560b-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="6560b-114">Na platformách Windows, se šifrují tajná hodnota prostřednictvím [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span><span class="sxs-lookup"><span data-stu-id="6560b-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
