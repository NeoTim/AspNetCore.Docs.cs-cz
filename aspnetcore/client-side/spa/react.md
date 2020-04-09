---
title: Použití šablony projektu React s ASP.NET Jádrem
author: SteveSandersonMS
description: Přečtěte si, jak začít s šablonou projektu ASP.NET základní jednostránkové aplikace (SPA) pro react a create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/react
ms.openlocfilehash: 9703a62eb7f779974382fe0fb01702d9fcd37d64
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664960"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>Použití šablony projektu React s ASP.NET Jádrem

Aktualizovaná šablona projektu React poskytuje pohodlný výchozí bod pro ASP.NET aplikace Core pomocí konvencí React a [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) k implementaci bohatého uživatelského rozhraní na straně klienta.

Šablona je ekvivalentní k vytvoření ASP.NET core projektu působit jako backend ROZHRANÍ API, a standardní CRA React projekt působit jako uživatelské rozhraní, ale s pohodlí hostování jak v jednom projektu aplikace, které mohou být vytvořeny a zveřejněny jako jedna jednotka.

Šablona projektu React není určena pro vykreslování na straně serveru (SSR). Pro SSR s Reagovat a Node.js, zvažte [Next.js](https://github.com/zeit/next.js/) nebo [Razzle](https://github.com/jaredpalmer/razzle).

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

Pokud máte nainstalovanou ASP.NET Core 2.1, není nutné instalovat šablonu projektu React.

Vytvořte nový projekt z příkazového `dotnet new react` řádku pomocí příkazu v prázdném adresáři. Například následující příkazy vytvoří aplikaci v adresáři *moje nová aplikace* a přejdou do tohoto adresáře:

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

Spusťte aplikaci z Visual Studia nebo rozhraní .NET Core CLI:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Otevřete vygenerovaný soubor *.csproj* a spusťte aplikaci jako obvykle.

Proces sestavení obnoví npm závislosti na první spuštění, což může trvat několik minut. Následná sestavení jsou mnohem rychlejší.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Ujistěte se, že `ASPNETCORE_Environment` máte `Development`proměnnou prostředí volanou s hodnotou . Ve Windows (v výzvách, které `SET ASPNETCORE_Environment=Development`nejsou powershelly), spusťte . Na Linuxu nebo macOS spusťte `export ASPNETCORE_Environment=Development`.

Spusťte [dotnet sestavení](/dotnet/core/tools/dotnet-build) ověřit vaše aplikace staví správně. Při prvním spuštění proces sestavení obnoví npm závislosti, což může trvat několik minut. Následná sestavení jsou mnohem rychlejší.

Spuštění [montovny dotnet](/dotnet/core/tools/dotnet-run) spusťte.

---

Šablona projektu vytvoří aplikaci ASP.NET Core a aplikaci React. Aplikace ASP.NET Core je určena pro přístup k datům, autorizaci a další problémy na straně serveru. Aplikace React, která sídlí v podadresáři *ClientApp,* je určena pro všechny problémy s rozhraním.

## <a name="add-pages-images-styles-modules-etc"></a>Přidejte stránky, obrázky, styly, moduly atd.

Adresář *ClientApp* je standardní aplikace CRA React. Další informace naleznete v oficiální [dokumentaci cra.](https://create-react-app.dev/docs/getting-started/)

Existují mírné rozdíly mezi aplikací React vytvořenou touto šablonou a aplikací vytvořenou samotnou CRA; možnosti aplikace se však nemění. Aplikace vytvořená šablonou obsahuje rozložení založené na [Bootstrapu](https://getbootstrap.com/)a základní příklad směrování.

## <a name="install-npm-packages"></a>Instalace balíčků npm

Chcete-li nainstalovat balíčky npm od jiných výrobců, použijte příkazový řádek v podadresáři *ClientApp.* Příklad:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publikování a nasazení

Ve vývoji aplikace běží v režimu optimalizovaném pro vývojáře. Například balíčky JavaScriptu obsahují zdrojové mapy (takže při ladění můžete vidět původní zdrojový kód). Aplikace sleduje změny souborů JavaScript, HTML a CSS na disku a automaticky se znovu zkompiluje a znovu načte, když vidí, že se tyto soubory mění.

V produkčním prostředí můžete poskytovat verzi aplikace optimalizovanou pro výkon. Tato možnost je nakonfigurována tak, aby se stala automaticky. Při publikování, konfigurace sestavení vyzařuje minified, transpilované sestavení kódu na straně klienta. Na rozdíl od vývojového sestavení nevyžaduje produkční sestavení node.js, které mají být nainstalovány na serveru.

Můžete použít standardní [ASP.NET core hosting a metody nasazení](xref:host-and-deploy/index).

## <a name="run-the-cra-server-independently"></a>Spuštění serveru CRA nezávisle

Projekt je nakonfigurován tak, aby spustil vlastní instanci vývojového serveru CRA na pozadí při spuštění aplikace ASP.NET Core v režimu vývoje. To je výhodné, protože to znamená, že nemusíte spouštět samostatný server ručně.

Toto výchozí nastavení má nevýhodu. Pokaždé, když upravíte kód C# a aplikace ASP.NET Core musí být restartována, server CRA se restartuje. Pro spuštění zálohy je zapotřebí několik sekund. Pokud provádíte časté úpravy kódu C# a nechcete čekat na restartování serveru CRA, spusťte server CRA externě, nezávisle na procesu ASP.NET Core. Postupujte následovně:

1. Přidejte soubor *ENV* do podadresáře *ClientApp* s následujícím nastavením:

    ```
    BROWSER=none
    ```

    Tím se zabrání otevření webového prohlížeče při spuštění serveru CRA externě.

2. V příkazovém řádku přepněte do podadresáře *ClientApp* a spusťte vývojový server CRA:

    ```console
    cd ClientApp
    npm start
    ```

3. Upravte aplikaci ASP.NET Core tak, aby místo spuštění vlastní instance externího serveru CRA používala externí instanci serveru CRA. Ve třídě *Startup* nahraďte `spa.UseReactDevelopmentServer` vyvolání následujícím:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

Když spustíte aplikaci ASP.NET Core, nespustí server CRA. Místo toho se použije instance, kterou jste spustili ručně. To umožňuje rychlejší spuštění a restartování. Už nečeká, až se aplikace React pokaždé znovu vytvoří.

> [!IMPORTANT]
> "Vykreslování na straně serveru" není podporovanou funkcí této šablony. Naším cílem s touto šablonou je splnit paritu s "create-react-app". Jako takové scénáře a funkce, které nejsou zahrnuty v projektu "vytvořit reagovat aplikace" (například SSR) nejsou podporovány a jsou ponechány jako cvičení pro uživatele.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authentication/identity/spa>
