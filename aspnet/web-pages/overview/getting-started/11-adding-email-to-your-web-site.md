---
uid: web-pages/overview/getting-started/11-adding-email-to-your-web-site
title: Odesílání e-mailu z webového rozhraní ASP.NET stránek webu (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato kapitola vysvětluje postup odesílání automatizovaných e-mailovou zprávu na webové stránce.
ms.author: riande
ms.date: 02/20/2014
ms.assetid: fc49bcb9-f1a9-4048-8c3f-b60951853200
msc.legacyurl: /web-pages/overview/getting-started/11-adding-email-to-your-web-site
msc.type: authoredcontent
ms.openlocfilehash: f388ac1a97bde39cffe6b592b436d7af0d419a5f
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021388"
---
<a name="sending-email-from-an-aspnet-web-pages-razor-site"></a>Odesílání e-mailů z webu rozhraní ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak odeslat e-mailovou zprávu na webové stránce při použití webových stránek ASP.NET (Razor).
> 
> Co se dozvíte:
> 
> - Postup odesílání e-mailovou zprávu ze svého webu.
> - Jak připojit soubor e-mailové zprávě.
> 
> Toto je funkce technologie ASP.NET zavedené v následujícím článku:
> 
> - `WebMail` Pomocné rutiny.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2.


<a id="Sending_Email_Messages"></a>
## <a name="sending-email-messages-from-your-website"></a>Odesílání e-mailové zprávy z webu

Existuje spousta důvodů, proč potřebujete odesílat e-maily z vašeho webu. Může odesílat zprávy potvrzení pro uživatele, nebo můžete odeslat oznámení sami sebe (například, který je zaregistrován nového uživatele.) `WebMail` Pomocné rutiny usnadňuje odeslání e-mailu.

Použít `WebMail` pomocné rutiny, je nutné mít přístup k serveru SMTP. (SMTP jsou zahrnovaného *Simple Mail Transfer Protocol*.) SMTP server je e-mailový server, který pouze předává zprávy na server příjemce &#8212; je straně odchozích e-mailu. Pokud používáte pro svůj web prostřednictvím poskytovatele hostitelských služeb, pravděpodobně nastavené můžete pomocí e-mailu a jejich se dozvíte, co je název vašeho serveru SMTP. Pokud pracujete v podnikové síti, správce nebo oddělení IT může obvykle poskytují informace o serveru SMTP, který vám pomůže. Pokud pracujete v domácnostech, dokonce je možné otestovat pomocí poskytovatele běžné e-mailu, který lze zjistit název serveru SMTP. Obvykle potřebujete:

- Název serveru SMTP.
- Číslo portu. Toto je téměř vždy 25. Však může vyžadovat svého poskytovatele internetových služeb, abyste použili port 587. Pokud používáte e-mailu vrstvu secure Sockets Layer (SSL), můžete potřebovat jiný port. Zeptejte se svého poskytovatele e-mailu.
- Přihlašovací údaje (uživatelské jméno a heslo).

V tomto postupu vytvoříte dvě stránky. První stránka má formulář, který umožňuje uživatelům zadat popis, jako kdyby byly vyplněním formuláře technické podpory. První stránka odešle její informace na druhé stránce. Na druhé stránce kód extrahuje informace o uživateli a odešle e-mailovou zprávu. Také zobrazí se zpráva s potvrzením, že byla přijata hlášení o problému.

![[image]](11-adding-email-to-your-web-site/_static/image1.jpg)

> [!NOTE]
> Pro zjednodušení tento příklad kódu inicializuje `WebMail` pomocné rutiny přímo na stránku, kde používáte. Pro skutečné weby, je však lepší představu vložit soubor globální inicializační kód tímto způsobem tak, aby je inicializovat `WebMail` pomocné rutiny pro všechny soubory ve vašem webu. Další informace najdete v tématu [přizpůsobení chování v celém webu pro webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202906#Setting_Values_For_Helpers).


1. Vytvoření nového webu.
2. Přidejte novou stránku s názvem *EmailRequest.cshtml* a přidejte následující kód: 

    [!code-html[Main](11-adding-email-to-your-web-site/samples/sample1.html)]

    Všimněte si, `action` atribut prvku formuláře je nastavená na *ProcessRequest.cshtml*. To znamená, že se odešle formulář na tuto stránku místo zpět na aktuální stránce.
3. Přidejte novou stránku s názvem *ProcessRequest.cshtml* na web a přidejte následující kód a značky:   

    [!code-cshtml[Main](11-adding-email-to-your-web-site/samples/sample2.cshtml)]

    V kódu získáte hodnoty polí formuláře, které byly odeslány na stránku. Poté je zapotřebí zavolat `WebMail` pomocné rutiny `Send` metodu pro vytvoření a odeslání e-mailové zprávy. Hodnoty pro použití jsou v tomto případě tvořené text, který zřetězí s hodnotami, které byly odeslány z formuláře.

    Kód pro tuto stránku se nachází uvnitř `try/catch` bloku. Pokud pro některou z důvodu pokus o odeslání e-mailu nefunguje (například, nejsou správné nastavení) kódu v `catch` bloku běží a nastaví `errorMessage` proměnné pro chyby, ke které došlo k chybě. (Další informace o `try/catch` bloků nebo `<text>` značku, přečtěte si téma [Úvod do ASP.NET Web Pages programování pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=251587#ID_HandlingErrors).)

    V těle stránky Pokud `errorMessage` proměnné je prázdný (výchozí), uživateli se zobrazí zpráva, že byl odeslán e-mailové zprávy. Pokud `errorMessage` proměnná je nastavená na hodnotu true, který uživatel vidí a zpráva, že došlo k potížím, odeslání zprávy.

    Všimněte si, že v části stránky, která zobrazí chybovou zprávu, je další test: `if(debuggingFlag)`. To je proměnná, která můžete nastavit na hodnotu true, pokud máte potíže, pošle se e-mail. Když `debuggingFlag` má hodnotu true, a pokud je nějaký problém, pošle se e-mail, další chybová zpráva se zobrazí, který zobrazuje cokoli, co ASP.NET ohlásil při pokusu o odeslání e-mailové zprávy. Veletrh upozornění, když: chybové zprávy, které hlásí technologie ASP.NET, pokud ho nelze odeslat e-mailovou zprávu, může být obecný. Například pokud technologie ASP.NET nemůže připojit k serveru SMTP (například, protože jste udělali chybu v názvu serveru), chyba `Failure sending mail`.

    > [!NOTE] 
    > 
    > **Důležité** když se zobrazí chybová zpráva z objektu výjimky (`ex` v kódu), proveďte *není* pravidelně předat prostřednictvím této zprávy pro uživatele. Objekty výjimky často zahrnují informace, které uživatelé neměli vidět a, která můžou být ohrožení zabezpečení. To je důvod, proč tento kód obsahuje proměnnou `debuggingFlag` jako přepínač, který se používá k zobrazení chybové zprávy a proč proměnná ve výchozím nastavení je nastavena na hodnotu false. Měli byste nastavit tuto proměnnou na hodnotu true (a tedy zobrazí chybová zpráva) *pouze* Pokud máte potíže s odesláním e-mailu a je třeba ladit. Po opravě potíží nastavit `debuggingFlag` zpět na hodnotu false.

    Upravte následující související nastavení v kódu e-mailu:

   - Nastavte `your-SMTP-host` na název serveru SMTP, který máte přístup.
   - Nastavte `your-user-name-here` na uživatelské jméno pro účet serveru SMTP.
   - Nastavte `your-account-password` heslo pro účet serveru SMTP.
   - Nastavte `your-email-address-here` vlastní e-mailovou adresu. Toto je e-mailovou adresu, které se zpráva poslala. (Některé poskytovateli e-mailu není vám umožní zadat jiný `From` adresy a používat vaše uživatelské jméno jako `From` adresu.)

     > [!TIP] 
     > 
     > <a id="configuring_email_settings"></a>
     > ### <a name="configuring-email-settings"></a>Konfigurace nastavení e-mailu
     > 
     > Může být někdy obtížné Ujistěte se, že máte správné nastavení pro server SMTP, číslo portu a tak dále. Tady je několik tipů:
     > 
     > - Název serveru SMTP je často něco jako `smtp.provider.com` nebo `smtp.provider.net`. Nicméně pokud publikování webu k poskytovateli hostingu, název serveru SMTP v tomto okamžiku může být `localhost`. Je to proto, že po publikování a vaše lokalita běží na serveru zprostředkovatele, e-mailový server může být místní z hlediska vaší aplikace. Tato změna názvů serverů může znamenat, že budete muset změnit název serveru SMTP jako součást procesu publikování.
     > - Číslo portu je obvykle 25. Ale někteří poskytovatelé vyžadovat použití port 587 nebo některé další porty.
     > - Ujistěte se, že používáte správné přihlašovací údaje. Pokud k poskytovateli hostingu, které jste publikovali vašeho webu, použijte přihlašovací údaje, které zprostředkovatel má výslovně uvedeno, jsou určené pro e-mailu. To může být liší od přihlašovacích údajů, které můžete použít k publikování.
     > - Někdy není nutné přihlašovací údaje vůbec. Pokud chcete odeslat e-mailu pomocí svého osobního poskytovatele internetových služeb, může být vašeho poskytovatele e-mailu už znáte svoje přihlašovací údaje. Po publikování, možná budete muset použít jiná pověření než při testování v místním počítači.
     > - Pokud vašeho poskytovatele e-mailu používá šifrování, je nutné nastavit `WebMail.EnableSsl` k `true`.
4. Spustit *EmailRequest.cshtml* stránku v prohlížeči. (Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.)
5. Zadejte název a popis problému a klikněte **odeslat** tlačítko. Budete přesměrováni na *ProcessRequest.cshtml* stránky, který potvrdí vaši zprávu a které vám pošle e-mailovou zprávu. 

    ![[image]](11-adding-email-to-your-web-site/_static/image2.jpg)

<a id="Sending_a_File"></a>
## <a name="sending-a-file-using-email"></a>Odeslání e-mailem

Můžete také odeslat soubory, které jsou připojené k e-mailové zprávy. V tomto postupu vytvořte textový soubor a dvě stránky HTML. Použijete textový soubor jako přílohu e-mailu.

1. Na webu, přidejte nový textový soubor a pojmenujte ho *MyFile.txt*.
2. Zkopírujte následující text a vložte ho do souboru: 

    `Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.`
3. Vytvoření stránky s názvem *SendFile.cshtml* a přidejte následující kód: 

    [!code-html[Main](11-adding-email-to-your-web-site/samples/sample3.html)]
4. Vytvoření stránky s názvem *ProcessFile.cshtml* a přidejte následující kód: 

    [!code-cshtml[Main](11-adding-email-to-your-web-site/samples/sample4.cshtml)]
5. Upravte následující související nastavení v kódu z příkladu e-mailu:

    - Nastavte `your-SMTP-host` na název serveru SMTP, ke kterým máte přístup.
    - Nastavte `your-user-name-here` na uživatelské jméno pro účet serveru SMTP.
    - Nastavte `your-email-address-here` vlastní e-mailovou adresu. Toto je e-mailovou adresu, které se zpráva poslala.
    - Nastavte `your-account-password` heslo pro účet serveru SMTP.
    - Nastavte `target-email-address-here` vlastní e-mailovou adresu. (Jako předtím by normálně posíláte e-mail někomu jinému, ale pro účely testování, můžete ho odeslat na vás.)
6. Spustit *SendFile.cshtml* stránku v prohlížeči.
7. Zadejte své jméno, řádek předmětu a název textového souboru, připojit (*MyFile.txt*).
8. Klikněte na tlačítko `Submit` tlačítko. Jako dříve, budete přesměrováni na *ProcessFile.cshtml* stránky, který potvrdí vaši zprávu a které vám pošle e-mailovou zprávu s připojený soubor.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky


- [Webové stránky ASP.NET (Razor) – průvodce řešením potíží](https://go.microsoft.com/fwlink/?LinkId=253001)
- [Simple Mail Transfer Protocol](https://msdn.microsoft.com/library/aa480435.aspx)
- [Přizpůsobení chování v celém webu pro webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202906)
