---
title: Podpora zásad ochrany dat na úrovni počítače v ASP.NET Core
author: rick-anderson
description: Přečtěte si o podpoře pro nastavení výchozích zásad pro všechny aplikace, které využívají ASP.NET Core ochraně dat v celém počítači.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 84f54b37dfff3112ea5ca84f931103624cfde90a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776835"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Podpora zásad ochrany dat na úrovni počítače v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Při použití v systému Windows má systém ochrany dat omezenou podporu pro nastavení výchozích zásad pro všechny aplikace, které využívají ASP.NET Core ochrany dat v celém počítači. Hlavním nápadem je, že správce může chtít změnit výchozí nastavení, například používané algoritmy nebo životnost klíče, aniž by musel ručně aktualizovat všechny aplikace v počítači.

> [!WARNING]
> Správce systému může nastavit výchozí zásady, ale nemůže je vymáhat. Vývojář aplikace může vždycky přepsat libovolnou hodnotu jedním z nich. Výchozí zásady ovlivňují jenom aplikace, u kterých vývojář nezadal explicitní hodnotu pro nastavení.

## <a name="setting-default-policy"></a>Nastavení výchozích zásad

Správce může nastavit výchozí zásady tak, že v registru systému nastaví známé hodnoty v následujícím klíči registru:

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Pokud jste v 64 operačním systému a chcete mít vliv na chování 32 aplikací, nezapomeňte nakonfigurovat Wow6432Node ekvivalent výše uvedeného klíče.

Níže jsou uvedené podporované hodnoty.

| Hodnota              | Typ   | Popis |
| ------------------ | :----: | ----------- |
| EncryptionType     | řetězec | Určuje, které algoritmy by se měly používat pro ochranu dat. Hodnota musí být CNG-CBC, CNG-GCM nebo spravovaná a jsou podrobněji popsány níže. |
| DefaultKeyLifetime | DWORD  | Určuje dobu života nově vygenerovaných klíčů. Hodnota je zadána ve dnech a musí být >= 7. |
| KeyEscrowSinks     | řetězec | Určuje typy, které se používají pro Key v úschově. Hodnota je čárkami oddělený seznam jímky Key v úschově, kde každý prvek v seznamu je kvalifikovaný název sestavení typu, který implementuje [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Typy šifrování

Pokud EncryptionType je CNG-CBC, systém je nakonfigurován tak, aby používal protokol symetrického bloku CBC pro zajištění důvěrnosti a HMAC pro pravost pomocí služeb poskytovaných službou Windows CNG (Další informace najdete v tématu [určení vlastních algoritmů Windows CNG](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) ). Následující další hodnoty jsou podporovány, přičemž každý z nich odpovídá vlastnosti typu CngCbcAuthenticatedEncryptionSettings.

| Hodnota                       | Typ   | Popis |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | řetězec | Název algoritmu šifrování symetrického bloku, který rozumí CNG. Tento algoritmus je otevřen v režimu CBC. |
| EncryptionAlgorithmProvider | řetězec | Název implementace poskytovatele CNG, která může vydávat algoritmus EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | Délka (v bitech) klíče, který má být odvozen pro algoritmus symetrického šifrování bloku. |
| HashAlgorithm               | řetězec | Název algoritmu hash srozumitelného pro CNG. Tento algoritmus je otevřen v režimu HMAC. |
| HashAlgorithmProvider       | řetězec | Název implementace poskytovatele CNG, která může vydávat algoritmus HashAlgorithm. |

Pokud EncryptionType je CNG-GCM, systém je nakonfigurován tak, aby používal šifrovací šifru symetrického bloku Galois/Counter pro zajištění důvěrnosti a pravosti se službami poskytovanými Windows CNG (další podrobnosti najdete v tématu [určení vlastních algoritmů CNG Windows](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) ). Následující další hodnoty jsou podporovány, přičemž každý z nich odpovídá vlastnosti typu CngGcmAuthenticatedEncryptionSettings.

| Hodnota                       | Typ   | Popis |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | řetězec | Název algoritmu šifrování symetrického bloku, který rozumí CNG. Tento algoritmus je otevřen v režimu Galois/Counter. |
| EncryptionAlgorithmProvider | řetězec | Název implementace poskytovatele CNG, která může vydávat algoritmus EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | Délka (v bitech) klíče, který má být odvozen pro algoritmus symetrického šifrování bloku. |

Pokud je EncryptionType spravované, je systém nakonfigurovaný tak, aby používal spravovaný SymmetricAlgorithm pro zajištění důvěrnosti a KeyedHashAlgorithm pro pravost (Další informace najdete v tématu [určení vlastních spravovaných algoritmů](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) ). Následující další hodnoty jsou podporovány, přičemž každý z nich odpovídá vlastnosti typu ManagedAuthenticatedEncryptionSettings.

| Hodnota                      | Typ   | Popis |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | řetězec | Kvalifikovaný název sestavení typu, který implementuje SymmetricAlgorithm. |
| EncryptionAlgorithmKeySize | DWORD  | Délka klíče (v bitech), který má být odvozen pro algoritmus symetrického šifrování. |
| ValidationAlgorithmType    | řetězec | Kvalifikovaný název sestavení typu, který implementuje KeyedHashAlgorithm. |

Pokud má EncryptionType jinou hodnotu než null nebo prázdné, při spuštění vyvolá systém ochrany dat výjimku.

> [!WARNING]
> Při konfiguraci výchozího nastavení zásad, které zahrnuje názvy typů (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), musí být typy k dispozici pro aplikaci. To znamená, že pro aplikace spuštěné v modulu CLR pro stolní počítače by sestavení, která obsahují tyto typy, měla být přítomna v globální mezipaměti sestavení (GAC). Pro ASP.NET Core aplikace běžící na .NET Core by se měly nainstalovat balíčky obsahující tyto typy.
