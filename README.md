# Bytehide Secrets Scanner for .NET ⚡

![Bytehide.Secrets.Scanner - Prevent secret leaks in .NET code with an AI-powered scanner that detects and protects API keys, tokens, and credentials.](https://www.bytehide.com/wp-content/uploads/2025/03/bytehide-secrets-scanner-for-dotnet.png)

**Bytehide.Secrets.Scanner** is a lightweight, powerful secret detection tool for .NET projects. It integrates directly into your build process to scan and protect your code from accidentally exposing sensitive data such as API keys, tokens, and passwords. It goes beyond traditional scanners by also analyzing your **post-compilation binaries** and offering advanced AI-based detection (in its advanced version) with a secure, zero-knowledge approach.

[![NuGet](https://img.shields.io/nuget/v/Bytehide.Secrets.Scanner.svg?style=flat-square)](https://www.nuget.org/packages/Bytehide.Secrets.Scanner/)
[![NuGet Downloads](https://img.shields.io/nuget/dt/Bytehide.Secrets.Scanner.svg?style=flat-square)](https://www.nuget.org/packages/Bytehide.Secrets.Scanner/)
[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg?style=flat-square)](https://github.com/ByteHide/bytehide-secrets-scanner-dotnet/blob/main/LICENSE)


---

## Important Security Notice

**Stay Cool & Secure:**
- **Local & Private:** All scanning is performed on your machine. Neither your source code nor your binaries ever leave your environment—total privacy guaranteed!
- **Beyond Source Code:** Unlike other scanners, Bytehide.Secrets.Scanner goes the extra mile by scanning your **post-compilation binaries**. This means it can catch secrets that might be injected during the build process and hidden from plain sight in your source code.

Enjoy the peace of mind knowing that your secrets are covered from every angle.

---

## Compatibility

**Bytehide.Secrets.Scanner** works with any .NET application, including **.NET Standard 2.0+, .NET Framework 4.7.2+, .NET 6, .NET 7, ASP.NET, .NET MAUI**, and more. If your project compiles under .NET, you can integrate this scanner.

---

## Installation

You can install **Bytehide.Secrets.Scanner** from NuGet.org:

<details>
  <summary><strong>.NET CLI</strong></summary>

```bash
dotnet add package Bytehide.Secrets.Scanner
```
</details>

<details>
  <summary><strong>Package Manager Console in Visual Studio</strong></summary>

```powershell
Install-Package Bytehide.Secrets.Scanner
```
</details>

<details>
  <summary><strong>PackageReference</strong></summary>

```xml
<PackageReference Include="Bytehide.Secrets.Scanner" Version="1.0.0">
  <PrivateAssets>all</PrivateAssets>
  <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
</PackageReference>
```
</details>

---

## Getting Started

### 1. Get Your Free Token

Create a free account at [cloud.bytehide.com/register](https://cloud.bytehide.com/register). In under two minutes, you can set up a **.NET Secrets project**:

1. Sign up or sign in to the ByteHide Cloud.
2. Create a new project and select **Secrets**.
3. Copy the **Project Token** shown in your project's dashboard.

For more details, check out our [official guide](https://www.bytehide.com/docs/platforms/dotnet/products/secrets/create-secrets-project).

### 2. Create the Configuration File

In your .NET project, create a file named `bytehide.secrets.json` with the following content:

```json
{
  "Name": "My Configuration",
  "Environment": "production",
  "ProjectToken": "<Your project token here>",
  "RunConfiguration": "*",
  "Enabled": true,
  "DisplayCode": false,
  "Actions": {
    "export": {
      "enabled": true,
      "encrypt": false,
      "prefix": "auto_"
    }
  }
}
```

- **Environment:** The environment (e.g., "production") for exporting secrets.
- **ProjectToken:** Your token from step 1.
- **RunConfiguration:** When to run the scanner (e.g., "debug", "release", or "*" for all).
- **Enabled:** Enable or disable the scanner (`true` / `false`).
- **DisplayCode:** If `true`, stores the code location of the secret in the ByteHide web panel (useful for auditing).
- **Actions:** Customize behavior such as exporting to the ByteHide secrets manager, sending alerts, or encrypting secrets in the final binary.  
  - **export.enabled:** Export the detected secrets to the ByteHide secrets manager.  
  - **export.encrypt:** Obfuscate/encrypt the secret key in the final binary to add extra protection against reverse engineering.  
  - **export.prefix:** A prefix to distinguish automatically exported secrets.

> **SECURITY WARNING**  
>  
> **Never publish or include your `bytehide.secrets.json` file** in:  
> - Public Git repositories (add it to `.gitignore`).  
> - Production servers or containers.  
> - Distributed NuGet packages (`.nupkg`).  
> - Mobile app bundles (APK/IPA).  
>  
> This file contains sensitive configuration data and must remain private.  
> Make sure to exclude it from all build outputs and deployment assets to prevent unauthorized access.


### 3. Build and Scan

With the `bytehide.secrets.json` file in place and **Bytehide.Secrets.Scanner** installed, simply build your project. The scanner will:

- Check if `"Enabled": true` and if your current build configuration matches `"RunConfiguration"`.
- Scan both source code and **post-compilation binaries**.
- Automatically export secrets to the ByteHide manager if configured.

### 4. Manage and Review

![Secrets automatic detection in dotnet](https://www.bytehide.com/wp-content/uploads/2025/03/Screenshot-2025-03-05-at-16.31.46.png)

All analysis and detections appear automatically in the ByteHide dashboard. If **Actions** are configured to `"export"`, the secrets will also be stored in the integrated ByteHide secrets manager—no need for extra services like Azure Key Vault.  

You can also configure alerts to be sent to Slack, Microsoft Teams, email, or any webhook you define. If you prefer, you can fail the build on secret detection or simply log a warning.

---

## Usage Examples

### Automatic Secret Detection

When integrated, the scanner will process your code during builds. For example, if your code contains a hardcoded API key:

```csharp
// Before: Sensitive data exposed
string apiKey = "sk_live_ABC123XYZ789";

// After: The scanner detects and transforms the secret
string apiKey = secure("i19k"); // The real value is hidden or exported
```

> The exact replacement format (e.g., `secure("i19k")`) depends on your configuration. In all cases, the real secret value is protected.

### Manual Secret Marking

If you need more granular control, install **ByteHide.ToolBox** and use the provided markers:

```csharp
using Bytehide.ToolBox.Secrets;

public class MyService
{
    [SecretsMarker.ThisIsASecret]
    private string connectionString = "Server=myServer;User Id=myUser;Password=myPassword;";
}

// Alternatively:
var password = SecretsMarker.ThisIsASecret("drowssap", "database_password");

// Or extension methods:
var password = "drowssap".IsASecret("my_db_password");
var password = "drowssap".IsASecret();
```

---

## Benefits and Use Cases

- **Enhanced Security:** Prevent accidental exposure of credentials in source code or compiled binaries.
- **Compliance:** Helps meet security standards and regulatory requirements by ensuring sensitive data is protected.
- **Seamless Integration:** Works automatically with your existing development and CI/CD tools, plus a built-in secrets manager.
- **Time-Saving:** Automates secret detection, letting you focus on building features rather than manual code reviews.

---

## Why Choose Bytehide.Secrets.Scanner?

- **Advanced AI Detection (Zero-Knowledge):** Tailor the scanner to match your project's unique requirements with privacy in mind.
- **Effortless Integration:** Easy to set up within your build process without invasive changes.
- **Cross-Platform Compatibility:** Supports the most widely used .NET frameworks and environments.
- **Built-In Secrets Manager:** No need for additional tools like Azure Key Vault. Export your secrets automatically to the ByteHide manager at no extra cost.
- **Comprehensive Documentation and Community Support:** Access detailed guides and join an active community for assistance and improvements.

---

## Get Started Today!

**Secure your code now!**  
Don't risk exposing sensitive information.  
- [Create your free account](https://cloud.bytehide.com/register) and get a token.  
- [Install Bytehide.Secrets.Scanner](https://www.nuget.org/packages/Bytehide.Secrets.Scanner) to experience automated protection for your projects.  
- Learn more in our [official documentation](https://bytehide.com/docs/platforms/dotnet/products/secrets).

![Bytehide.Secrets.Scanner - Prevent secret leaks in .NET code with an AI-powered scanner that detects and protects API keys, tokens, and credentials.](https://www.bytehide.com/wp-content/uploads/2025/03/bytehide-secrets-scanner-for-dotnet.png)
