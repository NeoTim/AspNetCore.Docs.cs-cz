---
uid: signalr/overview/guide-to-the-api/mapping-users-to-connections
title: Mapování uživatelů knihovny SignalR na připojení | Dokumentace Microsoftu
author: Rick-Anderson
description: Toto téma ukazuje, jak uchovávat informace týkající se uživatelů a jejich připojení. Patrick Fletcher pomohla zápisu v tomto tématu. Verze softwaru použitým v tomto tématu...
ms.author: riande
ms.date: 12/30/2014
ms.assetid: f80c08b1-3f1f-432c-980c-c7b6edeb31b1
msc.legacyurl: /signalr/overview/guide-to-the-api/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: f24d3a4828bc0863f8213e53f054aa7914edffad
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287759"
---
<a name="mapping-signalr-users-to-connections"></a>Mapování uživatelů knihovny SignalR na připojení
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Toto téma ukazuje, jak uchovávat informace týkající se uživatelů a jejich připojení.
>
> Patrick Fletcher pomohla zápisu v tomto tématu.
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

## <a name="introduction"></a>Úvod

Každé připojení klienta k rozbočovači předá id jedinečné připojení. Můžete načíst tuto hodnotu v `Context.ConnectionId` vlastnost kontext rozbočovače. Pokud vaše aplikace potřebuje pro mapování uživatele pro id připojení a uložení mapování, můžete použít jednu z následujících akcí:

- [Uživatelské ID zprostředkovatele (knihovnou SignalR 2)](#IUserIdProvider)
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
| ID uživatele zprostředkovatele | ![](mapping-users-to-connections/_static/image1.png) |  |  | ![](mapping-users-to-connections/_static/image2.png) |
| V paměti |  | ![](mapping-users-to-connections/_static/image3.png) |  | ![](mapping-users-to-connections/_static/image4.png) |
| Skupiny jednoho uživatele | ![](mapping-users-to-connections/_static/image5.png) |  |  | ![](mapping-users-to-connections/_static/image6.png) |
| Trvalé, externí | ![](mapping-users-to-connections/_static/image7.png) | ![](mapping-users-to-connections/_static/image8.png) | ![](mapping-users-to-connections/_static/image9.png) |  |

<a id="IUserIdProvider"></a>

## <a name="iuserid-provider"></a>IUserID poskytovatele

Tato funkce umožňuje uživatelům určit, co je ID uživatele podle IRequest přes nové rozhraní IUserIdProvider.

**IUserIdProvider**

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

Ve výchozím nastavení, bude implementace, která používá uživatele `IPrincipal.Identity.Name` jako uživatelské jméno. Chcete-li toto nastavení změnit, zaregistrovat vaše implementace `IUserIdProvider` s globální hostitelem při spuštění aplikace:

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

Z v rámci rozbočovač, budete mít k odesílání zpráv pro tyto uživatele prostřednictvím rozhraní API pro následující:

**Odesílání zprávy pro konkrétního uživatele**

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs?highlight=5)]

<a id="inmemory"></a>

## <a name="in-memory-storage"></a>Úložiště v paměti

Následující příklady ukazují, jak uchovávat informace o připojení a uživatele ve slovníku, která je uložená v paměti. Používá slovníku `HashSet` pro uložení id připojení. Kdykoli uživatel může mít víc než jedno připojení k aplikaci SignalR. Například uživatel, který je připojený prostřednictvím více zařízení nebo více než jedné karty prohlížeče by mít více než jeden id připojení.

Pokud aplikaci ukončí, dojde ke ztrátě všech informací, ale ho znovu naplní se uživatelé znovu zavést svoje připojení. Úložiště v paměti nebude fungovat, pokud prostředí obsahuje více než jednom webovém serveru, protože každý server bude mít samostatnou sadu připojení.

První příklad ukazuje třídu, která spravuje mapování uživatelů na připojení. Klíč pro HashSet bude uživatelské jméno.

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

Další příklad ukazuje způsob použití třídy mapování připojení od rozbočovače. Instance třídy jsou uložena v názvu proměnné `_connections`.

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a>Skupiny jednoho uživatele

Můžete vytvořit skupiny pro každého uživatele a pak odešle zprávu do této skupiny, pokud chcete oslovit pouze tohoto uživatele. Název každé skupiny je jméno uživatele. Pokud má uživatel více než jedno připojení, každé id připojení se přidá do skupiny uživatelů.

Neměli ručně uživatele odeberete ze skupiny po odpojení. Tato akce se provádí automaticky rozhraním SignalR.

Následující příklad ukazuje, jak implementovat skupiny jednoho uživatele.

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a>Trvalé, externí úložiště

Toto téma ukazuje, jak používat databáze nebo úložiště tabulek v Azure k ukládání informací o připojení. Tento přístup funguje, když máte více webových serverů, protože každý webový server může komunikovat s stejného úložiště dat. Pokud vaše webové servery zastavit pracovní nebo restartování aplikace `OnDisconnected` metoda není volána. Proto je možné, že úložiště dat bude obsahovat záznamy pro ID připojení, které už nejsou platné. Vyčistit osamocené záznamy, které staví na zneplatnit jakékoli připojení, který byl vytvořen mimo časový rámec, který je relevantní pro vaši aplikaci. Příklady v této části zahrnout hodnotu pro sledování při vytváření připojení, ale nezobrazovat návod k vyčištění starých záznamů, protože můžete chtít udělat jako proces na pozadí.

### <a name="database"></a>Databáze

Následující příklady ukazují, jak uchovávat informace o připojení a uživatele v databázi. Můžete použít libovolný technologií přístupu dat; Následující příklad ukazuje, jak definovat modely s využitím rozhraní Entity Framework. Tyto modely entity odpovídají databázové tabulky a pole. Datová struktura se může značně lišit v závislosti na požadavcích vaší aplikace.

První příklad ukazuje, jak lze definovat entitu uživatele, která můžou být spojené s mnoha entit připojení.

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]

Z centra, pak můžete sledovat stav každé připojení kódem zobrazeným pod.

[!code-csharp[Main](mapping-users-to-connections/samples/sample8.cs)]

<a id="azure"></a>
### <a name="azure-table-storage"></a>Úložiště tabulek v Azure

Následující příklad úložiště tabulek v Azure je podobně jako v příkladu databáze. Nezahrnuje všechny informace, které je třeba začít pomocí služby Azure Table Storage. Informace najdete v tématu [postupy používání úložiště Table z .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

Následující příklad ukazuje entitu tabulky pro ukládání informací o připojení. Rozdělují data podle uživatelského jména a identifikuje každé entity podle id připojení, takže uživatel může mít víc připojení v každém okamžiku.

[!code-csharp[Main](mapping-users-to-connections/samples/sample9.cs)]

V centru sledovat stav připojení jednotlivých uživatelů.

[!code-csharp[Main](mapping-users-to-connections/samples/sample10.cs)]
