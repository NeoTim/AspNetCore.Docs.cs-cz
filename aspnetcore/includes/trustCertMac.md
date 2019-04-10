---
ms.openlocfilehash: 2ec079606cb48670dbc3852482fd8d401e7db44b
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59472322"
---
* Důvěřujete certifikátu vývoj HTTPS spuštěním následujícího příkazu:

    ```console
    dotnet dev-certs https --trust
    ```

* Ve výstupu předchozího příkazu se zobrazí následující výstup:

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* Pokud se zobrazí výzva, zadejte uživatelské jméno admin a heslo.  Certifikát se teď nainstalovaný a důvěryhodné.

    Zobrazit [důvěřovat certifikátu vývoj pro ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) Další informace.