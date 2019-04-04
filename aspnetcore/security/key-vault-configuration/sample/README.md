---
ms.openlocfilehash: 209b5c41e17897693962954b1e795bdbb41f9384
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012731"
---
# <a name="key-vault-configuration-provider-sample-app"></a>Ukázkovou aplikaci v trezoru klíčů konfigurace zprostředkovatele

Tento příklad ukazuje použití konfigurace zprostředkovatele služby Azure Key Vault.

Ukázka spouští v jednom ze dvou režimů používaných určené `#define` příkazu v horní části *Program.cs* souboru. Pokyny najdete v tématu [direktivy preprocesoru ve vzorovém kódu](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate` &ndash; Ukazuje použití certifikát ID klienta aplikace Azure Key Vault a X.509 pro přístup k tajnými kódy uloženými v Azure Key Vault. Tuto verzi vzorku můžete spustit z libovolného místa a nasadit do Azure App Service nebo libovolného hostitele, která je schopná obsluhovat aplikace ASP.NET Core.
* `Managed` &ndash; Ukazuje, jak používat Azure [identita spravované služby](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace do služby Azure Key Vault pomocí ověřování Azure AD bez přihlašovacích údajů v kódu nebo konfigurace aplikace. ID klienta služby Azure AD a tajný kód se nevyžadují pro aplikace k ověření pomocí služby Azure Key Vault. Tato ukázka se musí nasadit do Azure App Service a prozkoumejte scearnio spravovaná identita.

Další informace najdete v tématu [konfigurace zprostředkovatele služby Azure Key Vault](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).
