## <a name="debug-diagnostics"></a>Diagnostika ladění

Pro podrobný výstup diagnostiky směrování nastavte `Logging:LogLevel:Microsoft` na `Debug`. Například ve vývojovém prostředí nastavte *nastavení aplikací. Development.json*:

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