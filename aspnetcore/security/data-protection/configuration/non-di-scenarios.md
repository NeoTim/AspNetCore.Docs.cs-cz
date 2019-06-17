---
title: Scénáře Nevyužívající injektáž pro ochranu dat v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak podporovat scénáře ochrany dat, kde nemůžete nebo nechcete použít službu poskytuje vkládání závislostí.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 62280a9f911b003383cbe348b9b62942766a2b99
ms.sourcegitcommit: f5762967df3be8b8c868229e679301f2f7954679
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048224"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Scénáře Nevyužívající injektáž pro ochranu dat v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Systém ochrany dat ASP.NET Core je obvykle [služby kontejneru přidá](xref:security/data-protection/consumer-apis/overview) a spotřebovávány závislé součásti prostřednictvím injektáž závislostí (DI). Existují však případy, kdy to není proveditelné nebo žádoucí, zejména v případě, že import systému do stávající aplikace.

Pro podporu těchto scénářů [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) balíček poskytuje konkrétního typu implementujícího typ [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), který nabízí jednoduchý způsob, jak používat ochranu dat bez nutnosti spoléhat se na DI. `DataProtectionProvider` Typ implementuje [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). Vytváření `DataProtectionProvider` vyžaduje pouze poskytnutí [DirectoryInfo](/dotnet/api/system.io.directoryinfo) instance k označení, kde by měla být uložena poskytovatele kryptografických klíčů, jak je znázorněno v následujícím příkladu kódu:

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

Ve výchozím nastavení `DataProtectionProvider` konkrétní typ nešifruje nezpracované klíče před uložením do systému souborů. Toto je pro zajištění podpory scénářů, kdy vývojář odkazuje na sdílené síťové složky a ochrana dat systému se nedá odvodit automaticky mechanismus odpovídající klidové šifrování pomocí klíče.

Kromě toho `DataProtectionProvider` není konkrétní typ [izolování aplikací](xref:security/data-protection/configuration/overview#per-application-isolation) ve výchozím nastavení. Všechny aplikace pomocí stejného klíče adresáře může sdílet několik datových částí pro co nejdelší jejich [účel parametry](xref:security/data-protection/consumer-apis/purpose-strings) shodovat.

[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) konstruktor přijímá volitelné konfigurace zpětného volání, který lze použít k úpravě chování systému. Následující ukázka demonstruje obnovení izolace pomocí explicitní volání konstruktoru [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). Vzorek ukazuje také konfigurace systému k automatickému šifrování trvalý klíče pomocí rozhraní Windows DPAPI. Pokud adresáři odkazuje na sdílenou jednotku UNC, můžete k distribuci certifikát sdílené ve všech příslušných počítačích a ke konfiguraci systému pomocí šifrování na základě certifikátů volání [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> Instance `DataProtectionProvider` konkrétní typ je jejich vytvoření náročné. Pokud aplikace udržuje více instancí tohoto typu, a pokud se pomocí stejného adresáře úložiště klíčů, může dojít ke snížení výkonu aplikace. Pokud používáte `DataProtectionProvider` typ, doporučujeme tento typ je vytvořit jednou a znovu použít co největší míře. `DataProtectionProvider` Typ a všechny [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) instance vytvořených z ní jsou bezpečné pro vlákna pro více volání.
