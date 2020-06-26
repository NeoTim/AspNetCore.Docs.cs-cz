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
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="dcbd2-103">Klíčová neměnnosti a nastavení klíčů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dcbd2-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="dcbd2-104">Jakmile je objekt uložen do záložního úložiště, jeho reprezentace je trvale opravena.</span><span class="sxs-lookup"><span data-stu-id="dcbd2-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="dcbd2-105">Do záložního úložiště je možné přidat nová data, ale stávající data nejdou nikdy provést.</span><span class="sxs-lookup"><span data-stu-id="dcbd2-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="dcbd2-106">Hlavním účelem tohoto chování je zabránit poškození dat.</span><span class="sxs-lookup"><span data-stu-id="dcbd2-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="dcbd2-107">Jedním z důsledků tohoto chování je, že jakmile se klíč zapíše do záložního úložiště, je neměnné.</span><span class="sxs-lookup"><span data-stu-id="dcbd2-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="dcbd2-108">Data o jejich vytvoření, aktivaci a vypršení platnosti nelze nikdy změnit, i když je lze odvolat pomocí `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="dcbd2-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="dcbd2-109">Kromě toho jsou tyto základní informace o algoritmech, materiál hlavních klíčů a šifrování ve vlastnostech REST také neměnné.</span><span class="sxs-lookup"><span data-stu-id="dcbd2-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="dcbd2-110">Pokud vývojář změní jakékoli nastavení, které má vliv na trvalost klíčů, tyto změny se projeví až po vygenerování dalšího klíče, a to buď prostřednictvím explicitního volání `IKeyManager.CreateNewKey` nebo prostřednictvím vlastního [automatického generování klíče](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) systému ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="dcbd2-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="dcbd2-111">Nastavení, která ovlivňují trvalost klíčů, jsou následující:</span><span class="sxs-lookup"><span data-stu-id="dcbd2-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="dcbd2-112">Výchozí životnost klíče</span><span class="sxs-lookup"><span data-stu-id="dcbd2-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="dcbd2-113">Šifrování klíče v mechanismu REST</span><span class="sxs-lookup"><span data-stu-id="dcbd2-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="dcbd2-114">Algoritmy s informacemi obsaženými v klíči</span><span class="sxs-lookup"><span data-stu-id="dcbd2-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="dcbd2-115">Pokud potřebujete, aby se tato nastavení nastavila dříve, než je další automatická doba provozu klíče, zvažte explicitní volání pro `IKeyManager.CreateNewKey` vynucení vytvoření nového klíče.</span><span class="sxs-lookup"><span data-stu-id="dcbd2-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="dcbd2-116">Nezapomeňte zadat explicitní datum aktivace ({Now + 2 dny} je dobrým pravidlem, které poznáte, aby bylo možné změnit rozšíření) a datum vypršení platnosti volání.</span><span class="sxs-lookup"><span data-stu-id="dcbd2-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="dcbd2-117">Všechny aplikace, které se dotýkají úložiště, by měly určovat stejné nastavení s `IDataProtectionBuilder` metodami rozšíření.</span><span class="sxs-lookup"><span data-stu-id="dcbd2-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="dcbd2-118">V opačném případě budou vlastnosti trvalého klíče závislé na konkrétní aplikaci, která vyvolala rutiny generování klíčů.</span><span class="sxs-lookup"><span data-stu-id="dcbd2-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
