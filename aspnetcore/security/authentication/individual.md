---
title: Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů
author: rick-anderson
description: Objevte články na základě ASP.NET Core projektů vytvořených pomocí individuálních uživatelských účtů.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 26f53b6452e307bbd0816c1a3604f38b04c6af15
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768647"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů

ASP.NET Core identita je zahrnuta v šablonách projektů v aplikaci Visual Studio s možností jednotlivé uživatelské účty.

Šablony ověřování jsou dostupné v .NET Core CLI s `-au Individual`:

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5833) pro ověřování webového rozhraní API.

<a name="no"></a>

## <a name="no-authentication"></a>Bez ověřování

Ověřování je zadáno v .NET Core CLI s `-au` možností. V aplikaci Visual Studio je k dispozici dialog **Změna ověřování** pro nové webové aplikace. Výchozí pro nové webové aplikace v aplikaci Visual Studio není **ověřování**.

Projekty vytvořené bez ověřování:

* Neobsahujte webové stránky a uživatelské rozhraní pro přihlášení a odhlášení.
* Neobsahujte ověřovací kód.

<a name="win"></a>

## <a name="windows-authentication"></a>Ověřování systému Windows

Ověřování systému Windows je zadáno pro nové webové aplikace v .NET Core CLI s `-au Windows` možností. V aplikaci Visual Studio je v dialogovém okně pro **změnu ověřování** k dispozici možnost **ověřování systému Windows** .

Pokud je vybraná možnost ověřování systému Windows, aplikace je nakonfigurovaná tak, aby používala [modul služby IIS pro ověřování systému Windows](xref:host-and-deploy/iis/modules). Ověřování systému Windows je určeno pro intranetové weby.

## <a name="dotnet-new-webapp-authentication-options"></a>dotnet – nové možnosti ověřování WebApp

V následující tabulce jsou uvedené možnosti ověřování pro nové webové aplikace:

| Možnost | Typ ověřování | Odkaz na další informace |
 | ----------------- | ------------ | ---------- |
| Žádná            |  Bez ověřování | | 
| Jednoho      |  Individuální ověřování | <xref:security/authentication/identity>
| IndividualB2C   |  Individuální ověřování hostované v cloudu s Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| SingleOrg       |  Ověřování organizace pro jednoho tenanta | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  Ověřování organizace pro více tenantů | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Ověřování systému Windows | [Ověřování systému Windows](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Nové možnosti ověřování WebApp sady Visual Studio

V následující tabulce jsou uvedeny možnosti ověřování, které jsou k dispozici při vytváření nové webové aplikace pomocí sady Visual Studio:

| Možnost | Typ ověřování | Odkaz na další informace |
 | ----------------- | ------------ | ---------- |
| Žádná            |  Bez ověřování | | 
| Individuální uživatelské účty/úložiště uživatelských účtů v aplikaci |  Individuální ověřování | <xref:security/authentication/identity> |
| Individuální uživatelské účty/připojení k existujícímu úložišti uživatelů v cloudu |  Individuální ověřování hostované v cloudu s Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| Pracovní nebo školní Cloud/jedna organizace  |  Ověřování organizace pro jednoho tenanta | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Pracovní nebo školní Cloud/několik organizací |  Ověřování organizace pro více tenantů | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Ověřování systému Windows | [Ověřování systému Windows](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>Další zdroje

V následujících článcích se dozvíte, jak používat kód vygenerovaný v ASP.NET Core šablonách, které používají jednotlivé uživatelské účty:

* [Potvrzení účtu a obnovení hesla v ASP.NET Core](xref:security/authentication/accconfirm)
* [Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací](xref:security/authorization/secure-data)
