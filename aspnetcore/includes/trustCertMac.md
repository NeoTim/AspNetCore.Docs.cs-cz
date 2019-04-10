---
ms.openlocfilehash: 2ec079606cb48670dbc3852482fd8d401e7db44b
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59472322"
---
* <span data-ttu-id="e8916-101">Důvěřujete certifikátu vývoj HTTPS spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="e8916-101">Trust the HTTPS development certificate by running the following command:</span></span>

    ```console
    dotnet dev-certs https --trust
    ```

* <span data-ttu-id="e8916-102">Ve výstupu předchozího příkazu se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="e8916-102">The preceding command displays the following output:</span></span>

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* <span data-ttu-id="e8916-103">Pokud se zobrazí výzva, zadejte uživatelské jméno admin a heslo.</span><span class="sxs-lookup"><span data-stu-id="e8916-103">Enter the admin username and password if prompted.</span></span>  <span data-ttu-id="e8916-104">Certifikát se teď nainstalovaný a důvěryhodné.</span><span class="sxs-lookup"><span data-stu-id="e8916-104">The certificate will now be installed and trusted.</span></span>

    <span data-ttu-id="e8916-105">Zobrazit [důvěřovat certifikátu vývoj pro ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) Další informace.</span><span class="sxs-lookup"><span data-stu-id="e8916-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>