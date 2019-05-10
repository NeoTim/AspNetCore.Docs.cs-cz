---
title: Zásady pro celý počítač ochrany dat podpory v ASP.NET Core
author: rick-anderson
description: Další informace o podpoře pro nastavení zásady pro celý počítač výchozí pro všechny aplikace, které využívají ochranu dat ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 70aaca7afcd3df22cebb4466fbd9845a2277688c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902979"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Zásady pro celý počítač ochrany dat podpory v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Pokud používáte Windows, ochrana dat systému má omezenou podporu pro nastavení zásady pro celý počítač výchozí pro všechny aplikace, které využívají ochranu dat ASP.NET Core. Obecnou myšlenku je, že možná budete chtít změnit výchozí nastavení, jako například používá algoritmy nebo životnosti klíče, aniž byste museli ručně aktualizovat všechny aplikace na počítači správcem.

> [!WARNING]
> Správce systému může nastavit výchozí zásady, ale nemůže vynutit. Vývojář aplikace můžete vždy přepíší libovolnou hodnotu s jedním z jejich vlastního výběru. Výchozí zásady ovlivní pouze aplikace tam, kde vývojáři nebyl zadán explicitní hodnotu pro třídu setting.

## <a name="setting-default-policy"></a>Nastavení výchozích zásad

Pokud chcete nastavit výchozí zásady, Správce může nastavit známé hodnoty v systémovém registru v následujícím klíči registru:

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Pokud jste na 64bitový operační systém a ovlivnit chování 32bitových aplikací, nezapomeňte nakonfigurovat Wow6432Node ekvivalent výše uvedený klíč.

Podporované hodnoty jsou uvedeny níže.

| Value              | Type   | Popis |
| ------------------ | :----: | ----------- |
| EncryptionType     | odkazy řetězců | Určuje, které algoritmy má být použito k ochraně dat. Hodnota musí být CNG-CBC, CNG GCM nebo spravované a je podrobněji popsaný níže. |
| DefaultKeyLifetime | DWORD  | Určuje dobu životnosti pro nově vygenerovat klíče. Hodnota zadaná ve dnech a musí být > = 7. |
| KeyEscrowSinks     | odkazy řetězců | Určuje typy, které se používají pro klíčů v úschově. Středníkem oddělený seznam klíčů v úschově jímky, kde každý prvek v seznamu je název kvalifikovaný pro sestavení typu, který implementuje hodnotu [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Typy šifrování

Pokud EncryptionType CNG-CBC, systém konfigurován pro použití režimu CBC symetrický blokových šifrách důvěrnost a HMAC pravosti pomocí služby poskytované Windows CNG (viz [zadání vlastní algoritmů Windows CNG](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) pro Další podrobnosti). Jsou podporovány následující další hodnoty, z nichž každá vlastnost v typu CngCbcAuthenticatedEncryptionSettings odpovídá.

| Value                       | Type   | Popis |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | odkazy řetězců | Název algoritmu symetrického bloku šifer podporovaných službou CNG. Tento algoritmus je otevřen v režimu CBC. |
| EncryptionAlgorithmProvider | odkazy řetězců | Název implementace poskytovatele CNG, který může vytvořit algoritmus načtou algoritmy EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | Délka klíče k odvození pro šifrovací algoritmus symetrického bloku (v bitech). |
| HashAlgorithm               | odkazy řetězců | Název algoritmu hash rozumí CNG. Tento algoritmus je otevřen v režimu HMAC. |
| HashAlgorithmProvider       | odkazy řetězců | Název implementace poskytovatele CNG, které mohou způsobit algoritmus HashAlgorithm. |

Pokud EncryptionType CNG GCM, systém je nakonfigurován pro účely symetrický blokových šifrách Galois/čítač režim utajení a pravosti pomocí služby poskytované Windows CNG (viz [zadání vlastní algoritmů Windows CNG](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) Další podrobnosti). Jsou podporovány následující další hodnoty, z nichž každá vlastnost v typu CngGcmAuthenticatedEncryptionSettings odpovídá.

| Value                       | Type   | Popis |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | odkazy řetězců | Název algoritmu symetrického bloku šifer podporovaných službou CNG. Tento algoritmus je otevřen v režimu Galois/čítač. |
| EncryptionAlgorithmProvider | odkazy řetězců | Název implementace poskytovatele CNG, který může vytvořit algoritmus načtou algoritmy EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | Délka klíče k odvození pro šifrovací algoritmus symetrického bloku (v bitech). |

Pokud je spravováno EncryptionType, systém je nakonfigurován pro účely spravované SymmetricAlgorithm důvěrnost a KeyedHashAlgorithm pravosti (naleznete v tématu [zadání vlastní spravované algoritmy](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) další podrobnosti). Jsou podporovány následující další hodnoty, z nichž každá vlastnost v typu ManagedAuthenticatedEncryptionSettings odpovídá.

| Hodnota                      | Type   | Popis |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | odkazy řetězců | Název kvalifikovaný pro sestavení typu, který implementuje SymmetricAlgorithm. |
| EncryptionAlgorithmKeySize | DWORD  | Délka klíče k odvození pro algoritmus symetrického šifrování (v bitech). |
| ValidationAlgorithmType    | odkazy řetězců | Název kvalifikovaný pro sestavení typu, který implementuje KeyedHashAlgorithm. |

Pokud EncryptionType má jakoukoli jinou hodnotu než null nebo je prázdný, ochrana dat systému dojde k výjimce při spuštění.

> [!WARNING]
> Při konfiguraci výchozí nastavení zásad, která zahrnuje názvy typů (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), typy musí být k dispozici pro aplikaci. To znamená, že pro aplikace běžící na modul Desktop CLR, sestavení, které obsahují tyto typy by měly být k dispozici v globální mezipaměti sestavení (GAC). Pro aplikace ASP.NET Core spuštěné v .NET Core musí být nainstalován balíčky, které obsahují tyto typy.
