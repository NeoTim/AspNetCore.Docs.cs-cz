---
title: Statické soubory v ASP.NET jádru
author: rick-anderson
description: Naučte se obsluhovat a zabezpečovat statické soubory a konfigurovat statické chování middlewaru ve webové aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 95a77defc7e98328e1f4e3615648b1d14485e51e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660123"
---
# <a name="static-files-in-aspnet-core"></a>Statické soubory v ASP.NET jádru

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a Scott [Addie](https://twitter.com/Scott_Addie)

Statické soubory, jako je HTML, CSS, obrázky a JavaScript, jsou datové zdroje, které aplikace ASP.NET Core slouží přímo klientům. Některé konfigurace je nutné povolit zobrazování těchto souborů.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="serve-static-files"></a>Obsluha statických souborů

Statické soubory jsou uloženy v [kořenovém](xref:fundamentals/index#web-root) adresáři projektu. Výchozí adresář je *{content root}/wwwroot*, ale lze jej změnit pomocí metody [UseWebRoot.](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) Další informace naleznete [v tématu Kořenový](xref:fundamentals/index#content-root) obsah a [Kořen webu.](xref:fundamentals/index#web-root)

Webový hostitel aplikace musí být informován o kořenovém adresáři obsahu.

::: moniker range=">= aspnetcore-2.0"

Metoda `WebHost.CreateDefaultBuilder` nastaví kořenový obsah do aktuálního adresáře:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Nastavte kořenový obsah na aktuální adresář vyvoláním [useContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) uvnitř `Program.Main`:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Statické soubory jsou přístupné prostřednictvím cesty vzhledem k [kořenovému adresáři webu](xref:fundamentals/index#web-root). Například šablona projektu **webové aplikace** obsahuje několik složek ve složce *wwwroot:*

* **wwwroot**
  * **Css**
  * **Obrázky**
  * **Js**

Formát URI pro přístup k souboru v podsložce *obrázků* je *http://\<server_address>/images/\<image_file_name>*. Například *http://localhost:9189/images/banner3.svg*.

::: moniker range=">= aspnetcore-2.1"

Pokud cílíte na rozhraní .NET Framework, přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) Pokud cílení .NET Core, [Microsoft.AspNetCore.App metabalíček](xref:fundamentals/metapackage-app) obsahuje tento balíček.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Pokud cílíte na rozhraní .NET Framework, přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) Pokud cílení .NET Core, [Microsoft.AspNetCore.All metabalíček](xref:fundamentals/metapackage) obsahuje tento balíček.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/)

::: moniker-end

Nakonfigurujte [middleware,](xref:fundamentals/middleware/index) který umožňuje zobrazování statických souborů.

### <a name="serve-files-inside-of-web-root"></a>Obsluhování souborů uvnitř kořenového adresáře webu

Vyvolat [metodu UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) v rámci `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Přetížení metody `UseStaticFiles` parameterless označuje soubory v [kořenovém adresáři webu](xref:fundamentals/index#web-root) jako servable. Následující odkazy na značky *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

V předchozím kódu znak `~/` vlnovky odkazuje na [kořenový adresář webu](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Obsluhování souborů mimo kořenový adresář webu

Zvažte hierarchii adresářů, ve které jsou statické soubory, které mají být podávány, umístěny mimo [kořenový adresář webu](xref:fundamentals/index#web-root):

* **wwwroot**
  * **Css**
  * **Obrázky**
  * **Js**
* **MyStaticFiles**
  * **Obrázky**
    * *banner1.svg*

Požadavek může přistupovat k souboru *banner1.svg* takto:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

V předchozím kódu je hierarchie adresářů *MyStaticFiles* veřejně vystavena prostřednictvím segmentu *StaticFiles* URI. Požadavek na *http://\<server_address>/StaticFiles/images/banner1.svg* slouží souboru *banner1.svg.*

Následující značky odkazují na *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Nastavení záhlaví odpovědí HTTP

Objekt [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) lze použít k nastavení hlavičky odpovědi HTTP. Kromě konfigurace statického souboru sloužícího z [kořenového adresáře webu](xref:fundamentals/index#web-root)nastaví `Cache-Control` záhlaví následující kód:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Metoda [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) existuje v balíčku [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)

Soubory byly veřejně cacheable po dobu 10 minut (600 sekund) ve vývojovém prostředí:

![Byla přidána záhlaví odpovědí zobrazující záhlaví Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorizace statického souboru

Middleware statického souboru neposkytuje kontroly autorizace. Všechny soubory, které poskytuje, včetně těch pod *wwwroot*, jsou veřejně přístupné. Poskytování souborů na základě autorizace:

* Uložte je mimo *wwwroot* a jakýkoli adresář přístupný pro middleware statického souboru.
* Podávejte je prostřednictvím akční metody, na kterou se vztahuje autorizace. Vrátí objekt [FileResult:](/dotnet/api/microsoft.aspnetcore.mvc.fileresult)

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Povolení procházení adresářů

Procházení adresářů umožňuje uživatelům webové aplikace zobrazit výpis adresáře a soubory v zadaném adresáři. Procházení adresářů je ve výchozím nastavení z bezpečnostních důvodů zakázáno (viz [Důležité informace).](#considerations) Povolte procházení adresářů vyvoláním metody [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) v : `Startup.Configure`

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Přidejte požadované služby vyvoláním metody [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) z `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Předchozí kód umožňuje procházení adresáře složky *wwwroot/images* pomocí url *http://\<server_address>/MyImages*s odkazy na každý soubor a složku:

![procházení adresářů](static-files/_static/dir-browse.png)

Informace o bezpečnostních rizicích při povolení procházení najdete v [tématu Důležité](#considerations) informace o bezpečnostních rizicích.

Všimněte `UseStaticFiles` si dvou volání v následujícím příkladu. První volání umožňuje zobrazování statických souborů ve složce *wwwroot.* Druhý hovor umožňuje procházení adresáře složky *wwwroot/images* pomocí adresy URL *http://\<server_address>/MyImages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Obsluha výchozího dokumentu

Nastavení výchozí domovské stránky poskytuje návštěvníkům logický výchozí bod při návštěvě webu. Chcete-li zostřit výchozí stránku, aniž by uživatel `Startup.Configure`plně kvalifikoval identifikátor URI, zavolejte metodu [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) z :

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles`musí být `UseStaticFiles` volána před sloužit výchozí soubor. `UseDefaultFiles`je url vypalovačka, která ve skutečnosti neslouží souboru. Povolit statické soubor `UseStaticFiles` middleware přes sloužit souboru.

Pomocí `UseDefaultFiles`aplikace požadavky na složku vyhledávají:

* *Default.htm*
* *default.html*
* *Index.htm*
* *index.html*

První soubor nalezený ze seznamu je obsluhován, jako by požadavek byl plně kvalifikovaný identifikátor URI. Adresa URL prohlížeče nadále odráží požadovaný identifikátor URI.

Následující kód změní výchozí název souboru na *mydefault.html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>kombinuje funkce `UseStaticFiles`aplikace , `UseDefaultFiles`a volitelně `UseDirectoryBrowser`.

Následující kód umožňuje zobrazování statických souborů a výchozího souboru. Procházení adresářů není povoleno.

```csharp
app.UseFileServer();
```

Následující kód vychází z přetížení bez parametrů povolením procházení adresáře:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Zvažte následující hierarchii adresářů:

* **wwwroot**
  * **Css**
  * **Obrázky**
  * **Js**
* **MyStaticFiles**
  * **Obrázky**
    * *banner1.svg*
  * *default.html*

Následující kód umožňuje statické soubory, výchozí soubory `MyStaticFiles`a procházení adresářů :

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser`musí být volána, `true`pokud je hodnota vlastnosti `EnableDirectoryBrowsing` :

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Pomocí hierarchie souborů a předchozího kódu adresy URL řeší následující:

| Identifikátor URI            |                             Odpověď  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<>/StaticFiles server_address*             |     MyStaticFiles/default.html |

Pokud v adresáři *MyStaticFiles* neexistuje žádný soubor s výchozím názvem, *vrátí http:// server_address\<>/StaticFiles* výpis adresáře s odkazy, na který lze kliknout:

![Seznam statických souborů](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>a <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> proveďte přesměrování na `http://{SERVER ADDRESS}/StaticFiles` straně klienta z (bez koncového lomítka) na `http://{SERVER ADDRESS}/StaticFiles/` (s koncovým lomítkem). Relativní adresy URL v adresáři *StaticFiles* jsou neplatné bez koncového lomítka.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Třída [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) obsahuje `Mappings` vlastnost sloužící jako mapování přípon souborů na typy obsahu MIME. V následující ukázce je několik přípon souborů registrováno na známé typy MIME. Rozšíření *.rtf* je nahrazeno a *.mp4* je odebráno.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Viz [typy obsahu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Nestandardní typy obsahu

Statická soubor Middleware chápe téměř 400 známých typů obsahu souborů. Pokud uživatel požaduje soubor s neznámým typem souboru, middleware statického souboru předá požadavek dalšímu middlewaru v kanálu. Pokud žádný middleware zpracovává požadavek, je vrácena odpověď *404 Not Found.* Pokud je povoleno procházení adresářů, zobrazí se v seznamu adresářů odkaz na soubor.

Následující kód umožňuje zobrazování neznámých typů a vykresluje neznámý soubor jako obrázek:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

S předchozím kódem je jako obrázek vrácen požadavek na soubor s neznámým typem obsahu.

> [!WARNING]
> Povolení [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) je bezpečnostní riziko. Je ve výchozím nastavení zakázána a její použití se nedoporučuje. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu k zobrazování souborů s nestandardními příponami.

## <a name="serve-files-from-multiple-locations"></a>Obsluhovat soubory z více míst

`UseStaticFiles`a `UseFileServer` výchozí nastavení poskytovatele souborů ukazuje na *wwwroot*. Můžete poskytnout další instance `UseStaticFiles` `UseFileServer` a s dalšími poskytovateli souborů k poskytování souborů z jiných umístění. Další informace naleznete v [tomto problému GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Požadavky

> [!WARNING]
> `UseDirectoryBrowser`a `UseStaticFiles` může prozradit tajemství. Důrazně doporučujeme zakázat procházení adresářů v produkčním prostředí. Pečlivě zkontrolujte, které `UseStaticFiles` adresáře jsou povoleny prostřednictvím nebo `UseDirectoryBrowser`. Celý adresář a jeho podadresáře se stanou veřejně přístupnými. Uklápěte soubory vhodné pro zobrazování veřejnosti ve vyhrazeném adresáři, například * \<content_root>/wwwroot*. Oddělit tyto soubory od Zobrazení MVC, Razor Stránky (2.x pouze), konfigurační soubory, atd.

* Adresy URL pro obsah `UseDirectoryBrowser` vystavený a `UseStaticFiles` podléhají omezení rozlišování malých a velkých písmen a omezení znaků základního systému souborů. Například Windows je malá&mdash;a velká písmena macOS a Linux nejsou.

* ASP.NET základní aplikace hostované ve službě IIS používají [ASP.NET core modul](xref:host-and-deploy/aspnet-core-module) k předávání všech požadavků do aplikace, včetně požadavků na statické soubory. Obslužná rutina statického souboru iis se nepoužívá. Nemá šanci zpracovat požadavky před jejich zpracováním modulem.

* Chcete-li odebrat obslužnou rutinu statického souboru služby IIS na úrovni serveru nebo webu, proveďte následující kroky:
    1. Přejděte na funkci **Moduly.**
    1. V seznamu vyberte **StaticFileModule.**
    1. Na postranním panelu **Akce** klikněte na **Odebrat.**

> [!WARNING]
> Pokud je povolena obslužná rutina statického souboru služby IIS **a** ASP.NET je nesprávně nakonfigurován základní modul, jsou obsluhovány statické soubory. K tomu dochází například v případě, že soubor *web.config* není nasazen.

* Umístěte soubory kódu (včetně *.cs* a *.cshtml)* mimo [kořen ový adresář](xref:fundamentals/index#web-root)projektu aplikace . Proto je vytvořeno logické oddělení mezi obsahem aplikace na straně klienta a kódem založeným na serveru. Tím zabráníte úniku kódu na straně serveru.

## <a name="additional-resources"></a>Další zdroje

* [Middleware](xref:fundamentals/middleware/index)
* [Úvod do ASP.NET Core](xref:index)
