# dotenv.net

[![CircleCI](https://circleci.com/gh/bolorundurowb/dotenv.net.svg?style=svg)](https://circleci.com/gh/bolorundurowb/dotenv.net) [![NuGet Badge](https://buildstats.info/nuget/dotenv.net)](https://www.nuget.org/packages/dotenv.net) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE) [![Coverage Status](https://coveralls.io/repos/github/bolorundurowb/dotenv.net/badge.svg?branch=master)](https://coveralls.io/github/bolorundurowb/dotenv.net?branch=master)

dotenv.net is a zero-dependency module that loads environment variables from a .env environment variable file into `System.Environment`. It has built in support for the in-built dependency injection framework packaged with ASP.NET Core. It now comes packaged with an interface that allows for reading environment variables wihtout repeated calls to `Environment.GetEnvironmentVariable("KEY");`.  If you have ideas or issues, create an issue.

## Contributors

Big ups to those who have contributed to this library. :clap:

[@bolorundurowb](https://github.com/bolorundurowb) [@joliveros](https://github.com/joliveros) [@vizeke](https://github.com/vizeke) [@merqlove](https://github.com/merqlove) [@tracker1](https://github.com/tracker1)  [@NaturalWill](https://github.com/NaturalWill)  [@texyh](https://github.com/texyh)

## Usage

### Conventional

First install the library as a dependency in your application from nuget

```
Install-Package dotenv.net
```

or

```
dotnet add package dotenv.net
```

or for paket

```
paket add dotenv.net
```

Create a file with no filename and an extension of `.env`.

A sample `.env` file would look like this:
```text
DB_HOST=localhost
DB_USER=root
DB_PASS=s1mpl3
```

in the `Startup.cs` file or as early as possible in your code add the following:

```csharp
using dotenv.net;


...


DotEnv.Config();
```

At times the `.env` would not be in the same directory as your assembly, in that case the following would apply

```csharp
using dotenv.net

...

var success = DotEnv.AutoConfig(); // success holds a value stating whether the env was found and read or not
```

this looks through your assembly's folder and three folders up for a `.env` file and loads that.

the values saved in your `.env` file would be availale in your application and can be accessed via:
 ```csharp
Environment.GetEnvironmentVariable("DB_HOST"); // would output 'localhost'
```

## Using with DI (`IServiceCollection`)

If using with ASP.NET Core or any other system that uses `IServiceCollection` for its dependency injection, in the `Startup.cs` file

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    // configure dotenv
    services.AddEnv(builder => {
        builder
        .AddEnvFile("/custom/path/to/your/env/vars")
        .AddThrowOnError(false)
        .AddEncoding(Encoding.ASCII);
    });
}
```

## Using with DI (`ContainerBuilder`)

If using with ASP.NET Core or any other system that uses `ContainerBuilder` for its dependency injection, in the `Startup.cs` file

``` csharp
...

// configure dotenv
containerBuilder.AddEnv(builder => {
    builder
    .AddEnvFile("/custom/path/to/your/env/vars")
    .AddThrowOnError(false)
    .AddEncoding(Encoding.ASCII);
});
```

With any of the above, your application would have the env file variables imported.

### Options

#### ThrowError

Default: `true`

You can specify if you want the library to error out if any issue arises or fail silently.

```csharp
DotEnv.Config(false); //fails silently
```

#### Path

Default: `.env`

You can specify a custom path if your file containing environment variables is
named or located differently.

```csharp
DotEnv.Config(true, "/custom/path/to/your/env/vars");
```

#### Encoding

Default: `Encoding.UTF8`

You may specify the encoding of your file containing environment variables
using this option.

```csharp
DotEnv.Config(true, ".env", Encoding.Unicode);
```

#### Trim Values

Default: `true`

You may specify whether or not you want the values retrieved to be trimmed i.e have all leading and trailing whitepaces removed.

```csharp
DotEnv.Config(true, ".env", Encoding.Unicode, false);
```

## Support For `IEnvReader`

With `v1.0.6` and above an interface `IEnvReader` has been introduced that specifies methods that help with reading values from the environment easily. The library has a default implementation `EnvReader` that can be added to the default ASP.NET Core DI framework (`IServiceCollection`).

### Using `EnvReader`

```csharp
using dotenv.net.Utilities;
...

var envReader = new EnvReader();
var value = envReader.GetValue("KEY");
```

### Using `IEnvReader` with DI

In the `StartUp.cs` file, in the `ConfigureServices` method

For (ASP.NET DI)

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddEnvReader();
    ...
}
```

For Autofac

```csharp
...

containerBuilder.AddEnvReader();
```

In the rest of your application, the `IEnvReader` interface can get injected and used. For example, in a `SampleController` class for example:

```csharp
public class SampleController
{
    private readonly IEnvReader _envReader;
    
    public SampleController(IEnvReader envReader)
    {
        _envReader = envReader;
    }
}
```

### IEnvReader Methods

#### string GetStringValue(string key)

Default: `null`

Retrieve a value from the current environment by the given key and throws an exception if not found.

#### int GetIntValue(string key)

Default: `0`

Retrieve a value from the current environment by the given key and throws an exception if not found.

#### double GetDoubleValue(string key)

Default: `0.0`

Retrieve a value from the current environment by the given key and throws an exception if not found.

#### decimal GetDecimalValue(string key)

Default: `0.0m`

Retrieve a value from the current environment by the given key and throws an exception if not found.

#### bool GetBooleanValue(string key)

Default: `false`

Retrieve a value from the current environment by the given key and throws an exception if not found.


#### bool TryGetStringValue(string key, out string value)

Default: `null`

A safer method to use when retrieving values from the environment as it returns a boolean value stating whether it successfully retrieved the value required.

#### bool TryGetIntValue(string key, out int value)

Default: `0`

A safer method to use when retrieving values from the environment as it returns a boolean value stating whether it successfully retrieved the value required.

#### bool TryGetDoubleValue(string key, out double value)

Default: `0.0`

A safer method to use when retrieving values from the environment as it returns a boolean value stating whether it successfully retrieved the value required.

#### bool TryGetDecimalValue(string key, out decimal value)

Default: `0.0m`

A safer method to use when retrieving values from the environment as it returns a boolean value stating whether it successfully retrieved the value required.

#### bool TryGetBooleanValue(string key, out bool value)

Default: `false`

A safer method to use when retrieving values from the environment as it returns a boolean value stating whether it successfully retrieved the value required.
