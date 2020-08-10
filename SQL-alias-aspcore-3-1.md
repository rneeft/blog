# Using SQL Alias with ASPCore 3.1

We are using SQL Aliases to make sure every developer can run unit tests and connect to the local SQL instance. When writing unit test with ASPCore 3.1 we were unable to use SQL Aliases because those are stored in the Windows registry and therefore not supported within ASPCore [1,2]. There is however a way to retrieve the SQL Aliases from the Registry [3] but only for the Windows platforms, for us that isn't an issue. 

```csharp
using System;
#if !NET40
using System.Runtime.InteropServices;
using System.Data.SqlClient;
#endif

namespace SqlAlias
{
    public static class Aliases
    {
        public static string Map(string connectionString)
        {
#if NET40
            return connectionString;
#else

            if (RuntimeInformation.IsOSPlatform(OSPlatform.Windows) &&
                RuntimeInformation.FrameworkDescription.StartsWith(".NET Core")) // netstandard library may be used from NetFX runtime
            {

                try
                {
                    var builder = new SqlConnectionStringBuilder(connectionString);

                    var newSource = (string)Microsoft.Win32.Registry.GetValue(@"HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\MSSQLServer\Client\ConnectTo", builder.DataSource, null);
                    if (newSource != null)
                        builder.DataSource = newSource.Substring(newSource.IndexOf(',') + 1);

                    return builder.ConnectionString;
                }
                catch (Exception ex)
                {
                    throw new Exception("Failed to map the SQL Server alias", ex);
                }
            }

            return connectionString;
#endif
        }
    }
}
```
The code can be used as follows:

``` csharp
public static string DatabaseConnectionString => Aliases.Map(Current.GetConnectionString("UnitTestDB"));
```

Droyad [3] publishing the code as NuGet package: https://www.nuget.org/packages/SqlAlias

### References

- [1] https://github.com/dotnet/runtime/issues/14945
- [2] https://stackoverflow.com/questions/45327293/issues-with-sql-alias-in-connectionstring-in-appsettings-json/45330995
- [3] https://github.com/droyad/SqlAlias