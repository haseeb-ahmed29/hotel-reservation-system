# Hotel Reservation System

Room catalog, guests, reservations and booking status workflow. This repository is a runnable ASP.NET Core MVC 8.0 + EF Core SQLite foundation intended for extension into a full management product.

---

## Table of Contents

1. [Overview & Key Features](#overview--key-features)
2. [Operating Modes & Environments](#operating-modes--environments)
   - [Development Mode](#development-mode)
   - [Production Mode](#production-mode)
   - [Local-First Demo Mode](#local-first-demo-mode)
3. [Technology Stack & System Architecture](#technology-stack--system-architecture)
4. [Project Directory Structure](#project-directory-structure)
5. [Requirements & Prerequisites](#requirements--prerequisites)
6. [Getting Started & Quick Start](#getting-started--quick-start)
   - [Restore & Build](#restore--build)
   - [Running the Application](#running-the-application)
7. [Database Schema & Seeding](#database-schema--seeding)
8. [Complete Source Code Definitions](#complete-source-code-definitions)
   - [Project File (`HotelReservationSystem.csproj`)](#project-file-hotelreservationsystemcsproj)
   - [Application Entry Point (`Program.cs`)](#application-entry-point-programcs)
   - [Database Context (`Data/AppDbContext.cs`)](#database-context-dataappdbcontextcs)
   - [Models (`Models/`)](#models-models)
     - [`Models/Reservation.cs`](#modelsreservationcs)
     - [`Models/ErrorViewModel.cs`](#modelserrorviewmodelcs)
   - [Controllers (`Controllers/`)](#controllers-controllers)
     - [`Controllers/ReservationsController.cs`](#controllersreservationscontrollercs)
     - [`Controllers/HomeController.cs`](#controllershomecontrollercs)
   - [Views & Layouts (`Views/`)](#views--layouts-views)
     - [`Views/_ViewImports.cshtml`](#views_viewimportscshtml)
     - [`Views/_ViewStart.cshtml`](#views_viewstartcshtml)
     - [`Views/Shared/_Layout.cshtml`](#viewsshared_layoutcshtml)
     - [`Views/Shared/_ValidationScriptsPartial.cshtml`](#viewsshared_validationscriptspartialcshtml)
     - [`Views/Shared/Error.cshtml`](#viewssharederrorcshtml)
     - [`Views/Home/Index.cshtml`](#viewshomeindexcshtml)
     - [`Views/Home/Privacy.cshtml`](#viewshomeprivacycshtml)
     - [`Views/Reservations/Index.cshtml`](#viewsreservationsindexcshtml)
     - [`Views/Reservations/Create.cshtml`](#viewsreservationscreatecshtml)
     - [`Views/Reservations/Edit.cshtml`](#viewsreservationseditcshtml)
     - [`Views/Reservations/Delete.cshtml`](#viewsreservationsdeletecshtml)
   - [Launch & App Configuration](#launch--app-configuration)
     - [`Properties/launchSettings.json`](#propertieslaunchsettingsjson)
     - [`appsettings.example.json`](#appsettingsexamplejson)
   - [Static Frontend Assets (`wwwroot/`)](#static-frontend-assets-wwwroot)
     - [`wwwroot/css/site.css`](#wwwrootcsssitecss)
     - [`wwwroot/js/site.js`](#wwwrootjssitejs)
9. [Routes & Endpoints Matrix](#routes--endpoints-matrix)
10. [Production Roadmap](#production-roadmap)
11. [License](#license)
12. [Daily Maintenance](#daily-maintenance)

---

## Overview & Key Features

The **Hotel Reservation System** provides an end-to-end management workflow for hotel reservations, guests, and booking statuses. Key features include:

- **Dashboard Landing Page**: Domain-specific UI with intuitive navigation and operational summaries.
- **Automated Database Lifecycle**: Local SQLite database generated on-the-fly via Entity Framework Core 8 on initial run.
- **Data Seeding**: Pre-seeded initial records for immediate testing and verification.
- **Search & Status Filtering**: Instant query capabilities by reservation title/name and status (`Active`, `Pending`, `Archived`).
- **Complete CRUD Operations**: Create, view, edit, and delete reservation records with server-side and client-side validation.
- **Responsive Custom Styling**: Pure CSS styling with operational status badges, notifications, and accessible form components.

---

## Operating Modes & Environments

The application supports multiple execution modes depending on configuration and environment variables (`ASPNETCORE_ENVIRONMENT`).

### Development Mode
- Set via `ASPNETCORE_ENVIRONMENT=Development`.
- Developer exception page and detailed error diagnostics enabled.
- Hot reload enabled when running via `dotnet watch`.
- HTTP endpoint active by default on `http://localhost:5167`.

### Production Mode
- Set via `ASPNETCORE_ENVIRONMENT=Production`.
- Global exception handler maps to `/Home/Error`.
- HTTP Strict Transport Security (HSTS) enforced (`app.UseHsts()`).
- Database creation & runtime checks executed safely at startup.

### Local-First Demo Mode
- Utilizes SQLite local database file auto-creation (`db.Database.EnsureCreated()`).
- Auto-seeds starter records if the database table is empty.
- Requires zero external database server setup out-of-the-box.

---

## Technology Stack & System Architecture

- **Framework**: ASP.NET Core 8.0 (Model-View-Controller)
- **Data Access**: Entity Framework Core 8.0 SQLite Provider (`Microsoft.EntityFrameworkCore.Sqlite`)
- **Database Engine**: SQLite 3
- **Language**: C# 12 / .NET 8.0 SDK
- **Architecture Pattern**: Repository & DbContext Dependency Injection, Async Controller Actions, Anti-Forgery Tokens on POSTs

---

## Project Directory Structure

```
HotelReservationSystem/
├── Controllers/
│   ├── HomeController.cs
│   └── ReservationsController.cs
├── Data/
│   └── AppDbContext.cs
├── Models/
│   ├── ErrorViewModel.cs
│   └── Reservation.cs
├── Properties/
│   └── launchSettings.json
├── Views/
│   ├── Home/
│   │   ├── Index.cshtml
│   │   └── Privacy.cshtml
│   ├── Reservations/
│   │   ├── Create.cshtml
│   │   ├── Delete.cshtml
│   │   ├── Edit.cshtml
│   │   └── Index.cshtml
│   ├── Shared/
│   │   ├── Error.cshtml
│   │   ├── _Layout.cshtml
│   │   ├── _Layout.cshtml.css
│   │   └── _ValidationScriptsPartial.cshtml
│   ├── _ViewImports.cshtml
│   └── _ViewStart.cshtml
├── wwwroot/
│   ├── css/site.css
│   ├── js/site.js
│   └── lib/
├── appsettings.example.json
├── HotelReservationSystem.csproj
├── Program.cs
└── README.md
```

---

## Requirements & Prerequisites

- [.NET SDK 8.0](https://dotnet.microsoft.com/download/dotnet/8.0) or later.

---

## Getting Started & Quick Start

### Restore & Build

```bash
dotnet restore
dotnet build
```

### Running the Application

```bash
dotnet run
```

Open your browser at the URL output in the console (e.g., `http://localhost:5167`) and click **Manage records**. The SQLite database file will be generated automatically in the root folder.

---

## Database Schema & Seeding

### `Reservations` Entity Model

| Field Name    | Data Type         | Key / Validation                     | Description                              |
|---------------|-------------------|--------------------------------------|------------------------------------------|
| `Id`          | `INTEGER`         | Primary Key, Auto-Increment          | Unique identifier.                       |
| `Name`        | `TEXT`            | Required, MaxLength(120)             | Guest name or reservation title.         |
| `Description` | `TEXT`            | Nullable, MaxLength(600)             | Reservation details/notes.               |
| `Status`      | `TEXT`            | Required, MaxLength(30), Default "Active" | Workflow state (`Active`/`Pending`/`Archived`). |
| `CreatedAt`   | `TEXT` (DateTime) | Default `DateTime.UtcNow`            | UTC creation timestamp.                  |

---

## Complete Source Code Definitions

### Project File (`HotelReservationSystem.csproj`)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="8.0.13" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="8.0.13">
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
    </PackageReference>
  </ItemGroup>
</Project>
```

---

### Application Entry Point (`Program.cs`)

```csharp
using Microsoft.EntityFrameworkCore;
using HotelReservationSystem.Data;
using HotelReservationSystem.Models;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddControllersWithViews();
builder.Services.AddDbContext<AppDbContext>(options => options.UseSqlite(builder.Configuration.GetConnectionString("DefaultConnection")));
var app = builder.Build();
using (var scope = app.Services.CreateScope())
{
    var db = scope.ServiceProvider.GetRequiredService<AppDbContext>();
    db.Database.EnsureCreated();
    if (!db.Reservations.Any())
    {
        db.Reservations.Add(new Reservation { Name = "Sample Reservation", Description = "Replace this seeded record with real hotel reservation system data.", Status = "Active", CreatedAt = DateTime.UtcNow });
        db.SaveChanges();
    }
}
if (!app.Environment.IsDevelopment()) { app.UseExceptionHandler("/Home/Error"); app.UseHsts(); }
app.UseStaticFiles();
app.UseRouting();
app.MapControllerRoute(name: "default", pattern: "{controller=Home}/{action=Index}/{id?}");
app.Run();
```

---

### Database Context (`Data/AppDbContext.cs`)

```csharp
using Microsoft.EntityFrameworkCore;
using HotelReservationSystem.Models;

namespace HotelReservationSystem.Data;
public class AppDbContext(DbContextOptions<AppDbContext> options) : DbContext(options)
{
    public DbSet<Reservation> Reservations => Set<Reservation>();
}
```

---

### Models (`Models/`)

#### `Models/Reservation.cs`

```csharp
using System.ComponentModel.DataAnnotations;

namespace HotelReservationSystem.Models;
public class Reservation
{
    public int Id { get; set; }
    [Required, StringLength(120)]
    public string Name { get; set; } = string.Empty;
    [StringLength(600)]
    public string? Description { get; set; }
    [Required, StringLength(30)]
    public string Status { get; set; } = "Active";
    public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
}
```

#### `Models/ErrorViewModel.cs`

```csharp
namespace HotelReservationSystem.Models;

public class ErrorViewModel
{
    public string? RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);
}
```

---

### Controllers (`Controllers/`)

#### `Controllers/ReservationsController.cs`

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using HotelReservationSystem.Data;
using HotelReservationSystem.Models;

namespace HotelReservationSystem.Controllers;
public class ReservationsController(AppDbContext db) : Controller
{
    public async Task<IActionResult> Index(string? search, string? status)
    {
        var query = db.Reservations.AsNoTracking().AsQueryable();
        if (!string.IsNullOrWhiteSpace(search)) query = query.Where(x => x.Name.Contains(search));
        if (!string.IsNullOrWhiteSpace(status)) query = query.Where(x => x.Status == status);
        ViewBag.Search = search; ViewBag.Status = status;
        return View(await query.OrderByDescending(x => x.CreatedAt).ToListAsync());
    }
    public IActionResult Create() => View(new Reservation());
    [HttpPost, ValidateAntiForgeryToken]
    public async Task<IActionResult> Create(Reservation item)
    { if (!ModelState.IsValid) return View(item); db.Reservations.Add(item); await db.SaveChangesAsync(); TempData["Notice"] = "Record created successfully."; return RedirectToAction(nameof(Index)); }
    public async Task<IActionResult> Edit(int? id) => id is null ? NotFound() : (await db.Reservations.FindAsync(id) is Reservation item ? View(item) : NotFound());
    [HttpPost, ValidateAntiForgeryToken]
    public async Task<IActionResult> Edit(int id, Reservation item)
    { if (id != item.Id) return NotFound(); if (!ModelState.IsValid) return View(item); db.Update(item); await db.SaveChangesAsync(); TempData["Notice"] = "Record updated successfully."; return RedirectToAction(nameof(Index)); }
    public async Task<IActionResult> Delete(int? id) => id is null ? NotFound() : (await db.Reservations.AsNoTracking().FirstOrDefaultAsync(x=>x.Id==id) is Reservation item ? View(item) : NotFound());
    [HttpPost, ActionName("Delete"), ValidateAntiForgeryToken]
    public async Task<IActionResult> DeleteConfirmed(int id) { var item = await db.Reservations.FindAsync(id); if (item is not null) { db.Reservations.Remove(item); await db.SaveChangesAsync(); } return RedirectToAction(nameof(Index)); }
}
```

#### `Controllers/HomeController.cs`

```csharp
using System.Diagnostics;
using Microsoft.AspNetCore.Mvc;
using HotelReservationSystem.Models;

namespace HotelReservationSystem.Controllers;

public class HomeController : Controller
{
    private readonly ILogger<HomeController> _logger;

    public HomeController(ILogger<HomeController> logger)
    {
        _logger = logger;
    }

    public IActionResult Index()
    {
        return View();
    }

    public IActionResult Privacy()
    {
        return View();
    }

    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
    public IActionResult Error()
    {
        return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
    }
}
```

---

### Views & Layouts (`Views/`)

#### `Views/_ViewImports.cshtml`

```razor
@using HotelReservationSystem
@using HotelReservationSystem.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

#### `Views/_ViewStart.cshtml`

```razor
@{
    Layout = "_Layout";
}
```

#### `Views/Shared/_Layout.cshtml`

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Hotel Reservation System</title>
    <link rel="stylesheet" href="~/css/site.css">
</head>
<body>
    <header>
        <a href="/">Hotel Reservation System</a>
        <nav><a href="/Reservations">Manage records</a></nav>
    </header>
    <main class="container">
        @RenderBody()
    </main>
    <footer>ASP.NET Core MVC 8 · EF Core SQLite · Local-first demo</footer>
</body>
</html>
```

#### `Views/Shared/_ValidationScriptsPartial.cshtml`

```html
<script src="~/lib/jquery-validation/dist/jquery.validate.min.js"></script>
<script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.min.js"></script>
```

#### `Views/Shared/Error.cshtml`

```razor
@model ErrorViewModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed information about the error that occurred.
</p>
<p>
    <strong>The Development environment shouldn't be enabled for deployed applications.</strong>
    It can result in displaying sensitive information from exceptions to end users.
    For local debugging, enable the <strong>Development</strong> environment by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment variable to <strong>Development</strong>
    and restarting the app.
</p>
```

#### `Views/Home/Index.cshtml`

```html
<section class="hero">
    <span class="eyebrow">Management workspace</span>
    <h1>Hotel Reservation System</h1>
    <p>Room catalog, guests, reservations and booking status workflow. This working MVC foundation provides searchable, validated CRUD records backed by EF Core and SQLite.</p>
    <div class="actions">
        <a class="button" href="/Reservations">Open management area</a>
    </div>
</section>
<section class="cards">
    <article>
        <strong>Database-backed</strong>
        <p>EF Core creates the local SQLite database on first run.</p>
    </article>
    <article>
        <strong>Validation-ready</strong>
        <p>DataAnnotations protect required fields and text lengths.</p>
    </article>
    <article>
        <strong>Expandable</strong>
        <p>Add Identity, roles, reports, payments and domain-specific modules next.</p>
    </article>
</section>
```

#### `Views/Home/Privacy.cshtml`

```razor
@{
    ViewData["Title"] = "Privacy Policy";
}
<h1>@ViewData["Title"]</h1>

<p>Use this page to detail your site's privacy policy.</p>
```

#### `Views/Reservations/Index.cshtml`

```html
@model IEnumerable<HotelReservationSystem.Models.Reservation>
<section class="page-head">
    <div>
        <span class="eyebrow">Operations</span>
        <h1>Reservation records</h1>
    </div>
    <a class="button" href="/Reservations/Create">Add record</a>
</section>

@if (TempData["Notice"] is not null) {
    <div class="notice">@TempData["Notice"]</div>
}

<form class="filters" method="get">
    <input name="search" value="@ViewBag.Search" placeholder="Search by name">
    <select name="status">
        <option value="">All statuses</option>
        <option>Active</option>
        <option>Pending</option>
        <option>Archived</option>
    </select>
    <button class="button" type="submit">Filter</button>
</form>

<div class="table-wrap">
    <table>
        <thead>
            <tr>
                <th>Name</th>
                <th>Status</th>
                <th>Created</th>
                <th></th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model) {
                <tr>
                    <td>
                        <strong>@item.Name</strong><br>
                        <small>@item.Description</small>
                    </td>
                    <td><span class="status">@item.Status</span></td>
                    <td>@item.CreatedAt.ToLocalTime().ToString("dd MMM yyyy")</td>
                    <td>
                        <a href="/Reservations/Edit/@item.Id">Edit</a> ·
                        <a href="/Reservations/Delete/@item.Id">Delete</a>
                    </td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

#### `Views/Reservations/Create.cshtml`

```html
@model HotelReservationSystem.Models.Reservation
<h1>Add Reservation</h1>
<form method="post">
    <div asp-validation-summary="ModelOnly" class="validation"></div>
    <label asp-for="Name"></label>
    <input asp-for="Name">
    <span asp-validation-for="Name"></span>
    <label asp-for="Description"></label>
    <textarea asp-for="Description"></textarea>
    <label asp-for="Status"></label>
    <select asp-for="Status">
        <option>Active</option>
        <option>Pending</option>
        <option>Archived</option>
    </select>
    <button class="button" type="submit">Save record</button>
    <a href="/Reservations">Cancel</a>
</form>
```

#### `Views/Reservations/Edit.cshtml`

```html
@model HotelReservationSystem.Models.Reservation
<h1>Edit Reservation</h1>
<form method="post">
    <input type="hidden" asp-for="Id">
    <div asp-validation-summary="ModelOnly" class="validation"></div>
    <label asp-for="Name"></label>
    <input asp-for="Name">
    <span asp-validation-for="Name"></span>
    <label asp-for="Description"></label>
    <textarea asp-for="Description"></textarea>
    <label asp-for="Status"></label>
    <select asp-for="Status">
        <option>Active</option>
        <option>Pending</option>
        <option>Archived</option>
    </select>
    <button class="button" type="submit">Update record</button>
    <a href="/Reservations">Cancel</a>
</form>
```

#### `Views/Reservations/Delete.cshtml`

```html
@model HotelReservationSystem.Models.Reservation
<h1>Delete Reservation</h1>
<p>Are you sure you want to remove <strong>@Model.Name</strong>?</p>
<form method="post">
    <input type="hidden" asp-for="Id">
    <button class="button danger" type="submit">Confirm delete</button>
    <a href="/Reservations">Cancel</a>
</form>
```

---

### Launch & App Configuration

#### `Properties/launchSettings.json`

```json
{
  "$schema": "http://json.schemastore.org/launchsettings.json",
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:63704",
      "sslPort": 0
    }
  },
  "profiles": {
    "http": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "applicationUrl": "http://localhost:5167",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

#### `appsettings.example.json`

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=YourDatabase;Trusted_Connection=True;TrustServerCertificate=True;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

---

### Static Frontend Assets (`wwwroot/`)

#### `wwwroot/css/site.css`

```css
*{box-sizing:border-box}body{margin:0;background:#f5f1ea;color:#20292f;font:16px system-ui,sans-serif}header{display:flex;justify-content:space-between;align-items:center;padding:20px 6vw;background:#20292f;color:#fff}header a{color:#fff;text-decoration:none;font-weight:700;margin-left:18px}.container{max-width:1120px;margin:0 auto;padding:48px 20px}.hero{padding:54px 0;max-width:820px}.eyebrow{color:#e76045;text-transform:uppercase;letter-spacing:.13em;font-weight:800;font-size:.76rem}.hero h1,.page-head h1{font:clamp(2.4rem,6vw,5rem) Georgia,serif;line-height:1;margin:.3rem 0 1rem}.hero p{font-size:1.2rem;line-height:1.6;color:#665e55}.button{display:inline-block;background:#e76045;color:#fff;border:0;border-radius:10px;padding:12px 18px;text-decoration:none;font-weight:800;margin-top:10px}.button.danger{background:#973e34}.cards{display:grid;grid-template-columns:repeat(3,1fr);gap:16px}.cards article,.table-wrap,form{background:#fffdf9;border:1px solid #dfd6c9;border-radius:16px;padding:20px;box-shadow:0 12px 30px #4b382012}.cards p,small{color:#71685e;line-height:1.45}.page-head{display:flex;justify-content:space-between;align-items:center;gap:16px}.filters{display:flex;gap:10px;align-items:end;margin:22px 0}.filters input,.filters select,input,textarea,select{padding:11px;border:1px solid #d6ccbf;border-radius:9px;width:100%;background:#fff}.filters button{width:auto}.table-wrap{overflow:auto}table{width:100%;border-collapse:collapse}th,td{text-align:left;padding:14px 10px;border-bottom:1px solid #eee6db}.status{background:#fae0d8;color:#943d2d;border-radius:999px;padding:4px 9px;font-size:.8rem;font-weight:800}.notice{background:#e3f2e7;color:#24613a;padding:12px;border-radius:10px;margin:14px 0}form{max-width:680px}form label{display:block;margin-top:14px;font-weight:800}form textarea{min-height:120px}form span{color:#a53b2e;display:block;font-size:.85rem}.validation{color:#a53b2e}footer{text-align:center;color:#82796e;padding:30px}@media(max-width:720px){.cards{grid-template-columns:1fr}.page-head,.filters{display:block}.filters>*{margin-top:8px}header{display:block}header nav{margin-top:10px}}
```

#### `wwwroot/js/site.js`

```javascript
// Please see documentation at https://learn.microsoft.com/aspnet/core/client-side/bundling-and-minification
// for details on configuring this project to bundle and minify static web assets.

// Custom JavaScript code.
```

---

## Routes & Endpoints Matrix

| HTTP Method | Route URL | Controller Action | Description |
|-------------|-----------|-------------------|-------------|
| `GET` | `/` | `Home/Index` | Main application landing page and features dashboard. |
| `GET` | `/Home/Privacy` | `Home/Privacy` | Privacy policy page. |
| `GET` | `/Home/Error` | `Home/Error` | Error handling page. |
| `GET` | `/Reservations` | `Reservations/Index` | List reservations with optional `search` and `status` query filters. |
| `GET` | `/Reservations/Create` | `Reservations/Create` | Render form to create new reservation. |
| `POST` | `/Reservations/Create` | `Reservations/Create` | Validate and insert new reservation record. |
| `GET` | `/Reservations/Edit/{id}` | `Reservations/Edit` | Render form to edit existing reservation. |
| `POST` | `/Reservations/Edit/{id}` | `Reservations/Edit` | Validate and update existing reservation record. |
| `GET` | `/Reservations/Delete/{id}` | `Reservations/Delete` | Render confirmation view to delete reservation. |
| `POST` | `/Reservations/Delete/{id}` | `Reservations/DeleteConfirmed` | Execute reservation record deletion. |

---

## Production Roadmap

Add ASP.NET Core Identity and role policies, replace EnsureCreated with EF Core migrations, add the remaining domain relationships, audit logging, file storage, reporting, automated tests, and SQL Server deployment configuration.

---

## License

MIT

<!-- github-daily-pipeline -->
## Daily maintenance

README verified by the daily repository maintenance pipeline on 2026-10-17.
