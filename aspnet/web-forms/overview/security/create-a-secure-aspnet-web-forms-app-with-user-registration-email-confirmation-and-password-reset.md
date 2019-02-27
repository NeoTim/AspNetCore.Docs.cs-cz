---
uid: web-forms/overview/security/create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset
title: Vytvoření zabezpečené aplikace webových formulářů ASP.NET s registrací uživatele, e-mailem potvrzení a resetováním hesla (C#) | Dokumentace Microsoftu
author: Erikre
description: V tomto kurzu se dozvíte, jak vytvořit aplikaci webových formulářů ASP.NET s registrací uživatele, e-mailové potvrzení a resetování hesla pomocí člen ASP.NET Identity...
ms.author: riande
ms.date: 10/02/2014
ms.assetid: 0a8d6044-5fab-4213-82d6-5618d5601358
msc.legacyurl: /web-forms/overview/security/create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset
msc.type: authoredcontent
ms.openlocfilehash: acc13776840408756901e20589b9efacc83ff2a9
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833680"
---
<a name="create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset-c"></a>Vytvoření zabezpečené aplikace webových formulářů ASP.NET s registrací uživatele, potvrzením e-mailu a resetováním hesla (C#)
====================
by [Erik Reitan](https://github.com/Erikre)

> V tomto kurzu se dozvíte, jak vytvořit aplikaci webových formulářů ASP.NET s registrací uživatele, e-mailové potvrzení a resetování hesla pomocí systém členství technologie ASP.NET Identity. Tento kurz je založený na Rick Anderson [kurz ASP.NET MVC](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md).


## <a name="introduction"></a>Úvod

Tento kurz vás provede kroky potřebné k vytvoření aplikace webových formulářů ASP.NET pomocí sady Visual Studio a technologii ASP.NET 4.5 pro vytvoření zabezpečené aplikace webových formulářů s registrací uživatele, e-mailové potvrzení a resetováním hesla.

### <a name="tutorial-tasks-and-information"></a>Kurz úkoly a informace:

- [Vytvoření ASP.NET Web Forms app](#createWebForms)
- [Hook Up SendGrid](#SG)
- [Vyžadovat e-mailové potvrzení před přihlášení](#require)
- [Obnovení hesla a obnovení](#reset)
- [Znovu poslat potvrzovací odkaz e-mailu](#rsend)
- [Řešení potíží s aplikací](#dbg)
- [Další prostředky](#addRes)

<a id="createWebForms"></a>
## <a name="create-an-aspnet-web-forms-app"></a>Vytvoření aplikace ASP.NET Web Forms

Začněte tím, že instalaci a používání [Visual Studio Express 2013 for Web](https://go.microsoft.com/fwlink/?LinkId=299058) nebo [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566). Nainstalujte [Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465) nebo vyšší také.

> [!NOTE]
> Upozornění: Je nutné nainstalovat [Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465) nebo vyšší, k dokončení tohoto kurzu.


1. Vytvoření nového projektu (**souboru**  - &gt; **nový projekt**) a vyberte **webová aplikace ASP.NET** šablony a nejnovější rozhraní .NET Framework verze z **nový projekt** dialogové okno.
2. Z **nový projekt ASP.NET** dialogové okno, vyberte **webových formulářů** šablony. Ponechte výchozí ověřování jako **jednotlivé uživatelské účty**. Pokud chcete hostovat aplikace ve službě Azure, nechte **hostovat v cloudu** zaškrtávací políčko zaškrtnuto.   
 Potom klikněte na **OK** k vytvoření nového projektu.  
    ![Dialogové okno Nový projekt ASP.NET](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image1.png)
3. Povolte vrstvy SSL (Secure Sockets) pro projekt. Postupujte podle kroků uvedených v **povolit šifrování SSL pro projekt** část [Začínáme s webovými formuláři sérii](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms).
4. Spuštění aplikace, klikněte na tlačítko **zaregistrovat** propojit a registraci nového uživatele. V tomto okamžiku je pouze ověření na e-mailu na základě [[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx) atribut zajistit e-mailovou adresu ve správném formátu. Budete upravovat kód pro přidání e-mailové potvrzení. Zavřete okno prohlížeče.
5. V **Průzkumníka serveru** sady Visual Studio (**zobrazení**  - &gt; **Průzkumníka serveru**), přejděte na **Connections\ dat DefaultConnection\Tables\AspNetUsers**, klikněte pravým tlačítkem myši a vyberte **Otevřít definici tabulky**. 

    Na následujícím obrázku `AspNetUsers` schéma tabulky:

    ![Schéma tabulky AspNetUsers](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image2.png)
6. V **Průzkumníka serveru**, klikněte pravým tlačítkem na **AspNetUsers** tabulce a vybrat **zobrazit Data tabulky**.  
  
    ![AspNetUsers tabulkových dat](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image3.png)  
 V tomto okamžiku není potvrzena e-mailu pro registrovaného uživatele.
7. Klikněte na řádek a vyberte Odstranit pro odstranění uživatele. Budete v dalším kroku znovu přidat tuto e-mailu a odešlete zprávu potvrzení e-mailovou adresu.

## <a name="email-confirmation"></a>Potvrzení e-mailu

Je osvědčeným postupem je potvrďte e-mailu při registraci nového uživatele k ověření, nejsou někdo zosobnění (to znamená, že se ještě nezaregistrovali někoho jiného e-mailu). Předpokládejme, že jste měli diskusní fórum, chcete zabránit `"bob@cpandl.com"` registroval jako `"joe@contoso.com"`. Bez potvrzení e-mailu `"joe@contoso.com"` může získat nežádoucí e-mailu vaší aplikace. Předpokládejme, že Bob neúmyslně zaregistrovaný jako `"bib@cpandl.com"` a kdyby si všimli, že nebudou moci používat obnovení hesla, protože aplikace nemá správnou e-mailovou. Potvrzení e-mailu zajišťuje pouze omezenou ochranu před roboty a neposkytuje ochranu z určené odesílateli nevyžádané pošty.

Obvykle chcete novým uživatelům zabránit v účtování žádná data k webu předtím, než byl potvrzen e-mail, ve zprávě SMS nebo jiný mechanismus. V následujících částech budeme povolit e-mailové potvrzení a upravovat kód, který nově zaregistrovaný uživatelům zabránit v přihlašování až do svého e-mailu byl potvrzen. V tomto kurzu použijete e-mailové služby SendGrid.

<a id="SG"></a>
## <a name="hook-up-sendgrid"></a>Připojení SendGrid

SendGrid změnila jeho rozhraní API byla zapsána v tomto kurzu. Aktuální SendGrid pokyny najdete v tématu [SendGrid](http://sendgrid.com/) nebo [povolit obnovení potvrzení a heslo účtu](xref:security/authentication/accconfirm#enable-account-confirmation-and-password-recovery).

Tento kurz vysvětluje pouze přidání e-mailové oznámení prostřednictvím [SendGrid](http://sendgrid.com/), můžete poslat e-mailu pomocí protokolu SMTP a další mechanismy (naleznete v tématu [další prostředky](#addRes)).

1. V sadě Visual Studio, otevřete **Konzola správce balíčků** (**nástroje**  - &gt; **Správce balíčků NuGet**  - &gt; **Konzola správce balíčků**) a zadejte následující příkaz:  
    `Install-Package SendGrid`
2. Přejděte [registrační stránku Azure SendGrid](https://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/) a bezplatná registrace účtu SendGrid. Můžete také zaregistrovat si bezplatný účet SendGrid přímo na [Sendgridu lokality](http://www.sendgrid.com).
3. Z **Průzkumníku řešení** otevřete *IdentityConfig.cs* soubor *aplikace\_Start* složky a přidejte následující kód zvýrazněné žlutou barvou na `EmailService` třída ke konfiguraci **SendGrid**:

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample1.cs?highlight=3,5,8-37)]
4. Přidejte také následující `using` příkazy na začátek *IdentityConfig.cs* souboru: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample2.cs?highlight=1-4)]
5. Pro zjednodušení této ukázku budete ukládat hodnoty účtu služby e-mailu v `appSettings` část *web.config* souboru. Přidejte následující kód XML zvýrazněné žlutou barvou na *web.config* soubor v kořenové složce vašeho projektu:

    [!code-xml[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample3.xml?highlight=2-5)]

    > [!WARNING]
    > Zabezpečení – nikdy ukládání citlivých dat ve zdrojovém kódu. V tomto příkladu účtu a přihlašovací údaje jsou uloženy v **appSetting** část *Web.config* souboru. V Azure, můžete bezpečně uložit tyto hodnoty na **[konfigurovat](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)** karta na portálu Azure portal. Související informace naleznete v tématu Rick Anderson s názvem [osvědčené postupy pro nasazení hesel a dalších citlivých dat do ASP.NET a Azure](https://go.microsoft.com/fwlink/?LinkId=513141).
6. Přidání hodnoty e-mailové služby tak, aby odrážely SendGrid hodnoty ověřování (uživatelské jméno a heslo), abyste mohli úspěšně odeslat e-mailu z vaší aplikace. Nezapomeňte použít název účtu SendGrid spíše než e-mailovou adresu, které jste zadali SendGrid.

### <a name="enable-email-confirmation"></a>Povolení e-mailové potvrzení

 Pokud chcete povolit e-mailové potvrzení, upravíte registrační kód pomocí následujících kroků.  
 

1. V *účet* složku, otevřete *Register.aspx.cs* použití modelu code-behind a aktualizovat `CreateUser_Click` metoda umožňuje následující zvýrazněný změny: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample4.cs?highlight=9-11)]
2. V **Průzkumníka řešení**, klikněte pravým tlačítkem na *Default.aspx* a vyberte **nastavit jako úvodní stránku**.
3. Spusťte aplikaci stisknutím klávesy **F5.** Po zobrazení stránky, klikněte na tlačítko **zaregistrovat** odkaz na stránce registrace.
4. Zadejte váš e-mail a heslo a klikněte **zaregistrovat** tlačítko si pošlete e-mailu přes SendGrid.  
   Aktuální stav projektu a kódu vám umožní uživateli přihlásit po jejich dokončení registrace, i když se nepotvrdily svého účtu.
5. Zkontrolujte e-mailový účet a klikněte na odkaz pro potvrzení e-mailu.  
   Jakmile odešlete registrační formulář se zaznamená.  
    ![Ukázkové webové stránky – přihlášení](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image4.png)

<a id="require"></a>
## <a name="require-email-confirmation-before-log-in"></a>Vyžadovat e-mailové potvrzení před přihlášení

I když zkontrolujete e-mailový účet, v tomto okamžiku by musíte kliknout na odkaz obsažený v ověřovací e-mailu bude plně přihlášené. V následující části upravíte kódu by nový uživatelé předtím, než se přihlásí mít potvrzené e-mailu (ověřit).

1. V **Průzkumníku řešení** sady Visual Studio, aktualizujte `CreateUser_Click` událost v *Register.aspx.cs* obsažené v kódu *účty* složka s následující zvýrazněný změny: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample5.cs?highlight=13-14,17-21)]
2. Aktualizace `LogIn` metodu *Login.aspx.cs* modelu code-behind s následujícími změnami zvýrazněné: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample6.cs?highlight=9-19,45-46)]

### <a name="run-the-application"></a>Spuštění aplikace

 Teď, když implementujete kód pro kontrolu, zda byl potvrzen e-mailovou adresu uživatele můžete zkontrolovat funkce u obou **zaregistrovat** a **přihlášení** stránky. 

1. Odstranit některé účty **AspNetUsers** tabulce, která obsahuje e-mailový alias, který chcete otestovat.
2. Spusťte aplikaci (**F5**) a ověřte nelze zaregistrovat jako uživatel, až se ujistíte e-mailovou adresu.
3. Před potvrzením svůj nový účet, prostřednictvím e-mailu, který byl právě odeslali, pokuste se přihlásit pomocí nového účtu.  
 Zobrazí se vám nedaří se přihlásit a, musíte mít potvrzené e-mailový účet.
4. Jakmile potvrdíte, e-mailová adresa, přihlaste se k aplikaci.

<a id="reset"></a>
## <a name="password-recovery-and-reset"></a>Obnovení hesla a obnovení

1. V sadě Visual Studio odebere znaky komentáře z `Forgot` metoda ve *Forgot.aspx.cs* obsažené v kódu *účet* složku tak, aby metoda se zobrazí jako následující: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample7.cs?highlight=16-18)]
2. Otevřít *Login.aspx* stránky. Nahraďte kód na konci **loginForm** části znázorněno dole: 

    [!code-aspx[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample8.aspx?highlight=52-53)]
3. Otevřít *Login.aspx.cs* použití modelu code-behind a zrušte komentář u následujícího řádku kódu zvýrazněné žlutou barvou z `Page_Load` obslužné rutiny události: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample9.cs?highlight=5)]
4. Spusťte aplikaci stisknutím klávesy **F5.** Po zobrazení stránky, klikněte na tlačítko **přihlášení** odkaz.
5. Klikněte na tlačítko **zapomněli jste heslo?** odkazu zobrazíte **zapomněli jste heslo** stránky.
6. Zadejte svou e-mailovou adresu a klikněte na tlačítko **odeslat** tlačítko si pošlete e-mailu na vaši adresu, která vám umožní obnovit své heslo.   
   Zkontrolujte e-mailový účet a klikněte na odkaz zobrazíte **resetovat heslo** stránky.
7. Na **resetovat heslo** stránky, zadejte e-mailu, heslo a potvrzení hesla. Potom stiskněte klávesu **resetování** tlačítko.  
   Po úspěšném resetování hesla, **změnit heslo** stránka bude zobrazena. Nyní můžete přihlásit pomocí nového hesla.

<a id="rsend"></a>
## <a name="resend-email-confirmation-link"></a>Znovu poslat potvrzovací odkaz e-mailu

Jakmile uživatel vytvoří nový místní účet, jsou e-mailem odkaz pro potvrzení, které je potřeba použít dřív, než se můžou přihlašovat Pokud uživatel omylem odstraní potvrzovací e-mail nebo nikdy přijde e-mailu, potřebují potvrzovací odkaz odeslán znovu. Následující změny kódu ukazují, jak ji povolit.

1. V sadě Visual Studio, otevřete **Login.aspx.cs** použití modelu code-behind a přidejte následující obslužnou rutinu události po `LogIn` obslužné rutiny události:   

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample10.cs)]
2. Upravit `LogIn` obslužné rutině událostí ve *Login.aspx.cs* modelu code-behind změnou kódu zvýrazněné žlutou barvou následujícím způsobem: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample11.cs?highlight=15-17)]
3. Aktualizace *Login.aspx* stránce přidáním kódu zvýrazněné žlutou barvou následujícím způsobem: 

    [!code-aspx[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample12.aspx?highlight=45-46)]
4. Odstranit některé účty **AspNetUsers** tabulce, která obsahuje e-mailový alias, který chcete otestovat.
5. Spusťte aplikaci (**F5**) a registraci e-mailovou adresu.
6. Před potvrzením svůj nový účet, prostřednictvím e-mailu, který byl právě odeslali, pokuste se přihlásit pomocí nového účtu.  
   Zobrazí se vám nedaří se přihlásit a, musíte mít potvrzené e-mailový účet. Kromě toho můžete potvrzovací zpráva teď znovu poslat e-mailový účet.
7. Zadejte e-mailovou adresu a heslo, stiskněte **znovu poslat potvrzovací** tlačítko.
8. Jakmile potvrdíte, e-mailovou adresu, na základě nově odeslaných e-mailové zprávy, přihlaste se k aplikaci.

<a id="dbg"></a>
## <a name="troubleshooting-the-app"></a>Řešení potíží s aplikací

Pokud jste neobdrželi e-mailu s odkazem na ověřit vaše přihlašovací údaje:

- Zkontrolujte složku s nevyžádanou nebo nevyžádané pošty.
- Přihlaste se do účtu SendGrid a klikněte na [odkaz e-mailu aktivita](https://sendgrid.com/logs/index).
- Je potřeba mít jistotu, použít název uživatelského účtu SendGrid jako *Web.config* hodnotu místo e-mailovou adresu účtu SendGrid.

<a id="addRes"></a>
## <a name="additional-resources"></a>Další prostředky

- [Odkazy na ASP.NET Identity doporučené zdroje informací](../../../identity/overview/getting-started/aspnet-identity-recommended-resources.md)
- [Potvrzení účtu a obnovení hesla s ASP.NET Identity](../../../identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity.md)
- [Série kurzů webových formulářů ASP.NET – přidání poskytovatele OAuth 2.0](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#OAuthWebForms)
- [Nasaďte aplikaci zabezpečení rozhraní ASP.NET Web Forms s nástroji Membership, OAuth a SQL Database do služby Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/)
- [Technologie ASP.NET webové formuláře sérii - povolit šifrování SSL pro projekt](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms)
