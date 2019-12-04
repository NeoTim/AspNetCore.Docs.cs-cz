---
title: Projděte si metody Details a DELETE aplikace ASP.NET Core.
author: rick-anderson
description: Přečtěte si informace o metodě kontroleru podrobností a zobrazení v základní aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 04eb2efa4e67d84e575580a6248d0b5b567064af
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803380"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>Projděte si metody Details a DELETE aplikace ASP.NET Core.

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Otevřete kontroler filmů a prověřte `Details` metodu:

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Modul generování uživatelského rozhraní MVC, který vytvořil tuto metodu Action, přidá komentář ukazující požadavek HTTP, který vyvolá metodu. V tomto případě se jedná o požadavek GET se třemi segmenty adresy URL, `Movies` kontrolérem, `Details` metodou a hodnotou `id`. Odvolání těchto segmentů je definováno v *Startup.cs*.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF usnadňuje hledání dat pomocí metody `FirstOrDefaultAsync`. Důležitou funkcí zabezpečení, která je integrována do metody, je, že kód ověřuje, zda metoda hledání nalezla film předtím, než se pokusí s ním něco udělat. Hacker by například mohl do lokality způsobit chyby tím, že změní adresu URL vytvořenou odkazy z `http://localhost:{PORT}/Movies/Details/1` na něco jako `http://localhost:{PORT}/Movies/Details/12345` (nebo některá jiná hodnota, která nepředstavuje skutečný film). Pokud jste nezkontrolovali film s hodnotou null, aplikace vyvolá výjimku.

Projděte si metody `Delete` a `DeleteConfirmed`.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Všimněte si, že metoda `HTTP GET Delete` neodstraní zadaný film, vrátí zobrazení videa, kde můžete odeslat (HttpPost) odstranění. Provádění operace odstranění v reakci na požadavek GET (nebo pro tuto skutečnost, provádění operace Edit, operace vytvoření nebo jakékoli jiné operace, která mění data) otevře bezpečnostní riziko.

Metoda `[HttpPost]`, která odstraňuje data, má název `DeleteConfirmed`, který metodě HTTP POST udělí jedinečný podpis nebo název. Níže jsou uvedené signatury dvou metod:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Modul CLR (Common Language Runtime) vyžaduje, aby přetížené metody měly jedinečný podpis parametru (stejný název metody, ale jiný seznam parametrů). Tady ale budete potřebovat dvě `Delete` metody – jednu pro GET a jednu pro POST--oba mají stejný podpis parametru. (Obě musí přijmout jedno celé číslo jako parametr.)

Existují dva přístupy k tomuto problému. jedna z nich poskytuje metody odlišné názvy. To je to, co byl mechanismus generování uživatelského rozhraní použit v předchozím příkladu. To však přináší malý problém: ASP.NET mapuje segmenty adresy URL na metody akcí podle názvu a Pokud přejmenujete metodu, směrování normálně nedokáže tuto metodu najít. Řešení je to, co vidíte v příkladu, což je přidání atributu `ActionName("Delete")` do metody `DeleteConfirmed`. Tento atribut provádí mapování pro systém směrování tak, aby adresa URL, která obsahuje/DELETE/pro požadavek POST, mohla najít metodu `DeleteConfirmed`.

Dalším běžným řešením pro metody, které mají stejný název a signatury, je umělá změna signatury metody POST tak, aby zahrnovala nadbytečný (nepoužitý) parametr. To je to, co jsme při přidání parametru `notUsed` provedli v předchozím příspěvku. Totéž můžete udělat pro `[HttpPost] Delete` metodu:

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Publikování do Azure

Informace o nasazení do Azure najdete v tématu [kurz: sestavení webové aplikace .NET Core a SQL Database v Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

> [!div class="step-by-step"]
> [Předchozí](validation.md)
