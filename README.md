[中文版](./README-zh.md)

# Nancy.Authentication.JwtBearer

A JwtBearer authentication provider for Nancy.It is available in NuGet (only supports .NET Core now).


# Quick Start

## Instatll the package first

`Install-Package Nancy.Authentication.JwtBearer`

## Create a Bootstrapper class

```csharp
public class Bootstrapper : Nancy.DefaultNancyBootstrapper
{
    protected override void ApplicationStartup(TinyIoCContainer container, IPipelines pipelines)
    {
        base.ApplicationStartup(container, pipelines);

        var keyByteArray = Encoding.ASCII.GetBytes("Y2F0Y2hlciUyMHdvbmclMjBsb3ZlJTIwLm5ldA==");
        var signingKey = new SymmetricSecurityKey(keyByteArray);

        var tokenValidationParameters = new TokenValidationParameters
        {
            // The signing key must match!
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = signingKey,

            // Validate the JWT Issuer (iss) claim
            ValidateIssuer = true,
            ValidIssuer = "http://www.c-sharpcorner.com/members/catcher-wong",

            // Validate the JWT Audience (aud) claim
            ValidateAudience = true,
            ValidAudience = "Catcher Wong",

            // Validate the token expiry
            ValidateLifetime = true,

            ClockSkew = TimeSpan.Zero
        };

        var configuration = new JwtBearerAuthenticationConfiguration
        {
            TokenValidationParameters = tokenValidationParameters,
            Challenge = "Guest"//if not use this,default to Bearer
        };

        pipelines.EnableJwtBearerAuthentication(configuration);
    }
}
```

## Add RequiresAuthentication in your modules

```csharp
public class MainModule : Nancy.NancyModule
{
    public MainModule()
    {
        this.RequiresAuthentication();

        Get("/", _ => 
        {
            return "From JwtBearer Authentication";
        });
    }
}
```