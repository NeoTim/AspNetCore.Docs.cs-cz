---
title: Získání knihovny na straně klienta v ASP.NET Core s LibMan
author: scottaddie
description: Naučte se instalovat prostředky knihovny na straně klienta v ASP.NET Core projektu pomocí Správce knihovny (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664666"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Získání knihovny na straně klienta v ASP.NET Core s LibMan

[Scott Addie](https://twitter.com/Scott_Addie)

Správce knihovny (LibMan) je jednoduchý nástroj pro pořízení knihovny na straně klienta. LibMan stáhne oblíbené knihovny a architektury ze systému souborů nebo ze služby [Content Delivery Network (CDN)](https://wikipedia.org/wiki/Content_delivery_network). Mezi podporované sítě CDN patří [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)a [unpkg](https://unpkg.com/#/). Vybrané soubory knihovny jsou načteny a umístěny do příslušného umístění v rámci ASP.NET Core projektu.

## <a name="libman-use-cases"></a>Případy použití LibMan

LibMan nabízí následující výhody:

* Stáhnou se jenom soubory knihovny, které potřebujete.
* Další nástroje, jako je [Node. js](https://nodejs.org), [npm](https://www.npmjs.com)a [Webpack](https://webpack.js.org), není nutné k získání podmnožiny souborů v knihovně.
* Soubory lze umístit do konkrétního umístění bez nutnosti vytvářet úlohy nebo provádět ruční kopírování souborů.

Další informace o výhodách LibMan naleznete [v moderním vývojovém webu v aplikaci Visual Studio 2017: segment LibMan](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).

LibMan není systém správy balíčků. Pokud již používáte Správce balíčků, například npm nebo [příze](https://yarnpkg.com), pokračujte v tomto postupu. LibMan se nevyvinuly, aby tyto nástroje nahradily.

## <a name="additional-resources"></a>Další zdroje

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Úložiště GitHub LibMan](https://github.com/aspnet/LibraryManager)
