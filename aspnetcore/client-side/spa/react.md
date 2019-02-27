---
title: Šablona projektu React pomocí ASP.NET Core
author: SteveSandersonMS
description: Zjistěte, jak začít pracovat s ASP.NET Core jedné stránky aplikace (SPA) šablona projektu pro React a vytvořit aplikaci react.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
uid: spa/react
ms.openlocfilehash: 3b2b2e67b5d577872bafefef5624a13ca1a22449
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899174"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>Šablona projektu React pomocí ASP.NET Core

Aktualizovaná šablona projektu React poskytuje příhodný výchozí bod pro ASP.NET Core aplikace pomocí React a [vytvořit aplikaci react](https://github.com/facebookincubator/create-react-app) konvence (CRA) k implementaci bohatě vybaveným a na straně klienta uživatelské rozhraní (UI).

Šablona je ekvivalentní k vytváření projektu aplikace ASP.NET Core tak, aby fungoval jako back-endu rozhraní API a standardní CRA React projekt tak, aby fungoval jako uživatelské rozhraní, ale se snadným ovládáním hostování i v projektu aplikace s jedním, který se dají vytvořit a publikovat jako jeden celek.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

Pokud máte ASP.NET Core 2.1 nainstalovaný, není nutné k instalaci šablona projektu React.

Vytvoření nového projektu z příkazového řádku pomocí příkazu `dotnet new react` v prázdném adresáři. Například následující příkazy vytvoří aplikaci *my nové app* adresáře a přepnete se do tohoto adresáře:

```console
dotnet new react -o my-new-app
cd my-new-app
```

Spuštění aplikace Visual Studio nebo .NET Core CLI:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Otevřete vygenerovaný *.csproj* souboru a spuštění aplikace jako za normálních okolností z něj.

Proces sestavení obnoví závislosti npm při prvním spuštění, což může trvat několik minut. Následující sestavení jsou mnohem rychlejší.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Ujistěte se, máte proměnnou prostředí volá `ASPNETCORE_Environment` s hodnotou `Development`. Na Windows (v výzev – prostředí PowerShell), spusťte `SET ASPNETCORE_Environment=Development`. V systému macOS nebo Linux spusťte `export ASPNETCORE_Environment=Development`.

Spustit [dotnet sestavení](/dotnet/core/tools/dotnet-build) správně k ověření aplikace sestavena. Při prvním spuštění procesu sestavení obnoví závislosti na npm, což může trvat několik minut. Následující sestavení jsou mnohem rychlejší.

Spustit [dotnet spustit](/dotnet/core/tools/dotnet-run) a spusťte aplikaci.

---

Šablona projektu vytvoří aplikace ASP.NET Core a aplikace s React. Aplikace ASP.NET Core je určena pro použití pro přístup k datům, autorizaci a další aspekty na straně serveru. Aplikace React, které se nacházejí v *ClientApp* podadresář, je určena pro použití pro všechny aspekty uživatelského rozhraní.

## <a name="add-pages-images-styles-modules-etc"></a>Přidání stránek, obrázků, styly, moduly, atd.

*ClientApp* adresář je standardní CRA reakce aplikace. Najdete v oficiální [CRA dokumentaci](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) Další informace.

Existují mírné rozdíly mezi React aplikace vytvořené pomocí této šablony a vytvořil CRA sama o sobě; Nicméně jsou schopnosti aplikace beze změny. Obsahuje aplikaci vytvořenou pomocí šablony [Bootstrap](https://getbootstrap.com/)– na základě rozložení a základní příklad směrování.

## <a name="install-npm-packages"></a>Instalace balíčků npm

Pokud chcete nainstalovat balíčky npm třetích stran, použijte příkazový řádek v *ClientApp* podadresáře. Příklad:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publikování a nasazení

Při vývoji se aplikace běží v režimu optimalizované pro usnadnění práce vývojářů. Například jazyka JavaScript sady obsahují zdrojových mapování (tak, aby při ladění, se zobrazí původní zdrojový kód). Aplikace sleduje jazyka JavaScript, HTML a CSS změny souborů na disku a automaticky se znovu zkompiluje a znovu načte, když vidí tyto soubory změnit.

V produkčním prostředí sloužit verzi vaší aplikace, které je optimalizované pro výkon. Ta se nakonfiguruje, která se provede automaticky. Když publikujete, konfigurace sestavení generuje minifikovaný, transpiled sestavení vašeho kódu na straně klienta. Na rozdíl od sestavení vývoj nevyžaduje produkční build Node.js nainstalovaný na serveru.

Můžete použít standardní [metody hostování a nasazení ASP.NET Core](xref:host-and-deploy/index).

## <a name="run-the-cra-server-independently"></a>Spusťte CRA server nezávisle na sobě

Projekt je nakonfigurován ke spuštění svoji vlastní instanci vývojový server sady CRA na pozadí při spuštění v režimu pro vývoj aplikace ASP.NET Core. Je to vhodné, protože to znamená, že není nutné ručně spustit na samostatný server.

Nevýhodou této výchozí nastavení není k dispozici. Pokaždé, když změníte kód jazyka C# a vaše ASP.NET Core, které aplikace potřebuje k restartování, CRA server se restartuje. Pár sekund jsou vyžadovány pro spuštění zálohování. Pokud vytváříte časté úpravy kódu jazyka C# a nechcete čekat, CRA server restartovat, spusťte server CRA externě, nezávisle na procesu ASP.NET Core. Postup:

1. Přidat *.env* do souboru *ClientApp* podadresář s následujícím nastavením:

    ```
    BROWSER=none
    ```
    
    Tím zabráníte ve webovém prohlížeči otevřít při spuštění serveru CRA externě.

2. V příkazovém řádku přejděte *ClientApp* podadresáře a spusťte vývojový server sady CRA:

    ```console
    cd ClientApp
    npm start
    ```

3. Upravte aplikace ASP.NET Core pro použití externího instance serveru CRA namísto jeho vlastní spuštění. Ve vaší *spuštění* třídy, nahraďte `spa.UseReactDevelopmentServer` vyvolání následujícím kódem:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

Při spuštění aplikace ASP.NET Core se nespustí CRA serveru. Místo toho se používá instanci, kterou jste spustili ručně. To umožňuje spuštění a restartování rychleji. Už čeká na opětovné sestavení pokaždé, když v aplikaci React.

> [!IMPORTANT]
> "Vykreslování na straně serveru" není podporovanou funkcí této šablony. Naším cílem při vytváření této šablony je pro splnění se "Vytvoření react-app". V důsledku toho scénáře a funkce není součástí projektu "Vytvoření react-app" (například SSR) nejsou podporovány a jsou ponechané jako cvičení pro daného uživatele.
