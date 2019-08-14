* Vystavte certifikát pro vývoj HTTPS tak, že spustíte následující příkaz:

  ```console
  dotnet dev-certs https --trust
  ```
  
  Předchozí příkaz nefunguje na Linux. Informace o důvěřování certifikátu najdete v dokumentaci k distribuci Linux.

  Předchozí příkaz zobrazí následující dialog:

  ![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

* Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .

  Další informace najdete v tématu [důvěryhodnost certifikátu pro vývoj ASP.NET Core https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .
  
