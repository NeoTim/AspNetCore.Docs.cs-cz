---
title: Začínáme s Razor komponenty
author: guardrex
description: Zjistěte, jak začít pracovat s rozhraní komponenty Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/get-started
ms.openlocfilehash: c83af10fd84bc8238f5fe20c66b91ba17de80ae3
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668096"
---
# <a name="get-started-with-razor-components"></a><span data-ttu-id="b7535-103">Začínáme s Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="b7535-103">Get started with Razor Components</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="setup"></a><span data-ttu-id="b7535-104">Instalace</span><span class="sxs-lookup"><span data-stu-id="b7535-104">Setup</span></span>

<span data-ttu-id="b7535-105">Nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="b7535-105">Install the following:</span></span>

1. <span data-ttu-id="b7535-106">[Sady SDK .NET core 2.1](https://go.microsoft.com/fwlink/?linkid=873092) (2.1.500 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="b7535-106">[.NET Core 2.1 SDK](https://go.microsoft.com/fwlink/?linkid=873092) (2.1.500 or later).</span></span>
1. <span data-ttu-id="b7535-107">[Visual Studio 2017](https://go.microsoft.com/fwlink/?linkid=873093) (15.9 nebo vyšší) se *vývoj pro ASP.NET a web* vybranou úlohou.</span><span class="sxs-lookup"><span data-stu-id="b7535-107">[Visual Studio 2017](https://go.microsoft.com/fwlink/?linkid=873093) (15.9 or later) with the *ASP.NET and web development* workload selected.</span></span>
1. <span data-ttu-id="b7535-108">Nejnovější [rozšíření služeb jazyka Blazor](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="b7535-108">The latest [Blazor Language Services extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span>
1. <span data-ttu-id="b7535-109">Šablony Blazor na příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="b7535-109">The Blazor templates on the command-line:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

<span data-ttu-id="b7535-110">Chcete-li vytvořit svůj první projekt v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b7535-110">To create your first project from Visual Studio:</span></span>

1. <span data-ttu-id="b7535-111">Vyberte **souboru** > **nový projekt** > **webové** > **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b7535-111">Select **File** > **New Project** > **Web** > **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="b7535-112">Ujistěte se, že **.NET Core** a **ASP.NET Core 2.1** jsou vybrány v horní části.</span><span class="sxs-lookup"><span data-stu-id="b7535-112">Make sure **.NET Core** and **ASP.NET Core 2.1** are selected at the top.</span></span>
1. <span data-ttu-id="b7535-113">Výběr šablony funkce Blazor a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="b7535-113">Choose the Blazor template and select **OK**.</span></span>

   ![Dialogové okno nové aplikace](https://msdnshared.blob.core.windows.net/media/2018/07/new-blazor-app-dialog-0.5.0.png)

1. <span data-ttu-id="b7535-115">Stisknutím klávesy **Ctrl-F5** a spusťte tak aplikaci *bez ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="b7535-115">Press **Ctrl-F5** to run the app *without the debugger*.</span></span> <span data-ttu-id="b7535-116">Spouštění v ladicím programu (**F5**) není v tuto chvíli nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="b7535-116">Running with the debugger (**F5**) isn't supported at this time.</span></span>

<span data-ttu-id="b7535-117">Chcete-li vytvořit novou aplikaci Blazor z příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="b7535-117">To create a new Blazor app from the command-line:</span></span>

```console
dotnet new blazor -o BlazorApp1
cd BlazorApp1
dotnet run
```

<span data-ttu-id="b7535-118">Gratulujeme!</span><span class="sxs-lookup"><span data-stu-id="b7535-118">Congrats!</span></span> <span data-ttu-id="b7535-119">Právě jste spustili svou první aplikaci Blazor!</span><span class="sxs-lookup"><span data-stu-id="b7535-119">You just ran your first Blazor app!</span></span>

![Domovská stránka Blazor aplikace](https://msdnshared.blob.core.windows.net/media/2018/04/blazor-bootstrap-4.png)

## <a name="help--feedback"></a><span data-ttu-id="b7535-121">Nápověda a zpětná vazba</span><span class="sxs-lookup"><span data-stu-id="b7535-121">Help & feedback</span></span>

<span data-ttu-id="b7535-122">Váš názor je pro nás obzvláště důležité v průběhu této fáze experimentální pro Blazor.</span><span class="sxs-lookup"><span data-stu-id="b7535-122">Your feedback is especially important to us during this experimental phase for Blazor.</span></span> <span data-ttu-id="b7535-123">Pokud narazíte na problémy nebo při pokusu o navýšení kapacity Blazor na něco zeptat, dejte nám vědět!</span><span class="sxs-lookup"><span data-stu-id="b7535-123">If you run into issues or have questions while trying out Blazor, please let us know!</span></span>

* <span data-ttu-id="b7535-124">[Soubor problémů na Githubu](https://github.com/aspnet/AspNetCore/issues) jakékoli problémy, které jste narazili nebo a nabízí návrhy pro zlepšení.</span><span class="sxs-lookup"><span data-stu-id="b7535-124">[File issues on GitHub](https://github.com/aspnet/AspNetCore/issues) for any problems you run into or to make suggestions for improvements.</span></span>
* <span data-ttu-id="b7535-125">Chat s námi a Blazor komunity na [Gitteru](https://gitter.im/aspnet/blazor) Pokud jste nevíte rady nebo sdílet Blazor fungování za vás.</span><span class="sxs-lookup"><span data-stu-id="b7535-125">Chat with us and the Blazor community on [Gitter](https://gitter.im/aspnet/blazor) if you get stuck or to share how Blazor is working for you.</span></span>

<span data-ttu-id="b7535-126">Poté, co jste si vyzkoušeli Blazor, dejte nám prosím vědět co si myslíte o našem průzkumu v rámci produktu.</span><span class="sxs-lookup"><span data-stu-id="b7535-126">After you've tried out Blazor, please let us know what you think by taking our in-product survey.</span></span> <span data-ttu-id="b7535-127">Stačí kliknout na odkaz průzkumu zobrazený na domovské stránce aplikace, když s některým z šablony projektů Blazor:</span><span class="sxs-lookup"><span data-stu-id="b7535-127">Just click the survey link shown on the app home page when running one of the Blazor project templates:</span></span>

![Blazor průzkumu](https://msdnshared.blob.core.windows.net/media/2018/05/blazor-survey-new.png)

## <a name="next-steps"></a><span data-ttu-id="b7535-129">Další kroky</span><span class="sxs-lookup"><span data-stu-id="b7535-129">Next steps</span></span>

<xref:tutorials/first-blazor-app>
