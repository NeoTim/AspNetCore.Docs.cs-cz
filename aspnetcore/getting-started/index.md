---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytvoří a spustí základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: getting-started
ms.openlocfilehash: b88460cdff5d8c30c6a28afdb4f67e8e0b6b819c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403362"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Kurz: Začínáme s ASP.NET Core

V tomto kurzu se dozvíte, jak vytvořit a spustit webovou aplikaci ASP.NET Core pomocí .NET Core CLI.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvořte projekt webové aplikace.
> * Důvěřovat vývojovému certifikátu.
> * Spusťte aplikaci.
> * Upravte Razor stránku.

Na konci budete mít na svém místním počítači spuštěnou funkční webovou aplikaci.

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
* `-o aspnetcoreapp`Parametr vytvoří adresář s názvem *aspnetcoreapp* se zdrojovými soubory aplikace.

### <a name="trust-the-development-certificate"></a>Důvěřovat vývojovému certifikátu

Důvěřovat vývojovému certifikátu HTTPS:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

Předchozí příkaz zobrazí následující dialog:

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

Předchozí příkaz zobrazí následující zprávu:

*Byl požadován vztah důvěryhodnosti s vývojovým certifikátem https. Pokud certifikát ještě není důvěryhodný, spustíme následující příkaz:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

Tento příkaz vás může vyzvat k zadání hesla pro instalaci certifikátu do systémového řetězce klíčů. Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, zadejte své heslo.

# <a name="linux"></a>[Linux](#tab/linux)

Informace o tom, jak důvěřovat certifikátu vývoje HTTPS, najdete v dokumentaci k distribuci systému Linux.

---

Další informace najdete v tématu [důvěryhodnost ASP.NET Core certifikát pro vývoj https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte následující příkazy:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Jakmile příkazové prostředí ukáže, že aplikace začala, přejděte na adresu `https://localhost:5001` .

## <a name="edit-a-razor-page"></a>Úprava Razor stránky

Otevřete *pages/index. cshtml* a upravte a uložte stránku s následujícím zvýrazněným označením:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Vyhledejte `https://localhost:5001` , aktualizujte stránku a ověřte, zda jsou zobrazeny změny.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořte projekt webové aplikace.
> * Důvěřovat vývojovému certifikátu.
> * Spusťte projekt.
> * Proveďte změnu.

Další informace o ASP.NET Core najdete v tématu doporučený postup výuky v úvodu:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
