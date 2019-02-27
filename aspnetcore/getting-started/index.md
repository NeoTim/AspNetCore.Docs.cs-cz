---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: 'Rychlý kurz, který vytvoří a spustí jednoduchou aplikaci Hello World pomocí ASP.NET Core.'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2019
uid: getting-started
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

* [.NET Core 2.2 SDK](https://www.microsoft.com/net/download/all)

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

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

Vyberte **Ano** Pokud vyjádříte souhlas s důvěřovat certifikátu vývoje.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```console
dotnet dev-certs https --trust
```

Ve výstupu předchozího příkazu se zobrazí následující zpráva:

*Byla vyžádána důvěřující vývojářský certifikát HTTPS. Pokud certifikát není důvěryhodný provedeme následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.
 
Tento příkaz příkaz vás může vyzvat k zadání hesla k instalaci certifikátu v řetězci klíčů systému. Zadejte svoje heslo, pokud vyjádříte souhlas s důvěřovat certifikátu vývoje.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

O tom, jak důvěřovat certifikátu protokolu HTTPS vývoj naleznete v dokumentaci k vaší distribuci Linuxu.

---

Další informace najdete v tématu [důvěřovat certifikátu vývoj pro ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte následující příkazy:

```console
cd aspnetcoreapp
dotnet run
```

Po příkazového okna označuje, aplikace byla spuštěna, přejděte do [ https://localhost:5001 ](https://localhost:5001). Klikněte na tlačítko **přijmout** přijměte zásady ochrany osobních údajů a soubory cookie. Tato aplikace nemá uchovává osobní údaje.

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

Další informace o ASP.NET Core najdete v tématu Doporučené výuky v úvodu:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
