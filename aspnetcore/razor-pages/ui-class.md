---
title: Opakovaně použitelné Razor uživatelského rozhraní v knihovnách tříd pomocí ASP.NET Core
author: Rick-Anderson
description: Vysvětluje, jak vytvářet opakovaně použitelné uživatelské rozhraní Razor pomocí částečných zobrazení v knihovně tříd v ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 420cc54701394673e2b442b1fdf999e421820fd5
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809117"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Vytvoření opakovaně použitelného uživatelského rozhraní pomocí projektu knihovny tříd Razor v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Zobrazení Razor, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do knihovny tříd Razor (RCL). RCL můžete zabalit a znovu použít. Aplikace můžete zahrnout RCL a přepsání, zobrazení a stránky, které obsahuje. Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor ( *.cshtml* soubor) na webu aplikace má přednost.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Vytvoření knihovny tříd obsahující Razor uživatelského rozhraní

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V aplikaci Visual Studio vyberte **vytvořit nový nový projekt**.
* > **Další**vyberte **knihovnu tříd Razor** .
* Pojmenujte knihovnu (například "RazorClassLib"), > **vytvořit**. Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.
* Pokud potřebujete podporovat zobrazení, vyberte možnost **stránky podpory a zobrazení** . Ve výchozím nastavení jsou podporovány pouze Razor Pages. Vyberte **vytvořit**.

Šablona knihovny tříd Razor (RCL) je ve výchozím nastavení standardně pro vývoj komponent Razor. Možnost **stránky podpory a zobrazení** podporuje stránky a zobrazení.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Z příkazového řádku, spusťte `dotnet new razorclasslib`. Příklad:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Šablona knihovny tříd Razor (RCL) je ve výchozím nastavení standardně pro vývoj komponent Razor. Předáním možnosti `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`) zajistíte podporu pro stránky a zobrazení.

Další informace najdete v tématu [dotnet nové](/dotnet/core/tools/dotnet-new). Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.

---

Přidáte soubory Razor RCL.

Šablony ASP.NET Core předpokládat RCL obsah je *oblasti* složky. Pokud chcete vytvořit RCL, který zpřístupňuje obsah v `~/Pages` místo `~/Areas/Pages`, přečtěte si téma [rozložení stránek RCL](#rcl-pages-layout) .

## <a name="reference-rcl-content"></a>Odkaz na obsah RCL

RCL lze odkazovat pomocí:

* Balíček NuGet. V tématu [balíčky NuGet vytváření](/nuget/create-packages/creating-a-package) a [se příkaz dotnet add package](/dotnet/core/tools/dotnet-add-package) a [vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName} .csproj*. Zobrazit [dotnet-přidat odkaz na](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Přepsání, zobrazení, částečná zobrazení a stránky

Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor ( *.cshtml* soubor) na webu aplikace má přednost. Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.

Ve vzorku ke stažení, přejmenujte *WebApp1/oblasti/MyFeature2* k *WebApp1/oblasti/MyFeature* otestovat prioritu.

Kopírovat *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* částečné zobrazení k *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aktualizace značky k označení nového umístění. Sestavení a spuštění aplikace a zkontrolujte, že verze aplikace s částečným se používá.

### <a name="rcl-pages-layout"></a>Rozložení stránek RCL

K odkazu RCL obsah, jako by šlo součást webové aplikace *stránky* složku, vytvořte projekt RCL s následující strukturou souboru:

* *RazorUIClassLib/stránky*
* *RazorUIClassLib/stránek/Shared*

Předpokládejme, že *RazorUIClassLib/stránek/Shared* obsahuje dva soubory částečné: *_Header.cshtml* a *_Footer.cshtml*. `<partial>` Značky může být přidán do *_Layout.cshtml* souboru:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Vytvoření RCL se statickými prostředky

RCL může vyžadovat doprovodné statické prostředky, na které lze odkazovat buď RCL, nebo náročné aplikace RCL. ASP.NET Core umožňuje vytváření RCLs, které zahrnují statické prostředky, které jsou k dispozici pro náročné aplikace.

Chcete-li zahrnout doprovodné materiály jako součást RCL, vytvořte složku *wwwroot* v knihovně tříd a zahrňte všechny požadované soubory do této složky.

Při balení RCL jsou do balíčku automaticky zahrnuty všechny doprovodné materiály ve složce *wwwroot* .

### <a name="exclude-static-assets"></a>Vyloučení statických prostředků

Chcete-li vyloučit statické prostředky, přidejte požadovanou cestu vyloučení do skupiny vlastností `$(DefaultItemExcludes)` v souboru projektu. Jednotlivé položky oddělte středníkem (`;`).

V následujícím příkladu není šablona *lib. CSS* ve složce *wwwroot* považována za statický prostředek a není obsažena v publikovaném RCL:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Integrace TypeScriptu

Zahrnutí souborů TypeScriptu do RCL:

1. Soubory TypeScript ( *. TS*) umístěte mimo složku *wwwroot* . Umístěte například soubory do složky *klienta* .

1. Nakonfigurujte výstup sestavení TypeScriptu pro složku *wwwroot* . Nastavte vlastnost `TypescriptOutDir` v rámci `PropertyGroup` v souboru projektu:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Přidejte cíl TypeScript jako závislost cíle `ResolveCurrentProjectStaticWebAssets` přidáním následujícího cíle do `PropertyGroup` v souboru projektu:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Využití obsahu z odkazovaného RCL

Soubory zahrnuté ve složce *wwwroot* v RCL jsou zpřístupněny buď RCL, nebo aplikacím, které využívají, v předponě `_content/{LIBRARY NAME}/`. Například knihovna s názvem *Razor. Class. lib* má za následek cestu k statickému obsahu v `_content/Razor.Class.Lib/`. Když vytváříte balíček NuGet a název sestavení není stejný jako ID balíčku, použijte ID balíčku pro `{LIBRARY NAME}`.

Nenáročné aplikace odkazují na statické prostředky poskytované knihovnou pomocí `<script>`, `<style>`, `<img>`a dalších značek HTML. Vybírající aplikace musí mít povolenou [podporu statických souborů](xref:fundamentals/static-files) v `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Při spuštění náročné aplikace z výstupu sestavení (`dotnet run`) jsou ve výchozím nastavení ve vývojovém prostředí povoleny statické webové prostředky. Pro podporu prostředků v jiných prostředích při spuštění z výstupu sestavení volejte `UseStaticWebAssets` na tvůrci hostitele v *program.cs*:

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

Při spuštění aplikace z publikovaného výstupu (`dotnet publish`) se nevyžaduje volání `UseStaticWebAssets`.

### <a name="multi-project-development-flow"></a>Vývojový tok pro více projektů

Když pracujete na náročné aplikaci:

* Prostředky v RCL zůstanou v původních složkách. Prostředky se nepřesunou do nenáročné aplikace.
* Jakákoli změna ve složce *WWWROOT* RCL se projeví v aplikaci po resestavení RCL a bez opětovného sestavení náročné aplikace.

Při sestavení RCL je vytvořen manifest, který popisuje umístění statických webových prostředků. Vybírající aplikace načte manifest za běhu za účelem využití prostředků z odkazovaných projektů a balíčků. Když se do RCL přidá nový Asset, musí se RCL znovu sestavit, aby se aktualizoval jeho manifest předtím, než bude mít k novému assetu přístup.

### <a name="publish"></a>Publikovat

Po publikování aplikace se doprovodné prostředky ze všech odkazovaných projektů a balíčků zkopírují do složky *wwwroot* publikované aplikace v části `_content/{LIBRARY NAME}/`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Zobrazení Razor, stránky, řadiče, modely stránek, [komponenty Razor](xref:blazor/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do knihovny tříd Razor (RCL). RCL můžete zabalit a znovu použít. Aplikace můžete zahrnout RCL a přepsání, zobrazení a stránky, které obsahuje. Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor ( *.cshtml* soubor) na webu aplikace má přednost.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Vytvoření knihovny tříd obsahující Razor uživatelského rozhraní

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.
* Vyberte **webová aplikace ASP.NET Core**.
* Název knihovny (například "RazorClassLib") > **OK**. Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.
* Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.
* Vyberte **knihovnu tříd Razor** > **OK**.

RCL má následující soubor projektu:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Z příkazového řádku, spusťte `dotnet new razorclasslib`. Příklad:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Další informace najdete v tématu [dotnet nové](/dotnet/core/tools/dotnet-new). Aby se zabránilo kolize názvů souborů s knihovnou generované zobrazení, ujistěte se ale nekončí název knihovny v `.Views`.

---

Přidáte soubory Razor RCL.

Šablony ASP.NET Core předpokládat RCL obsah je *oblasti* složky. Pokud chcete vytvořit RCL, který zpřístupňuje obsah v `~/Pages` místo `~/Areas/Pages`, přečtěte si téma [rozložení stránek RCL](#rcl-pages-layout) .

## <a name="reference-rcl-content"></a>Odkaz na obsah RCL

RCL lze odkazovat pomocí:

* Balíček NuGet. V tématu [balíčky NuGet vytváření](/nuget/create-packages/creating-a-package) a [se příkaz dotnet add package](/dotnet/core/tools/dotnet-add-package) a [vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName} .csproj*. Zobrazit [dotnet-přidat odkaz na](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Návod: vytvoření projektu RCL a použití z Razor Pages projektu

Můžete stáhnout [dokončený projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) a otestovat ho namísto jeho vytvoření. Vzorku ke stažení obsahuje další kódu a odkazy, které usnadňuje testování projektu. Zanecháte zpětnou vazbu v [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/6098) s komentáře na stažení ukázky a podrobné pokyny.

### <a name="test-the-download-app"></a>Testování aplikace ke stažení

Pokud jste nestáhli dokončené aplikace a by místo toho vytvořte projekt návodu, pokračujte [další části](#create-an-rcl).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Otevřít *.sln* souboru v sadě Visual Studio. Spusťte aplikaci.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Z příkazového řádku v *rozhraní příkazového řádku* adresáře, vytvářet RCL a webové aplikace.

```dotnetcli
dotnet build
```

Přesunout *WebApp1* adresáře a spusťte aplikaci:

```dotnetcli
dotnet run
```

---

Postupujte podle pokynů v [WebApp1 testu](#test-webapp1)

## <a name="create-an-rcl"></a>Vytvoření RCL

V této části se vytvoří RCL. Soubory Razor jsou přidány do RCL.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvoření projektu RCL:

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.
* Vyberte **webová aplikace ASP.NET Core**.
* Pojmenujte aplikaci **RazorUIClassLib** > **OK**.
* Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.
* Vyberte **knihovnu tříd Razor** > **OK**.
* Přidejte do ní soubor částečného zobrazení Razor *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Z příkazového řádku spusťte následující příkaz:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Předchozí příkazy:

* Vytvoří `RazorUIClassLib` RCL.
* Vytvoří stránku Razor _TEXT a přidá jej RCL. `-np` Parametr vytvoří, aniž by `PageModel`.
* Vytvoří [soubor _ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) soubor a přidá jej RCL.

*Soubor _ViewStart.cshtml* soubor je vyžadován pro rozložení stránky Razor projektu (která se přidá v další části).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Přidání Razor souborů a složek do projektu

* Nahraďte kód v *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* následujícím kódem:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Nahraďte kód v *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* následujícím kódem:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` je potřeba používat částečné zobrazení (`<partial name="_Message" />`). Místo včetně `@addTagHelper` direktiv, můžete přidat *_ViewImports.cshtml* souboru. Příklad:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Další informace o *_ViewImports.cshtml*, naleznete v tématu [import sdílených direktivy](xref:mvc/views/layout#importing-shared-directives)

* Sestavení knihovny tříd pro ověření, že zde nejsou žádné chyby kompilátoru:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Výstup sestavení obsahuje *RazorUIClassLib.dll* a *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* obsahuje kompilovaný obsah Razor.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Použití knihovny uživatelského rozhraní Razor z projektu pro stránky Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvoření webové aplikace stránky Razor:

* Z **Průzkumník řešení**klikněte pravým tlačítkem myši na řešení > **Přidat** >**Nový projekt**.
* Vyberte **webová aplikace ASP.NET Core**.
* Pojmenujte aplikaci **WebApp1**.
* Ověřte **ASP.NET Core 2.1** nebo novější je vybrána.
* Vyberte **Webová aplikace** > **OK**.

* Z **Průzkumníka řešení**, klikněte pravým tlačítkem na **WebApp1** a vyberte **nastavit jako spouštěný projekt**.
* Z **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **závislosti sestavení** > **závislosti projektu**.
* Zkontrolujte **RazorUIClassLib** jako závislost **WebApp1**.
* V **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **Přidat** > **odkaz**.
* V dialogovém okně **Správce odkazů** ověřte **RazorUIClassLib** > **OK**.

Spusťte aplikaci.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Vytvoření webové aplikace Razor Pages a souboru řešení obsahujícího aplikaci Razor Pages a RCL:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Sestavení a spuštění webové aplikace:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>WebApp1 testu

Přejděte na `/MyFeature/Page1` a ověřte, že se knihovna tříd uživatelského rozhraní Razor používá.

## <a name="override-views-partial-views-and-pages"></a>Přepsání, zobrazení, částečná zobrazení a stránky

Při zobrazení, částečná zobrazení nebo stránky Razor se nachází v webové aplikace a RCL kód Razor ( *.cshtml* soubor) na webu aplikace má přednost. Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.

Ve vzorku ke stažení, přejmenujte *WebApp1/oblasti/MyFeature2* k *WebApp1/oblasti/MyFeature* otestovat prioritu.

Kopírovat *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* částečné zobrazení k *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aktualizace značky k označení nového umístění. Sestavení a spuštění aplikace a zkontrolujte, že verze aplikace s částečným se používá.

### <a name="rcl-pages-layout"></a>Rozložení stránek RCL

K odkazu RCL obsah, jako by šlo součást webové aplikace *stránky* složku, vytvořte projekt RCL s následující strukturou souboru:

* *RazorUIClassLib/stránky*
* *RazorUIClassLib/stránek/Shared*

Předpokládejme, že *RazorUIClassLib/stránek/Shared* obsahuje dva soubory částečné: *_Header.cshtml* a *_Footer.cshtml*. `<partial>` Značky může být přidán do *_Layout.cshtml* souboru:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
