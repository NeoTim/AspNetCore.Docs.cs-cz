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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Různá rozhraní API ochrany ASP.NET Core dat

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Typy, které implementují některá z následujících rozhraní, by měly být bezpečné pro přístup z více vláken pro více volajících.

## <a name="isecret"></a>ISecret

`ISecret` Rozhraní představuje tajnou hodnotu, jako je například materiál kryptografického klíče. Obsahuje následující plochu rozhraní API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer` Metoda naplní poskytnutou vyrovnávací paměť nepůvodní tajnou hodnotou. Důvod, proč toto rozhraní API přebírá vyrovnávací paměť jako parametr, a `byte[]` nevrací přímo, je to, že volající může využít k připnutí objektu vyrovnávací paměti, což omezuje vystavení tajného kódu spravovanému systému uvolňování paměti.

`Secret` Typ je konkrétní implementace, `ISecret` kde je tajná hodnota uložena v paměti v procesu. Na platformách systému Windows je tajná hodnota šifrována prostřednictvím [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).
