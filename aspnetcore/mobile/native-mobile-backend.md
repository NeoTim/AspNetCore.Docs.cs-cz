---
title: Vytvoření back-endových služeb pro nativní mobilní aplikace pomocí ASP.NET Core
author: ardalis
description: Zjistěte, jak vytvořit back-endových služeb pro podporu nativních mobilních aplikací pomocí ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: mobile/native-mobile-backend
ms.openlocfilehash: b50d2593d7dc4b89472033898373e3a22fc9a7a3
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64899079"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Vytvoření back-endových služeb pro nativní mobilní aplikace pomocí ASP.NET Core

Podle [Steve Smith](https://ardalis.com/)

Mobilní aplikace může komunikovat s back-endových služeb ASP.NET Core. Pokyny pro připojení místní webové služby z emulátory Androidu a simulátory Iosu, najdete v části [připojení k místním webovým službám z iOS emulátory Androidu a simulátory](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Zobrazení nebo stažení ukázkového kódu služby back-endu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>Ukázka nativní mobilní aplikace

Tento kurz ukazuje, jak vytvořit back-endových služeb pro podporu nativních mobilních aplikací pomocí ASP.NET Core MVC. Používá [aplikace Xamarin Forms ToDoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) jako jeho nativního klienta, který obsahuje samostatné nativní klienty pro zařízení s Androidem, iOS, Windows Universal a Windows Phone. Vám může propojené kurzu a vytvořte nativní aplikaci (a nainstalujte potřebné bezplatné nástroje Xamarin), jakož i stáhnout ukázkové řešení Xamarin. Ukázka Xamarin zahrnuje služby projektu aplikace ASP.NET Web API 2, nahradí aplikace ASP.NET Core v tomto článku (se nezmění klientů).

![Proveďte zbývající aplikaci běžící v Androidu chytrém telefonu](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Funkce

Aplikace ToDoRest podporuje výpis, přidávání, odstraňování a aktualizace položek úkolů. Každá položka má ID, název, poznámky a vlastnost určující, jestli se to se dělá ještě.

Hlavní zobrazení položek, jak je uvedeno výše, uvádí název každé položky a označuje, pokud se provádí pomocí značka zaškrtnutí.

Klepnutím `+` ikonu otevře dialogové okno Přidat položky:

![Přidat položky dialogového okna](native-mobile-backend/_static/todo-android-new-item.png)

Klepnutím na položku na obrazovce hlavní seznam otevře úpravy dialogové okno, kde název položky, poznámky a Hotovo nastavení můžete upravit, nebo můžete odstranit položky:

![Upravit položky dialogového okna](native-mobile-backend/_static/todo-android-edit-item.png)

Tato ukázka je nakonfigurovaná ve výchozím nastavení používat back-endových služeb hostovaných developer.xamarin.com, které umožňují operacím jen pro čtení. K otestování sami proti aplikace ASP.NET Core vytvořená v další části v počítači spuštěný, bude nutné aktualizovat aplikace `RestUrl` konstantní. Přejděte `ToDoREST` projektu a otevřete *Constants.cs* souboru. Nahradit `RestUrl` pomocí adresy URL, která obsahuje váš počítač IP adresa (ne localhost nebo 127.0.0.1, protože tato adresa se používá z emulátor zařízení, ne ze svého počítače). Zahrnují číslo portu (5 000). Pokud chcete otestovat, jestli vaše služby fungovat s zařízení, ujistěte se, že nemáte aktivní brána firewall blokuje přístup na tento port.

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>Vytváří se projekt ASP.NET Core

Vytvořte novou webovou aplikaci ASP.NET Core v sadě Visual Studio. Zvolte šablonu webového rozhraní API a bez ověřování. Pojmenujte projekt *ToDoApi*.

![Dialogové okno nové webové aplikace ASP.NET máte zvolenou šablonu projektu webového rozhraní API](native-mobile-backend/_static/web-api-template.png)

Aplikace by měl odpovědět na všechny žádosti na portu 5000. Aktualizace *Program.cs* zahrnout `.UseUrls("http://*:5000")` dosáhnout:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> Zajistěte, aby že přímo, namísto za službu IIS Express, která ve výchozím nastavení ignoruje požadavky na jiné než místní spuštění aplikace. Spustit [dotnet spustit](/dotnet/core/tools/dotnet-run) z příkazového řádku, nebo zvolte název profilu aplikace v rozevíracím seznamu cíl ladění na panelu nástrojů sady Visual Studio.

Přidejte třídu modelu k reprezentaci položek úkolů. Označit požadovaných polí pomocí `[Required]` atribut:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

Metody rozhraní API vyžadují způsob, jak pracovat s daty. Použijte stejný `IToDoRepository` rozhraní původní Ukázka používá Xamarin:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

V tomto příkladu používá implementaci soukromé kolekce položek:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

Konfigurace implementace v *Startup.cs*:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

V tuto chvíli jste připraveni vytvořit *ToDoItemsController*.

> [!TIP]
> Další informace o vytváření webových rozhraní API v [sestavení první webové rozhraní API pomocí ASP.NET Core MVC a sady Visual Studio](../tutorials/first-web-api.md).

## <a name="creating-the-controller"></a>Vytvoření Kontroleru

Přidat nový kontroler do projektu, *ToDoItemsController*. By měla dědit z Microsoft.AspNetCore.Mvc.Controller. Přidat `Route` atribut označuje, že kontroler bude zpracovávat požadavky na cesta začínající řetězcem `api/todoitems`. `[controller]` Token v této trase je nahrazena název kontroleru (vynechání `Controller` přípony) a je zvláště užitečné pro globální trasy. Další informace o [směrování](../fundamentals/routing.md).

Kontroler vyžaduje `IToDoRepository` na funkci; požádat o instance tohoto typu pomocí konstruktoru kontroleru. Za běhu, tuto instanci vám poskytneme pomocí podpory rozhraní framework pro [injektáž závislostí](../fundamentals/dependency-injection.md).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

Toto rozhraní API podporuje čtyři různé příkazy HTTP k provádění operací CRUD (vytváření, čtení, Update, Delete) ve zdroji dat. Nejjednodušším z nich je operace čtení, které odpovídá požadavku HTTP GET.

### <a name="reading-items"></a>Čtení položky

Probíhá vyžádání seznamu položek se provádí požadavek GET na `List` metody. `[HttpGet]` Atribut na `List` metoda označuje, že tato akce jenom zpracování požadavků GET. Trasy pro tuto akci je Zadaná trasa na kontroleru. Není nutné nutně používat název akce jako součást trasy. Stačí zajistit, že každá akce má jedinečný a jednoznačným trasy. Směrování atributy lze použít na kontroleru a úrovně metody Vybudujte konkrétní trasy.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

`List` Metoda vrátí kód odpovědi 200 OK a všech položek ToDo serializovanou jako JSON.

Můžete otestovat vaši novou metodu rozhraní API pomocí různých nástrojů, jako například [Postman](https://www.getpostman.com/docs/), je vidět tady:

![Postman konzola znázorňující požadavek GET pro todoitems a text odpovědi zobrazují ve formátu JSON pro tři vrácených položek](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Vytvoření položky

Podle konvence vytváření nové datové položky se mapuje na operaci HTTP POST. `Create` Metoda má `[HttpPost]` atribut použit a přijímá `ToDoItem` instance. Protože `item` argument se předá v text příspěvku, tento parametr je upravená pomocí `[FromBody]` atribut.

Uvnitř metody je položka zaškrtnuta pro platnosti a předchozí existence v úložišti dat, a pokud dojde k žádné problémy, se přidá pomocí úložiště. Kontrola `ModelState.IsValid` provádí [ověření modelu](../mvc/models/validation.md)a by mělo být provedeno v každé rozhraní API metody, která přijímá vstup uživatele.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

Ukázka používá výčet s kódy chyb, které jsou předány do mobilního klienta:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

Testování, přidání nové položky pomocí nástroje Postman výběrem operací POST poskytuje nový objekt ve formátu JSON v textu požadavku. Měli byste také přidat zadáním hlavičky požadavku `Content-Type` z `application/json`.

![Postman konzola znázorňující POST a odpovědi](native-mobile-backend/_static/postman-post.png)

Metoda vrátí nově vytvořenou položku v odpovědi.

### <a name="updating-items"></a>Aktualizace položky

Úprava záznamů se provádí pomocí žádosti HTTP PUT. Než tuto změnu `Edit` metoda je téměř shodné s `Create`. Všimněte si, že pokud není nalezen záznam, `Edit` akce vrátí `NotFound` odpovědi (404).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

Testování pomocí nástroje Postman, změňte operaci Put. Zadejte data aktualizovaného objektu do těla požadavku.

![Postman konzola znázorňující PUT a odpovědi](native-mobile-backend/_static/postman-put.png)

Tato metoda vrátí hodnotu `NoContent` (204) reakci v případě úspěchu pro zajištění konzistence s již existující rozhraní API.

### <a name="deleting-items"></a>Odstranění položek

Odstranění záznamů se dosahuje tak provedení žádosti o odstranění služby a při předávání ID položky, která se má odstranit. Jako s aktualizacemi, bude přijímat žádosti pro položky, které neexistují `NotFound` odpovědi. V opačném případě se zobrazí úspěšné žádosti `NoContent` (204) odpovědi.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

Mějte na paměti, že při testování funkce odstraňování, nic se vyžaduje v textu požadavku.

![Postman konzola znázorňující DELETE a odpovědi](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>Běžné konvence webového rozhraní API

Při vývoji back-endových služeb pro vaši aplikaci, můžete přijít s konzistentní sadu vytváření názvů nebo zásady pro nakládání s vyskytující aspekty. Například ve službě výše uvedené požadavky na konkrétních záznamů, které nebyly nalezeny přijatých `NotFound` odpovědi, spíše než `BadRequest` odpovědi. Podobně, příkazy na tuto službu, které předáno vázána k modelu typy vždy zaškrtnuto `ModelState.IsValid` a vrátí `BadRequest` pro typy modelu je neplatný.

Jakmile identifikujete běžné zásady pro vaše rozhraní API, můžete obvykle zapouzdřit ho [filtr](../mvc/controllers/filters.md). Další informace o [jak zapouzdřit běžných zásad rozhraní API aplikace ASP.NET Core MVC](https://msdn.microsoft.com/magazine/mt767699.aspx).

## <a name="additional-resources"></a>Další zdroje

* [Ověřování a autorizace](/xamarin/xamarin-forms/enterprise-application-patterns/authentication-and-authorization)
