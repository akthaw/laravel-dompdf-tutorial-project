# How to Generate PDFs in Laravel with DomPDF

A step-by-step guide for integrating the popular `barryvdh/laravel-dompdf` package to generate dynamic PDF files from a Blade view. This project demonstrates a common business requirement like creating invoices, reports, or tickets.

---

### Table of Contents
1.  [Prerequisites](#prerequisites)
2.  [Step 1: Create a New Laravel Project](#step-1-create-a-new-laravel-project)
3.  [Step 2: Install the PDF Library](#step-2-install-the-pdf-library)
4.  [Step 3: Define the Route](#step-3-define-the-route)
5.  [Step 4: Create the Controller Logic](#step-4-create-the-controller-logic)
6.  [Step 5: Design the PDF Template](#step-5-design-the-pdf-template)
7.  [Step 6: Test Your Application](#step-6-test-your-application)
8.  [Troubleshooting](#troubleshooting)

---

### Prerequisites

Before you begin, ensure you have the following installed on your local machine:
*   **PHP** (version 8.0 or higher)
*   **Composer**
*   **The Laravel Installer**

### Step 1: Create a New Laravel Project

First, create a fresh Laravel application. Open your terminal and run the following commands:

# Create a new project named "laravel-pdf-tutorial"
```laravel new laravel-pdf-tutorial ```

# Navigate into the project directory
``` 
cd laravel-pdf-tutorial 
```

### Step 2: Install the PDF Library
Install the barryvdh/laravel-dompdf package using Composer. This package is a widely-used wrapper that makes the DomPDF library easy to use in Laravel.

In your terminal, run:
```
composer require barryvdh/laravel-dompdf 
```

Note: Modern Laravel versions use Package Auto-Discovery, so the package should be ready to use immediately without any manual setup in config/app.php.

### Step 3: Define the Route
Define a URL that will trigger the PDF generation. This route will point to a method in our controller.

Open the routes file at routes/web.php and add the following:

```
php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\PDFController; // 👈 Import the controller

Route::get('/', function () {
    return view('welcome');
});

// 👇 Define the new route for PDF generation
Route::get('/generate-pdf', [PDFController::class, 'generatePDF']); 
```

### Step 4: Create the Controller Logic
The controller will handle the business logic: preparing data and telling DomPDF to generate the document.

4.1. Generate the Controller File
Use the following Artisan command to create a new controller:

```
php artisan make:controller PDFController
```

4.2. Add the Generation Logic
Open the new file at app/Http/Controllers/PDFController.php and add the generatePDF method.
```
php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Barryvdh\DomPDF\Facade\Pdf; // 👈 Import the PDF facade

class PDFController extends Controller
{
    public function generatePDF()
    {
        // Data to be passed to the view
        $data = [
            'title' => 'Invoice #1234',
            'date' => date('F j, Y'),
            'content' => 'This is a sample invoice generated with Laravel and DomPDF.',
            'items' => [
                ['name' => 'Product A', 'quantity' => 2, 'price' => 50.00],
                ['name' => 'Product B', 'quantity' => 1, 'price' => 120.50],
                ['name' => 'Service C', 'quantity' => 5, 'price' => 15.00],
            ]
        ];

        // Load the view and pass the data
        $pdf = Pdf::loadView('myPDF', $data);

        // Download the PDF with a custom filename
        return $pdf->download('invoice-1234.pdf');
    }
}
```

### Step 5: Design the PDF Template
This Blade view is the HTML and CSS template for your PDF.

5.1. Create the View File
Create a new file at resources/views/myPDF.blade.php.

5.2. Add the HTML and CSS
Paste the following code into your myPDF.blade.php file. You can style this just like a regular web page.
```
html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>{{ $title }}</title>
    <style>
        body { font-family: 'Helvetica', sans-serif; font-size: 14px; }
        .container { max-width: 800px; margin: auto; }
        .header { text-align: center; margin-bottom: 20px; }
        .date { text-align: right; color: #555; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background-color: #f2f2f2; }
    </style>
</head>
<body>
    <div class="container">
        <h1>{{ $title }}</h1>
        <p class="date">Date: {{ $date }}</p>
        <hr>
        <p>{{ $content }}</p>
        <table>
            <thead>
                <tr>
                    <th>Item</th>
                    <th>Quantity</th>
                    <th>Price</th>
                    <th>Total</th>
                </tr>
            </thead>
            <tbody>
                @foreach($items as $item)
                <tr>
                    <td>{{ $item['name'] }}</td>
                    <td>{{ $item['quantity'] }}</td>
                    <td>${{ number_format($item['price'], 2) }}</td>
                    <td>${{ number_format($item['quantity'] * $item['price'], 2) }}</td>
                </tr>
                @endforeach
            </tbody>
        </table>
    </div>
</body>
</html>
```

### Step 6: Test Your Application
✅ Everything is now in place!

Start the Laravel development server from your terminal:

```
php artisan serve
```
Open your web browser and navigate to the following URL:
http://127.0.0.1:8000/generate-pdf

Your browser should immediately download invoice-1234.pdf. Open it to see your final document!

Troubleshooting
If you run into an error, check these common issues.

Error: Target class [PDFController] does not exist.
Cause: The routes file can't find your controller class.
Solution: Make sure you've added use App\Http\Controllers\PDFController; at the top of your routes/web.php file.
Error: Class "Barryvdh\DomPDF\Facade\Pdf" not found
Cause: Laravel's package autoloader cache is out of date and hasn't registered the Pdf alias.
Solution 1: Rebuild Composer's autoloader cache. This is fast and usually effective.

```composer dump-autoload```
Solution 2: Clear all of Laravel's cached configurations for a fresh start.

```php artisan optimize:clear```