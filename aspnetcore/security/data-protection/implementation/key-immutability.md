---
title: Klíčová neměnnosti a nastavení klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci rozhraní neměnnosti API pro ochranu dat ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 5bbd1fb28fc0330ccfe4e829ab0b2f86226c7166
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776913"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Klíčová neměnnosti a nastavení klíčů v ASP.NET Core

Jakmile je objekt uložen do záložního úložiště, jeho reprezentace je trvale opravena. Do záložního úložiště je možné přidat nová data, ale stávající data nejdou nikdy provést. Hlavním účelem tohoto chování je zabránit poškození dat.

Jedním z důsledků tohoto chování je, že jakmile se klíč zapíše do záložního úložiště, je neměnné. Data o jejich vytvoření, aktivaci a vypršení platnosti nelze nikdy změnit, i když je lze odvolat `IKeyManager`pomocí. Kromě toho jsou tyto základní informace o algoritmech, materiál hlavních klíčů a šifrování ve vlastnostech REST také neměnné.

Pokud vývojář změní jakékoli nastavení, které má vliv na trvalost klíčů, tyto změny se projeví až po vygenerování dalšího klíče, a to buď prostřednictvím explicitního volání `IKeyManager.CreateNewKey` nebo prostřednictvím vlastního [automatického generování klíče](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) systému ochrany dat. Nastavení, která ovlivňují trvalost klíčů, jsou následující:

* [Výchozí životnost klíče](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [Šifrování klíče v mechanismu REST](xref:security/data-protection/implementation/key-encryption-at-rest)

* [Algoritmy s informacemi obsaženými v klíči](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Pokud potřebujete, aby se tato nastavení nastavila dříve, než je další automatická doba provozu klíče, zvažte explicitní volání `IKeyManager.CreateNewKey` pro vynucení vytvoření nového klíče. Nezapomeňte zadat explicitní datum aktivace ({Now + 2 dny} je dobrým pravidlem, které poznáte, aby bylo možné změnit rozšíření) a datum vypršení platnosti volání.

>[!TIP]
> Všechny aplikace, které se dotýkají úložiště, by měly určovat `IDataProtectionBuilder` stejné nastavení s metodami rozšíření. V opačném případě budou vlastnosti trvalého klíče závislé na konkrétní aplikaci, která vyvolala rutiny generování klíčů.
