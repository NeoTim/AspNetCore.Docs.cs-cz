---
title: Ověření cloudu s Azure Active Directory B2C v ASP.NET Core
author: camsoper
description: Zjistěte, jak nastavit ověřování Azure Active Directory B2C s ASP.NET Core.
ms.date: 01/25/2018
ms.custom: mvc
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 2c544475ccd3eb76f2737fec1cf269ac86add372
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098984"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>Ověření cloudu s Azure Active Directory B2C v ASP.NET Core

Podle [kamera Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) je cloudové řešení správy identit pro webové a mobilní aplikace. Tato služba poskytuje ověřování pro aplikace hostované v cloudu i lokálně. Typy ověřování zahrnují jednotlivé účty, účty sociálních sítí a podnikových účtů federovaných. Kromě toho Azure AD B2C umožňuje ověřování službou Multi-Factor Authentication s minimální konfigurací.

> [!TIP]
> Azure Active Directory (Azure AD) a Azure AD B2C jsou nabídky samostatných produktů. Klient služby Azure AD představuje organizace, zatímco tenanta služby Azure AD B2C představuje kolekci identit pro použití s aplikacemi předávajících stran. Další informace najdete v tématu [Azure AD B2C: Nejčastější dotazy (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure Active Directory B2C
> * Registrace aplikace v Azure AD B2C
> * Vytvoření webové aplikace ASP.NET Core umožňují použít tenanta Azure AD B2C k ověřování pomocí sady Visual Studio
> * Konfigurace zásad řízení chování tenanta Azure AD B2C

## <a name="prerequisites"></a>Požadavky

Vyžadují splnění následujících předpokladů pro Tento názorný postup:

* [Předplatné Microsoft Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) (libovolná edice)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Vytvoření tenanta Azure Active Directory B2C

Vytvoření tenanta Azure Active Directory B2C [jak je popsáno v dokumentaci k](/azure/active-directory-b2c/active-directory-b2c-get-started). Po zobrazení výzvy přidružení tenanta s předplatným Azure je pro účely tohoto kurzu volitelný.

## <a name="register-the-app-in-azure-ad-b2c"></a>Zaregistrovat aplikaci v Azure AD B2C

V nově vytvořeného tenanta Azure AD B2C registrovat vaši aplikaci s použitím [kroky v dokumentaci k](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) pod **zaregistrovat webovou aplikaci** oddílu. Zastavení při **vytvořit tajný kód klienta aplikace webového** oddílu. Pro účely tohoto kurzu není nutné tajný kód klienta. 

Použijte následující hodnoty:

| Nastavení                       | Hodnota                     | Poznámky                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Název**                      | *&lt;Název aplikace&gt;*        | Zadejte **název** pro aplikace, který popisuje vaši aplikaci pro zákazníky.                                                                                                                                 |
| **Zahrnout webovou aplikaci / webové rozhraní API** | Ano                       |                                                                                                                                                                                                    |
| **Povolit implicitní tok**       | Ano                       |                                                                                                                                                                                                    |
| **Adresa URL odpovědi**                 | `https://localhost:44300/signin-oidc` | Adresy URL odpovědí jsou koncové body, kam Azure AD B2C vrací všechny tokeny, které vaše aplikace požaduje. Visual Studio poskytuje pomocí adresy URL odpovědi. Teď zadejte `https://localhost:44300/signin-oidc` k vyplnění formuláře. |
| **Identifikátor URI ID aplikace**                | Ponechte prázdné               | Pro účely tohoto kurzu není nutné.                                                                                                                                                                    |
| **Zahrnout nativního klienta**     | Ne                        |                                                                                                                                                                                                    |

> [!WARNING]
> Pokud nastavení adresy URL odpovědi jiných localhost, nezapomínejte [omezení v seznamu adresy URL odpovědi je povolené](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url). 

Po registraci aplikace, zobrazí se seznam aplikací v tenantovi. Vyberte aplikaci, která byla právě zaregistrováno. Vyberte **kopírování** ikony napravo **ID aplikace** pole, které chcete zkopírovat do schránky.

Žádné informace se dá nakonfigurovat v tenantovi Azure AD B2C v tuto chvíli, ale ponechte otevřené okno prohlížeče. Po vytvoření aplikace ASP.NET Core je další konfigurace.

## <a name="create-an-aspnet-core-app-in-visual-studio-2017"></a>Vytvoření aplikace ASP.NET Core v sadě Visual Studio 2017

Šablony Visual Studio webové aplikace můžete nakonfigurovat pro účely ověření tenanta Azure AD B2C.

V sadě Visual Studio:

1. Vytvořte novou webovou aplikaci ASP.NET Core. 
2. Vyberte **webovou aplikaci** ze seznamu šablon.
3. Vyberte **změna ověřování** tlačítko.
    
    ![Tlačítko Změnit ověřování](./azure-ad-b2c/_static/changeauth.png)

4. V **změna ověřování** dialogového okna, vyberte **jednotlivé uživatelské účty**a pak vyberte **připojit k existujícímu úložišti uživatelů v cloudu** v rozevírací nabídce. 
    
    ![Dialogové okno Změnit ověřování](./azure-ad-b2c/_static/changeauthdialog.png)

5. Vyplňte formulář následujícími hodnotami:
    
    | Nastavení                       | Hodnota                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Název domény**               | *&lt;název domény tenanta B2C&gt;*          |
    | **ID aplikace**            | *&lt;Vložte ID aplikace ze schránky&gt;* |
    | **Cestu zpětného volání**             | *&lt;Použijte výchozí hodnotu&gt;*                       |
    | **Zásady registrace / přihlášení** | `B2C_1_SiUpIn`                                        |
    | **Zásady resetování hesla**     | `B2C_1_SSPR`                                          |
    | **Upravit profil zásad**       | *&lt;Ponechte prázdné&gt;*                                 |
    
    Vyberte **kopírování** odkaz **identifikátor URI odpovědi** zkopírujte identifikátor URI odpovědi do schránky. Vyberte **OK** zavřete **změna ověřování** dialogového okna. Vyberte **OK** k vytvoření webové aplikace.

## <a name="finish-the-b2c-app-registration"></a>Dokončit registraci aplikace B2C

Vraťte se do okna prohlížeče s vlastností aplikace B2C stále otevřen. Změnit dočasnou **adresy URL odpovědi** zadaný starší hodnota zkopírovat ze sady Visual Studio. Vyberte **Uložit** v horní části okna.

> [!TIP]
> Pokud nezkopírovali příslušnou odpovědní adresu URL, použijte adresu HTTPS z karty ladění ve vlastnostech projektu webové a připojit **CallbackPath** hodnotu *appsettings.json*.

## <a name="configure-policies"></a>Konfigurace zásad

Použijte postup v dokumentaci k Azure AD B2C do [vytvořit zásadu registrace nebo přihlašování](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy)a potom [vytvořit zásady pro resetování hesla](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy). Použít ukázkové hodnoty uvedeny v dokumentaci pro **zprostředkovatelé Identity**, **atributy registrace**, a **deklarace identit aplikace**. Použití **spustit nyní** tlačítko a otestujte zásady, jak je popsáno v dokumentaci je volitelný.

> [!WARNING]
> Zkontrolujte názvy zásad jsou přesně tak, jak popisuje dokumentace, jak tyto zásady, které byly používány v **změna ověřování** dialogového okna v sadě Visual Studio. Názvy zásad se dá ověřit v *appsettings.json*.

## <a name="run-the-app"></a>Spuštění aplikace

V sadě Visual Studio, stiskněte klávesu **F5** sestavíte a spustíte aplikaci. Po spuštění webové aplikace, vyberte **přijmout** přijmout a použití souborů cookie (Pokud se zobrazí výzva) a pak vyberte **přihlášení**.

![Přihlaste se do aplikace](./azure-ad-b2c/_static/signin.png)

Prohlížeč přesměruje na tenanta Azure AD B2C. Přihlaste se pomocí existujícího účtu (Pokud se jeden vytvořil testování zásad) nebo vyberte **zaregistrujte se hned teď** vytvořte nový účet. **Zapomněli jste heslo?** odkaz se používá k obnovení zapomenutého hesla.

![Přihlášení k Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

Po úspěšném přihlášení, prohlížeč přesměruje do webové aplikace.

![Úspěch](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření tenanta Azure Active Directory B2C
> * Registrace aplikace v Azure AD B2C
> * Vytvoření webové aplikace ASP.NET Core umožňují použít tenanta Azure AD B2C k ověřování pomocí sady Visual Studio
> * Konfigurace zásad řízení chování tenanta Azure AD B2C

Teď, když aplikace ASP.NET Core je nakonfigurován na použití Azure AD B2C k ověřování, [Authorize atribut](xref:security/authorization/simple) umožňuje zabezpečit aplikaci. Pokračujte ve vývoji vaší aplikace tím:

* [Přizpůsobení uživatelského rozhraní Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Konfigurovat požadavky na složitost hesla](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Povolení služby Multi-Factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Nakonfigurovat zprostředkovatelé identity další, jako jsou například [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [na Twitteru ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)a další.
* [Použijte Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) k získání dalších informací o uživatelích, jako je členství ve skupině z tenanta Azure AD B2C.
* [Zabezpečení webového rozhraní API pomocí Azure AD B2C v ASP.NET Core](xref:security/authentication/azure-ad-b2c-webapi).
* [Volání webového rozhraní API .NET z webové aplikace .NET pomocí Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).
