---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytvoří a spustí základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/15/2019
uid: getting-started
ms.openlocfilehash: d1edf91f1b37ba2b69732471dc6c1f306ac5ad24
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081132"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Kurz: Začínáme s ASP.NET Core

V tomto kurzu se dozvíte, jak používat rozhraní příkazového řádku .NET Core k vytvoření a spuštění webové aplikace ASP.NET Core.

Naučíte se:

> [!div class="checklist"]
> * Vytvořte projekt webové aplikace.
> * Důvěřovat vývojovému certifikátu.
> * Spusťte aplikaci.
> * Úprava stránky Razor

Na konci budete mít na svém místním počítači spuštěnou funkční webovou aplikaci.

![Domovská stránka webové aplikace](_static/home-page.png)

## <a name="prerequisites"></a>Požadavky

* [Sada .NET Core 2,2 SDK](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a>Vytvoření projektu webové aplikace

Otevřete příkazové prostředí a zadejte následující příkaz:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

### <a name="trust-the-development-certificate"></a>Důvěřovat vývojovému certifikátu

Důvěřovat vývojovému certifikátu HTTPS:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

Předchozí příkaz zobrazí následující dialog:

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

Předchozí příkaz zobrazí následující zprávu:

*Byl požadován vztah důvěryhodnosti s vývojovým certifikátem HTTPS. Pokud certifikát ještě nedůvěřuje, spustíme následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

Tento příkaz vás může vyzvat k zadání hesla pro instalaci certifikátu do systémového řetězce klíčů. Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, zadejte své heslo.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Informace o subsystému Windows pro Linux najdete v tématu [Trust certifikát HTTPS v subsystému Windows pro Linux](xref:security/enforcing-ssl#wsl).

Informace o tom, jak důvěřovat certifikátu vývoje HTTPS, najdete v dokumentaci k distribuci systému Linux.

---

Další informace najdete v tématu [důvěryhodnost ASP.NET Core certifikát pro vývoj https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte následující příkazy:

```dotnetcli
cd aspnetcoreapp
dotnet run
```

Jakmile příkazové prostředí ukáže, že aplikace začala, přejděte na [https://localhost:5001](https://localhost:5001)adresu. Kliknutím na **přijmout** přijměte zásady ochrany osobních údajů a souborů cookie. Tato aplikace nemá uchovává osobní údaje.

## <a name="edit-a-razor-page"></a>Úprava stránky Razor

Otevřete *pages/index. cshtml* a upravte stránku pomocí následujícího zvýrazněného kódu:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Vyhledejte a ověřte, zda jsou změny zobrazeny. [https://localhost:5001](https://localhost:5001)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořte projekt webové aplikace.
> * Důvěřovat vývojovému certifikátu.
> * Spusťte projekt.
> * Proveďte změnu.

Další informace o ASP.NET Core najdete v tématu doporučený postup výuky v úvodu:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
