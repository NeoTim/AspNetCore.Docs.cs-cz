---
title: Zobrazení komponenty v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak komponenty zobrazení se používají v ASP.NET Core a jejich přidání do aplikací.
ms.author: riande
ms.date: 12/03/2018
uid: mvc/views/view-components
ms.openlocfilehash: 5812abad80cd906d6b9a7175bd7cdefd03a99eb3
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861326"
---
# <a name="view-components-in-aspnet-core"></a>Zobrazení komponenty v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="view-components"></a>Komponenty zobrazení

Zobrazení komponenty jsou podobné částečná zobrazení, ale jsou výrazně výkonnější. Zobrazení komponenty nepoužívejte vazby modelu a pouze závisí na poskytnutý při volání do něj data. Tento článek byl zapsán pomocí ASP.NET Core MVC, ale zobrazení komponenty také pracovat se stránkami Razor.

Zobrazení komponenty:

* Vykreslí blok dat, ne celou odpověď.
* Zahrnuje stejné oddělení z otázky a výhody testovatelnosti najít mezi kontroler a zobrazení.
* Můžete mít parametry a obchodní logiku.
* Obvykle je vyvolána z rozložení stránky.

Zobrazení komponenty jsou určeny kdekoli, že máte opakovaně použitelný vykreslování logiku, která je příliš složitý pro částečné zobrazení, jako například:

* Dynamické navigační nabídky
* Značka cloudu (kde dotazuje databázi)
* Panel přihlášení
* Nákupní košík
* Nedávno publikovaných článků
* Obsah bočního panelu na typické blogu
* Panel přihlášení, který by být vykreslen na každé stránce a zobrazit odkazy na odhlášení nebo se přihlaste, v závislosti na protokolu ve stavu uživatele

Komponenty zobrazení se skládá ze dvou částí: třídy (obvykle odvozen z [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) a výsledek se vrátí (obvykle zobrazení). Jako jsou řadiče, může být zobrazení komponenty POCO, ale Většina vývojářů budete chtít využívat výhod metod a vlastností, které jsou k dispozici odvozením z `ViewComponent`.

## <a name="creating-a-view-component"></a>Vytvoření zobrazení komponenty

Tato část obsahuje základní požadavky na vytvoření komponenty zobrazení. Později v tomto článku vytvoříme Zkontrolujte každý krok podrobně a vytvořte komponentu zobrazení.

### <a name="the-view-component-class"></a>Zobrazení komponentní třída

Komponentní třída zobrazení lze vytvořit pomocí některé z následujících akcí:

* Odvozování z *ViewComponent*
* Upravení třída s atributem `[ViewComponent]` atribut nebo odvozování z třídy s `[ViewComponent]` atribut
* Vytvoření třídy, kde název končí příponou *ViewComponent*

Jako jsou řadiče zobrazení komponenty musí být veřejné, bez vnoření a neabstraktní třídy. Název komponenty zobrazení je název třídy s příponou "ViewComponent" odebrat. To se dá také explicitně nastavit pomocí `ViewComponentAttribute.Name` vlastnost.

Zobrazení komponentní třída:

* Plně podporuje konstruktor [injektáž závislostí](../../fundamentals/dependency-injection.md)

* Není účastnit životního cyklu kontroleru, což znamená, že nemůžete použít [filtry](../controllers/filters.md) v komponentě zobrazení

### <a name="view-component-methods"></a>Zobrazení komponenty metody

Zobrazení komponenty definuje svou logikou v `InvokeAsync` metodu, která vrací `Task<IViewComponentResult>` nebo v synchronního `Invoke` metodu, která vrátí `IViewComponentResult`. Parametry pocházejí přímo z volání zobrazení komponenty, nikoli z vazby modelu. Zobrazení komponenty nikdy přímo zpracovává žádost. Obvykle inicializuje model zobrazení komponenty a předá ji do zobrazení voláním `View` metody. Stručně řečeno zobrazte metody komponenty:

* Definování `InvokeAsync` metodu, která vrací `Task<IViewComponentResult>` nebo synchronního `Invoke` metodu, která vrátí `IViewComponentResult`.
* Obvykle inicializuje model a předává je do zobrazení pomocí volání `ViewComponent` `View` metody.
* Parametry pocházejí z volání metody, ne HTTP. Neexistuje žádná vazba modelu.
* Nejsou dostupné přímo jako koncový bod HTTP. Se už vyvolána z uživatelského kódu (obvykle v zobrazení). Zobrazení komponenty nikdy zpracuje požadavek.
* Jsou přetížené na podpis a nikoli na jakékoli podrobnosti z aktuální žádosti HTTP.

### <a name="view-search-path"></a>Zobrazení cesty pro hledání

Modul runtime vyhledává zobrazení v následující cesty:

* Řetězec/Pages/součásti / {název komponenty zobrazení} / {název zobrazení}
* /Components/ /views/ {název řadiče} {název komponenty zobrazení} / {název zobrazení}
* / Zobrazení/Shared/Components / {View název komponenty} / {název zobrazení}

Výchozí název zobrazení pro součást zobrazení je *výchozí*, což znamená, že váš soubor zobrazení se obvykle nazývá *stránku Default.cshtml*. Můžete zadat název jiné zobrazení, při vytváření komponenty výsledný objekt zobrazení, nebo při volání `View` metody.

Doporučujeme pojmenovat soubor zobrazení *stránku Default.cshtml* a použít *zobrazení/Shared/Components / {název komponenty zobrazení} / {název zobrazení}* cestu. `PriorityList` Komponenta zobrazení používané v tomto příkladu používá *Views/Shared/Components/PriorityList/Default.cshtml* pro součásti zobrazení.

## <a name="invoking-a-view-component"></a>Vyvolání komponenty zobrazení

Chcete-li použít komponentu zobrazení, zavolejte následující uvnitř zobrazení:

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

Parametry předávané `InvokeAsync` metody. `PriorityList` z je vyvolána zobrazení komponenty vyvinuté v následujícím článku *Views/Todo/Index.cshtml* zobrazení souboru. V následujícím příkladu `InvokeAsync` metoda je volána s dva parametry:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a>Vyvolání komponenty zobrazení jako pomocné rutiny značky

Pro ASP.NET Core 1.1 a vyšší, můžete vyvolat komponentu zobrazení jako [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro):

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

Jazyka Pascal – třídy a metody parametry pro pomocné rutiny značek jsou přeloženy do jejich [snížit kebab případ](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). Pomocná rutina značky k vyvolání komponenty zobrazení používá `<vc></vc>` elementu. Zobrazení komponenty je určena následujícím způsobem:

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

Použití zobrazení komponenty jako pomocné rutiny značky, zaregistrovat sestavení obsahující pomocí zobrazení komponenty `@addTagHelper` směrnice. Pokud vaše komponenta zobrazení je v sestavení nazvané `MyWebApp`, přidejte následující direktivy k *_ViewImports.cshtml* souboru:

```cshtml
@addTagHelper *, MyWebApp
```

Zobrazení komponenty můžete zaregistrovat jako pomocné rutiny značky do souboru, která odkazuje na součást zobrazení. Zobrazit [Správa oboru pomocné rutiny značky](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) Další informace o tom, jak zaregistrovat pomocných rutin značek.

`InvokeAsync` Metodu použitou v tomto kurzu:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

Ve značkách pomocné rutiny značky:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

V příkladu výše `PriorityList` stane součástí zobrazení `priority-list`. Parametry pro zobrazení komponenty jsou předány jako atributy v malá písmena kebab.

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a>Vyvolání komponenty zobrazení přímo z kontroleru

Zobrazení komponenty jsou obvykle vyvolány ze zobrazení, ale můžete je vyvolat přímo z metody kontroleru. Při zobrazení komponenty nebudete definovat koncových bodů, jako jsou řadiče, je možné snadno implementovat akce kontroleru, který vrátí obsah `ViewComponentResult`.

V tomto příkladu je součásti zobrazení volání přímo z kontroleru:

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>Návod: Vytvoření jednoduché zobrazení komponenty

[Stáhněte si](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), sestavení a testování počátečního kódu. Je to Jednoduchý projekt s `Todo` kontroler, který zobrazí seznam *Todo* položky.

![Seznam úloh, ať už](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>Přidejte třídu ViewComponent

Vytvoření *ViewComponents* složky a přidejte následující `PriorityListViewComponent` třídy:

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

Poznámky k kód:

* Zobrazení komponentní třídy mohou být obsaženy v **jakékoli** složky v projektu.
* Protože třída název PriorityList**ViewComponent** končí příponou **ViewComponent**, modul runtime použije řetězec "PriorityList" při odkazování na komponentě třídy ze zobrazení. Já zatím vysvětlím, které podrobněji později.
* `[ViewComponent]` Atribut můžete změnit název slouží jako odkaz na komponentu zobrazení. Například jsme mohli jsme s názvem třídy `XYZ` a použít `ViewComponent` atribut:

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* `[ViewComponent]` Výše uvedený atribut říká Výběr komponent zobrazení použít název `PriorityList` při hledání zobrazení související s komponentou a použít řetězec "PriorityList" při odkazování na komponentě třídy ze zobrazení. Já zatím vysvětlím, které podrobněji později.
* Součást používá [injektáž závislostí](../../fundamentals/dependency-injection.md) zpřístupnit datového kontextu.
* `InvokeAsync` Zpřístupní metodu, která může být volána z zobrazení a to může trvat libovolný počet argumentů.
* `InvokeAsync` Metoda vrátí sadu `ToDo` položky, které splňují `isDone` a `maxPriority` parametry.

### <a name="create-the-view-component-razor-view"></a>Vytvořit zobrazení Razor komponenty zobrazení

* Vytvořte *zobrazení/Shared/Components* složky. Tato složka **musí** jmenovat *komponenty*.

* Vytvořte *zobrazení/Shared/součásti/PriorityList* složky. Tento název složky musí odpovídat názvu třídy zobrazení komponenty nebo název třídy minus příponu (Pokud jsme postupovali podle úmluvy a použít *ViewComponent* přípony v názvu třídy). Pokud jste použili `ViewComponent` atribut, název třídy by musí odpovídat atributu označení.

* Vytvoření *Views/Shared/Components/PriorityList/Default.cshtml* zobrazení Razor: [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]
    
   Zobrazení Razor přebírá seznam `TodoItem` a zobrazí je. Pokud komponentu zobrazení `InvokeAsync` metoda neprojde název zobrazení (jako v naší ukázce) *výchozí* používají konvence pro název zobrazení. Později v tomto kurzu můžu ukážeme, jak předat název zobrazení. Pokud chcete přepsat výchozí styl k určitému kontroleru, přidejte do specifické pro kontroler zobrazení složky zobrazení (například *Views/Todo/Components/PriorityList/Default.cshtml)*.
    
    Pokud je součást zobrazení specifické pro kontroler, můžete ho přidat do složky specifické pro kontroler (*Views/Todo/Components/PriorityList/Default.cshtml*).

* Přidat `div` obsahujícím volání do seznamu součástí priority k dolnímu okraji *Views/Todo/index.cshtml* souboru:

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]

Značky `@await Component.InvokeAsync` ukazuje syntaxi pro volání komponenty zobrazení. Prvním argumentem je název komponenty, které chceme volání nebo volání. Následující parametry jsou předány do komponenty. `InvokeAsync` můžete využít libovolný počet argumentů.

Testování aplikace. Následující obrázek ukazuje seznam úkolů a prioritu položky:

![seznam a prioritu položek todo](view-components/_static/pi.png)

Komponenty zobrazení můžete také volat přímo z kontroleru:

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![prioritu položek z IndexVC akce](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>Zadejte název zobrazení

Komponenta komplexní zobrazení může být nutné určit jiné než výchozí zobrazení za určitých podmínek. Následující kód ukazuje, jak zadat "PVC" zobrazení z `InvokeAsync` metody. Aktualizace `InvokeAsync` metodu `PriorityListViewComponent` třídy.

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

Kopírovat *Views/Shared/Components/PriorityList/Default.cshtml* soubor k zobrazení s názvem *Views/Shared/Components/PriorityList/PVC.cshtml*. Přidáte záhlaví označující, že se používá PVC zobrazení.

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

Aktualizace *Views/TodoList/Index.cshtml*:

<!-- Views/TodoList/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

Spusťte aplikaci a ověřte PVC zobrazení.

![Komponenty zobrazení prioritní](view-components/_static/pvc.png)

Pokud není PVC zobrazení vykresleno, ověřte, zda že jsou volání komponenty zobrazení s prioritou 4 nebo vyšší.

### <a name="examine-the-view-path"></a>Zkontrolujte cestu zobrazení

* Proto se vrátí zobrazení prioritní změňte parametr priority na tři nebo i rychleji.
* Dočasně přejmenujte *Views/Todo/Components/PriorityList/Default.cshtml* k *1Default.cshtml*.
* Testování aplikace, získáte následující chybu:

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* Kopírování *Views/Todo/Components/PriorityList/1Default.cshtml* k *Views/Shared/Components/PriorityList/Default.cshtml*.
* Přidat některé značky *Shared* Todo zobrazení komponenty k označení zobrazení je z *Shared* složky.
* Test **Shared** součásti zobrazení.

![Výstup úkolů s sdílené komponenty zobrazení](view-components/_static/shared.png)

### <a name="avoiding-magic-strings"></a>Jak se vyhnout magic řetězce

Pokud chcete kompilovat bezpečný přístup z více času, můžete nahradit název komponenty pevně zakódované zobrazení s názvem třídy. Vytvoření zobrazení komponenty bez přípony "ViewComponent":

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

Přidat `using` příkazu vaše Razor zobrazení souboru a použít `nameof` operátor:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a>Provedení synchronní práce

Rozhraní framework zpracovává volání synchronního `Invoke` metodu, pokud není nutné provádět asynchronní práce. Následující metoda vytvoří synchronního `Invoke` zobrazení komponenty:

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

Dílčí zobrazení Razor soubor obsahuje řetězce předaný `Invoke` – metoda (*Views/Home/Components/PriorityList/Default.cshtml*):

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

Zobrazení komponenty je vyvolán v souboru Razor (například *Views/Home/Index.cshtml*) pomocí některého z následujících postupů:

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [Pomocná rutina značky](xref:mvc/views/tag-helpers/intro)

Použít <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> přístup, zavolejte `Component.InvokeAsync`:

::: moniker-end

::: moniker range="< aspnetcore-1.1"

Zobrazení komponenty je vyvolán v souboru Razor (například *Views/Home/Index.cshtml*) s <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.

Volání `Component.InvokeAsync`:

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

Použití pomocné rutiny značky, zaregistrovat sestavení obsahující pomocí zobrazení komponenty `@addTagHelper` – direktiva (součást zobrazení je v sestavení nazvané `MyWebApp`):

```cshtml
@addTagHelper *, MyWebApp
```

Použití zobrazení komponenty pomocné rutiny značky v souboru kódu Razor:

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```
::: moniker-end

Označení metody `PriorityList.Invoke` je synchronní, ale Razor najde a volá metodu s `Component.InvokeAsync` v souboru označení.

## <a name="additional-resources"></a>Další zdroje

* [Injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection)
