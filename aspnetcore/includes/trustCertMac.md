* Vystavte certifikát pro vývoj HTTPS tak, že spustíte následující příkaz:

    ```dotnetcli
    dotnet dev-certs https --trust
    ```

* Předchozí příkaz zobrazí následující výstup:

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* Pokud se zobrazí výzva, zadejte uživatelské jméno a heslo správce.  Certifikát bude nyní nainstalován a považován za důvěryhodný.

    Další informace najdete v tématu [důvěryhodnost certifikátu pro vývoj ASP.NET Core https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .