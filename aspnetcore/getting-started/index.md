---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytvoří a spustí základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: getting-started
ms.openlocfilehash: 798f1ee87c05d886d8991e3f0230c8ebc6341ba8
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925103"
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

[!INCLUDE[](~/includes/3.0-SDK.md)]

## <a name="create-a-web-app-project"></a>Vytvoření projektu webové aplikace

Otevřete příkazové prostředí a zadejte následující příkaz:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

Předchozí příkaz:

* Vytvoří novou webovou aplikaci.  
* Parametr vytvoří adresář s názvem aspnetcoreapp se zdrojovými soubory aplikace. `-o aspnetcoreapp`

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
dotnet watch run
```

Jakmile příkazové prostředí ukáže, že aplikace začala, přejděte na [https://localhost:5001](https://localhost:5001)adresu.

## <a name="edit-a-razor-page"></a>Úprava stránky Razor

Otevřete *pages/index. cshtml* a upravte a uložte stránku s následujícím zvýrazněným označením:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

[https://localhost:5001](https://localhost:5001)Vyhledejte, aktualizujte stránku a ověřte, zda jsou zobrazeny změny.

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
