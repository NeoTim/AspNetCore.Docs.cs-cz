---
title: Statické soubory v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak poskytovat zabezpečení statické soubory a konfigurace statického souboru hostování chování middlewaru ve webové aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/static-files
ms.openlocfilehash: 114fee0795977043f3a74a81a15923a8bf5faf6b
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208632"
---
# <a name="static-files-in-aspnet-core"></a>Statické soubory v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Scott Addie](https://twitter.com/Scott_Addie)

Statické soubory, jako jsou HTML, CSS, obrázky a JavaScript, představují majetek, který obsluhuje aplikace ASP.NET Core přímo pro klienty. Některé konfigurace je potřeba povolit poskytování obsahu těchto souborů.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Doručování statických souborů

Statické soubory jsou uloženy v kořenovém adresáři vašeho projektu web. Výchozí adresář je  *\<content_root > / wwwroot*, ale můžete změnit prostřednictvím [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) metody. Zobrazit [obsahu kořenové](xref:fundamentals/index#content-root) a [kořenový adresář webové](xref:fundamentals/index#web-root) Další informace.

Hostitel webové aplikace musí být informováni obsahu kořenový adresář.

::: moniker range=">= aspnetcore-2.0"

`WebHost.CreateDefaultBuilder` Metoda nastaví kořenu obsahu do aktuálního adresáře:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Nastavení obsahu kořenovém adresáři aktuální adresář vyvoláním [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) uvnitř `Program.Main`:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Statické soubory jsou přístupné přes cestu relativní vzhledem k kořenový adresář webové. Například **webovou aplikaci** šablonu projektu obsahuje několik složek v rámci *wwwroot* složky:

* **wwwroot**
  * **css**
  * **Bitové kopie**
  * **js**

Formát identifikátoru URI pro přístup k souboru v *image* je podsložka *http://\<server_address > /images/\<image_file_name >*. Například *http://localhost:9189/images/banner3.svg*.

::: moniker range=">= aspnetcore-2.1"

Pokud se zaměřujete na rozhraní .NET Framework, přidejte [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do svého projektu balíček. Pokud je zaměřen na .NET Core [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) zahrnuje tento balíček.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Pokud se zaměřujete na rozhraní .NET Framework, přidejte [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do svého projektu balíček. Pokud je zaměřen na .NET Core [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage) zahrnuje tento balíček.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Přidat [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do svého projektu balíček.

::: moniker-end

Konfigurace [middleware](xref:fundamentals/middleware/index) umožňující poskytování obsahu statických souborů.

### <a name="serve-files-inside-of-web-root"></a>Soubory v rámci kořenový adresář webové poskytovat

Vyvolat [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) metody v rámci `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Bezparametrová `UseStaticFiles` soubory v kořenovém adresáři webové jako servable označí přetížení metody. Následující odkazy na kód *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

V předchozím kódu znak tilda `~/` odkazuje na webroot. Další informace najdete v tématu [kořenový adresář webové](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Soubory mimo kořenový adresář webové poskytovat

Vezměte v úvahu hierarchii adresářů, ve kterém se obsluhovat statické soubory nacházejí mimo kořenový adresář webové:

* **wwwroot**
  * **css**
  * **Bitové kopie**
  * **js**
* **MyStaticFiles**
  * **Bitové kopie**
    * *banner1.svg*

Žádost o přístup *banner1.svg* souboru nakonfigurováním Middleware statické soubory:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

V předchozím kódu *MyStaticFiles* hierarchii adresářů jsou veřejně dostupné prostřednictvím *StaticFiles* segment identifikátoru URI. Požadavek na *http://\<server_address > /StaticFiles/images/banner1.svg* slouží *banner1.svg* souboru.

Následující odkazy na kód *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Nastavit hlavičky HTTP odpovědi

A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) objekt lze použít k nastavení hlavičky HTTP odpovědi. Kromě poskytování obsahu statického souboru z kořenový adresář webové konfigurace, následující kód nastaví `Cache-Control` hlavičky:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

[HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) metoda nachází [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) balíčku.

Soubory byly provedeny veřejně možné ukládat do mezipaměti po dobu 10 minut (600 sekund) ve vývojovém prostředí:

![Byla přidána hlavičky odpovědi zobrazující hlavičku Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Statický soubor autorizace

Middleware statické soubory neposkytuje kontroly autorizace. Všechny soubory obsluhuje, včetně těch *wwwroot*, jsou veřejně přístupné. Poskytování souborů na základě autorizace:

* Store je mimo *wwwroot* a ke každému adresáři přístupné pro Middleware statické soubory.
* Zajišťovat obsluhu prostřednictvím metodu akce, pro které je použito autorizace. Vrátit [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) objektu:

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Povolí procházení adresáře

Procházení adresářů umožňuje uživatelům vaší webové aplikace najdete v seznamu adresářů a souborů v rámci zadaného adresáře. Procházení adresářů je zakázané ve výchozím nastavení z bezpečnostních důvodů (viz [aspekty](#considerations)). Povolit procházení vyvoláním adresářů [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) metoda ve `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Přidání požadovaných služeb vyvoláním [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) metodu z `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Předchozí kód umožňuje procházení adresáře *wwwroot/imagí* složky pomocí adresy URL *http://\<server_address > / MyImages*, s odkazy na všechny soubory a složky:

![procházení adresářů](static-files/_static/dir-browse.png)

Zobrazit [aspekty](#considerations) na bezpečnostní rizika při povolování procházení.

Všimněte si, dvě `UseStaticFiles` volá v následujícím příkladu. Umožňuje při obsluze statických souborů v prvním volání *wwwroot* složky. Druhé volání povolí procházení adresáře *wwwroot/imagí* složky pomocí adresy URL *http://\<server_address > / MyImages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Výchozí dokument

Nastavení výchozí domovskou stránku poskytuje návštěvníků logické výchozí bod při návštěvě webu. Chcete-li poskytovat výchozí stránku bez plně kvalifikovaného identifikátoru URI uživatele, zavolejte [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) metodu z `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles` musí být volána před `UseStaticFiles` poskytovat výchozí soubor. `UseDefaultFiles` je RW adresu URL, který bariéru ve skutečnosti soubor. Povolit statický soubor Middleware prostřednictvím `UseStaticFiles` poskytovat souboru.

S `UseDefaultFiles`, požadavky na složku vyhledávání:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

První soubor nalezen v seznamu obsluhuje jakoby byly plně kvalifikovaného identifikátoru URI požadavku. Adresa URL prohlížeče pokračuje tak, aby odrážely identifikátoru URI požadavku.

Následující kód změní výchozí název souboru *mydefault.html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) kombinuje funkce `UseStaticFiles`, `UseDefaultFiles`, a `UseDirectoryBrowser`.

Následující kód umožní obsluhující statické soubory a výchozí soubor. Procházení adresářů není povoleno.

```csharp
app.UseFileServer();
```

Následující kód staví na bez parametrů přetížení tím, že procházení adresářů:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Vezměte v úvahu následující hierarchii adresářů:

* **wwwroot**
  * **css**
  * **Bitové kopie**
  * **js**
* **MyStaticFiles**
  * **Bitové kopie**
    * *banner1.svg*
  * *default.html*

Následující kód umožní statické soubory, výchozí soubory a adresáře procházení `MyStaticFiles`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser` musí být voláno, když `EnableDirectoryBrowsing` hodnota vlastnosti je `true`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Pomocí hierarchie souborů a předcházející kódu, adresy URL vyřešíte takto:

| Identifikátor URI            |                             Odpověď  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

Pokud neexistuje žádný soubor s názvem výchozí v *MyStaticFiles* adresáři *http://\<server_address > / StaticFiles* vrátí výpis s odkazy kliknout, čímž adresáře:

![Seznam statické soubory](static-files/_static/db2.png)

> [!NOTE]
> `UseDefaultFiles` a `UseDirectoryBrowser` použijte adresu URL *http://\<server_address > / StaticFiles* bez do adresy koncové lomítko k aktivaci na straně klienta přesměrovat na *http://\<server_address > / StaticFiles /*. Všimněte si, že přidání do adresy koncové lomítko. Relativní adresy URL v rámci dokumenty jsou považovány za neplatné bez koncové lomítko.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

[FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) obsahuje třídy `Mappings` vlastnost slouží jako mapování přípony souborů pro typy MIME obsahu. V následujícím příkladu je několik přípony souborů zaregistrovaná známé typy MIME. *.Rtf* nahrazuje rozšíření a *MP4* se odebere.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Zobrazit [typy MIME obsahu](http://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Nestandardní typy obsahu

Middleware statické soubory rozumí téměř 400 souborů známých typů obsahu. Pokud uživatel požaduje souboru se neznámý typ souborů, Middleware statické soubory požadavek předá do další middleware v kanálu. Pokud žádné middleware zpracovává žádost, *404 Nenalezeno* vrátí odpověď. Pokud je povolené procházení adresáře, zobrazí se odkaz na soubor v seznamu adresářů.

Následující kód umožní obsluhující neznámé typy a vykreslí Neznámý soubor jako image:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Předchozí kód žádost o soubor s Neznámý typ obsahu se vrátí jako obrázek.

> [!WARNING]
> Povolení [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) představuje bezpečnostní riziko. Ve výchozím nastavení je zakázána a jeho použití se nedoporučuje. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu se, že soubory se nestandardní rozšíření.

### <a name="considerations"></a>Požadavky

> [!WARNING]
> `UseDirectoryBrowser` a `UseStaticFiles` může způsobit únik těchto tajných kódů. Důrazně doporučujeme zakázat procházení adresáře v produkčním prostředí. Pečlivě si prostudujte adresáře, které jsou povolené prostřednictvím `UseStaticFiles` nebo `UseDirectoryBrowser`. Celý adresář a jeho podadresářích stanou veřejně dostupné. Store soubory vhodný pro poskytování obsahu veřejně vyhrazené adresář, například  *\<content_root > / wwwroot*. Tyto soubory oddělte od zobrazení MVC Razor Pages (pouze 2.x), konfigurační soubory, atd.

* Adresy URL pro obsah s `UseDirectoryBrowser` a `UseStaticFiles` musí dodržovat rozlišování velikosti písmen a omezení pro znaky základní systému souborů. Například, velká a malá písmena Windows&mdash;macOS a Linux nejsou.

* Aplikace ASP.NET Core, které jsou hostované v IIS použít [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) předávat všechny požadavky na aplikace, včetně žádostí o statický soubor. Obslužná rutina statického souboru službě IIS se nepoužívá. Nemá žádné riziko pro zpracování žádostí, než se zpracování modulem.

* Proveďte následující kroky ve Správci služby IIS odebrat obslužnou rutinu statických souborů služby IIS na úrovni serveru nebo webu:
    1. Přejděte **moduly** funkce.
    1. Vyberte **iisver** v seznamu.
    1. Klikněte na tlačítko **odebrat** v **akce** bočním panelu.

> [!WARNING]
> Pokud je obslužná rutina statického souboru službě IIS povolená **a** modul ASP.NET Core není správně nakonfigurovaná, budou obsluhovat statické soubory. K tomu dojde, například, pokud *web.config* soubor není nasazen.

* Umístěte soubory kódu (včetně *.cs* a *.cshtml*) mimo kořenový adresář webové aplikace projektu. Logické rozdělení se proto vytvoří mezi obsah na straně klienta a kód založený na server aplikace. To zabraňuje úniku kód na straně serveru.

## <a name="additional-resources"></a>Další zdroje

* [Middleware](xref:fundamentals/middleware/index)
* [Úvod do ASP.NET Core](xref:index)
