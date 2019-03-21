---
ms.openlocfilehash: 6246247788eefc8f094ec1a7f156cb36715edb53
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210103"
---
# <a name="how-to-buildrun-secure-user-data-sample"></a><span data-ttu-id="2e99a-101">Jak sestavení nebo spuštění ukázková data zabezpečení uživatele</span><span class="sxs-lookup"><span data-stu-id="2e99a-101">How to build/run Secure user data sample</span></span>

* <span data-ttu-id="2e99a-102">Nastavení hesla pomocí nástroje Správce tajný kód:</span><span class="sxs-lookup"><span data-stu-id="2e99a-102">Set password with the Secret Manager tool:</span></span>

  `dotnet user-secrets set SeedUserPW <pw>`

* <span data-ttu-id="2e99a-103">Aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="2e99a-103">Update the database:</span></span>

  `dotnet ef database update`

* <span data-ttu-id="2e99a-104">Povolení HTTPS v projektu</span><span class="sxs-lookup"><span data-stu-id="2e99a-104">Enable HTTPS in the project</span></span>
