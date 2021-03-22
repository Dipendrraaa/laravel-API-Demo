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
Run Database migration. if you have alredy created the user table drop the table and run the migration.
````
php artisan migrate
````
## Step 4 
Add the Sanctum's middleware. Go to Kernel.php inside your App\Http Folder and add 
````
namespace App\Http;
use Illuminate\Foundation\Http\Kernel as HttpKernel;
use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;
````
Again scroll down and add following code in The application's route middleware groups section inside api as shown below.
````
EnsureFrontendRequestsAreStateful::class,
````
for Eg.
````
   /**
     * The application's route middleware groups.
     *
     * @var array
     */
    protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            // \Illuminate\Session\Middleware\AuthenticateSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],

        'api' => [
            EnsureFrontendRequestsAreStateful::class,
            'throttle:api',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
    ];
````
## Step 5
To use token based authentication add folloeing code inside User model. if you donot have User model create using command prompt.
````
use Laravel\Sanctum\HasApiTokens;
````
also add HasApiTokens inside User class as shown bwllow.
````
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
.....
}
````
## Step 6
Create Usercontroller using command prompt and add the following code inside user controller. 
````
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\User;
use Illuminate\Support\Facades\Hash;

class UserController extends Controller
{
    function login(Request $request)
    {
        $user= User::where('email', $request->email)->first();
        // print_r($data);
        if (!$user || !Hash::check($request->password, $user->password)) {
            return response([
                'message' => ['These credentials do not match our records.']
            ], 404);
        }

        $token = $user->createToken('my-app-token')->plainTextToken;

        $response = [
            'user' => $user,
            'token' => $token
        ];

        return response($response, 201);
    }
}
````
to test the code we can add dummy data to the user table and to add data you can add seeder for user model ` php artisan make:seeder UsersSeeder` 
and insert the record to the user table.
go to databases\seeders folder and open the seeder you just created add add the following code'
````
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;
class UsersTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        DB::table('users')->insert([
            'name' => 'Ram Prasad',
            'email' => 'ram@gmail.com',
            'password' => Hash::make('12345678')
        ]);
        //
    }
}
````
the Above code will create new record inside users table after you execute `php artisan db:seed --class=UsersSeeder
`
## add Route to the api.php
 ````
 Route::post("login",[UserController::class,'login']);
 ````
 ````
 Route::group(['middleware' => 'auth:sanctum'], function(){
    //you can add your secure urls here.
    });
   ````
   I will teach you how to test our login API. After successful attempt we will create API for User Register module. 
   
