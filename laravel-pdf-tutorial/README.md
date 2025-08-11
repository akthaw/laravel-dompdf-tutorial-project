How to Generate PDFs in Laravel with the DomPDF Package
This tutorial provides a step-by-step guide on how to integrate the popular barryvdh/laravel-dompdf package into a Laravel application to generate dynamic PDF files from a Blade view.

This project demonstrates a common real-world requirement for many web applications: creating invoices, reports, or other printable documents on the fly.

Prerequisites
Before you begin, ensure you have the following installed on your local machine:

PHP (version 8.0 or higher)
Composer
The Laravel Installer
Step 1: Create a New Laravel Project
First, let's create a fresh Laravel application. Open your terminal and run the following commands:

bash
# Create a new project named "laravel-pdf-tutorial"
laravel new laravel-pdf-tutorial

# Navigate into the project directory
cd laravel-pdf-tutorial
Step 2: Install the PDF Library
We will use Composer to install the barryvdh/laravel-dompdf package. This is a widely-used wrapper for the DomPDF library that integrates seamlessly with Laravel.

In your terminal, run:

bash
composer require barryvdh/laravel-dompdf
Modern Laravel versions use Package Auto-Discovery, so the package should be ready to use without any manual configuration.

Step 3: Create the Route
Next, we need to define a URL endpoint that users can visit to trigger the PDF generation.

Open the routes file at routes/web.php and add the following code:

php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\PDFController; // Import the controller

Route::get('/', function () {
    return view('welcome');
});

// Define the route for PDF generation
Route::get('/generate-pdf', [PDFController::class, 'generatePDF']);
Step 4: Create the Controller Logic
This controller will contain the main logic for preparing data and creating the PDF.

Generate the controller file using Artisan:

bash
php artisan make:controller PDFController
Now, open the newly created file at app/Http/Controllers/PDFController.php and add the generatePDF method:

php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Barryvdh\DomPDF\Facade\Pdf; // Import the PDF facade

class PDFController extends Controller
{
    public function generatePDF()
    {
        // 1. Prepare the data to be passed to the view
        $data = [
            'title' => 'Invoice #1234',
            'date' => date('F j, Y'),
            'content' => 'This is a sample invoice document generated dynamically with Laravel and DomPDF.',
            'items' => [
                ['name' => 'Product A', 'quantity' => 2, 'price' => 50.00],
                ['name' => 'Product B', 'quantity' => 1, 'price' => 120.50],
                ['name' => 'Service C', 'quantity' => 5, 'price' => 15.00],
            ]
        ];

        // 2. Load the Blade view and pass the data
        $pdf = Pdf::loadView('myPDF', $data);

        // 3. Optional: Set paper size and orientation
        // $pdf->setPaper('A4', 'landscape');

        // 4. Download the PDF file with a specific filename
        return $pdf->download('invoice-1234.pdf');
    }
}
Step 5: Design the PDF Template (Blade View)
This Blade file will act as the HTML template for our PDF.

Create a new view file at resources/views/myPDF.blade.php.

Add the following HTML and Blade code. You can use standard HTML and CSS to style your document.

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
        .content { margin-top: 30px; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background-color: #f2f2f2; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>{{ $title }}</h1>
        </div>
        <p class="date">Date: {{ $date }}</p>
        <div class="content">
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
    </div>
</body>
</html>
Step 6: Run and Test Your Application
Everything is now in place. Let's start the Laravel development server.

bash
php artisan serve
Open your web browser and navigate to the following URL:

http://127.0.0.1:8000/generate-pdf

Your browser should automatically download a file named invoice-1234.pdf. Open it to see your dynamically generated document!

Troubleshooting Common Errors
If you encounter issues, here are some common problems and their solutions.

Error: Target class [PDFController] does not exist.
This means the route file cannot find your controller.

Solution: Ensure you have added use App\Http\Controllers\PDFController; at the top of your routes/web.php file.
Error: Class "Barryvdh\DomPDF\Facade\Pdf" not found
This error indicates that Laravel's autoloader has not correctly registered the package's alias.

Solution: This can usually be fixed by rebuilding Composer's autoloader cache. Run the following command in your terminal:
bash
composer dump-autoload
Alternate Solution: You can also clear all of Laravel's caches, which is often a good idea after installing a new package.
bash
php artisan optimize:clear