---
title: Statické soubory v ASP.NET Core
author: rick-anderson
description: Naučte se zajišťovat a zabezpečovat statické soubory a konfigurovat statický soubor hostující chování middlewaru ve ASP.NET Core webové aplikaci.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: b1f84a936ee1327498abce660cd64f8d7d0a2864
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202791"
---
# <a name="static-files-in-aspnet-core"></a>Statické soubory v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)

Statické soubory, jako jsou HTML, CSS, obrázky a JavaScript, jsou assety, ASP.NET Core aplikace ve výchozím nastavení obsluhuje přímo klienty.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Obsluhovat statické soubory

Statické soubory jsou uloženy v [kořenovém adresáři webu](xref:fundamentals/index#web-root) projektu. Výchozí adresář je `{content root}/wwwroot` , ale dá se změnit pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metody. Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/index#content-root) a [webový kořenový adresář](xref:fundamentals/index#web-root).

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>Metoda nastaví kořen obsahu na aktuální adresář:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

Předchozí kód byl vytvořen pomocí šablony webové aplikace.

Statické soubory jsou přístupné prostřednictvím cesty relativní k [webovému kořenovému adresáři](xref:fundamentals/index#web-root). Například šablony projektu **webové aplikace** obsahují ve složce několik složek `wwwroot` :

* `wwwroot`
  * `css`
  * `js`
  * `lib`

Zvažte vytvoření složky *wwwroot/images* a přidání souboru *wwwroot/images/MyImage.jpg* . Formát identifikátoru URI pro přístup k souboru ve `images` složce je `https://<hostname>/images/<image_file_name>` . Například `https://localhost:5001/images/MyImage.jpg`.

### <a name="serve-files-in-web-root"></a>Obsluhovat soubory ve webovém kořenovém adresáři

Výchozí šablony webové aplikace volají <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> metodu v `Startup.Configure` , která umožňuje obsluhovat statické soubory:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

`UseStaticFiles`Přetížení metody bez parametrů označí soubory ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) jako servé. Následující značky odkazují na *wwwroot/image/MyImage.jpg*:

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

V předchozím kódu znak tildy `~/` ukazuje na [kořenový adresář webu](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Obsluhovat soubory mimo web root

Vezměte v úvahu hierarchii adresářů, ve které se statické soubory budou obsluhovat mimo [webový kořenový adresář](xref:fundamentals/index#web-root):

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

Požadavek může získat přístup k `red-rose.jpg` souboru nakonfigurováním middleware statického souboru následujícím způsobem:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

V předchozím kódu se hierarchie adresáře *MyStaticFiles* zveřejňuje veřejně prostřednictvím segmentu URI *StaticFiles* . Požadavek na `https://<hostname>/StaticFiles/images/red-rose.jpg` obsluhu souboru *red-rose.jpg* .

Následující značky odkazují na *MyStaticFiles/image/red-rose.jpg*:

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a>Nastavit hlavičky HTTP odpovědi

<xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Objekt lze použít k nastavení hlaviček odpovědi HTTP. Kromě konfigurace statického souboru obsluha z [webového kořenového adresáře](xref:fundamentals/index#web-root)nastaví následující kód `Cache-Control` hlavičku:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

Statické soubory jsou veřejně ukládat do mezipaměti po dobu 600 sekund:

![Hlavičky odpovědi ukazující hlavičku Cache-Control se přidaly.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorizace statického souboru

Middleware statických souborů neposkytuje kontroly autorizace. Všechny soubory obsluhované IT, včetně těch, které jsou v nástroji `wwwroot` , jsou veřejně přístupné. Pro obsluhu souborů na základě autorizace:

* Ukládejte je mimo `wwwroot` a jakýkoliv adresář přístupný pro middleware statických souborů.
* Obsluha prostřednictvím metody akce, na které se autorizace používá, a vrácení <xref:Microsoft.AspNetCore.Mvc.FileResult> objektu:

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a>Procházení adresářů

Procházení adresářů umožňuje výpis adresáře v rámci určených adresářů.

Procházení adresářů je ve výchozím nastavení zakázáno z bezpečnostních důvodů. Další informace najdete v tématu věnovaném [důležitým](#sc)informacím.

Povolit procházení adresářů pomocí:

* <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> v `Startup.ConfigureServices` .
* <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> v `Startup.Configure` .

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

Předchozí kód umožňuje procházení adresářů složky *wwwroot/images* pomocí adresy URL `https://<hostname>/MyImages` s odkazy na jednotlivé soubory a složky:

![procházení adresářů](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a>Obsluha výchozích dokumentů

Nastavení výchozí stránky poskytuje návštěvníkům výchozí bod na webu. Chcete-li zajišťovat výchozí stránku z `wwwroot` bez plně kvalifikovaného identifikátoru URI, zavolejte <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> metodu:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

`UseDefaultFiles` musí být volána předtím, než `UseStaticFiles` bude sloužit jako výchozí soubor. `UseDefaultFiles` je přepis adresy URL, který soubor neobsluhuje.

`UseDefaultFiles`V rámci aplikace vyhledá požadavky na složku v `wwwroot` hledání:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

První soubor, který se nachází v seznamu, se obsluhuje, jako by se jednalo o plně kvalifikovaný identifikátor URI. Adresa URL prohlížeče bude dál odpovídat požadovanému identifikátoru URI.

Následující kód změní výchozí název souboru na *mydefault.html*:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

Následující kód ukazuje `Startup.Configure` s předchozím kódem:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a>UseFileServer pro výchozí dokumenty

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> kombinuje funkce `UseStaticFiles` , `UseDefaultFiles` a volitelně `UseDirectoryBrowser` .

Volání `app.UseFileServer` pro povolení obsluhy statických souborů a výchozího souboru. Procházení adresářů není povoleno. Následující kód ukazuje `Startup.Configure` `UseFileServer` :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

Následující kód povoluje obsluhu statických souborů, výchozího souboru a procházení adresářů:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Následující kód ukazuje `Startup.Configure` s předchozím kódem:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

Vezměte v úvahu následující hierarchii adresářů:

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

Následující kód povoluje obsluhu statických souborů, výchozí soubor a procházení adresářů `MyStaticFiles` :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> musí být volána, pokud `EnableDirectoryBrowsing` je hodnota vlastnosti `true` .

Pomocí hierarchie souborů a předchozího kódu adresy URL překládat následujícím způsobem:

| Identifikátor URI            |      Odpověď  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | *MyStaticFiles/imagí/MyImage.jpg* |
| `https://<hostname>/StaticFiles` | *MyStaticFiles/default.html* |

Pokud v adresáři *MyStaticFiles* neexistuje žádný výchozí soubor s názvem, `https://<hostname>/StaticFiles` vrátí výpis adresáře s odkazy kliknutím:

![Seznam statických souborů](static-files/_static/db2.png)

<xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> a <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> provede přesměrování na straně klienta z cílového identifikátoru URI bez koncového `/`  na cílový identifikátor URI s koncovou `/` . Například z `https://<hostname>/StaticFiles` na `https://<hostname>/StaticFiles/` . Relativní adresy URL v adresáři *StaticFiles* jsou neplatné bez koncového lomítka ( `/` ).

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Třída obsahuje `Mappings` vlastnost, která slouží jako mapování přípon souborů na typy obsahu MIME. V následující ukázce jsou několik přípon souborů mapovány na známé typy MIME. Přípona *. RTF* je nahrazena a *. mp4* je odebráno:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

Následující kód ukazuje `Startup.Configure` s předchozím kódem:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

Viz [typy obsahu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Nestandardní typy obsahu

Middleware statických souborů rozumí téměř 400 známým typům obsahu souborů. Pokud uživatel požádá o soubor s neznámým typem souboru, middleware statických souborů předá požadavek dalšímu middlewaru v kanálu. Pokud žádost nezpracovává žádné middleware, je vrácena odpověď 404, která *nebyla nalezena* . Pokud je povoleno procházení adresářů, zobrazí se v seznamu adresářů odkaz na tento soubor.

Následující kód povoluje obsluhu neznámých typů a vykresluje neznámý soubor jako obrázek:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

Následující kód ukazuje `Startup.Configure` s předchozím kódem:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

V předchozím kódu se jako obrázek vrátí požadavek na soubor s neznámým typem obsahu.

> [!WARNING]
> Povolení <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> je bezpečnostní riziko. Ve výchozím nastavení je zakázané a její použití se nedoporučuje. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu pro obsluhu souborů s nestandardními rozšířeními.

## <a name="serve-files-from-multiple-locations"></a>Obsluhovat soubory z více míst

`UseStaticFiles` a `UseFileServer` jako výchozí pro poskytovatele souborů odkazující na `wwwroot` . Další instance `UseStaticFiles` a `UseFileServer` lze poskytnout jiným poskytovatelům souborů pro obsluhu souborů z jiných umístění. Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a>Požadavky na zabezpečení pro statické soubory

> [!WARNING]
> `UseDirectoryBrowser` a `UseStaticFiles` můžou neúniky tajných kódů. Zakázání procházení adresářů v produkčním prostředí se důrazně doporučuje. Pečlivě zkontrolujte, které adresáře jsou povoleny prostřednictvím `UseStaticFiles` nebo `UseDirectoryBrowser` . Celý adresář a jeho podadresáře se stanou veřejně přístupnými. Ukládejte soubory vhodné pro poskytování veřejnosti ve vyhrazeném adresáři, jako je například `<content_root>/wwwroot` . Oddělte tyto soubory od zobrazení MVC, Razor stránek, konfiguračních souborů atd.

* Adresy URL obsahu vystaveného nástroji `UseDirectoryBrowser` a `UseStaticFiles` podléhají omezením malých a velkých písmen a znakům základního systému souborů. Například Windows rozlišuje malá a velká písmena, ale macOS a Linux ne.

* ASP.NET Core aplikace hostované ve službě IIS používají [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) k přeposílání všech požadavků do aplikace, včetně požadavků na statické soubory. Obslužná rutina statických souborů služby IIS se nepoužívá a nemá možnost zpracovávat požadavky.

* Provedením následujících kroků ve Správci služby IIS odeberte obslužnou rutinu statického souboru služby IIS na úrovni serveru nebo webu:
    1. Přejděte do funkce **moduly** .
    1. V seznamu vyberte **StaticFileModule** .
    1. Na bočním panelu **Akce** klikněte na **Odebrat** .

> [!WARNING]
> Pokud je povolena obslužná rutina statických souborů služby IIS **a** modul ASP.NET Core je nesprávně nakonfigurován, jsou obsluhovány statické soubory. K tomu dojde například v případě, že soubor *web.config* není nasazen.

* Umístěte soubory kódu, včetně *přípon cs* a *. cshtml*, mimo [webový kořenový adresář](xref:fundamentals/index#web-root)projektu aplikace. Vytvoří se logické oddělení mezi obsahem aplikace na straně klienta a kódem serveru. Tím zabráníte úniku kódu na straně serveru.

## <a name="additional-resources"></a>Další zdroje

* [Middleware](xref:fundamentals/middleware/index)
* [Úvod do ASP.NET Core](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Scott Addie](https://twitter.com/Scott_Addie)

Statické soubory, jako jsou HTML, CSS, obrázky a JavaScript, jsou prostředky, které ASP.NET Core aplikace slouží přímo klientům. K povolení obsluhy těchto souborů je potřeba některá konfigurace.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Obsluhovat statické soubory

Statické soubory jsou uloženy v [kořenovém adresáři webu](xref:fundamentals/index#web-root) projektu. Výchozí adresář je *{root obsahu}/wwwroot*, ale dá se změnit prostřednictvím <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metody. Další informace najdete v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) a ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) .

Webový hostitel aplikace musí být informován o kořenovém adresáři obsahu.

`WebHost.CreateDefaultBuilder`Metoda nastaví kořen obsahu na aktuální adresář:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

Statické soubory jsou přístupné prostřednictvím cesty relativní k [webovému kořenovému adresáři](xref:fundamentals/index#web-root). Například šablona projektu **webové aplikace** obsahuje několik složek v rámci `wwwroot` složky:

* `wwwroot`
  * `css`
  * `images`
  * `js`

Formát identifikátoru URI pro přístup k souboru v podsložce *obrázky* je *http:// \<server_address> /images/ \<image_file_name> *. Příklad: *http://localhost:9189/images/banner3.svg*.

Pokud cílíte .NET Framework, přidejte do projektu balíček [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) . Při cílení na .NET Core obsahuje [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) tento balíček.

Nakonfigurujte [middleware](xref:fundamentals/middleware/index), který umožňuje obsluhu statických souborů.

### <a name="serve-files-inside-of-web-root"></a>Obsluhovat soubory uvnitř webového kořenového adresáře

Vyvolat <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> metodu v rámci `Startup.Configure` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

`UseStaticFiles`Přetížení metody bez parametrů označí soubory ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) jako servé. Následující značky odkazují na *wwwroot/images/banner1. SVG*:

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

V předchozím kódu znak tildy `~/` ukazuje na [kořenový adresář webu](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Obsluhovat soubory mimo web root

Vezměte v úvahu hierarchii adresářů, ve které se statické soubory budou obsluhovat mimo [webový kořenový adresář](xref:fundamentals/index#web-root):

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

Požadavek může získat přístup k souboru *banner1. SVG* nakonfigurováním middleware statického souboru následujícím způsobem:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

V předchozím kódu se hierarchie adresáře *MyStaticFiles* zveřejňuje veřejně prostřednictvím segmentu URI *StaticFiles* . Požadavek na *http:// \<server_address> /StaticFiles/images/banner1.SVG* obsluhuje soubor *banner1. SVG* .

Následující značky odkazují na *MyStaticFiles/images/banner1. SVG*:

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Nastavit hlavičky HTTP odpovědi

<xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Objekt lze použít k nastavení hlaviček odpovědi HTTP. Kromě konfigurace statického souboru obsluha z [webového kořenového adresáře](xref:fundamentals/index#web-root)nastaví následující kód `Cache-Control` hlavičku:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>Metoda existuje v balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

Soubory byly ve vývojovém prostředí veřejně uložené do mezipaměti po dobu 10 minut (600 sekund):

![Hlavičky odpovědi ukazující hlavičku Cache-Control se přidaly.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorizace statického souboru

Middleware statických souborů neposkytuje kontroly autorizace. Všechny soubory, které obsluhuje, včetně názvů *wwwroot*, jsou veřejně přístupné. Pro obsluhu souborů na základě autorizace:

* Uložte je mimo *wwwroot* a jakýkoliv adresář přístupný pro middleware statických souborů.
* Obsluhuje je pomocí metody akce, na které se autorizace používá. Vrátit <xref:Microsoft.AspNetCore.Mvc.FileResult> objekt:

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Povolit procházení adresářů

Procházení adresářů umožňuje uživatelům vaší webové aplikace zobrazit v zadaném adresáři výpis adresářů a soubory. Procházení adresářů je ve výchozím nastavení zakázáno z důvodů zabezpečení (viz téma [informace](#sc)). Povolit procházení adresářů vyvoláním <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> metody v `Startup.Configure` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Přidejte požadované služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> metody z `Startup.ConfigureServices` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Předchozí kód umožňuje procházení adresářů složky *wwwroot/imagí* pomocí adresy URL *http:// \<server_address> /MyImages*s odkazy na jednotlivé soubory a složky:

![procházení adresářů](static-files/_static/dir-browse.png)

Při povolování procházení si přečtěte [informace](#considerations) o bezpečnostních rizicích.

Všimněte si dvou `UseStaticFiles` volání v následujícím příkladu. První volání povoluje obsluhu statických souborů ve složce *wwwroot* . Druhé volání umožňuje procházení adresářů složky *wwwroot/imagí* pomocí adresy URL *http:// \<server_address> /MyImages*:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Slouží jako výchozí dokument.

Nastavení výchozí domovské stránky poskytuje návštěvníkům logický výchozí bod při návštěvě vašeho webu. Chcete-li obsloužit výchozí stránku bez toho, aby uživatel plně kvalifikován identifikátor URI, zavolejte <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> metodu z `Startup.Configure` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles` musí být volána předtím, než `UseStaticFiles` bude sloužit jako výchozí soubor. `UseDefaultFiles` je přepis adresy URL, který soubor ve skutečnosti neobsluhuje. Povolte middleware statických souborů prostřednictvím nástroje `UseStaticFiles` za účelem obsluhy souboru.

S `UseDefaultFiles` , požadavky na hledání složky pro:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

První soubor, který se nachází v seznamu, se obsluhuje, jako by se jednalo o plně kvalifikovaný identifikátor URI. Adresa URL prohlížeče bude dál odpovídat požadovanému identifikátoru URI.

Následující kód změní výchozí název souboru na *mydefault.html*:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> kombinuje funkce `UseStaticFiles` , `UseDefaultFiles` a volitelně `UseDirectoryBrowser` .

Následující kód povoluje obsluhu statických souborů a výchozího souboru. Procházení adresářů není povoleno.

```csharp
app.UseFileServer();
```

Následující kód je sestaven po přetěžování bez parametrů povolením procházení adresářů:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Vezměte v úvahu následující hierarchii adresářů:

* **wwwroot**
  * **funkcí**
  * **fotografií**
  * **js**
* **MyStaticFiles**
  * **fotografií**
    * *banner1. SVG*
  * *default.html*

Následující kód povoluje statické soubory, výchozí soubory a procházení adresářů pro `MyStaticFiles` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser` musí být volána, pokud `EnableDirectoryBrowsing` je hodnota vlastnosti `true` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Pomocí hierarchie souborů a předchozího kódu adresy URL překládat následujícím způsobem:

| Identifikátor URI            |                             Odpověď  |
| ------- | ------|
| *http:// \<server_address> /StaticFiles/images/banner1.SVG*    |      MyStaticFiles/images/banner1. SVG |
| *http:// \<server_address> /StaticFiles*             |     MyStaticFiles/default.html |

Pokud v adresáři *MyStaticFiles* neexistuje žádný výchozí soubor s názvem, *http:// \<server_address> /StaticFiles* vrátí výpis adresáře s odkazy kliknutím:

![Seznam statických souborů](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> a <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> provede přesměrování na straně klienta z `http://{SERVER ADDRESS}/StaticFiles` (bez koncového lomítka) na `http://{SERVER ADDRESS}/StaticFiles/` (s koncovým lomítkem). Relativní adresy URL v adresáři *StaticFiles* jsou neplatné bez koncového lomítka.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Třída obsahuje `Mappings` vlastnost, která slouží jako mapování přípon souborů na typy obsahu MIME. V následující ukázce jsou pro známé typy MIME zaregistrovány několik přípon souborů. Přípona *. RTF* je nahrazena a *. mp4* je odebráno.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Viz [typy obsahu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

Informace o tom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> , jak používat vlastní nebo nakonfigurovat jinou <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> aplikaci v Blazor serverových aplikacích, najdete v tématu <xref:blazor/fundamentals/additional-scenarios#static-files> .

## <a name="non-standard-content-types"></a>Nestandardní typy obsahu

Middleware statických souborů rozumí téměř 400 známým typům obsahu souborů. Pokud uživatel požádá o soubor s neznámým typem souboru, middleware statických souborů předá požadavek dalšímu middlewaru v kanálu. Pokud žádost nezpracovává žádné middleware, je vrácena odpověď 404, která *nebyla nalezena* . Pokud je povoleno procházení adresářů, zobrazí se v seznamu adresářů odkaz na tento soubor.

Následující kód povoluje obsluhu neznámých typů a vykresluje neznámý soubor jako obrázek:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

V předchozím kódu se jako obrázek vrátí požadavek na soubor s neznámým typem obsahu.

> [!WARNING]
> Povolení <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> je bezpečnostní riziko. Ve výchozím nastavení je zakázané a její použití se nedoporučuje. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu pro obsluhu souborů s nestandardními rozšířeními.

## <a name="serve-files-from-multiple-locations"></a>Obsluhovat soubory z více míst

`UseStaticFiles` a `UseFileServer` výchozím nastavením je poskytovatel souborů odkazující na *wwwroot*. Můžete poskytnout další instance `UseStaticFiles` a `UseFileServer` s dalšími poskytovateli souborů pro obsluhu souborů z jiných umístění. Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Požadavky

> [!WARNING]
> `UseDirectoryBrowser` a `UseStaticFiles` můžou neúniky tajných kódů. Zakázání procházení adresářů v produkčním prostředí se důrazně doporučuje. Pečlivě zkontrolujte, které adresáře jsou povoleny prostřednictvím `UseStaticFiles` nebo `UseDirectoryBrowser` . Celý adresář a jeho podadresáře se stanou veřejně přístupnými. Ukládejte soubory vhodné pro poskytování veřejnosti ve vyhrazeném adresáři, například * \<content_root> /wwwroot*. Oddělte tyto soubory od zobrazení MVC, Razor stránky (jenom 2. x), konfigurační soubory atd.

* Adresy URL obsahu vystaveného nástroji `UseDirectoryBrowser` a `UseStaticFiles` podléhají omezením malých a velkých písmen a znakům základního systému souborů. Například Windows rozlišuje malá a velká písmena &mdash; MacOS a Linux.

* ASP.NET Core aplikace hostované ve službě IIS používají [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) k přeposílání všech požadavků do aplikace, včetně požadavků na statické soubory. Obslužná rutina statického souboru služby IIS se nepoužívá. Nemá možnost zpracovávat požadavky předtím, než je modul zpracován.

* Provedením následujících kroků ve Správci služby IIS odeberte obslužnou rutinu statického souboru služby IIS na úrovni serveru nebo webu:
    1. Přejděte do funkce **moduly** .
    1. V seznamu vyberte **StaticFileModule** .
    1. Na bočním panelu **Akce** klikněte na **Odebrat** .

> [!WARNING]
> Pokud je povolena obslužná rutina statických souborů služby IIS **a** modul ASP.NET Core je nesprávně nakonfigurován, jsou obsluhovány statické soubory. K tomu dojde například v případě, že soubor *web.config* není nasazen.

* Umístěte soubory kódu (včetně *. cs* a *. cshtml*) mimo [kořenový adresář webu](xref:fundamentals/index#web-root)projektu aplikace. Vytvoří se logické oddělení mezi obsahem aplikace na straně klienta a kódem serveru. Tím zabráníte úniku kódu na straně serveru.

## <a name="additional-resources"></a>Další zdroje

* [Middleware](xref:fundamentals/middleware/index)
* [Úvod do ASP.NET Core](xref:index)

::: moniker-end
