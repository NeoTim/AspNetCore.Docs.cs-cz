## <a name="debug-diagnostics"></a>Diagnostika ladění

Pro podrobný výstup diagnostiky směrování nastavte `Logging:LogLevel:Microsoft` na `Debug` . Ve vývojovém prostředí nastavte úroveň protokolu v *appsettings.Development.jsna*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
