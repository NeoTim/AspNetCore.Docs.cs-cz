---
title: Prohlédněte si metody podrobnosti a odstranění aplikace ASP.NET Core
author: rick-anderson
description: Seznamte se s metodou kontroleru podrobností a zobrazením v základní aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 04eb2efa4e67d84e575580a6248d0b5b567064af
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662909"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>Prohlédněte si metody podrobnosti a odstranění aplikace ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Otevřete movie kontroler `Details` a zkontrolujte metodu:

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Modul scaffing Engine MVC, který vytvořil tuto metodu akce přidá komentář zobrazující požadavek HTTP, který vyvolá metodu. V tomto případě je požadavek GET se třemi segmenty URL, `Movies` řadič, `Details` metoda a `id` hodnota. Odvolání tyto segmenty jsou definovány v *Startup.cs*.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF usnadňuje vyhledávání dat pomocí `FirstOrDefaultAsync` metody. Důležitou funkcí zabezpečení integrovanou do metody je, že kód ověří, zda metoda hledání nalezla film dříve, než se s ním pokusí něco udělat. Hacker může například zavést chyby na webu změnou `http://localhost:{PORT}/Movies/Details/1` adresy URL `http://localhost:{PORT}/Movies/Details/12345` vytvořené odkazy z něčeho podobného (nebo jiné hodnoty, která nepředstavuje skutečný film). Pokud jste nezkontrolovali nulový film, aplikace by vyvolat výjimku.

Prozkoumejte `Delete` `DeleteConfirmed` metody a.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Všimněte `HTTP GET Delete` si, že metoda neodstraní zadaný film, vrátí zobrazení filmu, kde můžete odeslat (HttpPost) odstranění. Provedení operace odstranění v reakci na požadavek GET (nebo když na to přijde, provedení operace úprav, vytvoření operace nebo jiné operace, která mění data) otevře bezpečnostní díru.

Metoda, `[HttpPost]` která odstraní data, `DeleteConfirmed` je pojmenována tak, aby metoda HTTP POST měla jedinečný podpis nebo název. Dvě podpisy metody jsou uvedeny níže:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Běžný jazyk runtime (CLR) vyžaduje přetížené metody mít jedinečný podpis parametru (stejný název metody, ale jiný seznam parametrů). Zde však potřebujete `Delete` dvě metody – jednu pro GET a jednu pro POST – které mají oba stejný podpis parametru. (Oba potřebují přijmout jedno celé číslo jako parametr.)

Existují dva přístupy k tomuto problému, jeden je dát metody různé názvy. To je to, co lešení mechanismus udělal v předchozím příkladu. To však zavádí malý problém: ASP.NET mapuje segmenty adresy URL na metody akce podle názvu, a pokud přejmenujete metodu, směrování by normálně nebylo možné najít tuto metodu. Řešení je to, co vidíte v příkladu, který je přidat `ActionName("Delete")` atribut metody. `DeleteConfirmed` Tento atribut provádí mapování pro systém směrování tak, aby adresa URL, `DeleteConfirmed` která obsahuje /Delete/ pro požadavek POST najde metodu.

Další běžné řešení pro metody, které mají identické názvy a podpisy je uměle změnit podpis post metody zahrnout další (nepoužívané) parametr. To je to, co jsme udělali `notUsed` v předchozím příspěvku, když jsme přidali parametr. Dalo by se udělat totéž zde pro metodu: `[HttpPost] Delete`

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Publikování aplikací do Azure

Informace o nasazení do Azure najdete [v tématu: Vytvoření webové aplikace .NET Core a SQL Database ve službě Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

> [!div class="step-by-step"]
> [Předchozí](validation.md)
