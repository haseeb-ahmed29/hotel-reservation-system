# Hotel Reservation System

Room catalog, guests, reservations and booking status workflow. This repository is a runnable ASP.NET Core MVC 8.0 + EF Core SQLite foundation intended for extension into a full management product.

---

## Table of Contents

1. [Overview & Features](#overview--features)
2. [Technology Stack](#technology-stack)
3. [Project Directory Structure](#project-directory-structure)
4. [Requirements & Prerequisites](#requirements--prerequisites)
5. [Getting Started](#getting-started)
   - [Restore & Build](#restore--build)
   - [Running the Application](#running-the-application)
6. [Database Schema & Seeding](#database-schema--seeding)
7. [Complete Source Code Definitions](#complete-source-code-definitions)
   - [Project Configuration (`HotelReservationSystem.csproj`)](#project-configuration-hotelreservationsystemcsproj)
   - [Application Entry Point (`Program.cs`)](#application-entry-point-programcs)
   - [Database Context (`Data/AppDbContext.cs`)](#database-context-dataappdbcontextcs)
   - [Data Models (`Models/`)](#data-models-models)
     - [`Models/Reservation.cs`](#modelsreservationcs)
     - [`Models/ErrorViewModel.cs`](#modelserrorviewmodelcs)
   - [Controllers (`Controllers/`)](#controllers-controllers)
     - [`Controllers/ReservationsController.cs`](#controllersreservationscontrollercs)
     - [`Controllers/HomeController.cs`](#controllershomecontrollercs)
   - [Views (`Views/`)](#views-views)
     - [`Views/Shared/_Layout.cshtml`](#viewsshared_layoutcshtml)
     - [`Views/Home/Index.cshtml`](#viewshomeindexcshtml)
     - [`Views/Reservations/Index.cshtml`](#viewsreservationsindexcshtml)
     - [`Views/Reservations/Create.cshtml`](#viewsreservationscreatecshtml)
     - [`Views/Reservations/Edit.cshtml`](#viewsreservationseditcshtml)
     - [`Views/Reservations/Delete.cshtml`](#viewsreservationsdeletecshtml)
   - [Configuration (`appsettings.example.json`)](#configuration-appsettingsexamplejson)
8. [Routes & Endpoints](#routes--endpoints)
9. [Production Roadmap](#production-roadmap)
10. [License](#license)
11. [Daily Maintenance](#daily-maintenance)

---

## Overview & Features

The **Hotel Reservation System** provides an end-to-end management workflow for hotel reservations. Key working features include:

- **Dashboard Landing Page**: Domain-specific UI with clear operational navigation.
- **Automated Database Lifecycle**: SQLite database created automatically with Entity Framework Core 8 on initial run.
- **Data Seeding**: Pre-seeded initial records for immediate testing and verification.
- **Search & Filter**: Search reservations by guest name and filter by status (`Active`, `Pending`, `Archived`).
- **Full CRUD Support**: Create, read, edit, and delete reservation records with server-side and client-side validation.
- **Responsive Custom UI**: CSS styling featuring operational badges, notifications, and accessible form controls.

---

## Technology Stack

- **Framework**: ASP.NET Core 8.0 (MVC)
- **ORM**: Entity Framework Core 8.0 (EF Core SQLite)
- **Database**: SQLite (local file-based storage)
- **Language**: C# 12 / .NET 8 SDK
- **Frontend**: Razor Views (CSHTML), CSS3, JavaScript, jQuery Validation

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

## Getting Started

### Restore & Build

```bash
dotnet restore
dotnet build
```

### Running the Application

```bash
dotnet run
```

Open the local HTTP URL printed in the terminal (e.g., `http://localhost:5000` or `https://localhost:5001`) and navigate to **Manage records**. The SQLite database file will automatically be created in the project root on the first launch.

---

## Database Schema & Seeding

### `Reservations` Table

| Field Name   | Data Type     | Constraints                          | Description                       |
|--------------|---------------|--------------------------------------|-----------------------------------|
| `Id`         | `INTEGER`     | Primary Key, Auto Increment          | Unique reservation identifier.    |
| `Name`       | `TEXT`        | Required, Max Length 120             | Guest or reservation title/name.  |
| `Description`| `TEXT`        | Nullable, Max Length 600             | Additional details/notes.         |
| `Status`     | `TEXT`        | Required, Max Length 30, Default `Active` | Status (`Active`/`Pending`/`Archived`). |
| `CreatedAt`  | `TEXT` (DateTime) | Default `DateTime.UtcNow`           | UTC timestamp of creation.        |

---

## Complete Source Code Definitions

### Project Configuration (`HotelReservationSystem.csproj`)

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

### Data Models (`Models/`)

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

### Views (`Views/`)

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

### Configuration (`appsettings.example.json`)

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

## Routes & Endpoints

| HTTP Method | URL Route | Controller / Action | Description |
|-------------|-----------|---------------------|-------------|
| `GET` | `/` | `Home/Index` | Landing dashboard page. |
| `GET` | `/Home/Privacy` | `Home/Privacy` | Privacy policy page. |
| `GET` | `/Reservations` | `Reservations/Index` | Search and list reservation records. |
| `GET` | `/Reservations/Create` | `Reservations/Create` | Form to create a new reservation. |
| `POST` | `/Reservations/Create` | `Reservations/Create` | Process creation of new reservation. |
| `GET` | `/Reservations/Edit/{id}` | `Reservations/Edit` | Form to edit existing reservation. |
| `POST` | `/Reservations/Edit/{id}` | `Reservations/Edit` | Process update to existing reservation. |
| `GET` | `/Reservations/Delete/{id}` | `Reservations/Delete` | Confirmation prompt to delete reservation. |
| `POST` | `/Reservations/Delete/{id}` | `Reservations/DeleteConfirmed` | Execute deletion of reservation record. |

---

## Production Roadmap

Add ASP.NET Core Identity and role policies, replace EnsureCreated with EF Core migrations, add the remaining domain relationships, audit logging, file storage, reporting, automated tests, and SQL Server deployment configuration.

---

## License

MIT

<!-- github-daily-pipeline -->
## Daily maintenance

README verified by the daily repository maintenance pipeline on 2026-10-17.
