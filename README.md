## Laravel Auth Routes with Localization Minimal

Get started quickly with Laravel Auth Routes with Localization.

## Setup

git clone https://github.com/GenesisLista/laravel_auth_multi_language.git

## Copy .env

cp .env.example env

## Generate App Key

php artisan key:generate

## Start server

php artisan serve

## Laravel Auth Routes with localization

- This a Laravel 8 scaffolding for auth routes with localization
- It can be used for multiple languages
- The project was used English and Arabic Language
- Goal is to have a prefix en/sa on the URL
- http://localhost/sgcerp/public/en or http://localhost/sgcerp/public/sa

## Create a laravel 8 project

composer create-project --prefer-dist laravel/laravel projectname

## Change directory to the created folder

cd projectname/

## Install Laravel UI Package

composer require laravel/ui

## Run NPM

npm install && npm run dev
- On the first run there will be issue, run the command again to resolve
- The above command will generate the auth routes, a HomeController, auth views, and a app.blade.php layout file

## Create database and edit .env file

DB_DATABASE=localhost
DB_USERNAME=root
DB_PASSWORD=

## Run migrate

php artisan migrate

## FIRST STEP - create prefix LOCALE

### Change the routes/web.php

Route::get('/', function () {
    return view('welcome');
});

Auth::routes();

Route::get('/home', [App\Http\Controllers\HomeController::class, 'index'])->name('home');

to

Route::group([
    'prefix' => '{locale}',
    'where' => ['locale' => '[a-zA-Z]{2}']
], function(){

    Route::get('/', function () {
        return view('welcome');
    });
    
    Auth::routes();
    
    Route::get('/home', [App\Http\Controllers\HomeController::class, 'index'])->name('home');
    
});




