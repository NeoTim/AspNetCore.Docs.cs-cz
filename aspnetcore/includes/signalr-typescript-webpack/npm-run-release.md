---
ms.openlocfilehash: c82571d3cfa57ccd6e7c83f654f119bdd8991486
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210441"
---
```console
npm run release
```

<span data-ttu-id="1943a-101">Tento příkaz provede na straně klienta prostředků ke zpracování při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1943a-101">This command yields the client-side assets to be served when running the app.</span></span> <span data-ttu-id="1943a-102">Prostředky jsou umístěné v *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="1943a-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="1943a-103">Webpacku dokončit následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="1943a-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="1943a-104">Vymazat obsah *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="1943a-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="1943a-105">Převést TypeScript pro JavaScript&mdash;tento proces se označuje jako *transpilation*.</span><span class="sxs-lookup"><span data-stu-id="1943a-105">Converted the TypeScript to JavaScript&mdash;a process known as *transpilation*.</span></span>
* <span data-ttu-id="1943a-106">Pozměnění generovaný JavaScript, který chcete zmenšit velikost souboru&mdash;tento proces se označuje jako *připravenost k minifikaci*.</span><span class="sxs-lookup"><span data-stu-id="1943a-106">Mangled the generated JavaScript to reduce file size&mdash;a process known as *minification*.</span></span>
* <span data-ttu-id="1943a-107">Zpracované soubory jazyka JavaScript, CSS a HTML z zkopírovány *src* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="1943a-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="1943a-108">Vloží do následující prvky *wwwroot/index.html* souboru:</span><span class="sxs-lookup"><span data-stu-id="1943a-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="1943a-109">A `<link>` označit, odkazující *wwwroot/main.\< Hodnota hash\>.css* souboru.</span><span class="sxs-lookup"><span data-stu-id="1943a-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="1943a-110">Tato značka je umisťovaný bezprostředně před uzavírací `</head>` značky.</span><span class="sxs-lookup"><span data-stu-id="1943a-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="1943a-111">A `<script>` značky, odkazující minifikovaný *wwwroot/main.\< Hodnota hash\>js* souboru.</span><span class="sxs-lookup"><span data-stu-id="1943a-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="1943a-112">Tato značka je umisťovaný bezprostředně před uzavírací `</body>` značky.</span><span class="sxs-lookup"><span data-stu-id="1943a-112">This tag is placed immediately before the closing `</body>` tag.</span></span>
