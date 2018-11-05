---
uid: web-pages/overview/api-reference/asp-net-web-pages-api-reference
title: Webové stránky ASP.NET (Razor) – rozhraní API rychlé odkazy | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato stránka obsahuje seznam s krátkou příklady z nejčastěji používaných objektů, vlastnosti a metody pro programování rozhraní ASP.NET Web Pages se syntaxí Razor.
ms.author: riande
ms.date: 02/10/2014
ms.assetid: 4001cb9b-3bfd-4ace-8a89-1561d8421e2c
msc.legacyurl: /web-pages/overview/api-reference/asp-net-web-pages-api-reference
msc.type: authoredcontent
ms.openlocfilehash: 656987f8a725f81dbca7a72594d7d03bc542fabe
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021619"
---
<a name="aspnet-web-pages-razor-api-quick-reference"></a>Webové stránky ASP.NET (Razor) – rozhraní API rychlé odkazy
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tato stránka obsahuje seznam s krátkou příklady z nejčastěji používaných objektů, vlastnosti a metody pro programování rozhraní ASP.NET Web Pages se syntaxí Razor.
> 
> Popisy označené "(v2)" byla zavedena v rozhraní ASP.NET Web Pages verze 2.
> 
> Referenční dokumentace rozhraní API najdete v článku [ASP.NET Web Pages referenční dokumentaci](https://go.microsoft.com/fwlink/?LinkId=208659) na webové stránce MSDN.
> 
> ## <a name="software-versions"></a>Verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2 a ASP.NET Web Pages 1.0 (s výjimkou funkce označené v2).


Tato stránka obsahuje referenční informace pro následující:

- [Třídy](#Classes)
- [Data](#Data)
- [Pomocné rutiny](#Helpers)
- [Ověřování](#Validation)

<a id="Classes"></a>
## <a name="classes"></a>Třídy

### `AppState[key], AppState[index],App`

Obsahuje data, která můžete sdílet jakékoli stránky v aplikaci. Můžete použít dynamické `App` vlastnost přístup ke stejným datům, jako v následujícím příkladu:

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample1.html)]

### `AsBool(), AsBool(true|false)`

Převede řetězcovou hodnotu na logickou hodnotu (true/false). Vrátí hodnotu false nebo zadanou hodnotu, pokud řetězec nepředstavuje true nebo false.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample2.cs)]

### `AsDateTime(), AsDateTime(value)`

Převede řetězcové hodnoty data a času. Vrátí `DateTime.MinValue` nebo zadanou hodnotu, pokud řetězec nepředstavuje datum/čas.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample3.cs)]

### `AsDecimal(), AsDecimal(value)`

Převede řetězcovou hodnotu na desítkovou hodnotu. Vrátí 0,0 nebo pokud řetězec nepředstavuje desítkovou hodnotu zadanou hodnotu.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample4.cs)]

### `AsFloat(), AsFloat(value)`

Převede řetězcovou hodnotu plovoucí desetinnou čárkou. Vrátí 0,0 nebo pokud řetězec nepředstavuje desítkovou hodnotu zadanou hodnotu.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample5.cs)]

### `AsInt(), AsInt(value)`

Převede řetězcovou hodnotu na celé číslo. Vrátí hodnotu 0 nebo zadanou hodnotu, pokud řetězec nepředstavuje celé číslo.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample6.cs)]

### `Href(path [, param1 [, param2]])`

Vytvoří adresu URL pro kompatibilní s prohlížečem z místní cesta k souboru, s částmi volitelné další cestu.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample7.cshtml)]

### `Html.Raw(value)`

Vykreslí *hodnota* jako značka HTML místo vykreslením výstupu jako kódu HTML.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample8.cshtml)]

### `IsBool(), IsDateTime(), IsDecimal(), IsFloat(), IsInt()`

Vrátí true, pokud hodnotu lze převést z řetězce na zadaný typ.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample9.cs)]

### `IsEmpty()`

Vrátí true, pokud objekt nebo proměnná nemá žádnou hodnotu.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample10.cs)]

### `IsPost`

Vrátí true, pokud je žádost POST. (Původní požadavky jsou obvykle GET).

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample11.cs)]

### `Layout`

Určuje cestu ke stránce rozložení chcete použít pro tuto stránku.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample12.html)]

### `PageData[key], PageData[index],Page`

Obsahuje data sdílená mezi stránky, stránkami rozložení a částečnými stránkami aktuálního požadavku. Můžete použít dynamické `Page` vlastnost přístup ke stejným datům, jako v následujícím příkladu:

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample13.html)]

### `RenderBody()`

(Rozložení stránky) Vykreslí obsah stránky obsahu, který se nenachází v libovolné pojmenovaných oddílů.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample14.cs)]

### `RenderPage(path, values)`  
`RenderPage(path[,param1 [, param2]])`

Vykreslí stránku obsahu pomocí zadané cesty a volitelné doplňující data. Můžete získat hodnoty nadbytečné parametry z `PageData` pozice (např. 1) nebo klíče (např. 2).

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample15.js)]

### `RenderSection(sectionName [, required = true|false])`

(Rozložení stránky) Vykreslí obsah oddíl, který má název. Nastavte *požadované* na hodnotu false, aby oddíl nepovinný.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample16.js)]

### `Request.Cookies[key]`

Získá nebo nastaví hodnotu souboru cookie HTTP.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample17.cs)]

### `Request.Files[key]`

Získá soubory, které byly odeslány v aktuální požadavek.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample18.js)]

### `Request.Form[key]`

Získá data, která byla publikována ve formě (jako řetězce). `Request[key]` kontroluje i `Request.Form` a `Request.QueryString` kolekce.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample19.cs)]

### `Request.QueryString[key]`

Získá data, která byla zadaná v řetězci dotazu adresy URL. `Request[key]` kontroluje i `Request.Form` a `Request.QueryString` kolekce.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample20.cs)]

### `Request.Unvalidated(key)`  
`Request.Unvalidated().QueryString|Form|Cookies|Headers[key]`

Ověření pro element formuláře, hodnoty řetězce dotazu, soubor cookie nebo hodnotu hlavičky požadavku selektivně zakáže. Žádost o ověření je ve výchozím nastavení povolené a zabrání uživatelům v publikování značky nebo další potenciálně nebezpečný obsah.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample21.cs)]

### `Response.AddHeader(name, value)`

Přidá do odpovědi hlavičku HTTP serveru.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample22.cs)]

### `Response.OutputCache(seconds [, sliding] [, varyByParams])`

Ukládá do mezipaměti výstup stránky na určitou dobu. Volitelně můžete nastavit *klouzavé* resetovat vypršení časového limitu na každé stránce přístup a *varyByParams* pro ukládání do mezipaměti různé verze stránky pro každý řetězec dotazu v žádosti o stránku.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample23.js)]

### `Response.Redirect(path)`

Přesměruje žádost prohlížeče na nové umístění.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample24.js)]

### `Response.SetStatus(httpStatusCode)`

Nastaví stavový kód HTTP odeslané do prohlížeče.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample25.cs)]

### `Response.WriteBinary(data [, mimetype])`

Zapíše obsah *dat* do odpovědi pomocí volitelného typu MIME.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample26.js)]

### `Response.WriteFile(file)`

Zapíše obsah souboru do odpovědi.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample27.cs)]

### `@section(sectionName) {content }`

(Rozložení stránky) Definuje oddíl obsahu, který má název.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample28.cshtml)]

### `Server.HtmlDecode(htmlText)`

Dekóduje řetězec kódovaný jazykem HTML.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample29.cs)]

### `Server.HtmlEncode(text)`

Zakóduje řetězec pro vykreslování v kódu HTML.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample30.cs)]

### `Server.MapPath(virtualPath)`

Vrací fyzickou cestu serveru pro zadanou virtuální cestu.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample31.cs)]

### `Server.UrlDecode(urlText)`

Dekóduje text z adresy URL.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample32.cs)]

### `Server.UrlEncode(text)`

Zakóduje text do adresy URL.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample33.cs)]

### `Session[key]`

Získá nebo nastaví hodnotu, která existuje, dokud uživatel nezavře prohlížeč.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample34.css)]

### `ToString()`

Zobrazí řetězec představující hodnotu objektu.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample35.html)]

### `UrlData[index]`

Získá doplňující data z adresy URL (například */MyPage/ExtraData*).

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample36.cs)]

### `WebSecurity.ChangePassword(userName,currentPassword,newPassword)`

Změní heslo pro zadaného uživatele.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample37.cs)]

### `WebSecurity.ConfirmAccount(accountConfirmationToken)`

Potvrzuje se tím účet pomocí účtu potvrzovacím tokenem.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample38.cs)]

### `WebSecurity.CreateAccount(userName, password`  
 `[, requireConfirmationToken = true|false])`

Vytvoří nový uživatelský účet pomocí zadaného uživatelského jména a hesla. Tak, aby vyžadovala potvrzovací token, předejte hodnotu true pro *requireConfirmationToken.*

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample39.cs)]

### `WebSecurity.CurrentUserId`

Získá identifikátor celočíselné aktuálně přihlášeného uživatele.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample40.cs)]

### `WebSecurity.CurrentUserName`

Získá název aktuálně přihlášeného uživatele.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample41.cs)]

### `WebSecurity.GeneratePasswordResetToken(username`  
 `[, tokenExpirationInMinutesFromNow])`

Generuje token resetování hesla, který můžete poslat v e-mailu uživatele tak, aby uživatel můžete resetovat heslo.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample42.cs)]

### `WebSecurity.GetUserId(userName)`

Vrátí ID uživatele uživatelského jména.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample43.cs)]

### `WebSecurity.IsAuthenticated`

Vrátí true, pokud je aktuální uživatel přihlášen.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample44.cs)]

### `WebSecurity.IsConfirmed(userName)`

Vrátí true, pokud byl uživatel potvrzen (například přes e-mail s potvrzením).

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample45.cs)]

### `WebSecurity.IsCurrentUser(userName)`

Vrátí true, pokud aktuální uživatelské jméno odpovídá zadanému uživatelskému jménu.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample46.cs)]

### `WebSecurity.Login(userName,password[, persistCookie])`

Přihlásí uživatele v nastavením ověřovací token v souboru cookie.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample47.cs)]

### `WebSecurity.Logout()`

Protokoly out uživatele tak, že odeberete token souboru cookie pro ověřování.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample48.css)]

### `WebSecurity.RequireAuthenticatedUser()`

Pokud uživatel není ověřen, nastaví stav protokolu HTTP na 401 (Neautorizováno).

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample49.css)]

### `WebSecurity.RequireRoles(roles)`

Pokud má aktuální uživatel není členem jedné ze zadaných rolí, nastaví stav protokolu HTTP na 401 (Neautorizováno).

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample50.html)]

### `WebSecurity.RequireUser(userId)`  
`WebSecurity.RequireUser(userName)`

Pokud má aktuální uživatel není uživatel určený parametrem *uživatelské jméno*, nastaví stav protokolu HTTP na 401 (Neautorizováno).

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample51.css)]

### `WebSecurity.ResetPassword(passwordResetToken,newPassword)`

Pokud je platný token pro resetování hesla, změní heslo uživatele na nové heslo.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample52.css)]

<a id="Data"></a>
## <a name="data"></a>Data

### `Database.Execute(SQLstatement [,parameters]`

Spustí *Příkaz_sql* (s volitelnými parametry) například vložit, odstranit nebo aktualizovat a vrátí počet záznamů ovlivněný.

[!code-sql[Main](asp-net-web-pages-api-reference/samples/sample53.sql)]

### `Database.GetLastInsertId()`

Vrátí sloupec identity naposledy vloženého řádku.

[!code-sql[Main](asp-net-web-pages-api-reference/samples/sample54.sql)]

### `Database.Open(filename)`  
`Database.Open(connectionStringName)`

Otevře se soubor zadaná databáze nebo databáze pomocí pojmenovaného připojovacího řetězce z *Web.config* souboru.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample55.cs)]

### `Database.OpenConnectionString(connectionString)`

Otevře se v databázi pomocí připojovacího řetězce. (Tím se liší od `Database.Open`, která využívá název připojovacího řetězce.)

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample56.cs)]

### `Database.Query(SQLstatement[,parameters])`

Dotazuje databázi pomocí *Příkaz_sql* (volitelně předávání parametrů) a vrátí výsledky jako kolekci.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample57.html)]

### `Database.QuerySingle(SQLstatement [, parameters])`

Spustí *Příkaz_sql* (s volitelnými parametry) a vrátí jeden záznam.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample58.cs)]

### `Database.QueryValue(SQLstatement [, parameters])`

Spustí *Příkaz_sql* (s volitelnými parametry) a vrátí jednu hodnotu.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample59.cs)]

<a id="Helpers"></a>
## <a name="helpers"></a>Pomocné rutiny

### `Analytics.GetGoogleHtml(webPropertyId)`

Vykreslí kód jazyka JavaScript v Google Analytics pro zadané ID.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample60.js)]

### `Analytics.GetStatCounterHtml(project,security)`

Vykreslí kód Analytics StatCounter jazyka JavaScript pro zadaný projekt.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample61.css)]

### `Analytics.GetYahooHtml(account)`

Vykreslí kód Analytics Yahoo jazyka JavaScript pro zadaný účet.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample62.js)]

### `Bing.SearchBox([boxWidth])`

Předá vyhledávání Bingu. Chcete-li určit lokalitu pro účely vyhledávání a název vyhledávacího pole, můžete nastavit `Bing.SiteUrl` a `Bing.SiteTitle` vlastnosti. Obvykle nastavte tyto vlastnosti  *\_AppStart* stránky.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample63.html)]

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample64.cshtml)]

### `Chart(width,height [, template] [, templatePath])`

Inicializuje grafu.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample65.cshtml)]

### `Chart.AddLegend([title] [, name])`

Přidá do grafu legendu.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample66.cshtml)]

### `Chart.AddSeries([name] [, chartType] [, chartArea]`  
 `[, axisLabel] [, legend] [, markerStep] [, xValue]`  
 `[, xField] [, yValues] [, yFields] [, options])`

Přidává řadu hodnot do grafu.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample67.cshtml)]

### `Crypto.Hash(string [, algorithm])`  
`Crypto.Hash(bytes [, algorithm])`

Vrátí hodnotu hash pro zadaná data. Výchozí algoritmus je `sha256`.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample68.html)]

### `Facebook.LikeButton(href [, buttonLayout] [, showFaces] [, width] [, height]`   
 `[, action] [, font] [, colorScheme] [, refLabel])`

Umožňuje uživatelům Facebooku připojení na stránky.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample69.js)]

### `FileUpload.GetHtml([initialNumberOfFiles] [, allowMoreFilesToBeAdded]`  
 `[, includeFormTag] [, addText] [, uploadText])`

Vykreslí uživatelského rozhraní pro nahrávání souborů.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample70.html)]

### `GamerCard.GetHtml(gamerTag)`

Vykreslí zadané značky hráče Xbox.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample71.js)]

### `Gravatar.GetHtml(email [, imageSize] [, defaultImage] [, rating]`  
 `[, imageExtension] [, attributes])`

Vykreslí obraz Gravatar zadané e-mailových adres.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample72.css)]

### `Json.Encode(object)`

Převede datový objekt na řetězec ve formátu JavaScript Object Notation (JSON).

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample73.cs)]

### `Json.Decode(string)`

Převede vstupní řetězec kódovaný ve formátu JSON na datový objekt, který může iterovat nebo vložit do databáze.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample74.cs)]

### `LinkShare.GetHtml(pageTitle[, pageLinkBack] [, twitterUserName]`  
 `[, additionalTweetText] [, linkSites])`

Vykreslí sociálních sítí odkazuje pomocí zadaný název a volitelný adresy URL.

[!code-xml[Main](asp-net-web-pages-api-reference/samples/sample75.xml)]

### `ModelStateDictionary.AddError(key, errorMessage)`

Přidruží chybovou zprávu s polem formuláře. Použití `ModelState` pomocná rutina pro přístup k tomuto členu.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample76.cs)]

### `ModelStateDictionary.AddFormError(errorMessage)`

Přidruží chybovou zprávu s formuláři. Použití `ModelState` pomocná rutina pro přístup k tomuto členu.

[!code-powershell[Main](asp-net-web-pages-api-reference/samples/sample77.ps1)]

### `ModelStateDictionary.IsValid`

Vrátí true, pokud nejsou žádné chyby ověření. Použití `ModelState` pomocná rutina pro přístup k tomuto členu.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample78.cs)]

### `ObjectInfo.Print(value [, depth] [, enumerationLength])`

Vykreslí vlastnosti a hodnoty objektu a všech podřízených objektů.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample79.css)]

### `Recaptcha.GetHtml([, publicKey] [, theme] [, language] [, tabIndex])`

Vykreslí ověřovací test nástroje reCAPTCHA.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample80.css)]

### `ReCaptcha.PublicKey`  
 `ReCaptcha.PrivateKey`

Nastaví veřejného a privátního klíče pro služby nástroje reCAPTCHA. Obvykle nastavte tyto vlastnosti  *\_AppStart* stránky.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample81.css)]

### `ReCaptcha.Validate([, privateKey])`

Vrátí výsledek testu nástroje reCAPTCHA.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample82.cs)]

### `ServerInfo.GetHtml()`

Vykreslí stavové informace o rozhraní ASP.NET Web Pages.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample83.cshtml)]

### `Twitter.Profile(twitterUserName)`

Vykreslí Twitteru datového proudu pro zadaného uživatele.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample84.js)]

### `Twitter.Search(searchQuery)`

Vykreslí Twitteru datového proudu pro zadaný hledaný text.

[!code-xml[Main](asp-net-web-pages-api-reference/samples/sample85.xml)]

### `Video.Flash(filename [, width, height])`

Vykreslí Flash přehrávač videa pro zadaný soubor s volitelné šířku a výšku.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample86.cshtml)]

### `Video.MediaPlayer(filename [, width, height])`

Vykreslí Windows Media player pro zadaný soubor s volitelné šířku a výšku.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample87.cshtml)]

### `Video.Silverlight(filename, width, height)`

Vykreslí přehrávač Silverlight pro zadaný rozbočovač *.xap* soubor se požadovaná šířka a výška.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample88.cshtml)]

### `WebCache.Get(key)`

Vrátí objekt definovaný podle objektu *klíč*, nebo hodnota null, pokud objekt nebyl nalezen.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample89.cs)]

### `WebCache.Remove(key)`

Odebere objekt definovaný podle objektu *klíč* z mezipaměti.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample90.cs)]

### `WebCache.Set(key, value [, minutesToCache] [, slidingExpiration])`

Vloží *hodnotu* do mezipaměti v rámci určeného parametrem *klíč*.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample91.html)]

### `WebGrid(data)`

Vytvoří novou `WebGrid` pomocí dat z dotazu.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample92.cs)]

### `WebGrid.GetHtml()`

Vykreslí značku pro zobrazení dat v tabulce jazyka HTML.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample93.html)]

### `WebGrid.Pager()`

Vykreslí stránkování pro `WebGrid` objektu.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample94.html)]

### `WebImage(path)`

Načte obrázek ze zadané cesty.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample95.cs)]

### `WebImage.AddImagesWatermark(image)`

Přidá zadanou image jako vodoznak.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample96.cs)]

### `WebImage.AddTextWatermark(text)`

Přidá zadaný text do obrázku.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample97.cs)]

### `WebImage.FlipHorizontal()`  
`WebImage.FlipVertical()`

Převrátí obrázek vodorovně nebo svisle.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample98.css)]

### `WebImage.GetImageFromRequest()`

Načte bitovou kopii při publikování image na stránku při nahrávání souboru.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample99.cs)]

### `WebImage.Resize(width,height)`

Změní velikost bitovou kopii.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample100.css)]

### `WebImage.RotateLeft()`  
`WebImage.RotateRight()`

Otočí obrázek vlevo nebo vpravo.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample101.css)]

### `WebImage.Save(path [, imageFormat])`

Obrázek uloží do zadané cesty.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample102.js)]

### `WebMail.Password`

Nastaví heslo pro SMTP server. Za normálních okolností byste tuto vlastnost nastavit  *\_AppStart* stránky.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample103.cs)]

### `WebMail.Send(to, subject, body [, from] [, cc] [, filesToAttach] [, isBodyHtml]`  
 `[, additionalHeaders])`

Odešle e-mailovou zprávu.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample104.css)]

### `WebMail.SmtpServer`

Nastaví název serveru SMTP. Za normálních okolností byste tuto vlastnost nastavit<em>\_AppStart</em> stránky.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample105.html)]

### `WebMail.UserName`

Nastaví uživatelské jméno pro SMTP server. Obvykle tuto vlastnost měli nastavit  *\_AppStart* stránky.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample106.html)]

<a id="Validation"></a>
## <a name="validation"></a>Ověřování

### `Html.ValidationMessage(field)`

(v2) Vykreslí chybovou zprávu ověření pro zadané pole.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample107.cshtml)]

### `Html.ValidationSummary([message])`

(v2) Zobrazí seznam všech chyb ověření.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample108.cshtml)]

### `Validation.Add(field, validationType)`

(v2) Zaregistruje element vstupu uživatele pro zadaný typ ověřování.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample109.js)]

### `Validation.ClassFor(field)`

(v2) Dynamicky vykreslí atributy třídy šablony stylů CSS pro ověřování na straně klienta, aby lze formátovat chybových zpráv ověření. (Vyžaduje odkazovat na knihovny příslušný skript klienta a definovat třídy šablony stylů CSS.)

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample110.html)]

### `Validation.For(field)`

(v2) Umožňuje ověřování na straně klienta pro uživatele vstupní pole. (Vyžaduje, že odkazujete na příslušný skript klienta knihovny.)

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample111.html)]

### `Validation.IsValid()`

(v2) Vrátí true, pokud všechny elementy vstupu uživatele, které jsou registrovaných pro ověření obsahovat platné hodnoty.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample112.cs)]

### `Validation.RequireField(field[, errorMessage])`

(v2) Určuje, že uživatelé musí zadat hodnotu pro daný element vstupu uživatele.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample113.cs)]

### `Validation.RequireFields(field1[, field12, field3, ...])`

(v2) Určuje, že uživatelé musí zadat hodnoty pro jednotlivé elementy vstupu uživatele. Tato metoda neumožňuje zadat vlastní chybovou zprávu.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample114.html)]

### `Validator.DateTime ([error message])`  
`Validator.Decimal([error message])`  
`Validator.EqualsTo(otherField,[error message])`  
`Validator.Float([error message])`  
`Validator.Integer([error message])`  
`Validator.Range(min,max [, error message])`  
`Validator.RegEx(pattern[, error message])`  
`Validator.Required([error message])`  
`Validator.StringLength(length)`  
`Validator.Url([error message])`

(v2) Určuje ověřovací test, pokud použijete `Validation.Add` metody.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample115.js)]
