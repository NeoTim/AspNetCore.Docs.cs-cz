---
title: Vytváření back-end služeb pro nativní mobilní aplikace s ASP.NET Core
author: ardalis
description: Naučte se vytvářet služby back-endu pomocí ASP.NET Core MVC k podpoře nativních mobilních aplikací.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mobile/native-mobile-backend
ms.openlocfilehash: 00e0c93d200e7eec6fd6bdac1f1246fe0909fc54
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630533"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Vytváření back-end služeb pro nativní mobilní aplikace s ASP.NET Core

[Steve Smith](https://ardalis.com/)

Mobilní aplikace můžou komunikovat s ASP.NET Core službami back-endu. Pokyny k připojení místních webových služeb od simulátorů iOS a emulátorů Androidu najdete v tématu [připojení k místním webovým službám z simulátorů iOS a emulátorů Androidu](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Zobrazit nebo stáhnout ukázkový kód back-end služeb](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>Ukázková nativní mobilní aplikace

V tomto kurzu se dozvíte, jak vytvořit služby back-endu pomocí ASP.NET Core MVC k podpoře nativních mobilních aplikací. Používá [aplikaci Xamarin Forms ToDoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) jako nativního klienta, který zahrnuje samostatné nativní klienty pro zařízení s Androidem, iOS, Windows Universal a Windows Phone. Můžete postupovat podle propojeného kurzu a vytvořit nativní aplikaci (a nainstalovat potřebné bezplatné nástroje Xamarin) a stáhnout ukázkové řešení Xamarin. Ukázka Xamarin zahrnuje projekt služeb ASP.NET Web API 2, který nahrazuje aplikace ASP.NET Core webového rozhraní API 2 (bez nutnosti změny klienta).

![Spuštění aplikace REST na telefonu s Androidem](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Funkce

Aplikace ToDoRest podporuje výpis, přidávání, odstraňování a aktualizaci položek úkolů. Každá položka má ID, název, poznámky a vlastnost, která označuje, zda byla provedena ještě předtím.

Hlavní zobrazení položek, jak je uvedeno výše, uvádí název každé položky a označuje, zda se značka zaškrtne.

Klepnutím na `+` ikonu se otevře dialogové okno Přidat položku:

![Dialogové okno Přidat položku](native-mobile-backend/_static/todo-android-new-item.png)

Klepnutím na položku na obrazovce hlavní seznam se otevře dialogové okno pro úpravy, ve kterém můžete upravit název položky, poznámky a hotové nastavení, nebo můžete položku odstranit:

![Dialogové okno Upravit položku](native-mobile-backend/_static/todo-android-edit-item.png)

Tato ukázka je ve výchozím nastavení nakonfigurovaná tak, aby používala back-end služby hostované na developer.xamarin.com, což povoluje operace jen pro čtení. Pokud si ji chcete vyzkoušet sami s aplikací ASP.NET Core vytvořenou v další části běžící na vašem počítači, budete muset aktualizovat `RestUrl` konstantu aplikace. Přejděte do `ToDoREST` projektu a otevřete soubor *Constants.cs* . Nahraďte `RestUrl` adresu URL, která obsahuje IP adresu vašeho počítače (ne localhost nebo 127.0.0.1), protože tato adresa se používá z emulátoru zařízení, nikoli z počítače). Zahrňte také číslo portu (5000). Aby bylo možné testovat, zda vaše služby pracují se zařízením, ujistěte se, že nemáte aktivní bránu firewall blokující přístup k tomuto portu.

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>Vytvoření projektu ASP.NET Core

Vytvořte novou ASP.NET Core webovou aplikaci v aplikaci Visual Studio. Vyberte šablonu webové rozhraní API a bez ověřování. Pojmenujte projekt *ToDoApi*.

![Dialogové okno Nová webová aplikace v ASP.NET se zvolenou šablonou projektu webového rozhraní API](native-mobile-backend/_static/web-api-template.png)

Aplikace by měla reagovat na všechny požadavky vytvořené na port 5000. Aktualizujte *program.cs* , aby se `.UseUrls("http://*:5000")` dosáhlo tohoto:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> Ujistěte se, že aplikaci spouštíte přímo, a ne za IIS Express, která ve výchozím nastavení ignoruje jiné požadavky než místní. Spusťte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) z příkazového řádku nebo vyberte profil název aplikace z rozevíracího seznamu cíl ladění na panelu nástrojů sady Visual Studio.

Přidejte třídu modelu, která bude představovat položky úkolů. Označte povinná pole s `[Required]` atributem:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

Metody rozhraní API vyžadují nějaký způsob, jak pracovat s daty. Použijte stejné `IToDoRepository` rozhraní, jaké používá původní ukázka Xamarin:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

Pro tuto ukázku implementace používá pouze soukromou kolekci položek:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

Konfigurace implementace v *Startup.cs*:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

V tuto chvíli jste připraveni vytvořit *ToDoItemsController*.

> [!TIP]
> Přečtěte si další informace o vytváření webových rozhraní API v [sestavení prvního webového rozhraní API pomocí ASP.NET Core MVC a sady Visual Studio](../tutorials/first-web-api.md).

## <a name="creating-the-controller"></a>Vytváření kontroleru

Přidejte do projektu nový kontroler, *ToDoItemsController*. Měl by dědit z Microsoft. AspNetCore. Mvc. Controller. Přidejte `Route` atribut, který označuje, že kontroler zpracuje požadavky provedené na cesty začínající na `api/todoitems` . `[controller]`Token v trase je nahrazen názvem kontroleru (vynechání `Controller` přípony) a je zvláště užitečný pro globální trasy. Přečtěte si další informace o [Směrování](../fundamentals/routing.md).

Kontroler vyžaduje `IToDoRepository` funkci. požádejte o instanci tohoto typu prostřednictvím konstruktoru kontroleru. Za běhu se tato instance poskytne s použitím podpory rozhraní pro [vkládání závislostí](../fundamentals/dependency-injection.md).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

Toto rozhraní API podporuje čtyři různé příkazy HTTP pro provádění operací CRUD (vytvoření, čtení, aktualizace, odstranění) na zdroji dat. Nejjednodušší z nich je operace čtení, která odpovídá požadavku HTTP GET.

### <a name="reading-items"></a>Čtení položek

Požadavek na seznam položek se provádí s požadavkem GET na `List` metodu. `[HttpGet]`Atribut `List` metody označuje, že by tato akce měla zpracovat pouze požadavky GET. Trasa pro tuto akci je trasa určená na řadiči. Nemusíte nutně použít název akce jako součást trasy. Stačí, abyste zajistili, že každá akce má jedinečnou a jednoznačnou trasu. Atributy směrování lze použít jak na úrovni kontroléru, tak na úrovni metody pro sestavování specifických tras.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

`List`Metoda vrátí kód odpovědi 200 OK a všechny položky ToDo serializované jako JSON.

Novou metodu rozhraní API můžete otestovat pomocí nejrůznějších nástrojů, jako je například [post](https://www.getpostman.com/docs/), jak je znázorněno zde:

![Dodatečná konzola ukazující požadavek GET pro TodoItems a tělo odpovědi zobrazující JSON pro tři vrácené položky](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Vytváření položek

Podle konvence je vytváření nových datových položek mapováno na operaci HTTP POST. V `Create` metodě je `[HttpPost]` použit atribut a je přijímána `ToDoItem` instance. Vzhledem k `item` tomu, že argument je předán v těle příspěvku, tento parametr určuje `[FromBody]` atribut.

V rámci metody je položka kontrolována platnosti a předchozí existence v úložišti dat, a pokud nedojde k žádnému problému, bude přidána pomocí úložiště. Kontrola `ModelState.IsValid` provede [ověření modelu](../mvc/models/validation.md)a měla by se provést v každé metodě rozhraní API, která přijímá vstup uživatele.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

Ukázka používá výčet obsahující chybové kódy, které jsou předány mobilnímu klientovi:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

Otestujte přidáním nových položek pomocí příkazu post tak, že vyberete příkaz POST, který poskytuje nový objekt ve formátu JSON v těle požadavku. Měli byste také přidat hlavičku požadavku určující a `Content-Type` `application/json` .

![Dodatečná konzola ukazující příspěvek a odpověď](native-mobile-backend/_static/postman-post.png)

Metoda vrátí nově vytvořenou položku v odpovědi.

### <a name="updating-items"></a>Aktualizace položek

Změny záznamů se provádí pomocí požadavků HTTP PUT. Kromě této změny `Edit` je metoda skoro shodná s `Create` . Všimněte si, že pokud se záznam nenajde, `Edit` akce vrátí `NotFound` odpověď (404).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

Chcete-li testovat pomocí metody post, změňte operaci na PUT. Zadejte data aktualizovaných objektů v těle žádosti.

![Dodatečná konzola ukazující vložení a odpověď](native-mobile-backend/_static/postman-put.png)

Tato metoda vrátí `NoContent` odpověď (204), pokud je úspěšná, kvůli konzistenci s dříve existujícím rozhraním API.

### <a name="deleting-items"></a>Odstraňování položek

Odstranění záznamů je provedeno tím, že se do služby vyžádají žádosti o odstranění a předáte ID položky, která se má odstranit. Stejně jako u aktualizací jsou požadavky na položky, které neexistují, přijímat `NotFound` odpovědi. V opačném případě bude úspěšná žádost dostávat `NoContent` odpověď (204).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

Všimněte si, že při testování funkce odstranění není v těle žádosti nic nutné.

![Konzola pro publikování ukazující odstranění a odpověď](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>Společné konvence webového rozhraní API

Při vývoji back-end služeb pro vaši aplikaci budete chtít vytvořit s konzistentním nastavením konvencí nebo zásad pro zpracování otázek mezi jednotlivými průřezy. Například ve výše uvedené službě požadavky na konkrétní záznamy, které nebyly nalezeny, neobdržely `NotFound` odpověď, nikoli `BadRequest` odpověď. Podobně příkazy provedené pro tuto službu, které byly předány v typu vázané na model, jsou vždy zkontrolovány `ModelState.IsValid` a vráceny `BadRequest` pro neplatné typy modelů.

Jakmile zjistíte společné zásady pro vaše rozhraní API, můžete je obvykle zapouzdřit ve [filtru](../mvc/controllers/filters.md). Přečtěte si další informace o [tom, jak zapouzdřit společné zásady rozhraní API v aplikacích ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Další zdroje informací

* [Ověřování a autorizace](/xamarin/xamarin-forms/enterprise-application-patterns/authentication-and-authorization)
