---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Rychlý kurz, který vytvoří a spustí jednoduchou aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2018
uid: getting-started
ms.openlocfilehash: dc85fe9189c93476859a6c00d60ec24d6eeec3fa
ms.sourcegitcommit: a16352c1c88a71770ab3922200a8cd148fb278a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53335309"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Kurz: Začínáme s ASP.NET Core

Tento návod ukazuje, jak vytvořit webovou aplikaci ASP.NET jádro pomocí rozhraní příkazového řádku .NET Core.

Se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření projektu webové aplikace.
> * Povolte místní HTTPS.
> * Spusťte aplikaci.
> * Úprava stránky syntaxe Razor.

Na konci máte spuštěn v místním počítači pomocí aplikace pracovní web app.

![Domovská stránka aplikace](_static/home-page.png)

## <a name="prerequisites"></a>Požadavky

* [.NET core 2.2 SDK](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a>Vytvoření projektu webové aplikace

Otevřete příkazové prostředí a zadejte následující příkaz:

```console
dotnet new webapp -o aspnetcoreapp
```

## <a name="enable-local-https"></a>Povolit místní HTTPS

Důvěřujete certifikátu vývoj HTTPS:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
dotnet dev-certs https --trust
```

Ve výstupu předchozího příkazu se zobrazí následující dialogové okno:

![Dialogové okno upozornění zabezpečení](_static/cert.png)

Vyberte **Ano** Pokud vyjádříte souhlas s důvěřovat certifikátu vývoje.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```console
dotnet dev-certs https --trust
```

Ve výstupu předchozího příkazu se zobrazí následující zpráva:

*Byla vyžádána důvěřující vývojářský certifikát HTTPS. Pokud certifikát není důvěryhodný provedeme následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.  
* Tento příkaz vás může vyzvat k zadání hesla k instalaci certifikátu v řetězci klíčů systému.

Heslo: *

Zadejte svoje heslo, pokud vyjádříte souhlas s důvěřovat certifikátu vývoje.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

O tom, jak důvěřovat certifikátu protokolu HTTPS vývoj naleznete v dokumentaci k vaší distribuci Linuxu.

---

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte následující příkazy:

```console
cd aspnetcoreapp
dotnet run
```

Přejděte do [ https://localhost:5001 ](https://localhost:5001). Klikněte na tlačítko **přijmout** přijměte zásady ochrany osobních údajů a soubory cookie. Tato aplikace nemá uchovává osobní údaje.

## <a name="edit-a-razor-page"></a>Úprava stránky syntaxe Razor

Otevřít *Pages/Index.cshtml* a upravovat na stránce s následující zvýrazněný kód:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Přejděte do [ https://localhost:5001 ](https://localhost:5001)a ověřte změny jsou zobrazeny.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření projektu webové aplikace.
> * Povolte místní HTTPS.
> * Spusťte projekt.
> * Proveďte změnu.

Další informace o základní technologie ASP.NET naleznete v tématu zavedení:

> [!div class="nextstepaction"]
> <xref:index>
