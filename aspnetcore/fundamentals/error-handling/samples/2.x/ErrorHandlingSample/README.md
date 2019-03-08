---
ms.openlocfilehash: b1f7c306533e70f84e73a020c74756b91cae7ea7
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665737"
---
# <a name="error-handling-sample-application"></a>Chyba při zpracování ukázkové aplikace

Tato ukázková aplikace předvádí scénáře popsané v [zpracování chyb v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling) tématu.

## <a name="configure-a-custom-exception-handling-page"></a>Konfigurace vlastní výjimky zpracování stránky

Pokud není aplikace spuštěna ve vývojovém prostředí, Middleware zpracování výjimky:

* Zachytává výjimky.
* Protokoly výjimky.
* Znovu provede požadavek na alternativní kanálu v zadané cestě.

## <a name="configure-custom-exception-handling-code"></a>Konfigurace vlastního kódu pro zpracování výjimek

Alternativa k poskytování koncový bod pro chyby stránky zpracování vlastní výjimky je poskytnout lambda `UseExceptionHandler`. Použití výrazu lambda s `UseExceptionHandler` umožňuje přístup k chybě před vrácením odpovědi.

Ukázková aplikace předvádí vlastní kód ve zpracování výjimek `Startup.Configure`. Postupujte podle pokynů v horní části *Startup.cs* souboru (`LambdaErrorHandler`). Po spuštění aplikace, aktivuje výjimku **vyvolat výjimky** odkaz na indexovou stránku.
