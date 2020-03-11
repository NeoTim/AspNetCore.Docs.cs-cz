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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Různá rozhraní API ochrany ASP.NET Core dat

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Typy, které implementují některý z následujících rozhraní by měly být bezpečné pro vlákna pro více volání.

## <a name="isecret"></a>ISecret

Rozhraní `ISecret` představuje tajnou hodnotu, jako je například materiál kryptografického klíče. Obsahuje následující plochu rozhraní API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

Metoda `WriteSecretIntoBuffer` naplní poskytnutou vyrovnávací paměť nepůvodní tajnou hodnotou. Důvod, proč toto rozhraní API přebírá vyrovnávací paměť jako parametr, a nevrací `byte[]` přímo, je to, že volající bude chtít připnout objekt vyrovnávací paměti a omezit vystavení tajného přístupu spravovanému systému uvolňování paměti.

`Secret` typ je konkrétní implementace `ISecret`, kde tajná hodnota je uložena v paměti v procesu. Na platformách systému Windows je tajná hodnota šifrována prostřednictvím [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).
