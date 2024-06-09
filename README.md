# aws-cognito-.netCore-integration
A .NET Core Web API that integrates AWS Cognito for authentication, supports optional Multi-Factor Authentication (MFA) setup during the first login, and displays QR codes in Swagger UI. The project includes custom JavaScript for Swagger UI to render QR code images based on API responses.
## Features

- User login with AWS Cognito integration
- Optional Multi-Factor Authentication (MFA) setup
- QR code generation for MFA using TOTP
- Display of QR codes in Swagger UI via Base64 encoded images

## Prerequisites

- [.NET 6.0 SDK](https://dotnet.microsoft.com/download/dotnet/6.0)
- AWS Cognito setup with user pool and app client
- [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) for Swagger integration
- [QRCoder](https://www.nuget.org/packages/QRCoder/) for QR code generation

## Getting Started

### Installation

1. Clone the repository:

    ```bash
    git clone https://github.com/your-username/CognitoMFAQRCodeAPI.git
    cd CognitoMFAQRCodeAPI
    ```

2. Install the necessary packages:

    ```bash
    dotnet restore
    ```

### Configuration

1. Set up your AWS Cognito configuration in `appsettings.json`:

    ```json
    {
      "Cognito": {
        "ClientId": "your_client_id",
        "ClientSecret": "your_client_secret",
        "UserPoolId": "your_user_pool_id",
        "Secret": "your_totp_secret"
      }
    }
    ```

2. Ensure your `Startup.cs` is configured to use Swagger and serve static files:

    ```csharp
    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();
            services.AddSwaggerGen(c =>
            {
                c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
            });
        }

        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseStaticFiles(); // Ensure static files are served

            app.UseRouting();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllers();
            });

            app.UseSwagger();
            app.UseSwaggerUI(c =>
            {
                c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
                c.InjectJavascript("/swagger-ui/custom.js"); // Inject custom JS
            });
        }
    }
    ```

### Running the Application

1. Build and run the application:

    ```bash
    dotnet run
    ```

2. Navigate to the Swagger UI at `http://localhost:5000/swagger`.

3. Use the `/login` endpoint to log in. If the response contains a Base64 encoded QR code image, it will be displayed in the Swagger UI.

### API Endpoints

- `POST /login`: Authenticates a user and optionally returns a QR code image for MFA setup.

### Example Code

**`UserLoginModel.cs`**:

```csharp
public record UserLoginModel(string Email, string Password);
