---
uid: identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity
title: Potvrzení účtu a obnovení hesla s ASP.NET Identity (C#) | Dokumentace Microsoftu
author: HaoK
description: Před tímto kurzem, které by se měla Dokončit nejdříve vytvořte zabezpečenou webovou aplikaci ASP.NET MVC 5 s přihlášením, resetovat heslo a potvrzení e-mailu. V tomto kurzu...
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 8d54180d-f826-4df7-b503-7debf5ed9fb3
msc.legacyurl: /identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: 47dc2c1044a5964624ba2f8af4f174a2fd99d3e8
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236403"
---
# <a name="account-confirmation-and-password-recovery-with-aspnet-identity-c"></a>Účet potvrzení a heslo pro obnovení s ASP.NET Identity (C#)

> Před zahájením tohoto kurzu byste měli nejprve dokončit [vytvořit zabezpečenou webovou aplikaci ASP.NET MVC 5 s přihlášením, resetovat heslo a potvrzení e-mailu](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md). Tento kurz obsahuje další podrobnosti a obsahuje pokyny k nastavení e-mailu pro místní účet potvrzení a umožnit uživatelům resetovat zapomenuté heslo v ASP.NET Identity.

Místní uživatelský účet vyžaduje, aby uživatel muset vytvořit heslo pro účet a toto heslo (zabezpečené) uložená ve webové aplikaci. ASP.NET Identity podporuje také účtů na sociálních sítích, které nevyžadují uživateli vytvořit heslo aplikace. [Účtů na sociálních sítích](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) třetích stran (jako jsou Google, Twitter, Facebook nebo Microsoft) použít k ověřování uživatelů. Toto téma obsahuje následující:

- [Vytvoření aplikace ASP.NET MVC](#createMvc) a seznamte se s funkcemi ASP.NET Identity.
- [Ukázka Identity sestavení](#build)
- [Nastavení e-mailové potvrzení](#email)

Novým uživatelům registrovat jejich e-mailový alias, který vytvoří místní účet.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image1.png)

Vyberete tlačítko Registrovat odešle e-mail s potvrzením obsahující ověřovací token do svých e-mailovou adresu.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image2.png)

Uživateli se odešle e-mailu s potvrzovacím tokenem pro svůj účet.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image3.png)

Výběrem odkazu potvrdí účet.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image4.png)

<a id="passwordReset"></a>

## <a name="password-recoveryreset"></a>Obnovení/obnovení hesla

Místní uživatelé zapomenou své heslo může mít token zabezpečení odeslaných e-mailový účet, což jim umožňuje resetovat jejich hesla.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image5.png)  
  
Uživatel bude brzy dostanete e-mail s odkazem, což jim umožní obnovit své heslo.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image6.png)  
Vyberete odkaz přejdou na stránku pro resetování.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image7.png)  
  
Výběr **resetování** bude tlačítko potvrďte heslo se resetovalo.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image8.png)

<a id="createMvc"></a>

## <a name="create-an-aspnet-web-app"></a>Vytvoření webové aplikace ASP.NET

Začněte tím, že instalaci a používání [Visual Studio 2017](https://visualstudio.microsoft.com/).


1. Vytvoření nového projektu ASP.NET Web a vyberte šablonu MVC. Webové formuláře ASP.NET Identity, také podporují tak může podle podobných kroků ve webové aplikaci formulářů.
2. Změnit ověření **jednotlivé uživatelské účty**.
3. Spusťte aplikaci, vyberte **zaregistrovat** propojit a zaregistrovat uživatele. V tomto okamžiku je pouze ověření na e-mailu [[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx) atribut.
4. V Průzkumníku serveru přejděte do **Data Connections\DefaultConnection\Tables\AspNetUsers**klikněte pravým tlačítkem a vyberte **Otevřít definici tabulky**.

    Na následujícím obrázku `AspNetUsers` schématu:

    ![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image9.png)
5. Klikněte pravým tlačítkem na **AspNetUsers** tabulce a vybrat **zobrazit Data tabulky**.  
  
    ![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image10.png)  
  
   V tomto okamžiku není potvrzena e-mailu.

Výchozí úložiště dat pro ASP.NET Identity je Entity Framework, ale můžete je nakonfigurovat pomocí jiných úložišť dat a přidat další pole. Zobrazit [další prostředky](#addRes) oddílu na konci tohoto kurzu.

[Třídy pro spuštění OWIN](../../../aspnet/overview/owin-and-katana/owin-startup-class-detection.md) ( *Startup.cs* ) se volá, když aplikace se spustí a vyvolá `ConfigureAuth` metoda *aplikace\_Start\Startup.Auth.cs*, který nakonfiguruje kanál OWIN a inicializuje ASP.NET Identity. Zkontrolujte `ConfigureAuth` metody. Každý `CreatePerOwinContext` volání zaregistruje zpětné volání (uložené v `OwinContext`), která bude volána jednou za žádost o vytvoření instance zadaného typu. Můžete nastavit zarážky v konstruktoru a `Create` metoda každého typu (`ApplicationDbContext, ApplicationUserManager`) a ověřit, se nazývají s každým požadavkem. Na instanci `ApplicationDbContext` a `ApplicationUserManager` je uložen v kontextu OWIN, který se dá dostat v celé aplikaci. ASP.NET Identity zavěšení do kanálu OWIN prostřednictvím middlewaru souboru cookie. Další informace najdete v tématu [za správu životního cyklu požadavku pro třídu objektu UserManager. v ASP.NET Identity](https://blogs.msdn.com/b/webdev/archive/2014/02/12/per-request-lifetime-management-for-usermanager-class-in-asp-net-identity.aspx).

Pokud změníte profil zabezpečení, je generována a uložené v nové razítko zabezpečení `SecurityStamp` pole *AspNetUsers* tabulky. Všimněte si, `SecurityStamp` pole se liší od soubor cookie zabezpečení. Soubor cookie zabezpečení není uložený v `AspNetUsers` tabulky (nebo kdekoli jinde v Identity DB). Token souboru cookie zabezpečení je podepsaný držitelem, pomocí [DPAPI](https://msdn.microsoft.com/library/system.security.cryptography.protecteddata.aspx) a je vytvořen s `UserId, SecurityStamp` a informace doby vypršení platnosti.

Middlewaru souboru cookie. zkontroluje soubor cookie s každým požadavkem. `SecurityStampValidator` Metodu `Startup` třídy volání databáze a zkontroluje razítko zabezpečení pravidelně uvedené s `validateInterval`. Tato situace nastane pouze každých 30 minut (v našem příkladu) nezměníte svůj profil zabezpečení. Chcete-li minimalizovat cest k databázi bylo zvoleno intervalu 30 minut. V tématu Moje [kurz dvojúrovňové ověřování](index.md) další podrobnosti.

Za komentáře v kódu `UseCookieAuthentication` metoda podporuje ověřování souborů cookie. `SecurityStamp` Pole a přidružený kód poskytuje další vrstvu zabezpečení, které vaše aplikace, pokud změníte svoje heslo, budete přihlášeni mimo prohlížeč, který jste přihlášení. `SecurityStampValidator.OnValidateIdentity` Metoda umožní aplikaci ověřit token zabezpečení při přihlášení uživatele, který se používá, když změníte heslo nebo pomocí externího přihlášení. To je potřeba zajistit, že nejsou zneplatněny žádné tokeny (soubory cookie), vygenerované pomocí starého hesla. Ukázkový projekt změníte, které uživatelské heslo pak nový token je generován pro uživatele, nejsou zneplatněny žádné předchozí tokeny a `SecurityStamp` pole se aktualizuje.

Systém identit lze nakonfigurovat aplikaci tak když se změní na profil zabezpečení uživatelů (například když uživatel změní své heslo nebo změny přidružené přihlášení (například Facebook, Google, účet Microsoft, atd.), je uživatel přihlášen mimo všechny instance prohlížeče. Například následujícím obrázku je [jednotné odhlášení ukázka](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SingleSignOutSample/readme.txt) aplikaci, která umožňuje uživateli Odhlásit všechny instance prohlížeče (v tomto případě IE, Firefox a Chrome) tak, že vyberete jedno tlačítko. Ukázku můžete také umožňuje pouze odhlaste se z instance určitého webového prohlížeče.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image11.png)

[Jednotné odhlášení ukázka](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SingleSignOutSample/readme.txt) aplikace ukazuje, jak ASP.NET Identity umožňuje znovu vygenerovat token zabezpečení. To je potřeba zajistit, že nejsou zneplatněny žádné tokeny (soubory cookie), vygenerované pomocí starého hesla. Tato funkce poskytuje vyšší úroveň zabezpečení, které vaše aplikace; Pokud změníte svoje heslo, které se odhlásíte kde přihlášení do této aplikace.

*Aplikace\_Start\IdentityConfig.cs* soubor obsahuje `ApplicationUserManager`, `EmailService` a `SmsService` třídy. `EmailService` a `SmsService` třídy každý implementují `IIdentityMessageService` rozhraní, takže budete mít v každé třídě konfigurace e-mailu a SMS běžné metody. Tento kurz vysvětluje pouze přidání e-mailové oznámení prostřednictvím [SendGrid](http://sendgrid.com/), můžete poslat e-mailu pomocí protokolu SMTP a další mechanismy.

`Startup` Třída také obsahuje kotle talíře přidání přihlašování přes sociální sítě (Facebook, Twitter, atd.), najdete v mé kurzu [aplikaci MVC 5 s nástroji služby Facebook, Twitter, LinkedIn a Google OAuth2 přihlašování](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) pro další informace.

Zkontrolujte `ApplicationUserManager` třídu, která obsahuje informace o identitě uživatelů a konfiguruje následující funkce:

- Požadavky na sílu hesla.
- Uživatel uzamčení (pokusy a čas).
- Dvoufaktorové ověřování (2FA). Můžu v další kurz zabývat 2FA a SMS.
- Zapojování e-mailu a služby SMS. (I zabývat SMS v jiném kurzu).

`ApplicationUserManager` Třídy je odvozen z obecného `UserManager<ApplicationUser>` třídy. `ApplicationUser` je odvozen od [IdentityUser](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework.identityuser.aspx). `IdentityUser` je odvozen od obecného `IdentityUser` třídy:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample1.cs)]

Výše uvedené vlastnosti shodovat s vlastností v `AspNetUsers` tabulky uvedené výše.

Obecné argumenty na `IUser` umožňují odvození třídy pomocí různých typů pro primární klíč. Zobrazit [ChangePK](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/ChangePK/readme.txt) vzorku, který ukazuje, jak změnit primární klíč z řetězce na int nebo identifikátor GUID.

### <a name="applicationuser"></a>ApplicationUser

`ApplicationUser` (`public class ApplicationUserManager : UserManager<ApplicationUser>`) je definován v *Models\IdentityModels.cs* jako:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample2.cs?highlight=8-9)]

Výše uvedený zvýrazněný kód generuje [ClaimsIdentity](https://msdn.microsoft.com/library/system.security.claims.claimsidentity.aspx). ASP.NET Identity a ověřování souborů Cookie OWIN jsou založené na deklaracích, proto rozhraní framework vyžaduje aplikaci, která vygeneruje `ClaimsIdentity` pro daného uživatele. `ClaimsIdentity` obsahuje informace o všech deklarací identity pro uživatele, jako je například uživatelské jméno, věk a ke kterým rolím uživatel patří. Můžete také přidat další deklarace identity pro uživatele v této fázi.

OWIN `AuthenticationManager.SignIn` metoda předá `ClaimsIdentity` a přihlásí uživatel:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample3.cs?highlight=4-6)]

[Aplikace MVC 5 s Facebook, Twitter, LinkedIn a Google OAuth2 přihlašování](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) ukazuje, jak můžete přidat další vlastnosti `ApplicationUser` třídy.

## <a name="email-confirmation"></a>Potvrzení e-mailu

Je vhodné pro potvrzení e-mailu novému uživateli zaregistrovat k ověření, nejsou zosobnění někdo jiný (to znamená, že se ještě nezaregistrovali někoho jiného e-mailu). Předpokládejme, že jste měli diskusní fórum, chcete zabránit `"bob@example.com"` registroval jako `"joe@contoso.com"`. Bez potvrzení e-mailu `"joe@contoso.com"` může získat nežádoucí e-mailu vaší aplikace. Předpokládejme, že Bob neúmyslně zaregistrovaný jako `"bib@example.com"` a kdyby si všimli, že nebudou moci používat obnovit heslo, protože aplikace nemá správnou e-mailovou. Potvrzení e-mailu zajišťuje pouze omezenou ochranu před roboty a neposkytuje ochranu z určené spammery, mají mnoho pracovní e-mailu aliasů můžete použít k registraci. V následující ukázce uživatel nebude moct změnit svoje heslo, dokud svůj účet potvrzený (podle jejich výběr potvrzovacího odkazu byla přijata na e-mailový účet, která jsou zaregistrována.) Tento pracovní postup můžete použít k ostatním scénářům, třeba odeslání odkazu k potvrzení a k resetování hesla na nové účty vytvořené správcem odesílání e-mailu uživatele při změnily jejich profil a tak dále. Obvykle chcete novým uživatelům zabránit v účtování žádná data k webu předtím, než byly potvrzeny e-mailem, textovou zprávu SMS nebo jiný mechanismus. <a id="build"></a>

## <a name="build-a-more-complete-sample"></a>Ucelenější ukázku sestavení

V této části použijete ke stažení ucelenější ukázku, kterou pak ve spolupráci s NuGet.

1. Vytvořte nový ***prázdný*** webový projekt ASP.NET.
2. V konzole Správce balíčků zadejte následující příkazy: 

    [!code-console[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample4.cmd)]

   V tomto kurzu použijeme [SendGrid](http://sendgrid.com/) k odesílání e-mailu. `Identity.Samples` Balíček nainstaluje budeme pracovat s kódem.
3. Nastavte [projektu pro použití protokolu SSL](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md).
4. Otestujte vytvoření místního účtu spuštěním aplikace, vyberete **zaregistrovat** propojit a účtování registračním formuláři.
5. Vyberte odkaz e-mailu ukázku, což simuluje e-mailové potvrzení.
6. Odebrat je ukázka e-mailu odkaz potvrzovací kód z ukázkového ( `ViewBag.Link` kód v účet kontroleru. Zobrazit `DisplayEmail` a `ForgotPasswordConfirmation` metody akce a zobrazeními razor).

> [!WARNING]
> Pokud změníte některá nastavení zabezpečení v této ukázce, výroby aplikací bude chtít projít auditu zabezpečení, která explicitně volá provedené změny.


## <a name="examine-the-code-in-appstartidentityconfigcs"></a>Zkoumání kódu v aplikaci\_Start\IdentityConfig.cs

Vzorek ukazuje, jak vytvořit účet a přidejte ji tak *správce* role. E-mailu v ukázce by měl nahraďte e-mailu, který budete používat pro účet správce. Nejjednodušší způsob teď chcete vytvořit účet správce, je prostřednictvím kódu programu v `Seed` metody. Věříme, že budete mít v budoucnu nástroj, který vám umožní vytvořit a správy uživatelů a rolí. Vzorový kód vám umožní vytvořit a spravovat uživatele a role, ale nejprve musíte mít účet správce ke spuštění role a stránky pro správu uživatelů. V této ukázce se vytvoří účet správce při nasadí databáze.

Změnit heslo a změňte název na účet, kam budete dostávat e-mailová oznámení.

> [!WARNING]
> Zabezpečení – nikdy ukládání citlivých dat ve zdrojovém kódu.

Jak už bylo zmíněno dříve, `app.CreatePerOwinContext` volání ve třídě spuštění přidá zpětná volání, aby `Create` metoda aplikace databáze obsahu, správce a role správce tříd uživatelů. Volání kanálu OWIN `Create` metodu na tyto třídy pro každý požadavek a uložený kontext pro každou třídu. Kontroler účtů zveřejňuje správce uživatele z kontextu protokolu HTTP (který obsahuje kontext OWIN):

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample5.cs)]

Když se uživatel zaregistruje místní účet, `HTTP Post Register` volání metody:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample6.cs)]

Výše uvedený kód používá model dat k vytvoření nového uživatelského účtu pomocí e-mailu a heslo, které zadáte. Pokud e-mailový alias je v úložišti dat, vytvoření účtu se nepovedlo a znovu se zobrazí formulář. `GenerateEmailConfirmationTokenAsync` Metoda vytvoří zabezpečené potvrzovací token a uloží jej v úložišti dat ASP.NET Identity. [Url.Action](https://msdn.microsoft.com/library/dd505232(v=vs.118).aspx) metoda vytvoří odkaz, který obsahuje `UserId` a potvrzovací token. Tento odkaz se pak pošle e-mailem uživateli, může uživatel vybrat na odkaz v jejich e-mailová aplikace potvrďte svůj účet.

<a id="email"></a>

## <a name="set-up-email-confirmation"></a>Nastavení e-mailové potvrzení

Přejděte [Azure SendGrid registrační stránku](https://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/) a bezplatná registrace účtu. Přidejte kód, podobně jako následující příkaz pro konfiguraci služby SendGrid:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample7.cs?highlight=5)]

> [!NOTE]
> E-mailoví klienti často přijímají pouze textové zprávy (žádné HTML). Byste měli poskytnout zprávu v text nebo HTML. V příkladu SendGrid výše, používá se k tomu `myMessage.Text` a `myMessage.Html` výše uvedeném kódu.


Následující kód ukazuje, jak odeslat e-mailům prostřednictvím [MailMessage](https://msdn.microsoft.com/library/system.net.mail.mailmessage.aspx) třídy where `message.Body` vrátí pouze na odkaz.

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample8.cs)]

> [!WARNING]
> Zabezpečení – nikdy ukládání citlivých dat ve zdrojovém kódu. Účet a přihlašovací údaje jsou uložené v nastavení appSetting. V Azure, můžete bezpečně uložit tyto hodnoty na **[konfigurovat](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)** karta na portálu Azure portal. Zobrazit [osvědčené postupy pro nasazení hesel a dalších citlivých dat do ASP.NET a Azure](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).


Zadání přihlašovacích údajů Sendgridu, spusťte aplikaci, se registrují v e-mailový alias vyberete potvrdit odkaz v e-mailu. Chcete zjistit, jak to můžete udělat pomocí vaší [Outlook.com](http://outlook.com) e-mailový účet, najdete v článku Jan Atten [ C# konfigurace SMTP pro hostitel SMTP Outlook.Com](http://typecastexception.com/post/2013/12/20/C-SMTP-Configuration-for-OutlookCom-SMTP-Host.aspx) a jeho[ASP.NET Identity 2.0: Nastavení ověření účtu a povolení dvoufaktorového](http://typecastexception.com/post/2014/04/20/ASPNET-Identity-20-Setting-Up-Account-Validation-and-Two-Factor-Authorization.aspx) příspěvky.

Když uživatel vybere **zaregistrovat** tlačítko na jejich e-mailovou adresu přijde potvrzovací e-mail obsahující ověřovací token.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image12.png)

Uživateli se odešle e-mailu s potvrzovacím tokenem pro svůj účet.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image13.png)

## <a name="examine-the-code"></a>Prozkoumejte kód

Následující kód ukazuje `POST ForgotPassword` metody.

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample9.cs)]

Metoda se bez upozornění selže, pokud nebyl potvrzen e-mail uživatele. Pokud chyba byla publikována pro neplatná e-mailovou adresu, uživateli se zlými úmysly může pomocí těchto informací najít platné ID (e-mailu aliasy) vůči útokům.

Následující kód ukazuje `ConfirmEmail` metoda ve kontroler účtů, která je volána, když uživatel vybere potvrzovacího odkazu v e-mailu k nim:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample10.cs)]

Jakmile se použil token zapomenutého hesla, je neplatná. Následující změny kódu v `Create` – metoda (v *aplikace\_Start\IdentityConfig.cs* souboru) nastaví tokeny vyprší za 3 hodiny.

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample11.cs?highlight=6-8)]

Pomocí výše uvedený kód zapomenutého hesla a tokenů potvrzení e-mailu vyprší za 3 hodiny. Výchozí hodnota `TokenLifespan` je za jeden den.

Následující kód ukazuje metodu potvrzení e-mailu:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample12.cs)]

 Aby vaše aplikace lépe zabezpečit, podporuje ASP.NET Identity Dvojúrovňového ověřování (2FA). Zobrazit [identitu ASP.NET 2.0: Nastavení ověření účtu a povolení dvoufaktorového](http://typecastexception.com/post/2014/04/20/ASPNET-Identity-20-Setting-Up-Account-Validation-and-Two-Factor-Authorization.aspx) podle Atten Jan. I když uzamčení účtu lze nastavit na selhání pokusů o přihlášení hesel, tento přístup umožňuje vaše přihlašovací údaje náchylné k [DOS](http://en.wikipedia.org/wiki/Denial-of-service_attack) uzamčení. Doporučujeme že použít uzamčení účtu jenom pomocí 2FA.  
<a id="addRes"></a>

## <a name="additional-resources"></a>Další zdroje

- [Přehled poskytovatelů vlastního úložiště pro ASP.NET Identity](../extensibility/overview-of-custom-storage-providers-for-aspnet-identity.md)
- [Aplikace MVC 5 s Facebook, Twitter, LinkedIn a Google OAuth2 přihlašování](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) také ukazuje, jak přidat informace o profilu do tabulky uživatelů.
- [ASP.NET MVC a Identity 2.0: Základy práce s](http://typecastexception.com/post/2014/04/20/ASPNET-MVC-and-Identity-20-Understanding-the-Basics.aspx) podle Atten Jan.
- [Úvod do ASP.NET Identity](../getting-started/introduction-to-aspnet-identity.md)
- [Oznamujeme vydání verze RTM technologie ASP.NET identity 2.0.0](https://blogs.msdn.com/b/webdev/archive/2014/03/20/test-announcing-rtm-of-asp-net-identity-2-0-0.aspx) napsal Pranav Rastogi.
