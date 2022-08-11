<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400"></a></p>

# Excel Import using Laravel 9 Test project

This is a basic Laravel 9 project created to test Import data from an Excel file using "[Laravel-Excel](https://laravel-excel.com/)" composer plugin by [Maatwebsite](https://github.com/SpartnerNL/Laravel-Excel).

## Logs
---
### Step 1. Install the "maatwebsite/excel" composer package

Ran below command to install "Laravel-Excel" plugin.
```
composer require maatwebsite/excel
```
Got the below errors
```
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - maatwebsite/excel[3.1.36, ..., 3.1.x-dev] require phpoffice/phpspreadsheet ^1.18 -> satisfiable by phpoffice/phpspreadsheet[1.18.0, ..., 1.24.1].
    - maatwebsite/excel[3.1.0, ..., 3.1.25] require php ^7.0 -> your php version (8.1.0) does not satisfy that requirement.
    - maatwebsite/excel[3.1.26, ..., 3.1.35] require illuminate/support 5.8.*|^6.0|^7.0|^8.0 -> found illuminate/support[v5.8.0, ..., 5.8.x-dev, v6.0.0, ..., 6.x-dev, v7.0.0, ..., 7.x-dev, v8.0.0, ..., 8.x-dev] but these were not loaded, likely because it conflicts with another require. 
    - phpoffice/phpspreadsheet[1.18.0, ..., 1.22.0] require psr/simple-cache ^1.0 -> found psr/simple-cache[1.0.0, 1.0.1] but the package is fixed to 3.0.0 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.
    - phpoffice/phpspreadsheet[1.23.0, ..., 1.24.1] require psr/simple-cache ^1.0 || ^2.0 -> found psr/simple-cache[1.0.0, 1.0.1, 2.0.0, 2.x-dev] but the package is fixed to 3.0.0 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.
    - Root composer.json requires maatwebsite/excel ^3.1 -> satisfiable by maatwebsite/excel[3.1.0, ..., 3.1.x-dev].

Use the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.      
You can also try re-running composer require with an explicit version constraint, e.g. "composer require maatwebsite/excel:*" to figure out if any version is installable, or "composer require maatwebsite/excel:^2.1" if you know which you need.

Installation failed, reverting ./composer.json and ./composer.lock to their original content.
```

Then modified the command as below and ran again.
```
composer require maatwebsite/excel --with-all-dependencies
```
**Maatwebsite/excel** plugin installed with the below messages
```Generating optimized autoload files
> Illuminate\Foundation\ComposerScripts::postAutoloadDump
> @php artisan package:discover --ansi

   INFO  Discovering packages.

  fruitcake/laravel-cors ................................................................................... DONE
  laravel/sail ............................................................................................. DONE  
  laravel/sanctum .......................................................................................... DONE  
  laravel/tinker ........................................................................................... DONE  
  maatwebsite/excel ........................................................................................ DONE  
  nesbot/carbon ............................................................................................ DONE  
  nunomaduro/collision ..................................................................................... DONE  
  nunomaduro/termwind ...................................................................................... DONE  
  spatie/laravel-ignition .................................................................................. DONE  

83 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
> @php artisan vendor:publish --tag=laravel-assets --ansi --force

   INFO  No publishable resources for tag [laravel-assets].
```
### Step 2. Register Plugin’s Service in Providers & Aliases

Placed following code inside the **config/app.php** file.
```
'providers' => [
  .......
  .......
  .......
  Maatwebsite\Excel\ExcelServiceProvider::class,
 
 ],  
'aliases' => [ 
  .......
  .......
  .......
  'Excel' => Maatwebsite\Excel\Facades\Excel::class,
], 
```
Executed below command, and generated a new config file as **config/excel.php**.
```
php artisan vendor:publish --provider="Maatwebsite\Excel\ExcelServiceProvider"
```

### Step 2**. Generate Fake Records, Migrate Table

```
php artisan migrate
```
Once the migration is completed, then execute the command to generate the fake records.
```
php artisan tinker
User::factory()->count(5)->create();
exit
```

### Step 3. Construct Route

Define 3 routes in **routes/web.php** that handle the import and export for Excel and CSV files.
```bash
<?php
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\UserController;
/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/
Route::get('file-import-export', [UserController::class, 'fileImportExport']);
Route::post('file-import', [UserController::class, 'fileImport'])->name('file-import');
Route::get('file-export', [UserController::class, 'fileExport'])->name('file-export');
```

### Step 4. Make Import Class

Ran the below command. 
```bash
php artisan make:import UsersImport --model=User
```
This created **app/Imports/UsersImport.php** file which is called inside the **UserController**. This **UserImport.php** file handles the excel import functionality.

### Step 5. Make Export Class

Ran the below command. 
```bash
php artisan make:export UsersExport --model=User
```
This created **app/Exports/UsersExport.php** file which is called inside the **UserController**. This **UsersExport.php** file handles the excel import functionality.

### Step 6. Make Controller

Ran the below command to create a controller to define methods to handle import/export functions. 
```bash
php artisan make:controller UserController
```
Added functions to handle import/export in the UserController for User Table.

### Step 7. Make a Blade View for Importing/Exporting Excel file

Created a **resources/views/file-import.blade.php** file to set up the view. Placed the following code inside the blade view file:
```
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Import Export Excel & CSV to Database in Laravel 7</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css">
</head>
<body>
    <div class="container mt-5 text-center">
        <h2 class="mb-4">
            Laravel 7 Import and Export CSV & Excel to Database Example
        </h2>
        <form action="{{ route('file-import') }}" method="POST" enctype="multipart/form-data">
            @csrf
            <div class="form-group mb-4" style="max-width: 500px; margin: 0 auto;">
                <div class="custom-file text-left">
                    <input type="file" name="file" class="custom-file-input" id="customFile">
                    <label class="custom-file-label" for="customFile">Choose file</label>
                </div>
            </div>
            <button class="btn btn-primary">Import data</button>
            <a class="btn btn-success" href="{{ route('file-export') }}">Export data</a>
        </form>
    </div>
</body>
</html>
```
