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
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Začínáme s rozhraními API ochrany dat v ASP.NET Core

<a name="security-data-protection-getting-started"></a>

Ve své nejjednodušší, ochranu dat se skládá z následujících kroků:

1. Vytvoření ochrany pomocí dat z zprostředkovatel ochrany dat.

2. Volání `Protect` metoda s daty, které chcete chránit.

3. Volání `Unprotect` metoda s daty, které chcete změnit zpět na prostý text.

Většina architektur a modely aplikace, jako je ASP.NET Core nebo SignalR, už konfiguraci systému ochrany dat a přidejte ho do kontejneru služby, ke kterým přistupujete prostřednictvím vkládání závislostí. Následující příklad ukazuje konfiguraci služby kontejneru pro vkládání závislostí a registrace zásobník ochrany dat, příjem zprostředkovatel ochrany dat prostřednictvím DI, vytváření ochrana a ochrana pak odvolanými data.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Při vytváření ochranného zařízení musí zadat jeden nebo více [účelové řetězce](xref:security/data-protection/consumer-apis/purpose-strings). Řetězec účelu poskytuje izolaci mezi příjemci. Například ochranného zařízení vytvořené pomocí řetězce "green" účel nemohli zrušení ochrany dat poskytované ochranného zařízení s účelem "nachová".

>[!TIP]
> Instance `IDataProtectionProvider` a `IDataProtector` jsou bezpečné pro vlákna pro více volání. Se předpokládá, který po získá odkaz na komponentu `IDataProtector` prostřednictvím volání `CreateProtector`, použije tento odkaz pro více volání `Protect` a `Unprotect`.
>
>Volání `Unprotect` cryptographicexception – vyvolá výjimku, pokud nelze ověřit nebo dešifrovat znalosti chráněné datové části. Některé součásti staví na Ignorovat chyby během odemknout operace komponenta, která načte ověřovací soubory cookie může zpracovat tuto chybu a zpracovávat žádosti, jako kdyby byla žádný soubor cookie vůbec spíše než nesplní žádost rovnou předplatit. Komponenty, které chcete toto chování musí konkrétně zachytit cryptographicexception – místo požití všechny výjimky.
