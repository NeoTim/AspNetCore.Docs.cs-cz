---
ms.openlocfilehash: b1f7c306533e70f84e73a020c74756b91cae7ea7
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665737"
---
# <a name="error-handling-sample-application"></a><span data-ttu-id="4b576-101">Chyba při zpracování ukázkové aplikace</span><span class="sxs-lookup"><span data-stu-id="4b576-101">Error Handling Sample Application</span></span>

<span data-ttu-id="4b576-102">Tato ukázková aplikace předvádí scénáře popsané v [zpracování chyb v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling) tématu.</span><span class="sxs-lookup"><span data-stu-id="4b576-102">This sample app demonstrates the scenarios described in the [Handle errors in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling) topic.</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="4b576-103">Konfigurace vlastní výjimky zpracování stránky</span><span class="sxs-lookup"><span data-stu-id="4b576-103">Configure a custom exception handling page</span></span>

<span data-ttu-id="4b576-104">Pokud není aplikace spuštěna ve vývojovém prostředí, Middleware zpracování výjimky:</span><span class="sxs-lookup"><span data-stu-id="4b576-104">When the app isn't running in the Development environment, Exception Handling Middleware:</span></span>

* <span data-ttu-id="4b576-105">Zachytává výjimky.</span><span class="sxs-lookup"><span data-stu-id="4b576-105">Catches exceptions.</span></span>
* <span data-ttu-id="4b576-106">Protokoly výjimky.</span><span class="sxs-lookup"><span data-stu-id="4b576-106">Logs exceptions.</span></span>
* <span data-ttu-id="4b576-107">Znovu provede požadavek na alternativní kanálu v zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="4b576-107">Re-executes the request in an alternate pipeline at the path provided.</span></span>

## <a name="configure-custom-exception-handling-code"></a><span data-ttu-id="4b576-108">Konfigurace vlastního kódu pro zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="4b576-108">Configure custom exception handling code</span></span>

<span data-ttu-id="4b576-109">Alternativa k poskytování koncový bod pro chyby stránky zpracování vlastní výjimky je poskytnout lambda `UseExceptionHandler`.</span><span class="sxs-lookup"><span data-stu-id="4b576-109">An alternative to serving an endpoint for errors with a custom exception handling page is to provide a lambda to `UseExceptionHandler`.</span></span> <span data-ttu-id="4b576-110">Použití výrazu lambda s `UseExceptionHandler` umožňuje přístup k chybě před vrácením odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4b576-110">Using a lambda with `UseExceptionHandler` allows access to the error before returning the response.</span></span>

<span data-ttu-id="4b576-111">Ukázková aplikace předvádí vlastní kód ve zpracování výjimek `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="4b576-111">The sample app demonstrates custom exception handling code in `Startup.Configure`.</span></span> <span data-ttu-id="4b576-112">Postupujte podle pokynů v horní části *Startup.cs* souboru (`LambdaErrorHandler`).</span><span class="sxs-lookup"><span data-stu-id="4b576-112">Follow the instructions at the top of the *Startup.cs* file (`LambdaErrorHandler`).</span></span> <span data-ttu-id="4b576-113">Po spuštění aplikace, aktivuje výjimku **vyvolat výjimky** odkaz na indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="4b576-113">After the app starts, trigger an exception with the **Throw Exception** link on the Index page.</span></span>
