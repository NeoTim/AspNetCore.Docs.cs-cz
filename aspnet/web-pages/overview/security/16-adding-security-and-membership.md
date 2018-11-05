---
uid: web-pages/overview/security/16-adding-security-and-membership
title: Přidání zabezpečení a členství v ASP.NET Web stránky webu (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato kapitola se dozvíte, jak zabezpečit svůj web tak, že některé stránky jsou k dispozici pouze na uživatele, kteří přihlášení. (Potěší vás také vytvoření stránky tha...
ms.author: riande
ms.date: 02/24/2014
ms.assetid: 7a77c2c0-deea-4290-a9c3-97958891758e
msc.legacyurl: /web-pages/overview/security/16-adding-security-and-membership
msc.type: authoredcontent
ms.openlocfilehash: 1c36adf23f3b53e4fbf3dbdce7ca85664b32c975
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021596"
---
<a name="adding-security-and-membership-to-an-aspnet-web-pages-razor-site"></a>Přidání zabezpečení a s členstvím na ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak zabezpečit na webu rozhraní ASP.NET Web Pages (Razor) tak, že některé stránky jsou k dispozici pouze na uživatele, kteří přihlášení. (Potěší vás také vytvoření stránky, které všem uživatelům přístup k.)
> 
> **Co se dozvíte:** 
> 
> - Jak vytvořit web, který obsahuje registrační stránku a na přihlašovací stránce tak, aby pro některé stránky můžete omezit přístup pouze členy.
> - Jak vytvořit veřejný a jen pro členské stránky.
> - Jak definovat role, které jsou skupiny, které mají oprávnění různé zabezpečení na webu, a jak přiřadit uživatele k roli.
> - Jak zabránit ve vytváření členské účty automatizovaným programům (robotům) pomocí testu CAPTCHA.
>   
> 
> Toto jsou funkce technologie ASP.NET v následujícím článku:
> 
> - Službou WebMatrix **Starter Site** šablony.
> - `WebSecurity` Pomocné rutiny a `Roles` třídy.
> - `ReCaptcha` Pomocné rutiny.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Rozhraní ASP.NET Web Pages (Razor) 2
> - Služba WebMatrix 3
> - Rozhraní ASP.NET Web Helpers knihovny


Váš web můžete nastavit tak, aby uživatelé můžou přihlásit do něj &#8212; tedy tak, aby lokalita podporuje *členství*. To může být užitečné z mnoha důvodů. Váš web může mít například stránky, které by měla být k dispozici pouze pro členy. V některých případech můžete potřebovat uživatele k přihlášení aby bylo možné odeslat zpětnou vazbu nebo napište komentář.

I v případě, že váš web podporuje členství, uživatelé nebudou nutně vyžadují k přihlášení aby pomocí stránky na webu. Uživatelé, kteří nejsou přihlášeni jsou označovány jako *anonymním uživatelům*.

Uživatel může zaregistrovat na webu a pak přihlásit k webu. Tento web vyžaduje uživatelské jméno (e-mailovou adresu) a heslo, abyste se přesvědčili, že uživatelé koho se. Tento proces přihlášení a potvrdí identitu uživatele se označuje jako *ověřování*.

Můžete nastavit zabezpečení a s členstvím různými způsoby:

- Pokud používáte služby WebMatrix, snadno je vytvořit jako nové lokality na základě **Starter Site** šablony. Tato šablona je již nakonfigurován pro zabezpečení a členství a už obsahuje registrační stránku, přihlašovací stránku a tak dále.

    Web vytvořený pomocí šablony má také možnost umožňuje uživatelům přihlášení pomocí externí web, jako je Facebook, Google nebo Twitter.
- Pokud chcete zvýšit zabezpečení o stávající web, nebo pokud už nechcete používat **Starter Site** šablony, můžete vytvořit vlastní stránce pro registraci, přihlašovací stránku a tak dále.

Tento článek se zaměřuje na první možnost &mdash; přidání zabezpečení pomocí **Starter Site** šablony. Také obsahuje základní informace o tom, jak implementovat vlastní zabezpečení a odkazy na další informace o tom, jak to udělat. Je také informace o tom, jak povolit externí přihlášení, která je popsána podrobněji věnovaný samostatný článek.

## <a name="creating-website-security-using-the-starter-site-template"></a>Zabezpečení vytváření webu pomocí šablony webu Starter

V nástroji WebMatrix, můžete použít **Starter Site** šablony k vytvoření webu, který obsahuje následující:

- Databáze, která se používá k ukládání uživatelských jmen a hesel pro členy.
- Registrační stránku, kde anonymní (nové) uživatelé můžou registrovat.
- Přihlášení a odhlášení stránky.
- Heslo pro obnovení a obnovit stránku.

Následující postup popisuje, jak vytvořit web a nakonfigurujte ho.

1. Spusťte službu WebMatrix a **rychlý Start** stránce **šablony z webu**.
2. Vyberte **Starter Site** šablonu a pak klikněte na tlačítko **OK**. Služba WebMatrix vytvoří novou lokalitu.
3. V levém podokně klikněte **soubory** selektor pracovního prostoru.
4. V kořenové složce vašeho webu, otevřete  *\_AppStart.cshtml* soubor, který představuje zvláštní soubor, který se používá pro jiné globální nastavení. Obsahuje některé příkazy, které jsou označené jako komentář pomocí `//` znaků:

    [!code-csharp[Main](16-adding-security-and-membership/samples/sample1.cs)]

    Konfigurovat tyto příkazy `WebMail` pomocné rutiny, které slouží k odesílání e-mailů. Systém členství můžete použít e-mailu pro odesílání zpráv potvrzení, když uživatelé registrují, nebo ji ke změně hesla. (Například po registraci uživatelé dostanou e-mail, který zahrnuje odkaz, který mohou kliknout, aby bylo možné dokončit proces registrace.)

    Odesílání e-mailů vyžaduje přístup k serveru SMTP, jak je popsáno v [přidání e-mailu na serveru ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202899). Nastavení e-mailu budete ukládat v této centrální  *\_AppStart.cshtml* souboru, takže není nutné opakovaně kódu na každé stránce, která můžete odesílat e-mailu. (Není nutné konfigurovat nastavení SMTP k nastavení registrační databázi, potřebujete jenom nastavení SMTP, pokud chcete ověřit uživatele z jejich e-mailový alias a umožnit uživatelům resetovat zapomenuté heslo.)
5. Zrušením komentáře u příkazy tak, že odeberete `//` from in front of každé z nich.

    Pokud nechcete nastavit e-mailové potvrzení, můžete přeskočit tento krok a na další krok. Pokud nejsou nastavené hodnoty SMTP, je ihned k dispozici bez potvrzovací e-mail nový účet.
6. Upravte následující nastavení související s e-mailu v kódu:

   - Nastavte `WebMail.SmtpServer` na název serveru SMTP, který máte přístup.
   - Ponechte `WebMail.EnableSsl` nastavena na `true`. Toto nastavení chrání vaše přihlašovací údaje, které se odesílají na server SMTP, šifrování je.
   - Nastavte `WebMail.UserName` na uživatelské jméno pro účet serveru SMTP.
   - Nastavte `WebMail.Password` heslo pro účet serveru SMTP.
   - Nastavte `WebMail.From` vlastní e-mailovou adresu. Toto je e-mailovou adresu, které se zpráva poslala.

     > [!NOTE] 
     > 
     > **Tip** Další informace o hodnotách těchto vlastností najdete v tématu [konfigurace nastavení e-mailu](https://go.microsoft.com/fwlink/?LinkID=202906#configuring_email_settings) v [přizpůsobení chování v celém webu pro webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkID=202906).
7. Uložte a zavřete  *\_AppStart.cshtml*.
8. Spustit *stránku Default.cshtml* stránku v prohlížeči.

    ![zabezpečení členství-2](16-adding-security-and-membership/_static/image1.png)

   > [!NOTE]
   > Pokud se zobrazí chyba s oznámením, že vlastnost musí být instance `ExtendedMembershipProvider`, nemusí být web nakonfigurován pro použití systému členství ASP.NET Web Pages (SimpleMembership). V některých případech může dojít v případě poskytovatele hostingu server je nakonfigurován jinak než vaše místní server. Chcete-li to vyřešit, přidejte následující prvek na web *Web.config* souboru:
   > 
   > [!code-xml[Main](16-adding-security-and-membership/samples/sample2.xml)]
   > 
   > Přidejte tento element jako podřízený objekt `<configuration>` elementu a jako partnerské zařízení `<system.web>` elementu.
9. V pravém horním rohu stránky, klikněte **zaregistrovat** odkaz. *Register.cshtml* zobrazí se stránka.
10. Zadejte uživatelské jméno a heslo a potom klikněte na tlačítko **zaregistrovat**.

    ![zabezpečení členství-3](16-adding-security-and-membership/_static/image2.png)

    Při vytvoření webu z **Starter Site** šablony, databázi s názvem *StarterSite.sdf* byl vytvořen na webu *aplikace\_Data* složky. Informace o uživateli se během registrace, přidá do databáze. Pokud jste nastavili hodnoty SMTP, zpráva přijde e-mailovou adresu, že kterou jste použili, můžete dokončit registraci.

    ![zabezpečení členství-4](16-adding-security-and-membership/_static/image3.png)
11. Přejděte do vaší e-mailový program a vyhledejte zprávu, která bude mít potvrzovací kód a hypertextový odkaz na web.
12. Klikněte na odkaz aktivovat svůj účet. Potvrzení hypertextový odkaz, otevře se stránka s potvrzením registrace.

    ![zabezpečení členství-5](16-adding-security-and-membership/_static/image4.png)
13. Klikněte na tlačítko **přihlášení** propojit a pak se přihlaste pomocí účtu, který je zaregistrovaný.

      Po přihlášení **přihlášení** a **zaregistrovat** odkazy jsou nahrazené **odhlášení** odkaz. Vaše přihlašovací jméno se zobrazí jako odkaz. (Tento odkaz vám umožní přejít na stránku, kde můžete změnit heslo.)

      ![zabezpečení členství-6](16-adding-security-and-membership/_static/image5.png)

      > [!NOTE]
      > Ve výchozím nastavení rozhraní ASP.NET web pages odeslání pověření na server ve formátu prostého textu (jako čitelný text). Produkční lokality by měl používat zabezpečený protokol HTTP (https://, označované také jako *SSL* nebo SSL) k šifrování citlivých informací, které se vyměňují se serverem. Můžete požadované e-mailové zprávy k odeslání pomocí protokolu SSL nastavením `WebMail.EnableSsl=true` stejně jako v předchozím příkladu. Další informace o SSL najdete v tématu [zabezpečení komunikace webových: certifikáty SSL a https://](https://go.microsoft.com/fwlink/?LinkId=208660).

## <a name="additional-membership-functionality-in-the-site"></a>Členství v další funkce na webu

Vaše lokalita obsahuje další funkce, které mohou uživatelé spravovat své účty. Uživatelé takto:

- Změňte své heslo. Po přihlášení, klikněte na uživatelské jméno (což je odkaz). To přejdou na stránku, kde můžete vytvořit nové heslo (*Account/ChangePassword.cshtml*).
- Obnovte zapomenuté heslo. Na stránce přihlášení je odkaz (**zapomněli jste heslo?**), která přebírá uživatele na stránku (*Account/ForgotPassword.cshtml*) ve kterém můžete zadat e-mailovou adresu. Web je odešle e-mailovou zprávu, která má odkaz, který mohou kliknout, aby bylo možné nastavit nové heslo (*Account/PasswordReset.cshtml*).

Můžete taky umožnit uživatelům přihlásit se pomocí externí web, může taky, jak je popsáno později.

<a id="Creating_a_Members-Only_Page"></a>
## <a name="creating-a-members-only-page"></a>Vytvoření stránky pouze pro členy

Prozatím, všem uživatelům můžete přejít na libovolné stránce na webu. Můžete chtít mít stránky, které jsou k dispozici pouze na uživatele, kteří se přihlásili, ale (to znamená na členy). Technologie ASP.NET umožňuje vytvářet stránky, které je přístupný pouze členy přihlášeni. Obvykle Pokud anonymní uživatelé se pokusí otevřít stránku pouze pro členy, je přesměrovat je na přihlašovací stránku.

V tomto postupu vytvoříte složku, která bude obsahovat stránky, které jsou k dispozici pouze pro přihlášeného uživatele.

1. V kořenovém adresáři serveru vytvořte novou složku. (Na pásu karet klikněte na šipku pod **nový** a klikněte na tlačítko **novou složku**.)
2. Název nové složky *členy*.
3. Uvnitř *členy* složky, vytvoří novou stránku a s názvem ho *MembersInformation.cshtml*.
4. Nahraďte následující kód a kódu existující obsah:

    [!code-cshtml[Main](16-adding-security-and-membership/samples/sample3.cshtml)]

    Tento kód `IsAuthenticated` vlastnost `WebSecurity` objektu, který vrátí `true` Pokud byl uživatel přihlášen. Pokud uživatel není přihlášen, kód volá `Response.Redirect` odesílat uživatelům *Login.cshtml* stránku *účet* složky.

    Obsahuje adresu URL přesměrování `returnUrl` dotazování řetězcová hodnota, která používá `Request.Url.LocalPath` pro nastavení cesty k aktuální stránce. Pokud jste nastavili `returnUrl` hodnota v řetězci dotazu takto (a pokud návratová adresa URL je místní cesta), přihlašovací stránky se vrátí uživatele na tuto stránku, až po přihlášení.

    Kód také nastaví  *\_SiteLayout.cshtml* stránku jako její rozložení stránky. (Další informace o rozložení stránky najdete v tématu [vytvoření konzistentního rozložení v ASP.NET Web Pages Sites](https://go.microsoft.com/fwlink/?LinkId=202891).)
5. Spuštění tohoto webu. Pokud jste ještě přihlášeni, klikněte na tlačítko **odhlášení** tlačítko v horní části stránky.
6. V prohlížeči, požadavek stránku */členy/MembersInformation*. Například adresa URL může vypadat takto:

    `http://localhost:38366/Members/MembersInformation`

    (Číslo portu (38366) bude pravděpodobně lišit v adrese URL.)

    Budete přesměrováni na *Login.cshtml* stránce, protože nejste přihlášeni.
7. Přihlaste se pomocí účtu, který jste vytvořili dříve. Budete přesměrováni zpět *MembersInformation* stránky. Vzhledem k tomu, že jste přihlášeni, tentokrát uvidíte obsah stránky.

Chcete-li zabezpečit přístup k více stránek, můžete postupujte takto:

- Přidáte kontrolu zabezpečení na každou stránku.
- Vytvoření  *\_PageStart.cshtml* stránku ve složce, kde chráněné ponechat a přidat existuje kontrolu zabezpečení. *\_PageStart.cshtml* stránky funguje jako globální stránky pro všechny stránky ve složce. Tato technika je vysvětleno podrobněji [přizpůsobení chování v celém webu pro webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202906#Using__PageStart.cshtml_to_Restrict_Folder_Access).

## <a name="creating-security-for-groups-of-users-roles"></a>Vytváření zabezpečení pro skupiny uživatelů (role)

Pokud váš web obsahuje mnoho členů, není efektivní ke kontrole oprávnění pro každého uživatele zvlášť před vám mohly zobrazit stránka. Místo toho můžete provést je vytvořit skupiny, nebo *role*, aby jednotlivé členy patřily do. Potom můžete zkontrolovat oprávnění na základě role. V této části vytvoříte &quot;správce&quot; roli a potom vytvořit stránku, který je přístupný uživatelům, kteří jsou v (patřící k) této role.

Systém členství technologie ASP.NET je nastavení k podpoře rolí. Ale na rozdíl od členství registrace a přihlášení **Starter Site** šablona neobsahuje stránky, které vám pomůžou spravovat role. (Správa rolí je úlohou správy spíše než uživatelského úkolu.) Můžete však přidat skupiny přímo v databázi členství ve službě WebMatrix.

1. V nástroji WebMatrix, klikněte na tlačítko **databází** selektor pracovního prostoru.
2. V levém podokně otevřete *StarterSite.sdf* uzel, otevřete **tabulky** uzel a poté dvojitým kliknutím *webové stránky\_role* tabulky.

    ![zabezpečení členství-7](16-adding-security-and-membership/_static/image6.png)
3. Přidejte roli s názvem &quot;správce&quot;. *RoleId* pole se vyplní automaticky. (Primární klíč a je nastavená jako pole určení, jak je vysvětleno v [Úvod k práci s databází na webech ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202893).)
4. Poznamenejte si hodnotu je pro *RoleId* pole. (Pokud je toto první role, které definujete, bude 1.)

    ![zabezpečení členství-8](16-adding-security-and-membership/_static/image7.png)
5. Zavřít *webové stránky\_role* tabulky.
6. Otevřít *UserProfile* tabulky.
7. Poznamenejte si *UserId* hodnotu jedné nebo více uživatelů v tabulce a potom zavřete v tabulce.
8. Otevřít *webové stránky\_UserInRoles* table a zadejte *UserID* a *RoleID* hodnotu do tabulky. Například umístit uživatele 2 do &quot;správce&quot; role, zadali byste tyto hodnoty:

    ![zabezpečení členství-9](16-adding-security-and-membership/_static/image8.png)
9. Zavřít *webové stránky\_UsersInRoles* tabulky.

    Teď, když máte role definována, můžete nakonfigurovat stránku, která je dostupná uživatelům, kteří jsou v této roli.
10. V kořenové složce webu vytvořte novou stránku s názvem *AdminError.cshtml* a nahraďte existující obsah následujícím kódem. To bude na stránku, která uživatelé přesměrovaní na Pokud nejsou povoleny přístup ke stránce.

    [!code-cshtml[Main](16-adding-security-and-membership/samples/sample4.cshtml)]
11. V kořenové složce webu vytvořte novou stránku s názvem *AdminOnly.cshtml* a nahraďte existující kód následujícím kódem:

    [!code-cshtml[Main](16-adding-security-and-membership/samples/sample5.cshtml)]

    `Roles.IsUserInRole` Vrátí metoda `true` zda je aktuální uživatel členem dané roli (v tomto případě roli "admin").
12. Spustit *stránku Default.cshtml* v prohlížeči, ale není přihlášení. (Pokud už jste přihlášení, odhlášení.)
13. Do adresního řádku prohlížeče přidejte *AdminOnly* v adrese URL. (Jinými slovy, žádosti *AdminOnly.cshtml* souboru.) Budete přesměrováni na *AdminError.cshtml* stránce, protože nejste přihlášeni aktuálně jako uživatel v &quot;správce&quot; role.
14. Vraťte se na *stránku Default.cshtml* a přihlaste se jako uživatel, který jste přidali do &quot;správce&quot; role.
15. Přejděte do *AdminOnly.cshtml* stránky. Tentokrát uvidíte stránku.

## <a name="preventing-automated-programs-from-joining-your-website"></a>Automatizovaným programům brání v připojení k webu

Na stránce přihlášení nezpůsobí ukončení automatizovaným programům (někdy označovány jako *webové roboty* nebo *robotů*) z registrace s vaším webem. Tento postup popisuje, jak povolit nástroje ReCaptcha testu na stránce registrace.

![/media/38777/ch16securitymembership-18.jpg](16-adding-security-and-membership/_static/image1.jpg)

1. Zaregistrujte svůj web najdete tady ReCaptcha.Net ([http://recaptcha.net](http://recaptcha.net)). Po dokončení registrace, zobrazí se veřejný klíč a soukromý klíč.
2. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste tak již neučinili.
3. V *účet* složku, otevřete soubor s názvem *Register.cshtml*.
4. V kódu v horní části stránky, vyhledejte následující řádky a Odkomentujte odebráním `//` znaky komentáře:

    [!code-csharp[Main](16-adding-security-and-membership/samples/sample6.cs)]
5. Nahraďte `PRIVATE_KEY` pomocí vlastního soukromého klíče nástroje ReCaptcha.
6. V kódu stránky, odeberte `@*` a `*@` přinesly znaky z kolem následující řádky v kódu stránky:

    [!code-cshtml[Main](16-adding-security-and-membership/samples/sample7.cshtml)]
7. Nahraďte `PUBLIC_KEY` vaším klíčem.
8. Pokud už ho nebyly odebrány, odeberte `<div>` element, který obsahuje text, který začíná na "Povolit ověřování CAPTCHA...". (Odebrat celý `<div>` elementu a jeho obsah.)

9. Spustit *stránku Default.cshtml* v prohlížeči. Pokud jste přihlášeni webu, klikněte na tlačítko **odhlášení** odkaz.
10. Klikněte na tlačítko **zaregistrovat** propojit a otestovat registrace pomocí testu CAPTCHA.

     ![zabezpečení – členství-10](16-adding-security-and-membership/_static/image9.png)

Další informace o `ReCaptcha` pomocné rutiny, najdete v článku [pomocí CATPCHA zabránit automatické programům (robotům) z pomocí technologie ASP.NET webu](https://go.microsoft.com/fwlink/?LinkId=251967).

<a id="Additional_Resources"></a>
## <a name="letting-users-log-in-using-an-external-site"></a>Jak uživatelům umožnit přihlásit pomocí externí web

**Starter Site** šablona obsahuje kód a kód, který umožňuje uživatelům přihlášení pomocí Facebooku, Windows Live, Twitter, Google a Yahoo. Ve výchozím nastavení tato funkce není povolená. Obecný postup pro použití s informacemi uživatelům přihlásit se pomocí těchto externích poskytovatelů, je to:

- Rozhodněte, které z externí weby chcete podporovat.
- V případě potřeby, přejděte k dané lokalitě a nastavit aplikaci přihlášení. (Například máte k tomu, aby bylo možné povolit přihlášení k Facebooku.)
- Ve vaší lokalitě nakonfigurujte poskytovatele. Ve většině případů stačí zrušit komentář nějaký kód ve službě  *\_AppStart.cshtml* souboru.
- Přidání značek na registrační stránku, která umožní uživatelům odkaz na externí web pro přihlášení. Obvykle můžete zkopírovat kód potřebujete a o něco změní celý text.

Podrobné pokyny najdete v tématu [povolení přihlášení z externích webů na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251969).

Po přihlášení uživatele z jiné lokality se uživatel vrátí do vaší lokality a *přidruží* tohoto přihlášení s webem. V důsledku toho tím se vytvoří záznam členství na vašem webu pro externí přihlášení uživatele. To vám umožní používat normální zařízení členství (například role) s externí přihlášení.

## <a name="adding-security-to-an-existing-website"></a>Přidání zabezpečení na existující web

Postup dříve v tomto článku se spoléhá na použití **Starter Site** šablony jako základ pro zabezpečení webu. Pokud není praktické pro spuštění z **Starter Site** šablony nebo zkopírovat na příslušných stránkách webu na základě této šablony, můžete implementovat stejný typ zabezpečení na vlastním webu kódu sami. Vytvoření stejné typy stránek – registrace, přihlášení a tak dále a pak použít třídy a pomocné rutiny k nastavení členství.

Základní proces je popsán v blogovém příspěvku [nejzákladnější možnost pro implementaci zabezpečení syntaxe Razor rozhraní ASP.NET](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2240). Většina práce se provádí pomocí následujících metod a vlastností `WebSecurity` pomocné rutiny:

- [WebSecurty.UserExists](https://msdn.microsoft.com/library/webmatrix.webdata.websecurity.userexists(v=vs.99).aspx), [WebSecurity.CreateUserAndAccount](https://msdn.microsoft.com/library/webmatrix.webdata.websecurity.createuserandaccount(v=vs.99).aspx). Tyto metody umožňují určit, jestli uživatel je už zaregistrované a jejich registrace.
- [WebSecurty.IsAuthenticated](https://msdn.microsoft.com/library/webmatrix.webdata.websecurity.isauthenticated(v=vs.99).aspx). Tato vlastnost umožňuje určit, zda je aktuální uživatel přihlášen. To je užitečné přesměrovat uživatele na přihlašovací stránku, pokud již uživatel není přihlášený.
- [WebSecurity.Login](https://msdn.microsoft.com/library/webmatrix.webdata.websecurity.login(v=vs.99).aspx), [WebSecurity.Logout](https://msdn.microsoft.com/library/webmatrix.webdata.websecurity.logout(v=vs.99).aspx). Tyto metody se uživatel přihlásil, snížení nebo navýšení kapacity.
- [WebSecurity.CurrentUserName](https://msdn.microsoft.com/library/webmatrix.webdata.websecurity.currentusername(v=vs.99).aspx). Tato vlastnost je užitečná pro zobrazení jméno přihlášeného uživatele (Pokud je uživatel přihlášen).
- [WebSecurity.ConfirmAccount](https://msdn.microsoft.com/library/gg569286(v=vs.99).aspx). Tato metoda je užitečná, pokud jste nastavili pro registraci e-mailové potvrzení. (Podrobnosti jsou popsány v blogovém příspěvku [pomocí funkce potvrzení pro zabezpečení rozhraní ASP.NET Web Pages](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2267).)

Ke správě rolí, můžete použít [role](https://msdn.microsoft.com/library/gg538398(v=vs.99).aspx) a [členství](https://msdn.microsoft.com/library/gg569035(v=vs.99).aspx) třídy, jak je popsáno v příspěvku na blogu.

## <a name="additional-resources"></a>Další prostředky

- [Přizpůsobení chování v celém webu](https://go.microsoft.com/fwlink/?LinkId=202906)
- [Zabezpečení komunikace webových: Certifikáty SSL a https://](https://go.microsoft.com/fwlink/?LinkId=208660)
- [Nejzákladnější možnost pro implementaci zabezpečení syntaxe Razor rozhraní ASP.NET](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2240) a [pomocí funkce potvrzení pro zabezpečení rozhraní ASP.NET Web Pages](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2267). Toto jsou blogové příspěvky, které popisují, jak implementovat bez použití funkcí členství technologie ASP.NET **Starter Site** šablony.
- [Povolení přihlášení z externích webů na webu s webovými stránkami ASP.NET](https://go.microsoft.com/fwlink/?LinkId=251969)
- [Reference k rozhraní API třídy metodu WebSecurity](https://msdn.microsoft.com/library/webmatrix.webdata.websecurity(v=vs.99)) (MSDN)
- [Reference k rozhraní API třídy SimpleRoleProvider](https://msdn.microsoft.com/library/webmatrix.webdata.simpleroleprovider(v=vs.99)) (MSDN)
- [Reference k rozhraní API třídy SimpleMembershipProvider](https://msdn.microsoft.com/library/webmatrix.webdata.simplemembershipprovider(v=vs.99)) (MSDN)
