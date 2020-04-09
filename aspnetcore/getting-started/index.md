---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytváří a spouští základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
uid: getting-started
ms.openlocfilehash: 86a0c8d017138a949fddc0356f3de548d368a4c0
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417601"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Výuka: Začínáme s ASP.NET Core

Tento kurz ukazuje, jak vytvořit a spustit ASP.NET webovou aplikaci Core pomocí rozhraní .NET Core CLI.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvořte projekt webové aplikace.
> * Důvěřujte vývojovému certifikátu.
> * Spusťte aplikaci.
> * Upravte stránku Razor.

Na konci budete mít funkční webovou aplikaci spuštěnou v místním počítači.

![Domovská stránka webové aplikace](_static/home-page.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>Vytvoření projektu webové aplikace

Otevřete příkazové prostředí a zadejte následující příkaz:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

Předchozí příkaz:

* Vytvoří novou webovou aplikaci.  
* Parametr `-o aspnetcoreapp` vytvoří adresář s názvem *aspnetcoreapp* se zdrojovými soubory pro aplikaci.

### <a name="trust-the-development-certificate"></a>Důvěřovat certifikátu vývoje

Důvěřujte vývojovému certifikátu HTTPS:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

Předchozí příkaz zobrazí následující dialog:

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

Pokud souhlasíte s tím, že certifikátu vývoje důvěřujete, vyberte **možnost Ano.**

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

Předchozí příkaz zobrazí následující zprávu:

*Bylo požadováno důvěřování vývojovému certifikátu HTTPS. Pokud certifikát ještě není důvěryhodný, spustíme následující příkaz:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

Tento příkaz vás může vyzvat k zadání hesla k instalaci certifikátu do systémové klíčenky. Pokud souhlasíte s tím, že certifikátu vývoje důvěřujete, zadejte heslo.

# <a name="linux"></a>[Linux](#tab/linux)

Podívejte se na dokumentaci k distribuci Linuxu o tom, jak důvěřovat vývojovému certifikátu HTTPS.

---

Další informace naleznete [v tématu Trust ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte následující příkazy:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Poté, co příkazové prostředí indikuje, že aplikace byla spuštěna, přejděte na `https://localhost:5001`.

## <a name="edit-a-razor-page"></a>Úprava stránky Razor

Otevřete *stránky/index.cshtml* a upravte a uložte stránku s následujícími zvýrazněnými značkami:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Přejděte `https://localhost:5001`na , aktualizujte stránku a ověřte, zda jsou zobrazeny změny.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořte projekt webové aplikace.
> * Důvěřujte vývojovému certifikátu.
> * Spusťte projekt.
> * Proveďte změnu.

Další informace o ASP.NET core najdete v doporučeném studijním programu v úvodu:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
