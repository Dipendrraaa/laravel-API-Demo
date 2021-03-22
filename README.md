# This is Demo app for Laravel API
Follow the given steps to setup login API in your mobile application using laravel.
## Step 1 
Install Laravel Sanctum.
go to your project directory and open command prompt or terminal and type following code.
~~~~
composer require laravel/sanctum
~~~~
## Step 2
publish config and migration files 
~~~~
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
~~~~
## Step 3 
Run Database migration. if you have alredy created teh
````
php artisan migrate
````
## Step 4 
Add the Sanctum's middleware.
