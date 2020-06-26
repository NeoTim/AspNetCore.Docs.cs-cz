---
title: Část 10, Projděte si metody Details a DELETE aplikace ASP.NET Core.
author: rick-anderson
description: Část 10 kurzů kurzu na ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 49422cdfac73e586ce3a035ee79d464e9d6a6827
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405156"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>Část 10, Projděte si metody Details a DELETE aplikace ASP.NET Core.

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Otevřete kontroler filmů a prověřte tuto `Details` metodu:

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Modul generování uživatelského rozhraní MVC, který vytvořil tuto metodu Action, přidá komentář ukazující požadavek HTTP, který vyvolá metodu. V tomto případě se jedná o požadavek GET se třemi segmenty adresy URL, `Movies` řadičem, `Details` metodou a `id` hodnotou. Odvolání těchto segmentů je definováno v *Startup.cs*.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF usnadňuje hledání dat pomocí `FirstOrDefaultAsync` metody. Důležitou funkcí zabezpečení, která je integrována do metody, je, že kód ověřuje, zda metoda hledání nalezla film předtím, než se pokusí s ním něco udělat. Hacker by například mohl do lokality způsobit chyby změnou adresy URL vytvořené odkazy z `http://localhost:{PORT}/Movies/Details/1` na něco podobného `http://localhost:{PORT}/Movies/Details/12345` (nebo jiné hodnoty, která nepředstavuje skutečný film). Pokud jste nezkontrolovali film s hodnotou null, aplikace vyvolá výjimku.

Projděte `Delete` si `DeleteConfirmed` metody a.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Všimněte si, že `HTTP GET Delete` Metoda neodstraní zadaný film, vrátí zobrazení videa, kde můžete odeslat (HTTPPOST) odstranění. Provádění operace odstranění v reakci na požadavek GET (nebo pro tuto skutečnost, provádění operace Edit, operace vytvoření nebo jakékoli jiné operace, která mění data) otevře bezpečnostní riziko.

`[HttpPost]`Metoda, která odstraňuje data, je pojmenována `DeleteConfirmed` tak, aby metodě http post poskytovala jedinečný podpis nebo název. Níže jsou uvedené signatury dvou metod:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Modul CLR (Common Language Runtime) vyžaduje, aby přetížené metody měly jedinečný podpis parametru (stejný název metody, ale jiný seznam parametrů). Zde však budete potřebovat dvě `Delete` metody--One pro Get a One pro post--oba mají stejný podpis parametru. (Obě musí přijmout jedno celé číslo jako parametr.)

Existují dva přístupy k tomuto problému. jedna z nich poskytuje metody odlišné názvy. To je to, co byl mechanismus generování uživatelského rozhraní použit v předchozím příkladu. To však přináší malý problém: ASP.NET mapuje segmenty adresy URL na metody akcí podle názvu a Pokud přejmenujete metodu, směrování normálně nedokáže tuto metodu najít. Řešení je to, co vidíte v příkladu, což je přidání `ActionName("Delete")` atributu do `DeleteConfirmed` metody. Tento atribut provádí mapování pro systém směrování, takže adresa URL, která obsahuje/DELETE/pro požadavek POST, tuto metodu najde `DeleteConfirmed` .

Dalším běžným řešením pro metody, které mají stejný název a signatury, je umělá změna signatury metody POST tak, aby zahrnovala nadbytečný (nepoužitý) parametr. To je to, co jsme při přidání parametru použili v předchozím příspěvku `notUsed` . To samé můžete udělat pro `[HttpPost] Delete` metodu:

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Publikování aplikací do Azure

Informace o nasazení do Azure najdete v tématu [kurz: sestavení webové aplikace .NET Core a SQL Database v Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

> [!div class="step-by-step"]
> [Předchozí](validation.md)
