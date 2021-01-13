## <a name="debug-diagnostics"></a>Отладка диагностики

Для подробного вывода диагностики построения маршрутов задайте для `Logging:LogLevel:Microsoft` значение `Debug`. Например, в среде разработки задайте уровень ведения журнала в *appsettings.Development.json*:

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
