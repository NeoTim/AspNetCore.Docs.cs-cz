---
title: Získání knihoven na straně klienta v ASP.NET Core s LibMan
author: scottaddie
description: Informace o instalaci knihoven na straně klienta v projektu aplikace ASP.NET Core pomocí Správce knihovny (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: a6ff0cc3342cfac74739387aa17046ed5050232f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900150"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Získání knihoven na straně klienta v ASP.NET Core s LibMan

Podle [Scott Addie](https://twitter.com/Scott_Addie)

Správce knihovny (LibMan) je nástroj pro získání zjednodušené, na straně klienta knihovny. LibMan stáhne oblíbených knihoven a architektur ze systému souborů nebo z [síť pro doručování obsahu (CDN)](https://wikipedia.org/wiki/Content_delivery_network). Podporované sítě CDN patří [CDNJS](https://cdnjs.com/) a [unpkg](https://unpkg.com/#/). Soubory vybrané knihovny jsou vyvolány a najdete v příslušné umístění v rámci projektu ASP.NET Core.

## <a name="libman-use-cases"></a>LibMan případy použití

LibMan nabízí následující výhody:

* Budou staženy pouze soubory knihovny, které potřebujete.
* Další nástroje, jako například [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), a [Webpacku](https://webpack.js.org), není nutné získat podmnožinu souborů v knihovně.
* Soubory mohou být umístěny v konkrétním umístění bez použití svislých úlohy sestavení nebo ručního kopírování.

Podívejte se na další informace o výhodách společnosti LibMan [front-endu moderního webového vývoje v sadě Visual Studio 2017: LibMan segmentu](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).

LibMan není systém správy balíčků. Pokud už používáte Správce balíčků, jako je například npm nebo [yarn](https://yarnpkg.com), tím pokračovat i. LibMan nebyl vyvinuté nahradit těchto nástrojů.

## <a name="additional-resources"></a>Další zdroje

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Úložiště LibMan GitHub](https://github.com/aspnet/LibraryManager)
