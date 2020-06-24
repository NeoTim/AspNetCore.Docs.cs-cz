---
title: Zabezpečené Blazor aplikace ASP.NET Core serveru
author: guardrex
description: Naučte se zabezpečit Blazor serverové aplikace jako aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: 2811e08fd2f6c66112ffa0bb40f474158f4c7a59
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292682"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Zabezpečené Blazor aplikace ASP.NET Core serveru

Od [Luke Latham](https://github.com/guardrex)

BlazorServerové aplikace jsou konfigurovány pro zabezpečení stejným způsobem jako aplikace ASP.NET Core. Další informace najdete v článcích v části <xref:security/index> . Témata v tomto přehledu se týkají konkrétně Blazor serveru. 

## <a name="blazor-server-project-template"></a>BlazorŠablona projektu serveru

BlazorŠablonu projektu serveru lze nakonfigurovat pro ověřování při vytvoření projektu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pokud <xref:blazor/get-started> chcete vytvořit nový Blazor serverový projekt s ověřovacím mechanismem, postupujte podle pokynů pro Visual Studio v článku.

Po výběru šablony ** Blazor aplikace serveru** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .

Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:

* **Bez ověřování**
* **Jednotlivé uživatelské účty**: uživatelské účty je možné uložit:
  * V aplikaci, která používá [Identity](xref:security/authentication/identity) systém ASP.NET Core.
  * S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Pracovní nebo školní účty**
* **Ověřování systému Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Podle pokynů Visual Studio Code v <xref:blazor/get-started> článku vytvořte nový Blazor serverový projekt s mechanismem ověřování:

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

1. V kroku **Konfigurace nového Blazor serveru aplikace** vyberte v rozevíracím seznamu **ověřování** možnost **individuální ověřování (v aplikaci)** .

1. Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci pomocí ASP.NET Core Identity .

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Podle pokynů .NET Core CLI v <xref:blazor/get-started> článku vytvořte nový Blazor serverový projekt s mechanismem ověřování:

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

Uživatelské rozhraní Identity na Blazor serverový projekt:

* [Bez existující autorizace](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [S autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).
