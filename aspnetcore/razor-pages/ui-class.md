---
title: Reusable Razor UI in class libraries with ASP.NET Core
author: Rick-Anderson
description: Vysvětluje, jak vytvořit opakovaně použitelné holicí strojek uI pomocí částečných zobrazení v knihovně tříd v ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667564"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Vytvoření opakovaně použitelného ui pomocí projektu knihovny tříd razor v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Zobrazení holicí strojek, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [komponenty Zobrazení](xref:mvc/views/view-components)a datové modely mohou být integrovány do knihovny tříd Razor (RCL). RCL lze zabalit a znovu použít. Aplikace mohou obsahovat seznam RCL a přepsat zobrazení a stránky, které obsahuje. Když se ve webové aplikaci a v RCL nachází zobrazení, částečné zobrazení nebo stránka Razor, má přednost značka Razor (*soubor .cshtml)* ve webové aplikaci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="create-a-class-library-containing-razor-ui"></a>Vytvoření knihovny tříd obsahující razor uI

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V sadě Visual Studio vyberte **Vytvořit nový projekt**.
* Vyberte **knihovnu** > tříd razor **další**.
* Pojmenujte knihovnu (například "RazorClassLib"), > **Vytvořit**. Chcete-li zabránit kolizi názvu souboru s knihovnou generovaného `.Views`zobrazení, zajistěte, aby název knihovny neskončil .
* Pokud potřebujete podporovat zobrazení, vyberte **stránky a zobrazení podpory.** Ve výchozím nastavení jsou podporovány pouze razor stránky. Vyberte **Vytvořit**.

Šablona třídy Razor (RCL) ve výchozím nastavení ve výchozím nastavení na vývoj komponenty Razor. Možnost **Podpora stránek a zobrazení** podporuje stránky a zobrazení.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Z příkazového řádku `dotnet new razorclasslib`spusťte . Příklad:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Šablona třídy Razor (RCL) ve výchozím nastavení ve výchozím nastavení na vývoj komponenty Razor. Pass `--support-pages-and-views` možnost`dotnet new razorclasslib --support-pages-and-views`( ) poskytovat podporu pro stránky a zobrazení.

Další informace naleznete [v tématu dotnet new](/dotnet/core/tools/dotnet-new). Chcete-li zabránit kolizi názvu souboru s knihovnou generovaného `.Views`zobrazení, zajistěte, aby název knihovny neskončil .

---

Přidejte soubory Razor do rcl.

Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *Oblasti.* Viz [Rozložení Stránek RCL](#rcl-pages-layout) k vytvoření rcl, `~/Areas/Pages`který zveřejňuje obsah v aplikaci `~/Pages` , nikoli .

## <a name="reference-rcl-content"></a>Referenční obsah RCL

RCL lze odkazovat:

* NuGet balíček. Viz [Vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) a [vytvořit a publikovat balíček NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{Název_projektu}.csproj*. Viz [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Přepsání zobrazení, částečných zobrazení a stránek

Když se ve webové aplikaci a v RCL nachází zobrazení, částečné zobrazení nebo stránka Razor, má přednost značka Razor (*soubor .cshtml)* ve webové aplikaci. Například přidejte *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* do WebApp1 a Stránka1 ve WebApp1 bude mít přednost před stránkou1 v RCL.

V ukázkovém stažení přejmenujte *WebApp1/Areas/MyFeature2* na *WebApp1/Areas/MyFeature,* abyste otestovali prioritu.

Zkopírujte částečné zobrazení *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* do částečného zobrazení *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aktualizujte značky označující nové umístění. Vytvořte a spusťte aplikaci a ověřte, zda se používá verze částečné části aplikace.

### <a name="rcl-pages-layout"></a>Rozložení Stránky RCL

Chcete-li odkazovat na obsah RCL, jako by byl součástí složky *Stránky* webové aplikace, vytvořte projekt RCL s následující strukturou souborů:

* *RazoruiClassLib/Stránky*
* *RazoruiClassLib/Stránky/Sdílené*

Předpokládejme, že *RazorUIClassLib/Pages/Shared* obsahuje dva částečné soubory: *_Header.cshtml* a *_Footer.cshtml*. Značky `<partial>` by mohly být přidány do souboru *_Layout.cshtml:*

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Vytvoření rcl se statickými datovými zdroji

RCL může vyžadovat doprovodné statické prostředky, na které lze odkazovat rcl nebo náročnou aplikací RCL. ASP.NET Core umožňuje vytváření zapsanic Než LL, které obsahují statické prostředky, které jsou k dispozici pro náročné aplikace.

Chcete-li zahrnout doprovodné datové zdroje jako součást rcl, vytvořte složku *wwwroot* v knihovně tříd a do této složky zahrňte všechny požadované soubory.

Při balení RCL jsou všechny doprovodné datové zdroje ve složce *wwwroot* automaticky zahrnuty do balíčku.

### <a name="exclude-static-assets"></a>Vyloučit statická aktiva

Chcete-li vyloučit statické datové zdroje, `$(DefaultItemExcludes)` přidejte požadovanou cestu vyloučení do skupiny vlastností v souboru projektu. Samostatné položky s`;`středníkem ( ).

V následujícím příkladu není šablona stylů *lib.css* ve složce *wwwroot* považována za statický datový zdroj a není zahrnuta v publikovaném seznamu RCL:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Integrace psacího stroje

Zahrnutí souborů jazyka TypeScript do rcl:

1. Umístěte soubory jazyka TypeScript (*Ts*) mimo složku *wwwroot.* Soubory můžete například umístit do složky *Klient.*

1. Nakonfigurujte výstup sestavení jazyka TypeScript pro složku *wwwroot.* Nastavte `TypescriptOutDir` vlastnost uvnitř `PropertyGroup` a v souboru projektu:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Zahrňte cíl Jazyka TypeScript `ResolveCurrentProjectStaticWebAssets` jako závislost cíle přidáním `PropertyGroup` následujícího cíle do souboru projektu:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Využití obsahu z odkazované hod-rcl

Soubory obsažené ve složce *wwwroot* rcl jsou vystaveny rcl nebo náročné aplikace `_content/{LIBRARY NAME}/`pod předponou . Například knihovna s názvem *Razor.Class.Lib* má za `_content/Razor.Class.Lib/`následek cestu ke statickému obsahu na . Při vytváření balíčku NuGet a název sestavení není stejný jako ID balíčku, použijte `{LIBRARY NAME}`ID balíčku pro .

Náročná aplikace odkazuje na statické datové zdroje `<script>` `<style>`poskytované `<img>`knihovnou pomocí značek , , a dalších značek HTML. Náročná aplikace musí mít [povolenou podporu statických souborů](xref:fundamentals/static-files) v `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Při spuštění náročné aplikace z`dotnet run`výstupu sestavení ( ), statické webové prostředky jsou povoleny ve výchozím nastavení ve vývojovém prostředí. Chcete-li podporovat prostředky v jiných prostředích `UseStaticWebAssets` při spuštění z výstupu sestavení, volejte tvůrce hostitele v *Program.cs*:

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

Volání `UseStaticWebAssets` není vyžadováno při spuštění aplikace z`dotnet publish`publikovaného výstupu ( ).

### <a name="multi-project-development-flow"></a>Vývoj více projektů

Při spuštění náročné aplikace:

* Datové zdroje v rcl zůstávají v původních složkách. Datové zdroje se nepřesunou do náročné aplikace.
* Všechny změny v rámci *složky wwwroot* RCL se projeví v náročné aplikaci po rcl je znovu a bez opětovnésestavení náročné aplikace.

Při sestavení rcl je vytvořen manifest, který popisuje umístění statických webových datových zdrojů. Náročná aplikace čte manifest za běhu, aby spotřebovával prostředky z odkazovaných projektů a balíčků. Když je nový datový zdroj přidán do rcl, rcl musí být znovu sestaveny aktualizovat svůj manifest před náročné aplikace přístup k nového datového zdroje.

### <a name="publish"></a>Publikování

Po publikování aplikace se doprovodné datové zdroje ze všech odkazovaných projektů a balíčků zkopírují `_content/{LIBRARY NAME}/`do *složky wwwroot* publikované aplikace v části .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Zobrazení holicí strojek, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [komponenty Zobrazení](xref:mvc/views/view-components)a datové modely mohou být integrovány do knihovny tříd Razor (RCL). RCL lze zabalit a znovu použít. Aplikace mohou obsahovat seznam RCL a přepsat zobrazení a stránky, které obsahuje. Když se ve webové aplikaci a v RCL nachází zobrazení, částečné zobrazení nebo stránka Razor, má přednost značka Razor (*soubor .cshtml)* ve webové aplikaci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="create-a-class-library-containing-razor-ui"></a>Vytvoření knihovny tříd obsahující razor uI

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* Vyberte **ASP.NET základní webovou aplikaci**.
* Pojmenujte knihovnu (například "RazorClassLib") > **OK**. Chcete-li zabránit kolizi názvu souboru s knihovnou generovaného `.Views`zobrazení, zajistěte, aby název knihovny neskončil .
* Ověřte, ASP.NET je **vybrána možnost Core 2.1** nebo novější.
* Vyberte **knihovnu** > tříd razor **OK**.

RCL má následující soubor projektu:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Z příkazového řádku `dotnet new razorclasslib`spusťte . Příklad:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Další informace naleznete [v tématu dotnet new](/dotnet/core/tools/dotnet-new). Chcete-li zabránit kolizi názvu souboru s knihovnou generovaného `.Views`zobrazení, zajistěte, aby název knihovny neskončil .

---

Přidejte soubory Razor do rcl.

Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *Oblasti.* Viz [Rozložení Stránek RCL](#rcl-pages-layout) k vytvoření rcl, `~/Areas/Pages`který zveřejňuje obsah v aplikaci `~/Pages` , nikoli .

## <a name="reference-rcl-content"></a>Referenční obsah RCL

RCL lze odkazovat:

* NuGet balíček. Viz [Vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) a [vytvořit a publikovat balíček NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{Název_projektu}.csproj*. Viz [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Návod: Vytvoření projektu RCL a použití z projektu Razor Pages

Můžete si stáhnout [celý projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) a otestovat jej, nikoli jej vytvořit. Ukázkové stažení obsahuje další kód a odkazy, které usnadňují testování projektu. Můžete zanechat zpětnou vazbu v [tomto problému GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098) s komentáři ke stažení ukázky versus podrobný návod.

### <a name="test-the-download-app"></a>Testování aplikace pro stažení

Pokud jste nestáhli dokončenou aplikaci a raději byste vytvořili projekt návodu, přejděte k [další části](#create-an-rcl).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Otevřete soubor *.sln* v sadě Visual Studio. Spusťte aplikaci.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Z příkazového řádku v *adresáři cli* vytvořte Seznam RCL a webovou aplikaci.

```dotnetcli
dotnet build
```

Přejděte do adresáře *WebApp1* a spusťte aplikaci:

```dotnetcli
dotnet run
```

---

Postupujte podle pokynů v [aplikaci Test WebApp1](#test-webapp1)

## <a name="create-an-rcl"></a>Vytvoření rcl

V této části je vytvořen rcl. Razor soubory jsou přidány do RCL.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvořte projekt RCL:

* V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* Vyberte **ASP.NET základní webovou aplikaci**.
* Název aplikace **RazorUIClassLib** > **OK**.
* Ověřte, ASP.NET je **vybrána možnost Core 2.1** nebo novější.
* Vyberte **knihovnu** > tříd razor **OK**.
* Přidejte soubor částečného zobrazení Razor s názvem *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Z příkazového řádku spusťte následující:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Předchozí příkazy:

* Vytvoří `RazorUIClassLib` RCL.
* Vytvoří _Message stránku a přidá ji do rcl. Parametr `-np` vytvoří stránku `PageModel`bez .
* Vytvoří soubor [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) a přidá jej do rcl.

Soubor *_ViewStart.cshtml* je nutné použít rozložení projektu Razor Pages (který je přidán v další části).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Přidání souborů a složek Razor do projektu

* Nahraďte značky v *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* následujícím kódem:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Nahraďte značky v *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* následujícím kódem:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`je nutné použít částečný`<partial name="_Message" />`pohled ( ). Místo zahrnutí `@addTagHelper` směrnice můžete přidat soubor *_ViewImports.cshtml.* Příklad:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Další informace o *_ViewImports.cshtml*najdete [v tématu Import sdílených směrnic](xref:mvc/views/layout#importing-shared-directives)

* Vytvořte knihovnu tříd a ověřte, že neexistují žádné chyby kompilátoru:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Výstup sestavení obsahuje *RazorUIClassLib.dll* a *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* obsahuje zkompilovaný obsah Razor.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Použití knihovny Razor UI z projektu Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvořte webovou aplikaci Razor Pages:

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na řešení > **Přidat** > **nový projekt**.  
* Vyberte **ASP.NET základní webovou aplikaci**.
* Pojmenujte aplikaci **WebApp1**.
* Ověřte, ASP.NET je **vybrána možnost Core 2.1** nebo novější.
* Vyberte **webovou aplikaci** > **OK**.

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **WebApp1** a vyberte **nastavit jako počáteční projekt**.
* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **webapp1** a vyberte **možnost Sestavení závislostí** > **projektů**.
* Zkontrolujte **RazorUIClassLib** jako závislost **WebApp1**.
* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **webapp1** a vyberte **přidat** > **odkaz**.
* V dialogovém **okně Správce odkazů** zaškrtněte **políčko RazorUIClassLib** > **OK**.

Spusťte aplikaci.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Vytvořte webovou aplikaci Razor Pages a soubor řešení obsahující aplikaci Razor Pages a RCL:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Vytvoření a spuštění webové aplikace:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Test WebApp1

Vyhledejte `/MyFeature/Page1` ověření, zda je používána knihovna tříd rozhraní Razor.

## <a name="override-views-partial-views-and-pages"></a>Přepsání zobrazení, částečných zobrazení a stránek

Když se ve webové aplikaci a v RCL nachází zobrazení, částečné zobrazení nebo stránka Razor, má přednost značka Razor (*soubor .cshtml)* ve webové aplikaci. Například přidejte *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* do WebApp1 a Stránka1 ve WebApp1 bude mít přednost před stránkou1 v RCL.

V ukázkovém stažení přejmenujte *WebApp1/Areas/MyFeature2* na *WebApp1/Areas/MyFeature,* abyste otestovali prioritu.

Zkopírujte částečné zobrazení *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* do částečného zobrazení *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aktualizujte značky označující nové umístění. Vytvořte a spusťte aplikaci a ověřte, zda se používá verze částečné části aplikace.

### <a name="rcl-pages-layout"></a>Rozložení Stránky RCL

Chcete-li odkazovat na obsah RCL, jako by byl součástí složky *Stránky* webové aplikace, vytvořte projekt RCL s následující strukturou souborů:

* *RazoruiClassLib/Stránky*
* *RazoruiClassLib/Stránky/Sdílené*

Předpokládejme, že *RazorUIClassLib/Pages/Shared* obsahuje dva částečné soubory: *_Header.cshtml* a *_Footer.cshtml*. Značky `<partial>` by mohly být přidány do souboru *_Layout.cshtml:*

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/class-libraries>
