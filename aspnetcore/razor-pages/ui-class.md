---
title: Opakovaně použitelné Razor uživatelské rozhraní v knihovnách tříd pomocí ASP.NET Core
author: Rick-Anderson
description: Vysvětluje, jak vytvořit opakovaně použitelné Razor uživatelské rozhraní pomocí částečných zobrazení v knihovně tříd v ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: 042ef8dcc7135e0c60baec972d405380d85a6a10
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019219"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a>Vytvoření opakovaně použitelného uživatelského rozhraní pomocí Razor projektu knihovny tříd v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razorzobrazení, stránky, řadiče, modely stránek, [ Razor komponenty](xref:blazor/components/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do Razor knihovny tříd (RCL). RCL může být zabalen a znovu použit. Aplikace mohou zahrnovat RCL a přepsat zobrazení a stránky, které obsahuje. V případě, že Razor je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka, má Razor přednost označení (soubor *. cshtml* ) ve webové aplikaci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>Vytvoření knihovny tříd obsahující Razor uživatelské rozhraní

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V aplikaci Visual Studio vyberte **vytvořit nový nový projekt**.
* Jako další vyberte ** Razor knihovnu tříd** > **Next**.
* Pojmenujte knihovnu (například " Razor že knihovna tříd"), > **vytvořit**. Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views` .
* Pokud potřebujete podporovat zobrazení, vyberte možnost **stránky podpory a zobrazení** . Ve výchozím nastavení Razor jsou podporovány pouze stránky. Vyberte **Vytvořit**.

RazorŠablona knihovny tříd (RCL) se standardně Razor používá pro vývoj komponent. Možnost **stránky podpory a zobrazení** podporuje stránky a zobrazení.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Z příkazového řádku spusťte příkaz `dotnet new razorclasslib` . Například:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

RazorŠablona knihovny tříd (RCL) se standardně Razor používá pro vývoj komponent. Předat `--support-pages-and-views` možnost ( `dotnet new razorclasslib --support-pages-and-views` ) pro poskytnutí podpory pro stránky a zobrazení.

Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new). Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views` .

---

Přidejte Razor soubory do RCL.

Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *oblasti* . Další informace najdete v tématu [RCL Page Layout (rozložení stránek](#rcl-pages-layout) ) pro vytvoření RCL, které zpřístupňuje obsah `~/Pages` místo `~/Areas/Pages` .

## <a name="reference-rcl-content"></a>Odkaz na obsah RCL

Na RCL může odkazovat:

* Balíček NuGet. Viz téma [vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidání balíčku](/dotnet/core/tools/dotnet-add-package) a [Vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}. csproj*. Viz [dotnet – přidat odkaz](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Přepsat zobrazení, částečná zobrazení a stránky

V případě, že Razor je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka, má Razor přednost označení (soubor *. cshtml* ) ve webové aplikaci. Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.

V ukázce stažení, přejmenujte *WebApp1/areas/MyFeature2* na *WebApp1/areas/MyFeature* na test priority.

Zkopírujte částečné zobrazení * Razor UIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* do *WebApp1/areas/MyFeature/Pages/Shared/_Message. cshtml*. Aktualizujte kód tak, aby označoval nové umístění. Sestavte a spusťte aplikaci, abyste ověřili, že se používá částečná verze aplikace.

### <a name="rcl-pages-layout"></a>Rozložení stránek RCL

Pokud chcete odkazovat na obsah RCL, jako by byl *součástí složky webových* aplikací, vytvořte projekt RCL s následující strukturou souborů:

* *RazorUIClassLib/stránky*
* *RazorUIClassLib/stránky/sdílené*

Předpokládejme, že * Razor UIClassLib/Pages/Shared* obsahují dva částečné soubory: *_Header. cshtml* a *_Footer. cshtml*. Do `<partial>` souboru *_Layout. cshtml* se dají přidat značky:

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

`dotnet pack`Místo verze NuGet.exe použijte příkaz `nuget pack` .

### <a name="exclude-static-assets"></a>Vyloučení statických prostředků

Chcete-li vyloučit statické prostředky, přidejte požadovanou cestu vyloučení do `$(DefaultItemExcludes)` skupiny vlastností v souboru projektu. Jednotlivé položky oddělte středníkem ( `;` ).

V následujícím příkladu není šablona *lib. CSS* ve složce *wwwroot* považována za statický prostředek a není obsažena v publikovaném RCL:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Integrace TypeScriptu

Zahrnutí souborů TypeScriptu do RCL:

1. Soubory TypeScript (*. TS*) umístěte mimo složku *wwwroot* . Umístěte například soubory do složky *klienta* .

1. Nakonfigurujte výstup sestavení TypeScriptu pro složku *wwwroot* . Nastavte `TypescriptOutDir` vlastnost uvnitř `PropertyGroup` souboru projektu:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Přidejte cíl TypeScript jako závislost `ResolveCurrentProjectStaticWebAssets` cíle přidáním následujícího cíle do `PropertyGroup` v souboru projektu:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Využití obsahu z odkazovaného RCL

Soubory zahrnuté ve složce *wwwroot* v RCL jsou zpřístupněny buď RCL, nebo aplikaci, která je v rámci předpony `_content/{LIBRARY NAME}/` . Například knihovna s názvem * Razor . Class. lib* má za následek cestu k statickému obsahu `_content/Razor.Class.Lib/` . Když vytváříte balíček NuGet a název sestavení není stejný jako ID balíčku, použijte ID balíčku pro `{LIBRARY NAME}` .

Vybírající aplikace odkazuje na statické prostředky poskytované knihovnou s `<script>` , `<style>` , `<img>` a dalšími značkami HTML. Vybírající aplikace musí mít povolenou [podporu statických souborů](xref:fundamentals/static-files) v `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Při spuštění náročné aplikace z výstupu sestavení ( `dotnet run` ) jsou ve výchozím nastavení ve vývojovém prostředí povoleny statické webové prostředky. Pro podporu prostředků v jiných prostředích při spuštění z výstupu sestavení zavolejte `UseStaticWebAssets` na tvůrce hostitele v *program.cs*:

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

`UseStaticWebAssets`Při spuštění aplikace z publikovaného výstupu () se volání nevyžaduje `dotnet publish` .

### <a name="multi-project-development-flow"></a>Vývojový tok pro více projektů

Když pracujete na náročné aplikaci:

* Prostředky v RCL zůstanou v původních složkách. Prostředky se nepřesunou do nenáročné aplikace.
* Jakákoli změna ve složce *WWWROOT* RCL se projeví v aplikaci po resestavení RCL a bez opětovného sestavení náročné aplikace.

Při sestavení RCL je vytvořen manifest, který popisuje umístění statických webových prostředků. Vybírající aplikace načte manifest za běhu za účelem využití prostředků z odkazovaných projektů a balíčků. Když se do RCL přidá nový Asset, musí se RCL znovu sestavit, aby se aktualizoval jeho manifest předtím, než bude mít k novému assetu přístup.

### <a name="publish"></a>Publikovat

Po publikování aplikace se doprovodné prostředky ze všech odkazovaných projektů a balíčků zkopírují do složky *wwwroot* publikované aplikace v části `_content/{LIBRARY NAME}/` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razorzobrazení, stránky, řadiče, modely stránek, [ Razor komponenty](xref:blazor/components/class-libraries), [zobrazení komponent](xref:mvc/views/view-components)a datové modely lze integrovat do Razor knihovny tříd (RCL). RCL může být zabalen a znovu použit. Aplikace mohou zahrnovat RCL a přepsat zobrazení a stránky, které obsahuje. V případě, že Razor je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka, má Razor přednost označení (soubor *. cshtml* ) ve webové aplikaci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>Vytvoření knihovny tříd obsahující Razor uživatelské rozhraní

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* Vyberte **ASP.NET Core webové aplikace**.
* Pojmenujte knihovnu (například " Razor že knihovna tříd") > **OK**. Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views` .
* Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.
* Vyberte ** Razor Knihovna tříd** > **OK**.

RCL má následující soubor projektu:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Z příkazového řádku spusťte příkaz `dotnet new razorclasslib` . Například:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new). Aby nedošlo ke kolizi názvů souborů s vygenerovanou knihovnou zobrazení, ujistěte se, že název knihovny nekončí `.Views` .

---

Přidejte Razor soubory do RCL.

Šablony ASP.NET Core předpokládají, že obsah RCL je ve složce *oblasti* . Další informace najdete v tématu [RCL Page Layout (rozložení stránek](#rcl-pages-layout) ) pro vytvoření RCL, které zpřístupňuje obsah `~/Pages` místo `~/Areas/Pages` .

## <a name="reference-rcl-content"></a>Odkaz na obsah RCL

Na RCL může odkazovat:

* Balíček NuGet. Viz téma [vytváření balíčků NuGet](/nuget/create-packages/creating-a-package) a [dotnet přidání balíčku](/dotnet/core/tools/dotnet-add-package) a [Vytvoření a publikování balíčku NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}. csproj*. Viz [dotnet – přidat odkaz](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a>Návod: vytvoření projektu RCL a použití z Razor projektu stránky

Můžete stáhnout [celý projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) a otestovat ho místo jeho vytvoření. Ukázkový soubor ke stažení obsahuje další kód a odkazy, které usnadňují testování projektu. Zpětnou vazbu k [tomuto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/6098) si můžete nechat s vašimi komentáři ke stažení ukázek a podrobnými pokyny.

### <a name="test-the-download-app"></a>Testování aplikace pro stažení

Pokud jste nestáhli dokončenou aplikaci a chcete místo toho vytvořit návodový projekt, přejděte k [Další části](#create-an-rcl).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Otevřete soubor *. sln* v aplikaci Visual Studio. Spusťte aplikaci.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Z příkazového řádku v adresáři *CLI* Sestavte RCL a webovou aplikaci.

```dotnetcli
dotnet build
```

Přejděte do adresáře *WebApp1* a spusťte aplikaci:

```dotnetcli
dotnet run
```

---

Postupujte podle pokynů v části [test WebApp1](#test-webapp1)

## <a name="create-an-rcl"></a>Vytvoření RCL

V této části se vytvoří RCL. Razorsoubory jsou přidány do RCL.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvořte projekt RCL:

* V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* Vyberte **ASP.NET Core webové aplikace**.
* Pojmenujte aplikaci ** Razor UIClassLib** > **OK**.
* Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.
* Vyberte ** Razor Knihovna tříd** > **OK**.
* Přidejte Razor soubor částečného zobrazení s názvem * Razor UIClassLib/areas/MyFeature/Pages/shared/_Message. cshtml*.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Z příkazového řádku spusťte následující příkaz:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Předchozí příkazy:

* Vytvoří `RazorUIClassLib` RCL.
* Vytvoří Razor stránku _Message a přidá ji do RCL. `-np`Parametr vytvoří stránku bez `PageModel` .
* Vytvoří soubor [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) a přidá jej do RCL.

Soubor *_ViewStart. cshtml* je vyžadován pro použití rozložení Razor projektu stránky (který je přidán v další části).

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a>Přidat Razor soubory a složky do projektu

* Nahraďte označení v * Razor UIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* následujícím kódem:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Nahraďte označení v * Razor UIClassLib/areas/MyFeature/Pages/Page1. cshtml* následujícím kódem:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`je vyžadováno pro použití částečného zobrazení ( `<partial name="_Message" />` ). Místo zahrnutí `@addTagHelper` direktivy můžete přidat *_ViewImports soubor. cshtml* . Například:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Další informace o *_ViewImports. cshtml*naleznete v tématu [importing Shared direktivy](xref:mvc/views/layout#importing-shared-directives)

* Sestavení knihovny tříd pro ověření, že nejsou k dispozici žádné chyby kompilátoru:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Výstup sestavení obsahuje * RazorUIClassLib.dll* a * RazorUIClassLib.Views.dll*. * RazorUIClassLib.Views.dll* obsahuje zkompilovaný Razor obsah.

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a>Použití Razor knihovny uživatelského rozhraní z Razor projektu stránky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvoření Razor webové aplikace Pages:

* Z **Průzkumník řešení**klikněte pravým tlačítkem myši na řešení > **Přidat** > **Nový projekt**.  
* Vyberte **ASP.NET Core webové aplikace**.
* Pojmenujte aplikaci **WebApp1**.
* Ověřte, zda je vybrána **ASP.NET Core 2,1** nebo novější.
* Vyberte **Webová aplikace** > **OK**.

* Z **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **nastavit jako spouštěný projekt**.
* Z **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **sestavení** závislosti > **projektu**.
* Ověřte ** Razor UIClassLib** jako závislost **WebApp1**.
* V **Průzkumník řešení**klikněte pravým tlačítkem na **WebApp1** a vyberte **Přidat** > **odkaz**.
* V dialogovém okně **Správce odkazů** , vraťte ** Razor UIClassLib** > **OK**.

Spusťte aplikaci.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Vytvořte Razor webovou aplikaci stránky a soubor řešení obsahující Razor aplikaci Pages a RCL:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Sestavte a spusťte webovou aplikaci:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>WebApp1 testu

Vyhledejte a `/MyFeature/Page1` Ověřte, zda Razor je knihovna tříd uživatelského rozhraní používána.

## <a name="override-views-partial-views-and-pages"></a>Přepsat zobrazení, částečná zobrazení a stránky

V případě, že Razor je v rámci webové aplikace i v RCL Nalezeno zobrazení, částečné zobrazení nebo stránka, má Razor přednost označení (soubor *. cshtml* ) ve webové aplikaci. Například přidejte *WebApp1/areas/MyFeature/Pages/Page1. cshtml* do WebApp1 a Page1 v WebApp1 bude mít přednost před Page1 v RCL.

V ukázce stažení, přejmenujte *WebApp1/areas/MyFeature2* na *WebApp1/areas/MyFeature* na test priority.

Zkopírujte částečné zobrazení * Razor UIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml* do *WebApp1/areas/MyFeature/Pages/Shared/_Message. cshtml*. Aktualizujte kód tak, aby označoval nové umístění. Sestavte a spusťte aplikaci, abyste ověřili, že se používá částečná verze aplikace.

### <a name="rcl-pages-layout"></a>Rozložení stránek RCL

Pokud chcete odkazovat na obsah RCL, jako by byl *součástí složky webových* aplikací, vytvořte projekt RCL s následující strukturou souborů:

* *RazorUIClassLib/stránky*
* *RazorUIClassLib/stránky/sdílené*

Předpokládejme, že * Razor UIClassLib/Pages/Shared* obsahují dva částečné soubory: *_Header. cshtml* a *_Footer. cshtml*. Do `<partial>` souboru *_Layout. cshtml* se dají přidat značky:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Další materiály

* <xref:blazor/components/class-libraries>
