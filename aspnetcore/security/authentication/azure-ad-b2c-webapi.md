---
title: Ověřování ve webovém rozhraní API pomocí Azure Active Directory B2C v ASP.NET Core
author: camsoper
description: Zjistěte, jak nastavit ověřování Azure Active Directory B2C pomocí webového rozhraní API ASP.NET Core. Otestujte ověření webového rozhraní API pomocí nástroje Postman.
ms.author: casoper
ms.date: 12/05/2019
ms.custom: mvc, seodec18
uid: security/authentication/azure-ad-b2c-webapi
ms.openlocfilehash: 0c7e8a1db924bdedf29468bf6b8cc17f03962c6d
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880744"
---
# <a name="authentication-in-web-apis-with-azure-active-directory-b2c-in-aspnet-core"></a>Ověřování ve webovém rozhraní API pomocí Azure Active Directory B2C v ASP.NET Core

Podle [kamera Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) je cloudové řešení správy identit pro webové a mobilní aplikace. Tato služba poskytuje ověřování pro aplikace hostované v cloudu i lokálně. Typy ověřování zahrnují jednotlivé účty, účty sociálních sítí a podnikových účtů federovaných. Azure AD B2C taky poskytuje ověřování službou Multi-Factor Authentication s minimální konfigurací.

Azure Active Directory (Azure AD) a Azure AD B2C jsou nabídky samostatných produktů. Klient služby Azure AD představuje organizace, zatímco tenanta služby Azure AD B2C představuje kolekci identit pro použití s aplikacemi předávajících stran. Další informace najdete v tématu [Azure AD B2C: Nejčastější dotazy (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

Vzhledem k tomu, že webové rozhraní API bez uživatelského rozhraní, že se nám přesměruje uživatele na zabezpečené token službě, jako je Azure AD B2C. Místo toho rozhraní API je předán nosný token z volající aplikace, které již ověření uživatele v Azure AD B2C. Rozhraní API pak ověří daný token bez zásahu uživatele s přímým přístupem.

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure Active Directory B2C.
> * Registrace webové rozhraní API v Azure AD B2C.
> * Vytvoření webového rozhraní API umožňují použít tenanta Azure AD B2C k ověřování pomocí sady Visual Studio.
> * Konfigurace zásad řízení chování tenanta Azure AD B2C.
> * Pomocí nástroje Postman pro simulaci webovou aplikaci, která se zobrazí dialogové okno přihlášení, načte token a používá jej k nastavení požadavek webového rozhraní API.

## <a name="prerequisites"></a>Požadavky

Vyžadují splnění následujících předpokladů pro Tento názorný postup:

* [Předplatné Microsoft Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [Postman](https://www.getpostman.com/postman)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Vytvoření tenanta Azure Active Directory B2C

Vytvoření tenanta Azure AD B2C [jak je popsáno v dokumentaci k](/azure/active-directory-b2c/active-directory-b2c-get-started). Po zobrazení výzvy přidružení tenanta s předplatným Azure je pro účely tohoto kurzu volitelný.

## <a name="configure-a-sign-up-or-sign-in-policy"></a>Konfigurace zásady registrace nebo přihlašování

Použijte postup v dokumentaci k Azure AD B2C do [vytvořit zásadu registrace nebo přihlašování](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions). Název zásady **SiUpIn**.  Použít ukázkové hodnoty uvedeny v dokumentaci pro **zprostředkovatelé Identity**, **atributy registrace**, a **deklarace identit aplikace**. Použití **spustit nyní** tlačítko k otestování zásady, jak je popsáno v dokumentaci je volitelné.

## <a name="register-the-api-in-azure-ad-b2c"></a>Registrace rozhraní API v Azure AD B2C

V nově vytvořeného tenanta Azure AD B2C, registrace vašeho rozhraní API pomocí [kroky v dokumentaci k](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) pod **registrace webového rozhraní API** oddílu.

Použijte následující hodnoty:

| Nastavení                       | Hodnota               | Poznámky                                                                                  |
|-------------------------------|---------------------|----------------------------------------------------------------------------------------|
| **Jméno**                      | *{Název rozhraní API}*        | Zadejte **název** pro aplikace, který popisuje vaši aplikaci pro zákazníky.                     |
| **Zahrnout webovou aplikaci / webové rozhraní API** | Ano                 |                                                                                        |
| **Povolit implicitní tok**       | Ano                 |                                                                                        |
| **Adresa URL odpovědi**                 | `https://localhost` | Adresy URL odpovědí jsou koncové body, kam Azure AD B2C vrací všechny tokeny, které vaše aplikace požaduje. |
| **Identifikátor URI ID aplikace**                | *api*               | Identifikátor URI není nutné přeložit na fyzickou adresu. Pouze musí být jedinečný.     |
| **Zahrnout nativního klienta**     | Ne                  |                                                                                        |

Jakmile je rozhraní API zaregistrované, zobrazí se seznam aplikací a rozhraní API v tenantovi. Vyberte rozhraní API, který byl dříve zaregistrován. Vyberte **kopírování** ikony napravo **ID aplikace** pole, které chcete zkopírovat do schránky. Vyberte **publikované obory** a ověřte, výchozí *user_impersonation* obor je k dispozici.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Vytvoření aplikace ASP.NET Core v aplikaci Visual Studio

Šablony Visual Studio webové aplikace můžete nakonfigurovat pro účely ověření tenanta Azure AD B2C.

V sadě Visual Studio:

1. Vytvořte novou webovou aplikaci ASP.NET Core. 
2. Vyberte **webového rozhraní API** ze seznamu šablon.
3. Vyberte **změna ověřování** tlačítko.

    ![Tlačítko Změnit ověřování](./azure-ad-b2c-webapi/change-auth-button.png)

4. V **změna ověřování** dialogového okna, vyberte **jednotlivé uživatelské účty** > **připojit k existujícímu úložišti uživatelů v cloudu**.

    ![Dialogové okno Změnit ověřování](./azure-ad-b2c-webapi/change-auth-dialog.png)

5. Vyplňte formulář následujícími hodnotami:

    | Nastavení                       | Hodnota                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Název domény**               | *{název domény tenanta B2C}*                |
    | **ID aplikace**            | *{ID aplikace ze schránky vložte}*       |
    | **Zásady registrace / přihlášení** | B2C_1_SiUpIn                                          |

    Vyberte **OK** zavřete **změna ověřování** dialogového okna. Vyberte **OK** k vytvoření webové aplikace.

Visual Studio vytvoří webové rozhraní API s kontrolerem s názvem *ValuesController.cs* , která vrací pevně definovaných hodnot pro požadavky GET. Třída je označena atributem [autorizovat](xref:security/authorization/simple) , takže všechny požadavky vyžadují ověření.

## <a name="run-the-web-api"></a>Spuštění webového rozhraní API

V sadě Visual Studio spusťte rozhraní API. Visual Studio spustí prohlížeč nasměrovaného na kořenové adresy URL rozhraní API. Poznačte si adresu URL do adresního řádku a nechat rozhraní API, které běží na pozadí.

> [!NOTE]
> Protože neexistuje žádný řadič definované pro kořenovou adresu URL, v prohlížeči může zobrazovat chybu 404 (stránka nebyla nalezena). Toto je očekávané chování.

## <a name="use-postman-to-get-a-token-and-test-the-api"></a>Získat token a otestování rozhraní API pomocí nástroje Postman

[Postman](https://getpostman.com/postman) je nástroj pro testování webového rozhraní API. Pro tento kurz simuluje Postman webové aplikace, který přistupuje k webové rozhraní API jménem uživatele.

### <a name="register-postman-as-a-web-app"></a>Zaregistrujte se jako webová aplikace Postman

Od Postman simuluje webové aplikace, který získává tokeny z tenanta Azure AD B2C, musí být zaregistrované v tenantovi jako webovou aplikaci. Registrovat pomocí nástroje Postman [kroky v dokumentaci k](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) pod **zaregistrovat webovou aplikaci** oddílu. Zastavení při **vytvořit tajný kód klienta aplikace webového** oddílu. Pro účely tohoto kurzu není nutné tajný kód klienta. 

Použijte následující hodnoty:

| Nastavení                       | Hodnota                            | Poznámky                           |
|-------------------------------|----------------------------------|---------------------------------|
| **Jméno**                      | Postman                          |                                 |
| **Zahrnout webovou aplikaci / webové rozhraní API** | Ano                              |                                 |
| **Povolit implicitní tok**       | Ano                              |                                 |
| **Adresa URL odpovědi**                 | `https://getpostman.com/postman` |                                 |
| **Identifikátor URI ID aplikace**                | *{ponechejte prázdné}*                  | Pro účely tohoto kurzu není nutné. |
| **Zahrnout nativního klienta**     | Ne                               |                                 |

Nově zaregistrovaný webové aplikace potřebuje oprávnění pro přístup k webovým rozhraním API jménem uživatele.  

1. Vyberte **Postman** v seznamu aplikací a pak vyberte **přístup přes rozhraní API** z nabídky na levé straně.
1. Vyberte **+ Přidat**.
1. V **vybrat rozhraní API** rozevíracím seznamu vyberte název webového rozhraní API.
1. V **vyberte obory** rozevírací seznam, omdwdatamart všechny obory.
1. Vyberte **OK**.

Poznámka: aplikace Postman ID aplikace, jako je potřeba získat nosný token.

### <a name="create-a-postman-request"></a>Vytvoření žádosti Postman

Spusťte Postman. Ve výchozím nastavení, zobrazí Postman **vytvořit nový** dialogové okno při spuštění. Pokud se zobrazí dialogové okno, vyberte **+ nová** tlačítko v levém horním rohu.

Z **vytvořit nový** dialogové okno:

1. Vyberte **požádat o**.

    ![Tlačítko žádost](./azure-ad-b2c-webapi/postman-create-new.png)

2. Zadejte *získat hodnoty* v **název žádosti** pole.
3. Vyberte **+ vytvořit kolekci** k vytvoření nové kolekce pro ukládání žádosti. Název kolekce *kurzy k ASP.NET Core* a pak vyberte značku zaškrtnutí.

    ![Vytvořit novou kolekci](./azure-ad-b2c-webapi/postman-create-collection.png)

4. Vyberte **uložit kurzy k ASP.NET Core** tlačítko.

### <a name="test-the-web-api-without-authentication"></a>Testování webového rozhraní API bez ověřování

Pokud chcete ověřit, že webové rozhraní API vyžaduje ověření, nejprve vytvoříte žádost o bez ověřování.

1. V **zadejte adresu URL žádosti** zadejte adresu URL pro `ValuesController`. Adresa URL je stejné, jako je zobrazen v prohlížeči s **hodnoty rozhraníapi/** připojí. Například `https://localhost:44375/api/values`.
2. Vyberte **odeslat** tlačítko.
3. Všimněte si, že stav odpovědi je *401 Neautorizováno*.

    ![401 Neautorizováno odpovědi](./azure-ad-b2c-webapi/postman-401-status.png)

> [!IMPORTANT]
> Pokud se zobrazí chyba "nepovedlo se získat odpověď", možná budete muset zakázat ověřování certifikátu SSL v [nastavení po](https://learning.getpostman.com/docs/postman/launching_postman/settings).

### <a name="obtain-a-bearer-token"></a>Získat nosný token

Chcete-li provést ověřený požadavek do webového rozhraní API, je potřeba nosný token. Postman usnadňuje přihlášení k tenantovi Azure AD B2C a získat token.

1. Na **autorizace** kartě **typ** rozevíracího seznamu vyberte **OAuth 2.0**. V **přidat autorizační data** rozevíracího seznamu vyberte **záhlaví požadavku**. Vyberte **získat nový přístupový Token**.

    ![Povolení kartu s nastavením](./azure-ad-b2c-webapi/postman-auth-tab.png)

2. Dokončení **získat nový přístupový TOKEN** dialogové okno následujícím způsobem:

   |                Nastavení                 |                                             Hodnota                                             |                                                                                                                                    Poznámky                                                                                                                                     |
   |----------------------------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |      **Název tokenu**       |                                          *{Název tokenu}*                                       |                                                                                                                   Zadejte popisný název pro daný token.                                                                                                                    |
   |      **Typ udělení**       |                                           Implicitní                                            |                                                                                                                                                                                                                                                                              |
   |     **Adresa URL zpětného volání**      |                                 `https://getpostman.com/postman`                              |                                                                                                                                                                                                                                                                              |
   |       **Ověřovací adresa URL**        | `https://login.microsoftonline.com/{tenant domain name}/oauth2/v2.0/authorize?p=B2C_1_SiUpIn` |  Nahraďte *{název domény tenantu}* s názvem domény vašeho tenanta. **Důležité**: Tato adresa URL musí mít stejný název domény jako co se nachází v `AzureAdB2C.Instance` ve webovém rozhraní API *appsettings.json* souboru. Další informace v POZNÁMCE&dagger;.                                                  |
   |       **ID klienta**       |                *{Zadejte aplikaci Postman **ID aplikace**}*                              |                                                                                                                                                                                                                                                                              |
   |         **Rozsah**         |         `https://{tenant domain name}/{api}/user_impersonation openid offline_access`       | Nahraďte *{název domény tenantu}* s názvem domény vašeho tenanta. Nahraďte *{api}* s identifikátorem URI ID aplikace dáte webového rozhraní API při první registraci (v tomto případě `api`). Vzor adresy URL je: `https://{tenant}.onmicrosoft.com/{api-id-uri}/{scope name}`.         |
   |         **Stav**         |                                      *{ponechejte prázdné}*                                          |                                                                                                                                                                                                                                                                              |
   | **Ověření klienta** |                                Odeslat přihlašovací údaje pro klienta v textu                                |                                                                                                                                                                                                                                                                              |

    > [!NOTE]
    > &dagger; Zobrazí se dialogové okno nastavení zásad, na portálu Azure Active Directory B2C dvě možné adresy URL: jeden ve formátu `https://login.microsoftonline.com/`{název domény tenantu} / {Další informace o cestě} a druhý ve formátu `https://{tenant name}.b2clogin.com/`{název domény tenantu} / {další informace o cestě}. Má **kritické** , součástí domény v `AzureAdB2C.Instance` ve webovém rozhraní API *appsettings.json* soubor odpovídá identifikátoru použitému ve webové aplikaci *appsettings.json* souboru. Jedná se o stejnou doménu, používá pro pole Adresa URL pro ověřování v nástroji Postman. Všimněte si, že Visual Studio používá mírně odlišné formát adresy URL, než co se zobrazí na portálu. Shodují domény, adresa URL funguje.

3. Vyberte **požádat o Token** tlačítko.

4. Postman se otevře nové okno obsahující tenanta Azure AD B2C přihlašovacího dialogového okna. Přihlaste se pomocí existujícího účtu (Pokud se jeden vytvořil testování zásad) nebo vyberte **zaregistrujte se hned teď** vytvořte nový účet. **Zapomněli jste heslo?** odkaz se používá k obnovení zapomenutého hesla.

5. Po úspěšném přihlášení, okno se zavře a **SPRAVOVAT přístupové TOKENY** se zobrazí dialogové okno. Posuňte se dolů dolů, vyberte **použijte Token** tlačítko.

    ![Kde najít tlačítko "Použití Token"](./azure-ad-b2c-webapi/postman-access-token.png)

### <a name="test-the-web-api-with-authentication"></a>Testování webového rozhraní API s ověřováním

Vyberte **odeslat** tlačítko si pošlete žádost znovu. Tato doba je stav odpovědi *200 OK* a datové části JSON je viditelný na odpověď **tělo** kartu.

![Datová část a úspěšné dokončení stav](./azure-ad-b2c-webapi/postman-success.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření tenanta Azure Active Directory B2C.
> * Registrace webové rozhraní API v Azure AD B2C.
> * Vytvoření webového rozhraní API umožňují použít tenanta Azure AD B2C k ověřování pomocí sady Visual Studio.
> * Konfigurace zásad řízení chování tenanta Azure AD B2C.
> * Pomocí nástroje Postman pro simulaci webovou aplikaci, která se zobrazí dialogové okno přihlášení, načte token a používá jej k nastavení požadavek webového rozhraní API.

Pokračujte ve vývoji vašeho rozhraní API tím:

* [Zabezpečení webové aplikace pomocí služby Azure AD B2C v ASP.NET Core](xref:security/authentication/azure-ad-b2c).
* [Volání webového rozhraní API .NET z webové aplikace .NET pomocí Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).
* [Přizpůsobení uživatelského rozhraní Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Konfigurovat požadavky na složitost hesla](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Povolení služby Multi-Factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Nakonfigurovat zprostředkovatelé identity další, jako jsou například [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [na Twitteru ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)a další.
* [Použijte Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) k získání dalších informací o uživatelích, jako je členství ve skupině z tenanta Azure AD B2C.
