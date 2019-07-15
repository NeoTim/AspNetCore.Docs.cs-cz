* <span data-ttu-id="cfe32-101">Důvěřujete certifikátu vývoj HTTPS spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="cfe32-101">Trust the HTTPS development certificate by running the following command:</span></span>

    ```console
    dotnet dev-certs https --trust
    ```

* <span data-ttu-id="cfe32-102">Ve výstupu předchozího příkazu se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="cfe32-102">The preceding command displays the following output:</span></span>

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* <span data-ttu-id="cfe32-103">Pokud se zobrazí výzva, zadejte uživatelské jméno admin a heslo.</span><span class="sxs-lookup"><span data-stu-id="cfe32-103">Enter the admin username and password if prompted.</span></span>  <span data-ttu-id="cfe32-104">Certifikát se teď nainstalovaný a důvěryhodné.</span><span class="sxs-lookup"><span data-stu-id="cfe32-104">The certificate will now be installed and trusted.</span></span>

    <span data-ttu-id="cfe32-105">Zobrazit [důvěřovat certifikátu vývoj pro ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) Další informace.</span><span class="sxs-lookup"><span data-stu-id="cfe32-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>