---
title: Zrušení ochrany datových částí, jejichž klíče byly odvolány v ASP.NET Core
author: rick-anderson
description: Naučte se, jak zrušit ochranu dat chráněných pomocí klíčů, které od nich byly odvolány, v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 55a7ec4052b3ab47d5ff41bbce3fc3f9662f609c
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913843"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="d501c-103">Zrušení ochrany datových částí, jejichž klíče byly odvolány v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d501c-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="d501c-104">Rozhraní API pro ochranu ASP.NET Core dat nejsou primárně určena pro neomezenou perzistenci důvěrných datových částí.</span><span class="sxs-lookup"><span data-stu-id="d501c-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="d501c-105">Další technologie, jako je [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) a [Azure Rights Management](/rights-management/) , jsou vhodnější pro scénář neurčitého úložiště a mají odpovídající možnosti silné správy klíčů.</span><span class="sxs-lookup"><span data-stu-id="d501c-105">Other technologies like [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="d501c-106">V takovém případě nebrání vývojářům v používání rozhraní API ochrany ASP.NET Core dat pro dlouhodobou ochranu důvěrných dat.</span><span class="sxs-lookup"><span data-stu-id="d501c-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="d501c-107">Klíče se nikdy neodebraly z klíčového prstence, takže `IDataProtector.Unprotect` může vždycky obnovovat stávající datové části, pokud jsou klíče k dispozici a jsou platné.</span><span class="sxs-lookup"><span data-stu-id="d501c-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="d501c-108">Pokud se ale vývojář pokusí zrušit ochranu dat chráněných pomocí odvolaného klíče, dojde k problému, protože `IDataProtector.Unprotect` v tomto případě vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="d501c-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="d501c-109">To může být jemné pro krátkodobé nebo přechodné datové části (jako jsou ověřovací tokeny), protože tyto typy datových částí lze snadno znovu vytvořit systémem a v nejhorším případě může být vyžadováno přihlášení návštěvníka webu.</span><span class="sxs-lookup"><span data-stu-id="d501c-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="d501c-110">Ale u trvalých datových částí `Unprotect` může vyvolání throw způsobit nepřijatelnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="d501c-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="d501c-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="d501c-111">IPersistedDataProtector</span></span>

<span data-ttu-id="d501c-112">Aby bylo možné podporovat scénář povolení ochrany datových částí i na přední stěně odvolaných klíčů, systém ochrany dat obsahuje `IPersistedDataProtector` typ.</span><span class="sxs-lookup"><span data-stu-id="d501c-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="d501c-113">Chcete-li získat instanci `IPersistedDataProtector` , jednoduše Získejte instanci `IDataProtector` v normálním způsobu a zkuste přetypování na `IDataProtector` `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="d501c-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="d501c-114">Ne všechny `IDataProtector` instance lze přetypovat na `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="d501c-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="d501c-115">Vývojáři by měli použít operátor jazyka C# nebo podobným způsobem, aby nedocházelo k výjimkám modulu runtime způsobených neplatnými přetypováními a měli by být připraveni zpracovat případ selhání odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="d501c-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="d501c-116">`IPersistedDataProtector`zpřístupňuje následující plochu rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="d501c-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="d501c-117">Toto rozhraní API přebírá chráněnou datovou část (jako bajtové pole) a vrací nechráněnou datovou část.</span><span class="sxs-lookup"><span data-stu-id="d501c-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="d501c-118">Neexistuje žádné přetížení založené na řetězci.</span><span class="sxs-lookup"><span data-stu-id="d501c-118">There's no string-based overload.</span></span> <span data-ttu-id="d501c-119">Tyto dva výstupní parametry jsou následující.</span><span class="sxs-lookup"><span data-stu-id="d501c-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="d501c-120">`requiresMigration`: bude nastaveno na hodnotu true, pokud klíč, který je použit k ochraně této datové části, již není aktivním výchozím klíčem, např. klíč použitý k ochraně této datové části je starý a od chvíle, kdy byla provedena operace s klíčovou operací.</span><span class="sxs-lookup"><span data-stu-id="d501c-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="d501c-121">Volající může chtít zvážit přeochranu datové části v závislosti na jejich obchodních potřebách.</span><span class="sxs-lookup"><span data-stu-id="d501c-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="d501c-122">`wasRevoked`: bude nastaveno na hodnotu true, pokud byl klíč použitý k ochraně této datové části odvolán.</span><span class="sxs-lookup"><span data-stu-id="d501c-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="d501c-123">Při předávání `ignoreRevocationErrors: true` do metody vyzkoušejte mimořádně opatrní `DangerousUnprotect` .</span><span class="sxs-lookup"><span data-stu-id="d501c-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="d501c-124">Pokud po volání této metody `wasRevoked` je hodnota true, klíč použitý k ochraně této datové části byl odvolán a pravost datové části by měla být považována za podezřelou.</span><span class="sxs-lookup"><span data-stu-id="d501c-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="d501c-125">V takovém případě můžete i nadále používat nechráněnou datovou část, pokud máte určitá samostatná ujištění, že je ze zabezpečené databáze místo odesílání nedůvěryhodným webovým klientem.</span><span class="sxs-lookup"><span data-stu-id="d501c-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
