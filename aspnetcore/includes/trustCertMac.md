* <span data-ttu-id="2767a-101">Vystavte certifikát pro vývoj HTTPS tak, že spustíte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2767a-101">Trust the HTTPS development certificate by running the following command:</span></span>

    ```dotnetcli
    dotnet dev-certs https --trust
    ```

* <span data-ttu-id="2767a-102">Předchozí příkaz zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="2767a-102">The preceding command displays the following output:</span></span>

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* <span data-ttu-id="2767a-103">Pokud se zobrazí výzva, zadejte uživatelské jméno a heslo správce.</span><span class="sxs-lookup"><span data-stu-id="2767a-103">Enter the admin username and password if prompted.</span></span>  <span data-ttu-id="2767a-104">Certifikát bude nyní nainstalován a považován za důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="2767a-104">The certificate will now be installed and trusted.</span></span>

    <span data-ttu-id="2767a-105">Další informace najdete v tématu [důvěryhodnost certifikátu pro vývoj ASP.NET Core https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .</span><span class="sxs-lookup"><span data-stu-id="2767a-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>