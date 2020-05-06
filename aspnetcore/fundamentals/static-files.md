---
title: Statické soubory v ASP.NET Core
author: rick-anderson
description: Naučte se zajišťovat a zabezpečovat statické soubory a konfigurovat statický soubor hostující chování middlewaru ve ASP.NET Core webové aplikaci.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: f7697260e6ab29d0e9ba955dfdf0c8c81e4e1130
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775489"
---
# <a name="static-files-in-aspnet-core"></a>Statické soubory v ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Scott Addie](https://twitter.com/Scott_Addie)

Statické soubory, jako jsou HTML, CSS, obrázky a JavaScript, jsou prostředky, které ASP.NET Core aplikace slouží přímo klientům. K povolení obsluhy těchto souborů je potřeba některá konfigurace.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Obsluhovat statické soubory

Statické soubory jsou uloženy v [kořenovém adresáři webu](xref:fundamentals/index#web-root) projektu. Výchozí adresář je *{root Content}/wwwroot*, ale dá se změnit prostřednictvím metody [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) . Další informace najdete v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) a ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) .

Webový hostitel aplikace musí být informován o kořenovém adresáři obsahu.

::: moniker range=">= aspnetcore-2.0"

`WebHost.CreateDefaultBuilder` Metoda nastaví kořen obsahu na aktuální adresář:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Nastavte kořen obsahu na aktuální adresář vyvoláním [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) uvnitř `Program.Main`:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Statické soubory jsou přístupné prostřednictvím cesty relativní k [webovému kořenovému adresáři](xref:fundamentals/index#web-root). Například šablona projektu **webové aplikace** obsahuje několik složek ve složce *wwwroot* :

* **wwwroot**
  * **funkcí**
  * **fotografií**
  * **js**

Formát identifikátoru URI pro přístup k souboru v podsložce *imagí* je *http://\<server_address>/images/\<image_file_name>*. Například *http://localhost:9189/images/banner3.svg*.

::: moniker range=">= aspnetcore-2.1"

Pokud cílíte .NET Framework, přidejte do projektu balíček [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) . Při cílení na .NET Core obsahuje [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) tento balíček.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Pokud cílíte .NET Framework, přidejte do projektu balíček [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) . Při cílení na rozhraní .NET Core obsahuje soubor [Microsoft. AspNetCore. All Metapackage](xref:fundamentals/metapackage) tento balíček.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Přidejte do projektu balíček [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .

::: moniker-end

Nakonfigurujte [middleware](xref:fundamentals/middleware/index) , který umožňuje obsluhu statických souborů.

### <a name="serve-files-inside-of-web-root"></a>Obsluhovat soubory uvnitř webového kořenového adresáře

Vyvolat metodu [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) v rámci `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Přetížení `UseStaticFiles` metody bez parametrů označí soubory ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) jako servé. Následující značky odkazují na *wwwroot/images/banner1. SVG*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

V předchozím kódu znak `~/` tildy ukazuje na [kořenový adresář webu](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Obsluhovat soubory mimo web root

Vezměte v úvahu hierarchii adresářů, ve které se statické soubory budou obsluhovat mimo [webový kořenový adresář](xref:fundamentals/index#web-root):

* **wwwroot**
  * **funkcí**
  * **fotografií**
  * **js**
* **MyStaticFiles**
  * **fotografií**
    * *banner1. SVG*

Požadavek může získat přístup k souboru *banner1. SVG* nakonfigurováním middleware statického souboru následujícím způsobem:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

V předchozím kódu se hierarchie adresáře *MyStaticFiles* zveřejňuje veřejně prostřednictvím segmentu URI *StaticFiles* . Požadavek na *http://\<server_address>/staticfiles/images/banner1.SVG* slouží jako soubor *banner1. SVG* .

Následující značky odkazují na *MyStaticFiles/images/banner1. SVG*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Nastavit hlavičky HTTP odpovědi

K nastavení hlaviček odpovědí HTTP lze použít objekt [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) . Kromě konfigurace statického souboru obsluha z [webového kořenového adresáře](xref:fundamentals/index#web-root)nastaví následující kód `Cache-Control` hlavičku:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

V balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) existuje metoda [HeaderDictionaryExtensions. Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) .

Soubory byly ve vývojovém prostředí veřejně uložené do mezipaměti po dobu 10 minut (600 sekund):

![Hlavičky odpovědi ukazující hlavičku Cache-Control se přidaly.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorizace statického souboru

Middleware statických souborů neposkytuje kontroly autorizace. Všechny soubory, které obsluhuje, včetně názvů *wwwroot*, jsou veřejně přístupné. Pro obsluhu souborů na základě autorizace:

* Uložte je mimo *wwwroot* a jakýkoliv adresář přístupný pro middleware statických souborů.
* Obsluhuje je pomocí metody akce, na které se autorizace používá. Vrátit objekt [výsledku](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) objektu:

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Povolit procházení adresářů

Procházení adresářů umožňuje uživatelům vaší webové aplikace zobrazit v zadaném adresáři výpis adresářů a soubory. Procházení adresářů je ve výchozím nastavení zakázáno z důvodů zabezpečení (viz téma [informace](#considerations)). Povolit procházení adresářů vyvoláním metody [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) v `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Přidejte požadované služby vyvoláním metody [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) z `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Předchozí kód umožňuje procházení adresářů složky *wwwroot/imagí* pomocí adresy URL *\<http://server_address>/MyImages*, s odkazy na jednotlivé soubory a složky:

![procházení adresářů](static-files/_static/dir-browse.png)

Při povolování procházení si přečtěte [informace](#considerations) o bezpečnostních rizicích.

Všimněte si dvou `UseStaticFiles` volání v následujícím příkladu. První volání povoluje obsluhu statických souborů ve složce *wwwroot* . Druhé volání umožňuje procházení adresářů složky *wwwroot/imagí* pomocí adresy URL *\<http://server_address>/MyImages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Slouží jako výchozí dokument.

Nastavení výchozí domovské stránky poskytuje návštěvníkům logický výchozí bod při návštěvě vašeho webu. Chcete-li obsloužit výchozí stránku bez toho, aby uživatel plně kvalifikován identifikátor URI [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , zavolejte metodu `Startup.Configure`UseDefaultFiles z:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles`musí být volána předtím `UseStaticFiles` , než bude sloužit jako výchozí soubor. `UseDefaultFiles`je přepis adresy URL, který soubor ve skutečnosti neobsluhuje. Povolte middleware statických `UseStaticFiles` souborů prostřednictvím nástroje za účelem obsluhy souboru.

S `UseDefaultFiles`, požadavky na hledání složky pro:

* *Default. htm*
* *Default. html*
* *index. htm*
* *index.html*

První soubor, který se nachází v seznamu, se obsluhuje, jako by se jednalo o plně kvalifikovaný identifikátor URI. Adresa URL prohlížeče bude dál odpovídat požadovanému identifikátoru URI.

Následující kód změní výchozí název souboru na *mydefault. html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>kombinuje funkce `UseStaticFiles`, `UseDefaultFiles`a volitelně. `UseDirectoryBrowser`

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
  * *Default. html*

Následující kód povoluje statické soubory, výchozí soubory a procházení adresářů pro `MyStaticFiles`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser`musí být volána, `EnableDirectoryBrowsing` Pokud je `true`hodnota vlastnosti:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Pomocí hierarchie souborů a předchozího kódu adresy URL překládat následujícím způsobem:

| Identifikátor URI            |                             Odpověď  |
| ------- | ------|
| *http://\<server_address>/staticfiles/images/banner1.SVG*    |      MyStaticFiles/images/banner1. SVG |
| *http://\<server_address>/staticfiles*             |     MyStaticFiles/default.html |

Pokud v adresáři *MyStaticFiles* neexistuje žádný výchozí soubor s názvem, *http://\<server_address>/staticfiles* vrátí seznam adresářů s odkazy kliknutím:

![Seznam statických souborů](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>a <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> provede přesměrování na straně klienta z `http://{SERVER ADDRESS}/StaticFiles` (bez koncového lomítka) na `http://{SERVER ADDRESS}/StaticFiles/` (s koncovým lomítkem). Relativní adresy URL v adresáři *StaticFiles* jsou neplatné bez koncového lomítka.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Třída [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) obsahuje `Mappings` vlastnost, která slouží jako mapování přípon souborů na typy obsahu MIME. V následující ukázce jsou pro známé typy MIME zaregistrovány několik přípon souborů. Přípona *. RTF* je nahrazena a *. mp4* je odebráno.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Viz [typy obsahu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Nestandardní typy obsahu

Middleware statických souborů rozumí téměř 400 známým typům obsahu souborů. Pokud uživatel požádá o soubor s neznámým typem souboru, middleware statických souborů předá požadavek dalšímu middlewaru v kanálu. Pokud žádost nezpracovává žádné middleware, je vrácena odpověď 404, která *nebyla nalezena* . Pokud je povoleno procházení adresářů, zobrazí se v seznamu adresářů odkaz na tento soubor.

Následující kód povoluje obsluhu neznámých typů a vykresluje neznámý soubor jako obrázek:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

V předchozím kódu se jako obrázek vrátí požadavek na soubor s neznámým typem obsahu.

> [!WARNING]
> Povolení [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) je bezpečnostní riziko. Ve výchozím nastavení je zakázané a její použití se nedoporučuje. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu pro obsluhu souborů s nestandardními rozšířeními.

## <a name="serve-files-from-multiple-locations"></a>Obsluhovat soubory z více míst

`UseStaticFiles`a `UseFileServer` výchozím nastavením je poskytovatel souborů odkazující na *wwwroot*. Můžete poskytnout další instance `UseStaticFiles` a `UseFileServer` s dalšími poskytovateli souborů pro obsluhu souborů z jiných umístění. Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Požadavky

> [!WARNING]
> `UseDirectoryBrowser`a `UseStaticFiles` můžou neúniky tajných kódů. Zakázání procházení adresářů v produkčním prostředí se důrazně doporučuje. Pečlivě zkontrolujte, které adresáře jsou povoleny `UseStaticFiles` prostřednictvím `UseDirectoryBrowser`nebo. Celý adresář a jeho podadresáře se stanou veřejně přístupnými. Ukládejte soubory vhodné pro poskytování veřejnosti ve vyhrazeném adresáři, například * \<content_root>/wwwroot*. Oddělte tyto soubory od zobrazení MVC Razor , stránky (jenom 2. x), konfigurační soubory atd.

* Adresy URL obsahu vystaveného nástroji `UseDirectoryBrowser` a `UseStaticFiles` podléhají omezením malých a velkých písmen a znakům základního systému souborů. Například Windows rozlišuje malá a velká písmena&mdash;MacOS a Linux.

* ASP.NET Core aplikace hostované ve službě IIS používají [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) k přeposílání všech požadavků do aplikace, včetně požadavků na statické soubory. Obslužná rutina statického souboru služby IIS se nepoužívá. Nemá možnost zpracovávat požadavky předtím, než je modul zpracován.

* Provedením následujících kroků ve Správci služby IIS odeberte obslužnou rutinu statického souboru služby IIS na úrovni serveru nebo webu:
    1. Přejděte do funkce **moduly** .
    1. V seznamu vyberte **StaticFileModule** .
    1. Na bočním panelu **Akce** klikněte na **Odebrat** .

> [!WARNING]
> Pokud je povolena obslužná rutina statických souborů služby IIS **a** modul ASP.NET Core je nesprávně nakonfigurován, jsou obsluhovány statické soubory. K tomu dojde například v případě, že není nasazen soubor *Web. config* .

* Umístěte soubory kódu (včetně *. cs* a *. cshtml*) mimo [kořenový adresář webu](xref:fundamentals/index#web-root)projektu aplikace. Vytvoří se logické oddělení mezi obsahem aplikace na straně klienta a kódem serveru. Tím zabráníte úniku kódu na straně serveru.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Middleware](xref:fundamentals/middleware/index)
* [Úvod do ASP.NET Core](xref:index)
