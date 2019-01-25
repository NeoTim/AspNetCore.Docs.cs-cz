---
uid: signalr/overview/older-versions/mapping-users-to-connections
title: Mapování uživatelů knihovny SignalR na připojení v SignalR 1.x | Dokumentace Microsoftu
author: bradygaster
description: Toto téma ukazuje, jak uchovávat informace týkající se uživatelů a jejich připojení.
ms.author: bradyg
ms.date: 10/17/2013
ms.assetid: ebbc93a8-e6c4-4122-8e0d-3aa42293c747
msc.legacyurl: /signalr/overview/older-versions/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: 1e65a11e08a5b060cf8b096b5fe5b90eb8dc5b51
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836711"
---
<a name="mapping-signalr-users-to-connections-in-signalr-1x"></a>Mapování uživatelů knihovny SignalR na připojení v SignalR 1.x
====================
podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Toto téma ukazuje, jak uchovávat informace týkající se uživatelů a jejich připojení.


## <a name="introduction"></a>Úvod

Každé připojení klienta k rozbočovači předá id jedinečné připojení. Můžete načíst tuto hodnotu v `Context.ConnectionId` vlastnost kontext rozbočovače. Pokud vaše aplikace potřebuje pro mapování uživatele pro id připojení a uložení mapování, můžete použít jednu z následujících akcí:

- [Úložiště v paměti](#inmemory), jako je například slovník
- [Skupiny SignalR pro každého uživatele](#groups)
- [Trvalé, externí úložiště](#database), jako jsou databázové tabulky nebo Azure table storage

Každá z těchto implementacích se zobrazí v tomto tématu. Můžete použít `OnConnected`, `OnDisconnected`, a `OnReconnected` metody `Hub` třídy ke sledování stavu připojení uživatele.

Nejlepším řešením pro vaše aplikace závisí na:

- Počet webových serverů, který je hostitelem vaší aplikace.
- Určuje, zda je nutné získat seznam aktuálně připojených uživatelů.
- Určuje, zda je potřeba uchovávat informace, skupiny a uživatele po restartování aplikace nebo serveru.
- Latence volání externí server určuje, zda je problém.

Následující tabulka uvádí, jaký přístup funguje pro tyto aspekty.

|  | Více než jeden server | Získat seznam aktuálně připojených uživatelů | Uchovávání informací po restartování | Zajištění optimálního výkonu |
| --- | --- | --- | --- | --- |
| V paměti |  | ![](mapping-users-to-connections/_static/image1.png) |  | ![](mapping-users-to-connections/_static/image2.png) |
| Skupiny jednoho uživatele | ![](mapping-users-to-connections/_static/image3.png) |  |  | ![](mapping-users-to-connections/_static/image4.png) |
| Trvalé, externí | ![](mapping-users-to-connections/_static/image5.png) | ![](mapping-users-to-connections/_static/image6.png) | ![](mapping-users-to-connections/_static/image7.png) |  |

<a id="inmemory"></a>

## <a name="in-memory-storage"></a>Úložiště v paměti

Následující příklady ukazují, jak uchovávat informace o připojení a uživatele ve slovníku, která je uložená v paměti. Používá slovníku `HashSet` pro uložení id připojení. Kdykoli uživatel může mít víc než jedno připojení k aplikaci SignalR. Například uživatel, který je připojený prostřednictvím více zařízení nebo více než jedné karty prohlížeče by mít více než jeden id připojení.

Pokud aplikaci ukončí, dojde ke ztrátě všech informací, ale ho znovu naplní se uživatelé znovu zavést svoje připojení. Úložiště v paměti nebude fungovat, pokud prostředí obsahuje více než jednom webovém serveru, protože každý server bude mít samostatnou sadu připojení.

První příklad ukazuje třídu, která spravuje mapování uživatelů na připojení. Klíč pro HashSet bude uživatelské jméno.

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

Další příklad ukazuje způsob použití třídy mapování připojení od rozbočovače. Instance třídy jsou uložena v názvu proměnné `_connections`.

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a>Skupiny jednoho uživatele

Můžete vytvořit skupiny pro každého uživatele a pak odešle zprávu do této skupiny, pokud chcete oslovit pouze tohoto uživatele. Název každé skupiny je jméno uživatele. Pokud má uživatel více než jedno připojení, každé id připojení se přidá do skupiny uživatelů.

Neměli ručně uživatele odeberete ze skupiny po odpojení. Tato akce se provádí automaticky rozhraním SignalR.

Následující příklad ukazuje, jak implementovat skupiny jednoho uživatele.

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a>Trvalé, externí úložiště

Toto téma ukazuje, jak používat databáze nebo úložiště tabulek v Azure k ukládání informací o připojení. Tento přístup funguje, když máte více webových serverů, protože každý webový server může komunikovat s stejného úložiště dat. Pokud vaše webové servery zastavit pracovní nebo restartování aplikace `OnDisconnected` metoda není volána. Proto je možné, že úložiště dat bude obsahovat záznamy pro ID připojení, které už nejsou platné. Vyčistit osamocené záznamy, které staví na zneplatnit jakékoli připojení, který byl vytvořen mimo časový rámec, který je relevantní pro vaši aplikaci. Příklady v této části zahrnout hodnotu pro sledování při vytváření připojení, ale nezobrazovat návod k vyčištění starých záznamů, protože můžete chtít udělat jako proces na pozadí.

### <a name="database"></a>Databáze

Následující příklady ukazují, jak uchovávat informace o připojení a uživatele v databázi. Můžete použít libovolný technologií přístupu dat; Následující příklad ukazuje, jak definovat modely s využitím rozhraní Entity Framework. Tyto modely entity odpovídají databázové tabulky a pole. Datová struktura se může značně lišit v závislosti na požadavcích vaší aplikace.

První příklad ukazuje, jak lze definovat entitu uživatele, která můžou být spojené s mnoha entit připojení.

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

Z centra, pak můžete sledovat stav každé připojení kódem zobrazeným pod.

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

### <a name="azure-table-storage"></a>Úložiště tabulek v Azure

Následující příklad úložiště tabulek v Azure je podobně jako v příkladu databáze. Nezahrnuje všechny informace, které je třeba začít pomocí služby Azure Table Storage. Informace najdete v tématu [postupy používání úložiště Table z .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

Následující příklad ukazuje entitu tabulky pro ukládání informací o připojení. Rozdělují data podle uživatelského jména a identifikuje každé entity podle id připojení, takže uživatel může mít víc připojení v každém okamžiku.

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

V centru sledovat stav připojení jednotlivých uživatelů.

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]
