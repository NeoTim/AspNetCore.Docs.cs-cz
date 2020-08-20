---
title: Získání knihovny na straně klienta v ASP.NET Core s LibMan
author: scottaddie
description: Naučte se instalovat prostředky knihovny na straně klienta v ASP.NET Core projektu pomocí Správce knihovny (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
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
uid: client-side/libman/index
ms.openlocfilehash: 8458f2fe1cdc139a3f22c7d73675301d89ad368e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625632"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Získání knihovny na straně klienta v ASP.NET Core s LibMan

[Scott Addie](https://twitter.com/Scott_Addie)

Správce knihovny (LibMan) je jednoduchý nástroj pro pořízení knihovny na straně klienta. LibMan stáhne oblíbené knihovny a architektury ze systému souborů nebo ze služby [Content Delivery Network (CDN)](https://wikipedia.org/wiki/Content_delivery_network). Mezi podporované sítě CDN patří [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)a [unpkg](https://unpkg.com/#/). Vybrané soubory knihovny jsou načteny a umístěny do příslušného umístění v rámci ASP.NET Core projektu.

## <a name="libman-use-cases"></a>Případy použití LibMan

LibMan nabízí následující výhody:

* Stáhnou se jenom soubory knihovny, které potřebujete.
* Další nástroje, například [Node.js](https://nodejs.org), [npm](https://www.npmjs.com)a [Webpack](https://webpack.js.org), není nutné k získání podmnožiny souborů v knihovně.
* Soubory lze umístit do konkrétního umístění bez nutnosti vytvářet úlohy nebo provádět ruční kopírování souborů.

Další informace o výhodách LibMan naleznete [v moderním vývojovém webu v aplikaci Visual Studio 2017: segment LibMan](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).

LibMan není systém správy balíčků. Pokud již používáte Správce balíčků, například npm nebo [příze](https://yarnpkg.com), pokračujte v tomto postupu. LibMan se nevyvinuly, aby tyto nástroje nahradily.

## <a name="additional-resources"></a>Další zdroje informací

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Úložiště GitHub LibMan](https://github.com/aspnet/LibraryManager)
