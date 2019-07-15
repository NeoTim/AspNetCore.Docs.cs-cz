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