---
title: Klíčová neměnnosti a nastavení klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci rozhraní neměnnosti API pro ochranu dat ASP.NET Core.
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 01fb3a155aefa34dcd9ede8e7d6ada8fe6bb751c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403817"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Klíčová neměnnosti a nastavení klíčů v ASP.NET Core

Jakmile je objekt uložen do záložního úložiště, jeho reprezentace je trvale opravena. Do záložního úložiště je možné přidat nová data, ale stávající data nejdou nikdy provést. Hlavním účelem tohoto chování je zabránit poškození dat.

Jedním z důsledků tohoto chování je, že jakmile se klíč zapíše do záložního úložiště, je neměnné. Data o jejich vytvoření, aktivaci a vypršení platnosti nelze nikdy změnit, i když je lze odvolat pomocí `IKeyManager` . Kromě toho jsou tyto základní informace o algoritmech, materiál hlavních klíčů a šifrování ve vlastnostech REST také neměnné.

Pokud vývojář změní jakékoli nastavení, které má vliv na trvalost klíčů, tyto změny se projeví až po vygenerování dalšího klíče, a to buď prostřednictvím explicitního volání `IKeyManager.CreateNewKey` nebo prostřednictvím vlastního [automatického generování klíče](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) systému ochrany dat. Nastavení, která ovlivňují trvalost klíčů, jsou následující:

* [Výchozí životnost klíče](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [Šifrování klíče v mechanismu REST](xref:security/data-protection/implementation/key-encryption-at-rest)

* [Algoritmy s informacemi obsaženými v klíči](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Pokud potřebujete, aby se tato nastavení nastavila dříve, než je další automatická doba provozu klíče, zvažte explicitní volání pro `IKeyManager.CreateNewKey` vynucení vytvoření nového klíče. Nezapomeňte zadat explicitní datum aktivace ({Now + 2 dny} je dobrým pravidlem, které poznáte, aby bylo možné změnit rozšíření) a datum vypršení platnosti volání.

>[!TIP]
> Všechny aplikace, které se dotýkají úložiště, by měly určovat stejné nastavení s `IDataProtectionBuilder` metodami rozšíření. V opačném případě budou vlastnosti trvalého klíče závislé na konkrétní aplikaci, která vyvolala rutiny generování klíčů.
