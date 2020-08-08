---
title: Různá rozhraní API ochrany ASP.NET Core dat
author: rick-anderson
description: Přečtěte si o rozhraní ASP.NET Core Data Protection ISecret.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 5ab8226779fb4209a7254b95eccac4be2d26b10d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019037"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Různá rozhraní API ochrany ASP.NET Core dat

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Typy, které implementují některá z následujících rozhraní, by měly být bezpečné pro přístup z více vláken pro více volajících.

## <a name="isecret"></a>ISecret

`ISecret`Rozhraní představuje tajnou hodnotu, jako je například materiál kryptografického klíče. Obsahuje následující plochu rozhraní API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer`Metoda naplní poskytnutou vyrovnávací paměť nepůvodní tajnou hodnotou. Důvod, proč toto rozhraní API přebírá vyrovnávací paměť jako parametr, a nevrací `byte[]` přímo, je to, že volající může využít k připnutí objektu vyrovnávací paměti, což omezuje vystavení tajného kódu spravovanému systému uvolňování paměti.

`Secret`Typ je konkrétní implementace, `ISecret` kde je tajná hodnota uložena v paměti v procesu. Na platformách systému Windows je tajná hodnota šifrována prostřednictvím [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).
