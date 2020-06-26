---
title: Zabezpečení Blazor Server aplikací ASP.NET Core
author: guardrex
description: Naučte se zabezpečit Blazor Server aplikace jako aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: ab3baad30f78c5d5e2f969b3292d4886fcd0406d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402309"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Zabezpečení Blazor Server aplikací ASP.NET Core

Od [Luke Latham](https://github.com/guardrex)

Blazor Serveraplikace jsou konfigurovány pro zabezpečení stejným způsobem jako aplikace ASP.NET Core. Další informace najdete v článcích v části <xref:security/index> . Témata v tomto přehledu se vztahují konkrétně na Blazor Server . 

## <a name="blazor-server-project-template"></a>Blazor ServerŠablona projektu

Blazor ServerŠablonu projektu lze nakonfigurovat pro ověřování při vytvoření projektu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pokud <xref:blazor/get-started> chcete vytvořit nový Blazor Server projekt s ověřovacím mechanismem, postupujte podle pokynů pro Visual Studio v článku.

Po výběru šablony ** Blazor Server aplikace** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .

Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:

* **Bez ověřování**
* **Jednotlivé uživatelské účty**: uživatelské účty je možné uložit:
  * V aplikaci, která používá [Identity](xref:security/authentication/identity) systém ASP.NET Core.
  * S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Pracovní nebo školní účty**
* **Ověřování systému Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postupujte podle pokynů Visual Studio Code v <xref:blazor/get-started> článku Vytvoření nového Blazor Server projektu s mechanismem ověřování:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Přípustné hodnoty ověřování ( `{AUTHENTICATION}` ) jsou uvedeny v následující tabulce.

| Mechanismus ověřování | Description |
| ------------------------ | ----------- |
| `None`výchozí         | Bez ověřování |
| `Individual`             | Uživatelé uložení v aplikaci pomocí ASP.NET CoreIdentity |
| `IndividualB2C`          | Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Ověřování organizace pro jednoho tenanta |
| `MultiOrg`               | Ověřování organizace pro více tenantů |
| `Windows`                | Ověřování systému Windows |

Pomocí `-o|--output` Možnosti příkaz používá hodnotu poskytnutou pro `{APP NAME}` zástupný text pro:

* Vytvořte složku pro projekt.
* Pojmenujte projekt.

Další informace najdete v tématu [`dotnet new`](/dotnet/core/tools/dotnet-new) Průvodce .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Postupujte podle pokynů Visual Studio pro Mac v <xref:blazor/get-started> článku.

1. V kroku **Konfigurace nového Blazor Server aplikace** vyberte v rozevíracím seznamu **ověřování** možnost **individuální ověřování (v aplikaci)** .

1. Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci pomocí ASP.NET Core Identity .

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Postupujte podle pokynů .NET Core CLI v <xref:blazor/get-started> článku Vytvoření nového Blazor Server projektu s mechanismem ověřování:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Přípustné hodnoty ověřování ( `{AUTHENTICATION}` ) jsou uvedeny v následující tabulce.

| Mechanismus ověřování | Description |
| ------------------------ | ----------- |
| `None`výchozí         | Bez ověřování |
| `Individual`             | Uživatelé uložení v aplikaci pomocí ASP.NET CoreIdentity |
| `IndividualB2C`          | Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Ověřování organizace pro jednoho tenanta |
| `MultiOrg`               | Ověřování organizace pro více tenantů |
| `Windows`                | Ověřování systému Windows |

Pomocí `-o|--output` Možnosti příkaz používá hodnotu poskytnutou pro `{APP NAME}` zástupný text pro:

* Vytvořte složku pro projekt.
* Pojmenujte projekt.

Další informace najdete v tématu [`dotnet new`](/dotnet/core/tools/dotnet-new) Průvodce .NET Core.

---

## <a name="scaffold-identity"></a>GenerováníIdentity

Uživatelské rozhraní Identity do Blazor Server projektu:

* [Bez existující autorizace](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [S autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).
