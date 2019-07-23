---
title: Správa balíčků na straně klienta nástrojem Bower v ASP.NET Core
author: rick-anderson
description: Správa balíčků na straně klienta nástrojem Bower.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 08/09/2018
uid: client-side/bower
ms.openlocfilehash: 1292b75bdfe1cea56d9c8bc282b0dd90fc945a33
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813533"
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a>Správa balíčků na straně klienta nástrojem Bower v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel rýže](https://twitter.com/noelrice1), a [Scott Addie](https://scottaddie.com)

> [!IMPORTANT]
> Zatímco Bower byla zachována jeho programu doporučujeme používat jiné řešení. [Správce knihovny](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (LibMan zkráceně) je nástroj pro získání nové knihoven na straně klienta ze sady Visual Studio (Visual Studio 15,8 nebo novější). Další informace naleznete v tématu <xref:client-side/libman/index>. Bower je podporováno v sadě Visual Studio přes verzi 15.5.
>
> Yarn s Webpacku je jeden oblíbenou alternativu, pro kterou [pokyny k migraci](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) jsou k dispozici.

[Bower](https://bower.io/) zavolá sama sebe "Správce balíčků pro web". Vyplní void zanechaný neschopnost Nugetu doručování statických souborů obsahu v ekosystému .NET. Pro projekty ASP.NET Core, jsou tyto statické soubory přináší knihoven na straně klienta, jako je [jQuery](https://jquery.com/) a [Bootstrap](https://getbootstrap.com/). Pro knihovny .NET, můžete dál používat [NuGet](https://www.nuget.org/) Správce balíčků.

Proces sestavení nové projekty vytvořené pomocí šablon projektů ASP.NET Core, nastavení na straně klienta. [jQuery](https://jquery.com/) a [Bootstrap](https://getbootstrap.com/) jsou nainstalované a podporované Bower.

Balíčky na straně klienta jsou uvedené v *bower.json* souboru. Šablony projektů ASP.NET Core nakonfiguruje *bower.json* s Bootstrap, jQuery a k ověřování jQuery.

V tomto kurzu přidáme podporu [knihovnou aplikací Font Awesome](http://fontawesome.io). Dá se nainstalovat balíčky bower s **spravovat balíčky Bower** uživatelského rozhraní nebo ručně v *bower.json* souboru.

### <a name="installation-via-manage-bower-packages-ui"></a>Instalace přes spravovat balíčky Bower uživatelského rozhraní

* Vytvoření nové aplikace ASP.NET Core Web s **webová aplikace ASP.NET Core (.NET Core)** šablony. Vyberte **webovou aplikaci** a **bez ověřování**.

* Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **spravovat balíčky Bower** (případně v hlavní nabídce **projektu** > **spravovat balíčky Bower**).

* V **Bower: \<název projektu\>**  okna, klikněte na kartu "Procházet" a vyfiltrujte seznam balíčků tak, že zadáte `font-awesome` do vyhledávacího pole:

  ![spravovat balíčky bower](bower/_static/manage-bower-packages.png)

* Ujistěte se, že "uložit změny do *bower.json*" zaškrtávací políčko zaškrtnuto. Z rozevíracího seznamu vyberte verzi a klikněte na tlačítko **nainstalovat** tlačítko. **Výstup** okno zobrazuje podrobné informace o instalaci.

### <a name="manual-installation-in-bowerjson"></a>Ruční instalace v bower.json

Otevřít *bower.json* a přidejte k závislostem "knihovnou aplikací font awesome". Technologie IntelliSense zobrazuje dostupné balíčky. Když je vybraný balíček, se zobrazí dostupné verze. Následující obrázky jsou starší a nebudou shodovat.

![IntelliSense Průzkumníku balíčků bower](bower/_static/add-package.png)

![verze technologie IntelliSense pro bower](bower/_static/version-intelliSense.png)

Bower používá [sémantické správy verzí](https://semver.org/) k uspořádání závislosti. Sémantické správy verzí, označované také jako SemVer identifikuje balíčky se schéma číslování \<hlavní >.\< podverze >. \<opravy >. Technologie IntelliSense zjednodušuje tím, že zobrazuje pouze několik běžné volby sémantické správy verzí. Horní položku v seznamu technologie IntelliSense (4.6.3 v příkladu výše) se považuje za nejnovější stabilní verze balíčku. Symbol stříšky (^) odpovídá nejnovější hlavní verzi a tilda (~) odpovídá nejnovější dílčí verzi.

Uložit *bower.json* souboru. Visual Studio sleduje *bower.json* změny v souboru. Po uložení, *nainstalovat bower* spuštění příkazu. Najdete v okně Výstup **Bower/npm** zobrazení pro přesný příkaz spustit.

Otevřít *.bowerrc* soubor *bower.json*. `directory` Je nastavena na *wwwroot/lib* označující umístění Bower nainstaluje balíček prostředků.

```json
{
 "directory": "wwwroot/lib"
}
```

Pole Hledat v Průzkumníku řešení můžete použít k vyhledání a knihovnou aplikací font awesome balíček zobrazíte.

Otevřít *Views\Shared\_Layout.cshtml* a knihovnou aplikací font awesome soubor CSS přidejte do prostředí [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) pro `Development`. Z Průzkumníka řešení, přetáhněte a umístěte *písmo awesome.css* uvnitř `<environment names="Development">` elementu.

[!code-html[](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

V produkční aplikaci přidat *písmo awesome.min.css* k pomocná rutina značky prostředí pro `Staging,Production`.

Nahraďte obsah *Views\Home\About.cshtml* Razor souboru následujícím kódem:

[!code-html[](bower/sample/About.cshtml)]

Spusťte aplikaci a přejděte do zobrazení o zkontrolujte, jestli funguje knihovnou aplikací font awesome balíčku.

## <a name="exploring-the-client-side-build-process"></a>Zkoumání proces sestavení na straně klienta

Většina šablony projektů ASP.NET Core je již nakonfigurován pro použití Bower. Tento návod další začíná prázdný projekt ASP.NET Core a přidá každého jednotlivého ručně, abyste získali představu pro použití Bower v projektu. Zobrazí se, co se stane strukturu projektu a modul runtime výstup jak každé změně konfigurace.

Obecné kroky pro použití s Bowerem proces sestavení na straně klienta jsou:

* Definujte balíčky používané ve vašem projektu. <!-- once defined, you don't need to download them, VS does -->
* Referenční dokumentace k balíčkům z webových stránek.

### <a name="define-packages"></a>Definování balíčků

Jakmile se seznam balíčků v *bower.json* souboru, Visual Studio stáhne. Následující příklad používá k načtení jQuery a Bootstrap pro Bower *wwwroot* složky.

* Vytvoření nové aplikace ASP.NET Core Web s **webová aplikace ASP.NET Core (.NET Core)** šablony. Vyberte **prázdný** šablony projektu a klikněte na tlačítko **OK**.

* V Průzkumníku řešení klikněte pravým tlačítkem na projekt > **přidat novou položku** a vyberte **konfigurační soubor Bower**. Poznámka: A *.bowerrc* také přidá soubor.

* Otevřít *bower.json*a přidat jquery a bootstrap na `dependencies` oddílu. Výsledná *bower.json* soubor bude vypadat jako v následujícím příkladu. Verze bude v průběhu času měnit a nemusí odpovídat na následujícím obrázku.

[!code-json[](bower/sample/bower.json?highlight=5,6)]

* Uložit *bower.json* souboru.

  Ověřte, projekt obsahuje *bootstrap* a *jQuery* adresáře v *wwwroot/lib*. Používá pro bower *.bowerrc* sloužící k instalaci prostředky v *wwwroot/lib*.

  Poznámka: Uživatelské rozhraní "Spravovat balíčky Bower" poskytuje alternativu k úpravě ručních souboru.

### <a name="enable-static-files"></a>Povolte statické soubory

* Přidat `Microsoft.AspNetCore.StaticFiles` do projektu balíček NuGet.
* Povolte statické soubory ke zpracování se [middleware se statickými soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions). Přidejte volání do [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions) k `Configure` metoda `Startup`.

[!code-csharp[](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a>Odkaz na balíčky

V této části vytvoříte stránku HTML k ověření, že může přistupovat k nasazených balíčků.

* Přidejte novou stránku HTML s názvem *Index.html* k *wwwroot* složky. Poznámka: Je nutné přidat soubor HTML k *wwwroot* složky. Ve výchozím nastavení, statický obsah nelze zpracovat v mimo *wwwroot*. Zobrazit [statické soubory](xref:fundamentals/static-files) Další informace.

  Nahraďte obsah *Index.html* následujícím kódem:

[!code-html[](bower/sample/Index.html)]

* Spusťte aplikaci a přejděte do `http://localhost:<port>/Index.html`. Můžete také s *Index.html* otevřen, stiskněte klávesu `Ctrl+Shift+W`. Ověřte, že se použije jumbotron stylu, kód jazyka jQuery reaguje, když dojde ke kliknutí na tlačítko a, že na spouštěcí tlačítko změní stav.

  ![jumbotron stylem](bower/_static/jumbotron.png)
