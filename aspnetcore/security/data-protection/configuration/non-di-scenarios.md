---
title: Scénáře, které se nepoužívají při ochraně dat v ASP.NET Core
author: rick-anderson
description: Naučte se podporovat scénáře ochrany dat, kde nemůžete nebo nechcete používat službu poskytovanou vkládáním závislostí.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 31013e97038338d72c98151e23a5caa68008ce4f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776822"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Scénáře, které se nepoužívají při ochraně dat v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core systému ochrany dat se obvykle [přidávají do kontejneru služby](xref:security/data-protection/consumer-apis/overview) a využívají závislé komponenty přes vkládání závislostí (di). Existují však případy, kdy to není proveditelné nebo požadované, zejména při importu systému do existující aplikace.

Pro podporu těchto scénářů poskytuje balíček [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) konkrétní typ, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), který nabízí jednoduchý způsob použití ochrany dat bez spoléhání na di. `DataProtectionProvider` Typ implementuje [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). `DataProtectionProvider` Konstrukce vyžaduje poskytnutí instance [DirectoryInfo](/dotnet/api/system.io.directoryinfo) , která označuje, kde by měly být uloženy kryptografické klíče poskytovatele, jak je vidět v následující ukázce kódu:

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

Ve výchozím nastavení `DataProtectionProvider` konkrétní typ nešifruje materiál nezpracovaného klíče před tím, než je uchovává v systému souborů. To je podpora scénářů, ve kterých vývojář odkazuje na sdílenou síťovou složku a systém ochrany dat, nemůže automaticky odvodit vhodný mechanismus šifrování klíčů.

`DataProtectionProvider` Konkrétní typ navíc [neizoluje aplikace](xref:security/data-protection/configuration/overview#per-application-isolation) ve výchozím nastavení. Všechny aplikace, které používají stejný adresář klíčů, můžou sdílet datové části, pokud odpovídají jejich [parametry účelu](xref:security/data-protection/consumer-apis/purpose-strings) .

Konstruktor [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) přijímá nepovinné zpětné volání konfigurace, které lze použít k úpravě chování systému. Následující ukázka ukazuje obnovení izolace explicitním voláním metody [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). Ukázka také ukazuje, jak nakonfigurovat systém tak, aby automaticky zašifroval trvalé klíče pomocí rozhraní Windows DPAPI. Pokud adresář odkazuje na sdílenou složku UNC, můžete chtít distribuovat sdílený certifikát napříč všemi relevantními počítači a nakonfigurovat systém tak, aby používal šifrování založené na certifikátech s voláním [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> Instance `DataProtectionProvider` konkrétního typu jsou nákladné pro vytvoření. Pokud aplikace zachovává více instancí tohoto typu a pokud všechny používají stejný adresář úložiště klíčů, může dojít ke snížení výkonu aplikace. Použijete-li `DataProtectionProvider` tento typ, doporučujeme vytvořit tento typ pouze jednou a znovu ho použít co nejvíce. `DataProtectionProvider` Typ a všechny instance [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) , které byly vytvořeny z něj, jsou bezpečné pro přístup z více vláken pro více volajících.
