---
title: Zabezpečení Blazor Server aplikací ASP.NET Core
author: guardrex
description: Naučte se zabezpečit Blazor Server aplikace jako aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: d6d0f6f859dbaef98c6c8a9c53fe9858705cdc0a
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805502"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a>Zabezpečení Blazor Server aplikací ASP.NET Core

Od [Luke Latham](https://github.com/guardrex)

Blazor Server aplikace jsou konfigurovány pro zabezpečení stejným způsobem jako aplikace ASP.NET Core. Další informace najdete v článcích v části <xref:security/index> . Témata v tomto přehledu se vztahují konkrétně na Blazor Server .

## <a name="no-locblazor-server-project-template"></a>Blazor Server Šablona projektu

Blazor ServerŠablonu projektu lze nakonfigurovat pro ověřování při vytvoření projektu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pokud <xref:blazor/tooling> chcete vytvořit nový Blazor Server projekt s ověřovacím mechanismem, postupujte podle pokynů pro Visual Studio v části.

Po výběru šablony ** Blazor Server aplikace** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .

Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:

* **Bez ověřování**
* **Jednotlivé uživatelské účty**: uživatelské účty je možné uložit:
  * V aplikaci, která používá [Identity](xref:security/authentication/identity) systém ASP.NET Core.
  * S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Pracovní nebo školní účty**
* **Ověřování systému Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postupujte podle pokynů Visual Studio Code v části <xref:blazor/tooling> a vytvořte nový Blazor Server projekt s mechanismem ověřování:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Přípustné hodnoty ověřování ( `{AUTHENTICATION}` ) jsou uvedeny v následující tabulce.

| Mechanismus ověřování | Popis |
| ------------------------ | ----------- |
| `None` výchozí         | Bez ověřování |
| `Individual`             | Uživatelé uložení v aplikaci pomocí ASP.NET Core Identity |
| `IndividualB2C`          | Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Ověřování organizace pro jednoho tenanta |
| `MultiOrg`               | Ověřování organizace pro více tenantů |
| `Windows`                | Ověřování systému Windows |

Pomocí `-o|--output` Možnosti příkaz používá hodnotu poskytnutou pro `{APP NAME}` zástupný text pro:

* Vytvořte složku pro projekt.
* Pojmenujte projekt.

Další informace najdete v tématu [`dotnet new`](/dotnet/core/tools/dotnet-new) Průvodce .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Postupujte podle pokynů Visual Studio pro Mac v části <xref:blazor/tooling> .

1. V kroku **Konfigurace nového Blazor Server aplikace** vyberte v rozevíracím seznamu **ověřování** možnost **individuální ověřování (v aplikaci)** .

1. Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci pomocí ASP.NET Core Identity .

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

Blazor ServerPomocí následujícího příkazu v příkazovém prostředí vytvořte nový projekt s mechanismem ověřování:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Přípustné hodnoty ověřování ( `{AUTHENTICATION}` ) jsou uvedeny v následující tabulce.

| Mechanismus ověřování | Popis |
| ------------------------ | ----------- |
| `None` výchozí         | Bez ověřování |
| `Individual`             | Uživatelé uložení v aplikaci pomocí ASP.NET Core Identity |
| `IndividualB2C`          | Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Ověřování organizace pro jednoho tenanta |
| `MultiOrg`               | Ověřování organizace pro více tenantů |
| `Windows`                | Ověřování systému Windows |

Pomocí `-o|--output` Možnosti příkaz používá hodnotu poskytnutou pro `{APP NAME}` zástupný text pro:

* Vytvořte složku pro projekt.
* Pojmenujte projekt.

Další informace najdete tady:

* Podívejte se na [`dotnet new`](/dotnet/core/tools/dotnet-new) příkaz v příručce .NET Core.
* Spusťte příkaz help pro Blazor Server šablonu ( `blazorserver` ) v příkazovém prostředí:

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a>Generování Identity

Uživatelské rozhraní Identity do Blazor Server projektu:

* [Bez existující autorizace](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [S autorizací](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).

## <a name="additional-resources"></a>Další zdroje informací

* [Rychlý Start: Přidání přihlašování s Microsoftem do webové aplikace ASP.NET Core](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [Rychlý Start: ochrana ASP.NET Core webového rozhraní API s platformou Microsoft identity](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
