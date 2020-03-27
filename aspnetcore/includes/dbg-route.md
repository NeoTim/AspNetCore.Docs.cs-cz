## <a name="debug-diagnostics"></a>Diagnostika ladění

Pro podrobný výstup diagnostiky směrování nastavte `Logging:LogLevel:Microsoft` na `Debug`. Například ve vývojovém prostředí nastavte *appSettings. Vývoj. JSON*:

```JSON
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}