---
title: Akvizice knihovny na straně klienta v ASP.NET Core s LibManem
author: scottaddie
description: Zjistěte, jak nainstalovat datové zdroje knihovny na straně klienta v projektu ASP.NET Core pomocí Správce knihovny (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664666"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Akvizice knihovny na straně klienta v ASP.NET Core s LibManem

Podle [Scott Addie](https://twitter.com/Scott_Addie)

Správce knihovny (LibMan) je lehký nástroj pro získávání knihovny na straně klienta. LibMan stahuje oblíbené knihovny a architektury ze souborového systému nebo ze [sítě pro doručování obsahu (CDN).](https://wikipedia.org/wiki/Content_delivery_network) Mezi podporované sítě CDN patří [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)a [unpkg](https://unpkg.com/#/). Vybrané soubory knihovny jsou načteny a umístěny na příslušném místě v rámci projektu ASP.NET Core.

## <a name="libman-use-cases"></a>Případy použití LibMan

LibMan nabízí následující výhody:

* Stahují se pouze soubory knihovny, které potřebujete.
* Další nástroje, například [Node.js](https://nodejs.org), [npm](https://www.npmjs.com)a [WebPack](https://webpack.js.org), nejsou nutné k získání podmnožiny souborů v knihovně.
* Soubory lze umístit do určitého umístění, aniž by se uchylovaly k vytváření úkolů nebo ručního kopírování souborů.

Další informace o výhodách společnosti LibMan nasledujte [moderní front-end ový vývoj webových aplikací ve Visual Studiu 2017: Segment LibMan](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).

LibMan není systém správy balíčků. Pokud již používáte správce balíčků, například npm nebo [přízi](https://yarnpkg.com), pokračujte v tom. LibMan nebyl vyvinut, aby tyto nástroje nahradil.

## <a name="additional-resources"></a>Další zdroje

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Úložiště LibMan GitHub](https://github.com/aspnet/LibraryManager)
