---
uid: web-pages/overview/testing-and-debugging/aspnet-web-pages-razor-troubleshooting-guide
title: ASP.NET – webové stránky příručky pro řešení potíží (Razor) | Dokumentace Microsoftu
author: tfitzmac
description: Tento článek popisuje problémy, které můžete mít při práci s webových stránek ASP.NET (Razor) a některé doporučené řešení. Verze softwaru Úvo ASP.NET Web...
ms.author: riande
ms.date: 02/10/2014
ms.assetid: 2a2c1833-0bfe-4e2e-9cc0-341b52c7b121
msc.legacyurl: /web-pages/overview/testing-and-debugging/aspnet-web-pages-razor-troubleshooting-guide
msc.type: authoredcontent
ms.openlocfilehash: c27139a720decd34a4ab89e6f93e71c97d123b45
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "41752607"
---
<a name="aspnet-web-pages-razor-troubleshooting-guide"></a>ASP.NET – webové stránky příručky pro řešení potíží (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje problémy, které můžete mít při práci s webových stránek ASP.NET (Razor) a některé doporučené řešení.
> 
> ## <a name="software-versions"></a>Verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2 a ASP.NET Web Pages 1.0.


Toto téma obsahuje následující oddíly:

- [Problémy se spouštěním stránky](#Issues_Running_.cshtml_Pages)
- [Problémy s kódem Razor](#IssuesWithRazorCode)
- [Problémy se zabezpečením a členství](#membership)
- [Problémy s odesláním e-mailu](#email)
- [Další prostředky](#AdditionalResources)

Obecné dotazy najdete v tématu [webových stránek ASP.NET (Razor) – nejčastější dotazy](https://go.microsoft.com/fwlink/?LinkId=253000).

<a id="Issues_Running_.cshtml_Pages"></a>
## <a name="issues-with-running-pages"></a>Problémy se spouštěním stránky

Celou řadou potíží může zabránit *.cshtml* a *.vbhtml* stránky ve správném spuštění. Tato část uvádí běžné chybové zprávy a pravděpodobně způsobí, že.

### <a name="http-error-403---forbidden-access-is-denied"></a>Chyba protokolu HTTP 403 – Zakázáno: Přístup byl odepřen.

*Nemáte oprávnění k zobrazení tohoto adresáře nebo stránky pomocí zadaných přihlašovacích údajů.*

K této chybě může dojít, pokud server není spuštěn správnou verzi rozhraní .NET Framework. Ujistěte se, že má počítač, na kterém běží server (místně nebo vzdáleně) alespoň nainstalované rozhraní .NET Framework 4. Také se ujistěte, že samotná aplikace je nakonfigurován ke spuštění správnou verzi.

Pokud tento problém se zobrazí místně při práci v nástroji WebMatrix, klikněte na tlačítko **lokality** prostoru a ve stromovém zobrazení klikněte na **nastavení**. V **vyberte verzi rozhraní .NET Framework** vyberte **.NET 4 (integrovaný režim)**. Pokud tato verze je již nastavena, zkuste spustit službu WebMatrix jako správce.

Ujistěte se, že kořenový web má alespoň jednu *.cshtml* soubor v ní.

Pokud tato chyba se zobrazí, když je webový server na vzdáleném serveru, obraťte se na správce serveru. Ověřte, že server má rozhraní .NET Framework 4 nebo novější. Také se ujistěte, že aplikace běží ve fondu aplikací, který je nakonfigurovaný na tuto verzi rozhraní.NET Framework.

Pokud budete mít kontrolu nad serveru, ujistěte se, že běží správnou verzi rozhraní .NET Framework. Můžete se taky může snažit opravíte spuštěním instalace `aspnet_regiis -iru` příkazu. (Například, pokud nainstalujete službu IIS po instalaci rozhraní .NET Framework, služby IIS nebudě správně nakonfigurovaná pro spuštění stránky technologie ASP.NET.) Další informace najdete v tématu [ASP.NET IIS Registrační nástroj (Aspnet\_regiis.exe)](https://msdn.microsoft.com/library/k6h9cz8h(v=vs.100).aspx).

### <a name="http-error-40314---forbidden"></a>Chyba protokolu HTTP 403.14 – zakázáno

*Webový server je nakonfigurován na obsah tohoto adresáře v seznamu.*

K této chybě může dojít, pokud si vyžádáte prostředek, který je chráněný (stejně jako *Web.config* soubor) nebo který je ve složce, která je chráněná (jako *aplikace\_Data* nebo *aplikace\_Kód*).

### <a name="http-error-40417---not-found"></a>Chyba protokolu HTTP 404.17 – Nenalezeno

*Požadovaný obsah se zdá být skript a nebude obsluhuje statický soubor obslužnou rutinou.*

K této chybě může dojít, pokud server není správně nakonfigurovaný pro použití rozhraní .NET Framework 4 nebo novější a proto nemůže rozpoznat kód v `@{ }` bloky. Naleznete v popisu výše pro *HTTP Chyba 403 – Zakázáno: přístup byl odepřen*.

### <a name="http-error-4047---not-found"></a>Chyba protokolu HTTP 404.7 – nebyl nalezen

*Modul filtrování žádostí je konfigurován k odepření přípona souboru*

K této chybě může dojít, pokud *.cshtml* nebo *.vbhtml* rozšíření byl explicitně zakázán na serveru. Příznaky tohoto problému je, které pracují adresy URL, pokud neobsahují rozšíření, ale adresy URL, které zahrnují *.cshtml* nebo *.vbhtml* nefungují. Opětovné povolení rozšíření na webu je možné řešení *Web.config* souboru. Následující příklad ukazuje, jak povolit *.cshtml* rozšíření.

[!code-xml[Main](aspnet-web-pages-razor-troubleshooting-guide/samples/sample1.xml?highlight=5-6)]

### <a name="http-error-4048---not-found"></a>Chyba protokolu HTTP 404.8 – nebyl nalezen

*Modul filtrování žádostí je konfigurován k odepření cestě v adrese URL, která obsahuje oddíl hiddenSegment.*

K této chybě může dojít, pokud si vyžádáte prostředek, který je chráněný (stejně jako *Web.config* soubor) nebo který je ve složce, která je chráněná (jako *aplikace\_Data* nebo *aplikace\_Kód*).

### <a name="this-type-of-page-is-not-served-server-error-in--application"></a>Tento typ stránky není poskytováni (Chyba serveru v aplikaci "/")

Naleznete v popisu výše pro HTTP 404.17 chyby.

<a id="IssuesWithRazorCode"></a>
## <a name="issues-with-razor-code"></a>Problémy s kódem Razor

### <a name="the-name-class-does-not-exist-in-the-current-context"></a>Název "*třídy*' neexistuje v aktuálním kontextu

Často, důvod se zobrazí tato chyba je, že `class` odkazy pomocné rutiny, ale pomocné rutiny není nainstalována. Například pokud se pokusíte použít pomocné rutiny, ale pokud jste nenainstalovali balíček z Nugetu, zobrazí se vám tato chyba. Pomocí Galerie ve službě WebMatrix můžete najít a nainstalovat pomocné rutiny.

Pokud je nainstalovaná pomocné rutiny, ale stránce stále nerozpoznal ho, vyzkoušejte přidávání přidat `using` příkaz kódu. V `using` prohlášení, odkaz na obor názvů, který obsahuje pomocné rutiny. Například základní pomocné rutiny, které jsou součástí balíčku ASP.NET Web Helpers jsou v `System.Web.Helpers` oboru názvů. V horní části stránky, kde chcete použít pomocné rutiny přidejte následující řádek:

`@using Microsoft.Web.Helpers;`

<a id="membership"></a>
## <a name="issues-with-security-and-membership"></a>Problémy se zabezpečením a členství

Pokud používáte systém integrované zabezpečení (členství) na webových stránkách ASP.NET (Razor), může dojít k následujícím potížím.

### <a name="to-call-this-method-the-membershipprovider-property-must-be-an-instance-of-extendedmembershipprovider"></a>Vlastnost "Membership.Provider" mohli volat tuto metodu, musí být instance "ExtendedMembershipProvider"

Tuto chybu můžete určit, že žádné `AspNetSqlMembershipProvider` třídy je nakonfigurované. (Příznakem je, že lokality funguje místně, ale vyvolá tuto chybu, když ji publikujete do serveru poskytovatele hostingu.) Jednu opravu tohoto problému je explicitně povolit jednoduché členství přidáním následujícího kódu na web *Web.config* souboru:

[!code-xml[Main](aspnet-web-pages-razor-troubleshooting-guide/samples/sample2.xml?highlight=6)]

<a id="email"></a>
## <a name="issues-with-sending-email"></a>Problémy s odesláním e-mailu

Chcete-li ladit může být náročné problémů s odesíláním e-mailu. Počáteční problém může být, že se nemůže připojit k serveru SMTP. Pokud je připojení úspěšné, ASP.NET předá zprávy na server SMTP. Může však být problémy s vlastní zprávě, který zabraňuje odeslání na server SMTP.

Pokud vaše aplikace neodesílá úspěšně e-mailu, zkuste následující:

- Název serveru SMTP je často něco jako `smtp.provider.com` nebo `smtp.provider.net`. Nicméně pokud publikování webu k poskytovateli hostingu, název serveru SMTP v tomto okamžiku může být `localhost`. K této situaci dochází, protože po publikování a vaše lokalita běží na serveru zprostředkovatele, může být místní z pohledu aplikace serveru SMTP. Tato změna názvů serverů může znamenat, že budete muset změnit název serveru SMTP jako součást procesu publikování.
- Číslo portu je obvykle 25. Ale někteří poskytovatelé vyžadovat použití port 587 nebo některé další porty. Obraťte se na vlastníka SMTP server, jaké číslo portu očekávané použití.
- Ujistěte se, že používáte správné přihlašovací údaje. Pokud k poskytovateli hostingu, které jste publikovali vašeho webu, použijte přihlašovací údaje, které zprostředkovatel má výslovně uvedeno, jsou určené pro e-mailu. Tyto přihlašovací údaje se může lišit od přihlašovací údaje, které můžete použít k publikování.
- Někdy není nutné přihlašovací údaje vůbec. Pokud odesíláte e-mailu pomocí svého osobního poskytovatele internetových služeb, může být vašeho poskytovatele e-mailu už znáte svoje přihlašovací údaje. Po publikování, možná budete muset použít jiná pověření než při testování v místním počítači.
- Pokud vašeho poskytovatele e-mailu používá šifrování, nastavit `WebMail.EnableSsl` k `true`.

Pokud dojde k chybě odesílání e-mailů, může se zobrazit standardní technologie ASP.NET chybovou zprávu, která vypadá takto:

![ASP.NET chybové zprávy, pokud dojde k nějakému problému s e-mailem](aspnet-web-pages-razor-troubleshooting-guide/_static/image1.png)

Můžete také ladit problémy s odesláním e-mailu pomocí `try-catch` bloku, jako v následujícím příkladu. Při použití `try-catch` blok ASP.NET nezobrazí její standardní chybové zprávy. Místo toho můžete zaznamenat chybu v `catch` části bloku.

[!code-cshtml[Main](aspnet-web-pages-razor-troubleshooting-guide/samples/sample3.cshtml)]

Nahraďte příslušnými hodnotami pro `your-SMTP-server-name`, a tak dále. Některé chybové zprávy, které se můžou objevovat tímto způsobem, patří:

- *Chyba při odeslání e-mailu.*

    -nebo-

    *Pokus o připojení se nezdařila, protože připojená strana neodpověděla řádně po určitou dobu nebo navázané připojení se nezdařila, protože připojený hostitel se nepodařilo odpovědět*

    Tato chyba obvykle znamená, že aplikace nelze připojit k serveru SMTP. Zkontrolujte název serveru a číslo portu.
- <em>Poštovní schránka není k dispozici. Odpověď serveru: 5.1.0 &lt; someuser@invaliddomain &gt; odesílatele odmítnuta: Neplatný odesílatel domény</em>

    Tuto zprávu můžete určit, že `From` adresa není správná nebo chybí.
- *Zadaný řetězec není ve formátu vyžadovaném pro e-mailovou adresu.*

    Tato chyba může znamenat, která hodnota `To` nebo `From` vlastnosti nerozeznal jako e-mailové adresy. (Technologie ASP.NET nemůže zkontrolovat, že je platný, pouze že se je ve správném formátu, jako je třeba e-mailovou adresu *name@domain.com*.)

> [!NOTE]
> Odeberte kód, který se zobrazí chyba (`@errorMessage`) před publikováním na stránce živého webu. Není vhodné umožňuje uživatelům zobrazit chybové zprávy, které jste získali ze serveru.


<a id="AdditionalResources"></a>
## <a name="additional-resources"></a>Další prostředky

[Webové stránky ASP.NET (Razor) – časté otázky](https://go.microsoft.com/fwlink/?LinkId=253000)

[Služba WebMatrix a webové stránky ASP.NET](https://forums.asp.net/1224.aspx/1?WebMatrix) fórum na webu ASP.NET
