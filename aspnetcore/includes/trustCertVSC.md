* Důvěřovat vývojovému certifikátu HTTPS spuštěním následujícího příkazu:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  Předchozí příkaz nefunguje na Linuxu. Důvěřujete certifikátu v dokumentaci k distribuci Linuxu.

  Předchozí příkaz zobrazí následující dialog:

  ![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

* Pokud souhlasíte s tím, že certifikátu vývoje důvěřujete, vyberte **možnost Ano.**

  Další informace najdete [v tématu Důvěryhodnost ASP.NET vývojového certifikátu Core HTTPS.](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)
  
