## Laravel Auth Routes with Localization Minimal

- Get started quickly with Laravel Auth Routes with Localization.
- You can clone the repository or follow the How To's below.

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

## SECOND STEP - create MIDDLEWARE

php artisan make:middleware SetLocale

### Inside app/Http/Middleware/SetLocale.php

- add function that will extract the first segment in the URL ie. en, sa 

public function handle(Request $request, Closure $next)
{
	app()->setlocale($request->segment(1)); <- add this, segment is the first part of the URL
	
	return $next($request);
}

### Add the class in the kernel.php under $routeMiddleware

'setlocale' => \App\Http\Middleware\SetLocale::class,

### Assign the class in the Route::group

- ['locale' => '[a-zA-Z]{2}'] <- this is a condition that the URL will accept onlt 2 characters either small or capital letter

Route::group([
    'prefix' => '{locale}',
    'where' => ['locale' => '[a-zA-Z]{2}'],
    'middleware' => 'setlocale', <- add this line
], function(){

    Route::get('/', function () {
        return view('welcome');
    });
    
    Auth::routes();
    
    Route::get('/home', [App\Http\Controllers\HomeController::class, 'index'])->name('home');
    
});

### Set automatic update from home to locale from outside the group

- when the browse to root folder it will force to redirect the link URL that locale is set

Route::get('/', function(){
    return redirect(app()->getlocale());
});

### Change the values on the welcome.blade.php

- change the values of URL and ROUTES

{{ url('/home') }} -> {{ url(app()->getLocale() . '/home') }}
{{ route('login') }} -> {{ route('login', app()->getLocale()) }}
{{ route('register') }} -> {{ route('register', app()->getLocale()) }}

### Change the values on the resources/views/layouts/app.blade.php

{{ route('login') }} -> {{ route('login', app()->getLocale()) }}
{{ route('register') }} -> {{ route('register', app()->getLocale()) }}
{{ route('logout') }} -> {{ route('logout', app()->getLocale()) }} 
{{ route('logout') }} -> {{ route('logout', app()->getLocale()) }}

### Change the values on the login.blade.php

{{ route('login') }} -> {{ route('login', app()->getLocale()) }}
{{ route('password.request') }} -> {{ route('password.request', app()->getLocale()) }}

### Change the values on the register.blade.php

{{ route('register') }} -> {{ route('register', app()->getLocale()) }}

### Change the values on the verify.blade.php

{{ route('verification.resend') }} -> {{ route('verification.resend', app()->getLocale()) }}

### Change the values on the reset.blade.php

{{ route('password.update') }} -> {{ route('password.update', app()->getLocale()) }}

### Change the values on the email.blade.php

{{ route('password.email') }} -> {{ route('password.email', app()->getLocale()) }}

### Change the values on the confirm.blade.php

{{ route('password.confirm') }} -> {{ route('password.confirm', app()->getLocale()) }}
{{ route('password.request') }} -> {{ route('password.request', app()->getLocale()) }}

## THIRD STEP - change the default routes function

- Open the following files and add the function at the bottom part

### Overwrite function on app/Htpp/Controllers/Auth/RegisterController.php

public function redirectTo()
{
	return app()->getLocale() . '/home';
}

### Overwrite function on app/Htpp/Controllers/Auth/LoginController.php

public function redirectTo()
{
	return app()->getLocale() . '/home';
}

## FOURTH STEP - implement the languages

- Insert LI on the app.blade.php

<!-- Right Side Of Navbar -->
<ul class="navbar-nav ml-auto">
	<li class="nav-item">
		<a class="nav-link" href="{{ route(\Illuminate\Support\Facades\Route::currentRouteName(), 'en') }}">EN</a>
	</li>
	<li class="nav-item"> 
		<a class="nav-link" href="{{ route(\Illuminate\Support\Facades\Route::currentRouteName(), 'sa') }}">SA</a>
	</li>
...

## Change config/app.php

- At the bottom of the locale, add the other languages

'locale' => 'en',

'available_locales' => [
	'en',
	'sa'
],

### Update LI, add @foreach loop
<!-- Right Side Of Navbar -->
<ul class="navbar-nav ml-auto">
	@foreach (config('app.available_locales') as $locale)
	<li class="nav-item">
		<a class="nav-link" href="{{ route(\Illuminate\Support\Facades\Route::currentRouteName(), $locale) }}">{{ strtoupper($locale) }}</a>
	</li>
	@endforeach
...

### Make the current language active
@foreach (config('app.available_locales') as $locale)
<li class="nav-item">
	<a class="nav-link"
	@if(app()->getLocale() == $locale) style="text-decoration: underline" @endif <- add this
	href="{{ route(\Illuminate\Support\Facades\Route::currentRouteName(), $locale) }}">{{ strtoupper($locale) }}</a>
</li>
@endforeach

## FIFTH STEP - Translations

- create json file
- resources/lang/sa.json

{
    "Login" : "تسجيل الدخول",
    "Password" : "كلمة المرور",
    "E-Mail Address" : "عنوان بريد الكتروني",
    "Register" : "يسجل",
    "Remember Me" : "تذكرنى"
}

### If there are routes that are not working, try to clear using the commands below
php artisan optimize
php artisan clear-compiled
php artisan cache:clear
php artisan route:clear
php artisan view:clear
php artisan config:clear



