---
uid: signalr/overview/guide-to-the-api/working-with-groups
title: Práce se skupinami v knihovně SignalR | Dokumentace Microsoftu
author: pfletcher
description: Toto téma popisuje, jak zachovat informace o členství ve skupině pomocí rozhraní API pro rozbočovač.
ms.author: riande
ms.date: 06/10/2014
ms.assetid: cd378ecd-3e9e-4236-b902-65916d85a048
msc.legacyurl: /signalr/overview/guide-to-the-api/working-with-groups
msc.type: authoredcontent
ms.openlocfilehash: bf4b8597fe9c0bff06b95cfb9970211106191fd4
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287634"
---
<a name="working-with-groups-in-signalr"></a>Práce se skupinami v knihovně SignalR
====================
podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Toto téma popisuje, jak přidat uživatele do skupin a zachovat informace o členství ve skupině.
>
> ## <a name="software-versions-used-in-this-topic"></a>Verze softwaru použitým v tomto tématu
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Funkce SignalR verze 2
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Předchozích verzích tohoto tématu
>
> Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).

## <a name="overview"></a>Přehled

Skupinami v knihovně SignalR poskytuje metodu pro vysílání zpráv do zadaného podmnožiny připojených klientů. Skupina může mít libovolný počet klientů a klienta můžete mít libovolný počet skupin. Není nutné explicitně vytvářet skupiny. V důsledku toho skupina se automaticky vytvoří při prvním volání Groups.Add zadáte její název a se odstraní, když odeberete poslední připojení z členství v ní. Úvod do používání skupin najdete v tématu [Správa členství ve skupině ze třídy rozbočovače](hubs-api-guide-server.md#groupsfromhub) v rozhraní API rozbočovače – Příručka pro Server.

Neexistuje žádné rozhraní API pro získání seznamu členství ve skupinách nebo seznam skupin. Funkce SignalR odesílá zprávy do klientů a skupin založené na modelu pub/sub a serveru neumožňuje spravovat seznam skupin nebo členství ve skupinách. To pomáhá maximalizovat škálovatelnost, protože pokaždé, když přidáte uzel do webové farmy, jakýkoli stav, který udržuje SignalR musí být rozšířena na nový uzel.

Při přidávání uživatele do skupiny použití `Groups.Add` metoda, uživatel dostane zprávy přesměruje do této skupiny pro dobu trvání aktuálního připojení, ale nad rámec aktuální připojení není trvalý členství uživatele ve skupině. Pokud chcete trvale uchovávat informace o skupinách a členství ve skupině, musí tato data ukládat v úložišti, jako jsou databáze nebo Azure table storage. Potom pokaždé, když uživatel připojí k vaší aplikaci načíst z úložiště skupiny, které uživatel patří a ručně přidejte tohoto uživatele do těchto skupin.

Když znovu připojovat po dočasné přerušení, uživatel automaticky znovu připojí dříve přiřazené skupiny. Automatické opětovné připojování ke rozbočovačů skupinu platí pouze při opětovné připojení, ne už při vytvoření nového připojení. Digitálně podepsaný token je předán z klienta, který obsahuje seznam dříve přiřazené skupiny. Pokud chcete ověřit, zda uživatel patří do požadované skupiny, výchozí chování můžete přepsat.

Toto téma obsahuje následující oddíly:

- [Přidávání a odebírání uživatelů](#add)
- [Volání členů skupiny](#call)
- [Ukládání členství ve skupinách v databázi](#storedatabase)
- [Ukládání členství ve skupině ve službě Azure table storage](#storeazuretable)
- [Ověřování členství ve skupině při obnovování spojení](#verify)

<a id="add"></a>

## <a name="adding-and-removing-users"></a>Přidávání a odebírání uživatelů

Chcete-li přidat nebo odebrat uživatele ze skupiny, zavolejte [přidat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) nebo [odebrat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) metody a předejte jí id uživatele, připojení a názvu skupiny jako parametry. Není nutné ručně odebrat uživatele ze skupiny po ukončení připojení.

Následující příklad ukazuje `Groups.Add` a `Groups.Remove` metod používaných v metodách rozbočovače.

[!code-csharp[Main](working-with-groups/samples/sample1.cs?highlight=5,10)]

`Groups.Add` a `Groups.Remove` metody spustit asynchronně.

Pokud chcete přidat klienta do skupiny a okamžitě odešle zprávu do klienta pomocí skupiny, budete muset Ujistěte se, že metoda Groups.Add skončí jako první. Následující příklady kódu ukazují, jak to udělat.

[!code-csharp[Main](working-with-groups/samples/sample2.cs?highlight=1,3)]

Obecně by neměla zahrnovat `await` při volání `Groups.Remove` metoda vzhledem k tomu, že id připojení, který se pokoušíte odebrat může nadále již nebudou dostupné. V takovém případě `TaskCanceledException` je vyvolána výjimka, jakmile vyprší časový limit žádosti. Pokud vaše aplikace musíte zajistit, že uživatel odebral ze skupiny před odesláním zprávy do skupiny, můžete přidat `await` před `Groups.Remove`a potom zachytit `TaskCanceledException` výjimku, která by mohla být vyvolána.

<a id="call"></a>

## <a name="calling-members-of-a-group"></a>Volání členů skupiny

Odesílat zprávy do všech členů skupiny nebo pouze členové zadané skupiny, jak je znázorněno v následujícím příkladu.

- **Všechny** připojených klientů v zadané skupině.

    [!code-css[Main](working-with-groups/samples/sample3.css)]
- Všechny připojené klienty v zadané skupině **s výjimkou určitých klientech**, identifikovaný podle ID připojení.

    [!code-csharp[Main](working-with-groups/samples/sample4.cs)]
- Všechny připojené klienty v zadané skupině **s výjimkou volajícího klienta**.

    [!code-css[Main](working-with-groups/samples/sample5.css)]

<a id="storedatabase"></a>

## <a name="storing-group-membership-in-a-database"></a>Ukládání členství ve skupinách v databázi

Následující příklady ukazují, jak uchovávat informace pro skupiny a uživatele v databázi. Můžete použít libovolný technologií přístupu dat; Následující příklad ukazuje, jak definovat modely s využitím rozhraní Entity Framework. Tyto modely entity odpovídají databázové tabulky a pole. Datová struktura se může značně lišit v závislosti na požadavcích vaší aplikace. Tento příklad obsahuje třídu s názvem `ConversationRoom` který bude jedinečný pro aplikaci, která umožňuje uživatelům připojit se k konverzací o různých tématům, jako jsou sportovní nebo zahrady. Tento příklad také zahrnuje třídu pro připojení. Třída připojení není nezbytně nutná pro sledování členství ve skupině, ale je často součástí robustní řešení pro sledování uživatelů.

[!code-csharp[Main](working-with-groups/samples/sample6.cs)]

Potom můžete v rozbočovači, načtěte informace o skupině a uživatele z databáze a ručně přidat uživatele do příslušných skupin. V příkladu neobsahuje kód pro sledování připojení uživatelů. V tomto příkladu `await` – klíčové slovo není použita před `Groups.Add` vzhledem k tomu, že zpráva se odešle okamžitě členy skupiny. Pokud chcete odeslat zprávu na všechny členy skupiny bezprostředně po přidání nového člena, chcete použít `await` – klíčové slovo, aby se zajistilo dokončení asynchronní operace.

[!code-csharp[Main](working-with-groups/samples/sample7.cs)]

<a id="storeazuretable"></a>

## <a name="storing-group-membership-in-azure-table-storage"></a>Ukládání členství ve skupině ve službě Azure table storage

Použití Azure table storage k ukládání informací o skupině a uživatel je podobný používání databáze. Následující příklad ukazuje entitu tabulky, která uloží uživatelské jméno a název skupiny.

[!code-csharp[Main](working-with-groups/samples/sample8.cs)]

V centru načtete přiřazených skupin, když se uživatel připojí.

[!code-csharp[Main](working-with-groups/samples/sample9.cs)]

<a id="verify"></a>

## <a name="verifying-group-membership-when-reconnecting"></a>Ověřování členství ve skupině při obnovování spojení

Ve výchozím nastavení SignalR automaticky znovu přiřadí uživatele do příslušných skupin při obnovování spojení z dočasné přerušení, jako je například pokud je připojení vyřadit a znovu vytvořit předtím, než vyprší časový limit připojení. Informace o skupině pro daného uživatele je předán do tokenu při opětovné připojení, a tento token ověření na serveru. Informace o procesu ověření u některých uživatelů do skupin najdete v tématu [některých skupin při obnovování spojení](../security/introduction-to-security.md#rejoingroup).

Obecně platí abyste používali výchozí chování automaticky opětovné připojování ke rozbočovačů, že skupiny na znovu připojit. Funkce SignalR skupiny nejsou určeny jako vhodný mechanismus zabezpečení pro omezení přístupu k citlivým datům. Pokud aplikace musí zkontrolovat členství ve skupině uživatele při obnovování spojení, však můžete přepsat výchozí chování. Změna výchozího chování lze přidat zatížení do databáze protože členství ve skupině uživatele musí být načten pro každý opětovné připojení, a nikoli jen když se uživatel připojí.

Pokud je nutné ověřit členství ve skupině na znovu připojit, vytvořte nový modul kanálu rozbočovače, který vrátí seznam přiřazených skupin, jak je znázorněno níže.

[!code-csharp[Main](working-with-groups/samples/sample10.cs)]

Poté přidejte modul do kanálu rozbočovače, jak je zdůrazněno níže.

[!code-csharp[Main](working-with-groups/samples/sample11.cs?highlight=4)]
