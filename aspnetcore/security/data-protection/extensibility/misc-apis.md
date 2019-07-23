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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Různé technologie ASP.NET Core rozhraními API na ochranu dat

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Typy, které implementují některý z následujících rozhraní by měly být bezpečné pro vlákna pro více volání.

## <a name="isecret"></a>ISecret

`ISecret` Rozhraní představuje hodnotu tajného kódu, jako je například kryptografické klíče. Obsahuje následující plochy rozhraní API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer` Metoda naplní poskytnutá vyrovnávací paměť s nezpracované hodnoty tajných kódů. Z důvodu tohoto rozhraní API trvá vyrovnávací paměti jako parametr místo vrácení `byte[]` přímo je, že to dává volající možnost připnout vyrovnávací paměti objektu, omezuje jeho vystavení tajných kódů spravované systému uvolňování paměti.

`Secret` Typ je konkrétní implementaci `ISecret` kde tajná hodnota uložená v paměti v procesu. Na platformách Windows, se šifrují tajná hodnota prostřednictvím [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).
