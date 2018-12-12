---
uid: signalr/overview/older-versions/working-with-groups
title: Práce se skupinami v knihovně SignalR 1.x | Dokumentace Microsoftu
author: pfletcher
description: Toto téma popisuje, jak zachovat informace o členství ve skupině pomocí rozhraní API pro rozbočovač.
ms.author: riande
ms.date: 10/21/2013
ms.assetid: 22929efd-68c9-4609-b76d-f8ba42fda01e
msc.legacyurl: /signalr/overview/older-versions/working-with-groups
msc.type: authoredcontent
ms.openlocfilehash: 5096e7a5eb8704762a21eeb88a128d92e75ede60
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287485"
---
<a name="working-with-groups-in-signalr-1x"></a>Práce se skupinami v knihovně SignalR 1.x
====================
podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Toto téma popisuje, jak přidat uživatele do skupin a zachovat informace o členství ve skupině.


## <a name="overview"></a>Přehled

Skupinami v knihovně SignalR poskytuje metodu pro vysílání zpráv do zadaného podmnožiny připojených klientů. Skupina může mít libovolný počet klientů a klienta můžete mít libovolný počet skupin. Není nutné explicitně vytvářet skupiny. V důsledku toho skupina se automaticky vytvoří při prvním volání Groups.Add zadáte její název a se odstraní, když odeberete poslední připojení z členství v ní. Úvod do používání skupin najdete v tématu [Správa členství ve skupině ze třídy rozbočovače](index.md) v rozhraní API rozbočovače – Příručka pro Server.

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

Pokud chcete přidat klienta do skupiny a okamžitě odešle zprávu do klienta pomocí skupiny, budete muset Ujistěte se, že metoda Groups.Add skončí jako první. Následující příklady kódu ukazují, jak to udělat, ji pomocí kódu, který funguje v rozhraní .NET 4.5 a pomocí kódu, který funguje v rozhraní .NET 4.

#### <a name="asynchronous-net-45-example"></a>Asynchronní rozhraní .NET 4.5 Příklad

[!code-csharp[Main](working-with-groups/samples/sample2.cs?highlight=1,3)]

#### <a name="asynchronous-net-4-example"></a>Asynchronní rozhraní .NET 4 příklad

[!code-csharp[Main](working-with-groups/samples/sample3.cs?highlight=3-4)]

Obecně by neměla zahrnovat `await` při volání `Groups.Remove` metoda vzhledem k tomu, že id připojení, který se pokoušíte odebrat může nadále již nebudou dostupné. V takovém případě `TaskCanceledException` je vyvolána výjimka, jakmile vyprší časový limit žádosti. Pokud vaše aplikace musíte zajistit, že uživatel odebral ze skupiny před odesláním zprávy do skupiny, můžete přidat `await` před Groups.Remove a pak catch `TaskCanceledException` výjimku, která by mohla být vyvolána.

<a id="call"></a>

## <a name="calling-members-of-a-group"></a>Volání členů skupiny

Odesílat zprávy do všech členů skupiny nebo pouze členové zadané skupiny, jak je znázorněno v následujícím příkladu.

- **Všechny** připojených klientů v zadané skupině. 

    [!code-css[Main](working-with-groups/samples/sample4.css)]
- Všechny připojené klienty v zadané skupině **s výjimkou určitých klientech**, identifikovaný podle ID připojení. 

    [!code-csharp[Main](working-with-groups/samples/sample5.cs)]
- Všechny připojené klienty v zadané skupině **s výjimkou volajícího klienta**. 

    [!code-css[Main](working-with-groups/samples/sample6.css)]

<a id="storedatabase"></a>

## <a name="storing-group-membership-in-a-database"></a>Ukládání členství ve skupinách v databázi

Následující příklady ukazují, jak uchovávat informace pro skupiny a uživatele v databázi. Můžete použít libovolný technologií přístupu dat; Následující příklad ukazuje, jak definovat modely s využitím rozhraní Entity Framework. Tyto modely entity odpovídají databázové tabulky a pole. Datová struktura se může značně lišit v závislosti na požadavcích vaší aplikace. Tento příklad obsahuje třídu s názvem `ConversationRoom` který bude jedinečný pro aplikaci, která umožňuje uživatelům připojit se k konverzací o různých tématům, jako jsou sportovní nebo zahrady. Tento příklad také zahrnuje třídu pro připojení. Třída připojení není nezbytně nutná pro sledování členství ve skupině, ale je často součástí robustní řešení pro sledování uživatelů.

[!code-csharp[Main](working-with-groups/samples/sample7.cs)]

Potom můžete v rozbočovači, načtěte informace o skupině a uživatele z databáze a ručně přidat uživatele do příslušných skupin. V příkladu neobsahuje kód pro sledování připojení uživatelů. V tomto příkladu `await` – klíčové slovo není použita před `Groups.Add` vzhledem k tomu, že zpráva se odešle okamžitě členy skupiny. Pokud chcete odeslat zprávu na všechny členy skupiny bezprostředně po přidání nového člena, chcete použít `await` – klíčové slovo, aby se zajistilo dokončení asynchronní operace.

[!code-csharp[Main](working-with-groups/samples/sample8.cs)]

<a id="storeazuretable"></a>

## <a name="storing-group-membership-in-azure-table-storage"></a>Ukládání členství ve skupině ve službě Azure table storage

Použití Azure table storage k ukládání informací o skupině a uživatel je podobný používání databáze. Následující příklad ukazuje entitu tabulky, která uloží uživatelské jméno a název skupiny.

[!code-csharp[Main](working-with-groups/samples/sample9.cs)]

V centru načtete přiřazených skupin, když se uživatel připojí.

[!code-csharp[Main](working-with-groups/samples/sample10.cs)]

<a id="verify"></a>

## <a name="verifying-group-membership-when-reconnecting"></a>Ověřování členství ve skupině při obnovování spojení

Ve výchozím nastavení SignalR automaticky znovu přiřadí uživatele do příslušných skupin při obnovování spojení z dočasné přerušení, jako je například pokud je připojení vyřadit a znovu vytvořit předtím, než vyprší časový limit připojení. Informace o skupině pro daného uživatele je předán do tokenu při opětovné připojení, a tento token ověření na serveru. Informace o procesu ověření u některých uživatelů do skupin najdete v tématu [některých skupin při obnovování spojení](index.md).

Obecně platí abyste používali výchozí chování automaticky opětovné připojování ke rozbočovačů, že skupiny na znovu připojit. Funkce SignalR skupiny nejsou určeny jako vhodný mechanismus zabezpečení pro omezení přístupu k citlivým datům. Pokud aplikace musí zkontrolovat členství ve skupině uživatele při obnovování spojení, však můžete přepsat výchozí chování. Změna výchozího chování lze přidat zatížení do databáze protože členství ve skupině uživatele musí být načten pro každý opětovné připojení, a nikoli jen když se uživatel připojí.

Pokud je nutné ověřit členství ve skupině na znovu připojit, vytvořte nový modul kanálu rozbočovače, který vrátí seznam přiřazených skupin, jak je znázorněno níže.

[!code-csharp[Main](working-with-groups/samples/sample11.cs)]

Poté přidejte modul do kanálu rozbočovače, jak je zdůrazněno níže.

[!code-csharp[Main](working-with-groups/samples/sample12.cs?highlight=10)]
