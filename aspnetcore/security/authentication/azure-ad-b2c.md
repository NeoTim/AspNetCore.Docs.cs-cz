---
title: Cloudové ověřování pomocí Azure Active Directory B2C v ASP.NET Core
author: camsoper
description: Zjistěte, jak nastavit Azure Active Directory B2C ověřování pomocí ASP.NET Core.
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 136fa47788456492a9a7fe6d9d9e5996c13e8c20
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727271"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>Cloudové ověřování pomocí Azure Active Directory B2C v ASP.NET Core

[Soper vačky](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) je cloudové řešení pro správu identit pro webové a mobilní aplikace. Tato služba poskytuje ověřování pro aplikace hostované v cloudu i lokálně. Typy ověřování zahrnují jednotlivé účty, účty sociálních sítí a podnikových účtů federovaných. Kromě toho Azure AD B2C možné poskytovat vícefaktorové ověřování s minimální konfigurací.

> [!TIP]
> Azure Active Directory (Azure AD) a Azure AD B2C jsou nabídky samostatných produktů. Klient služby Azure AD představuje organizace, zatímco tenanta služby Azure AD B2C představuje kolekci identit pro použití s aplikacemi předávajících stran. Další informace najdete v tématu [Azure AD B2C: nejčastější dotazy (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření tenanta Azure Active Directory B2C
> * Registrace aplikace v Azure AD B2C
> * Pomocí sady Visual Studio vytvořit webovou aplikaci ASP.NET Core nakonfigurovanou tak, aby pro ověřování používala klienta Azure AD B2C
> * Konfigurace zásad řídících chování klienta Azure AD B2C

## <a name="prerequisites"></a>Předpoklady

Vyžadují splnění následujících předpokladů pro Tento názorný postup:

* [Předplatné Microsoft Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Vytvoření tenanta Azure Active Directory B2C

Vytvořte tenanta Azure Active Directory B2C [, jak je popsáno v dokumentaci](/azure/active-directory-b2c/active-directory-b2c-get-started). Po zobrazení výzvy přidružení tenanta s předplatným Azure je pro účely tohoto kurzu volitelný.

## <a name="register-the-app-in-azure-ad-b2c"></a>Registrace aplikace v Azure AD B2C

V nově vytvořeném tenantovi Azure AD B2C Zaregistrujte svoji aplikaci pomocí [kroků v dokumentaci](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) v části **zaregistrovat webovou aplikaci** . Zastavte se v části **Vytvoření tajného klíče klienta webové aplikace** . Pro účely tohoto kurzu není nutné tajný kód klienta. 

Použijte následující hodnoty:

| Nastavení                       | Hodnota                     | Poznámky:                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Název**                      | *název aplikace &lt;&gt;*        | Zadejte **název** aplikace, který popíše vaši aplikaci pro uživatele.                                                                                                                                 |
| **Zahrnout webovou aplikaci nebo webové rozhraní API** | Ano                       |                                                                                                                                                                                                    |
| **Povolit implicitní tok**       | Ano                       |                                                                                                                                                                                                    |
| **Adresa URL odpovědi**                 | `https://localhost:44300/signin-oidc` | Adresy URL odpovědí jsou koncové body, kam Azure AD B2C vrací všechny tokeny, které vaše aplikace požaduje. Visual Studio poskytuje adresu URL odpovědi, která se má použít. Prozatím zadejte `https://localhost:44300/signin-oidc` k vyplnění formuláře. |
| **Identifikátor URI ID aplikace**                | Ponechte prázdné               | Pro účely tohoto kurzu není nutné.                                                                                                                                                                    |
| **Zahrnout nativního klienta**     | Ne                        |                                                                                                                                                                                                    |

> [!WARNING]
> Pokud nastavili adresu URL odpovědi, která není localhost, pamatujte na [omezení, co je v seznamu Adresa URL odpovědi povolené](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application). 

Po registraci aplikace se zobrazí seznam aplikací v tenantovi. Vyberte aplikaci, která byla právě zaregistrována. Vyberte ikonu **kopírování** napravo od pole **ID aplikace** a zkopírujte ji do schránky.

V tuto chvíli není možné v tenantovi Azure AD B2C nakonfigurovat nic dalšího, ale okno prohlížeče zůstane otevřené. Po vytvoření ASP.NET Core aplikace je k dispozici více konfigurací.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Vytvoření aplikace ASP.NET Core v aplikaci Visual Studio

Šablony Visual Studio webové aplikace můžete nakonfigurovat pro účely ověření tenanta Azure AD B2C.

V sadě Visual Studio:

1. Vytvořte novou webovou aplikaci ASP.NET Core. 
2. V seznamu šablon vyberte možnost **Webová aplikace** .
3. Vyberte tlačítko **změnit ověřování** .
    
    ![Tlačítko Změnit ověřování](./azure-ad-b2c/_static/changeauth.png)

4. V dialogu **změnit ověřování** vyberte **jednotlivé uživatelské účty**a potom v rozevíracím seznamu vyberte **připojit k existujícímu úložišti uživatelů v cloudu** . 
    
    ![Dialogové okno Změnit ověřování](./azure-ad-b2c/_static/changeauthdialog.png)

5. Vyplňte formulář následujícími hodnotami:
    
    | Nastavení                       | Hodnota                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Název domény**               | *&lt;název domény vašeho tenanta B2C&gt;*          |
    | **ID aplikace**            | *&lt;do schránky vložit ID aplikace&gt;* |
    | **Cesta zpětného volání**             | *&lt;použít výchozí hodnotu&gt;*                       |
    | **Zásady registrace nebo přihlašování** | `B2C_1_SiUpIn`                                        |
    | **Resetovat zásady hesel**     | `B2C_1_SSPR`                                          |
    | **Upravit zásady profilu**       | *&lt;ponechte prázdné&gt;*                                 |
    
    Vyberte odkaz **Kopírovat** vedle **identifikátoru URI odpovědi** pro zkopírování identifikátoru URI odpovědi do schránky. Kliknutím na **tlačítko OK** zavřete dialogové okno **změnit ověřování** . Vyberte **OK** a vytvořte webovou aplikaci.

## <a name="finish-the-b2c-app-registration"></a>Dokončení registrace aplikace B2C

Vraťte se do okna prohlížeče, ve kterém jsou pořád otevřené vlastnosti aplikace B2C. Změňte zadanou **adresu URL dočasné odpovědi** na hodnotu zkopírovanou ze sady Visual Studio. V horní části okna vyberte **Uložit** .

> [!TIP]
> Pokud jste nezkopírovali adresu URL odpovědi, použijte adresu HTTPS z karty ladění ve vlastnostech webového projektu a přidejte hodnotu **CallbackPath** z souboru *appSettings. JSON*.

## <a name="configure-policies"></a>Konfigurace zásad

Pomocí kroků v dokumentaci Azure AD B2C [vytvořte zásadu registrace nebo přihlašování](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)a pak [Vytvořte zásady resetování hesel](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions). Použijte ukázkové hodnoty uvedené v dokumentaci pro **zprostředkovatele identity**, **atributy registrace**a **deklarace aplikací**. Použití tlačítka **Spustit nyní** k otestování zásad, jak je popsáno v dokumentaci, je volitelné.

> [!WARNING]
> Zajistěte, aby názvy zásad byly přesně popsané v dokumentaci, protože se tyto zásady používaly v dialogovém okně **změnit ověřování** v aplikaci Visual Studio. Názvy zásad lze ověřit v souboru *appSettings. JSON*.

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a>Konfigurace základních možností OpenIdConnectOptions/JwtBearer/cookie

Pokud chcete přímo nakonfigurovat základní možnosti, použijte v `Startup.ConfigureServices`odpovídající konstanty schématu:

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a>Spusťte aplikaci

V aplikaci Visual Studio stiskněte klávesu **F5** a sestavte a spusťte aplikaci. Po spuštění webové aplikace vyberte **přijmout** a přijměte použití souborů cookie (Pokud se zobrazí výzva) a pak vyberte **Přihlásit**se.

![Přihlášení k aplikaci](./azure-ad-b2c/_static/signin.png)

Prohlížeč přesměruje na tenanta Azure AD B2C. Přihlaste se pomocí existujícího účtu (Pokud se vytvořila možnost testování zásad), nebo vyberte **zaregistrovat** se a vytvořte nový účet. Odkaz **zapomenuté heslo** slouží k resetování zapomenutého hesla.

![Azure AD B2C přihlášení](./azure-ad-b2c/_static/b2csts.png)

Po úspěšném přihlášení se prohlížeč přesměruje na webovou aplikaci.

![Úspěch](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření tenanta Azure Active Directory B2C
> * Registrace aplikace v Azure AD B2C
> * Použijte Visual Studio k vytvoření ASP.NET Core webové aplikace, která je nakonfigurovaná tak, aby používala klienta Azure AD B2C pro ověřování.
> * Konfigurace zásad řídících chování klienta Azure AD B2C

Teď, když je aplikace ASP.NET Core nakonfigurovaná tak, aby používala Azure AD B2C k ověřování, můžete k zabezpečení vaší aplikace použít [atribut autorizovat](xref:security/authorization/simple) . Pokračujte v vývoji aplikace pomocí učení:

* [Přizpůsobení Azure AD B2Cho uživatelského rozhraní](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Konfigurace požadavků na složitost hesla](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)
* [Povolte službu Multi-Factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Nakonfigurujte další zprostředkovatele identity, jako je [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)a další.
* [Pomocí Graph API Azure AD](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) načtěte Další informace o uživateli, například členství ve skupině, z tenanta Azure AD B2C.
* [Zabezpečte ASP.NET Core webové rozhraní API pomocí Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).
* [Volání webového rozhraní API .NET z webové aplikace .NET pomocí Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).