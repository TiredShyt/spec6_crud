### 1. Install Laravel Framework app
```bash 
D:
cd \
md laravel 
cd laravel
composer create-project laravel/laravel laravel_crud_BSOLON1
cd laravel_crud_BSOLON1
code . 
```

### 2. Create and Configure Database Credentials
### Create Database Open workbench then run this command 
```bash
create database laravel_crud_BSOLON1;
```



### Find .env file then update accordingly
```bash
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_crud_BSOLON1
DB_USERNAME=root
DB_PASSWORD=root
DB_COLLATION=utf8mb4_unicode_ci
```


### 3. Create a Model with Migration, Resource Controller and Requests for Validation

```bash 
php artisan make:model Product -mcr --requests
```

### 4. Update Product Migration

### find this file 
```bash
database\migrations\YYYY_MM_DD_TIMESTAMP_create_products_table.php
```
then update accordingly

```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;


return new class extends Migration
{
#Run the migrations.

public function up(): void {
    Schema::create('products', function (Blueprint $table) {
        $table->id();
        $table->string('code')->unique();
        $table->string('name');
        $table->integer('quantity');
        $table->decimal('price', 8, 2);
        $table->text('description')->nullable();
        $table->timestamps();
    });
}
/**
* Reverse the migrations.
*/
public function down(): void
{
    Schema::dropIfExists('products');
}
};
```


### 5. Migrate Tables to Database

```bash
php artisan migrate
```

### 6. Define product resource routes

### find this file 

```bash
routes/web.php
```
```php
<?php
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ProductController;

Route::get('/', function () { return
    view('welcome');
});

Route::resource('products', ProductController::class);
```


### check routes by 
```bash
php artisan route:list
```

### 7. Update Code in Product Model 

### find this file 

```bash
app\Models\Product.php
```
```php 
<?php
namespace App\Models;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
    use HasFactory;
    protected $fillable = [ 'code',
    'name',
    'quantity',
    'price',
    'description'
    ];
}
```
### 8. Update Code in Product Controller
```bash
app\Http\Controllers\ProductController.php
```
```php
<?php
namespace App\Http\Controllers;
use App\Models\Product;
use App\Http\Requests\StoreProductRequest;
use App\Http\Requests\UpdateProductRequest;
use Illuminate\View\View;
use Illuminate\Http\RedirectResponse;


class ProductController extends Controller
{

/* Display a listing of the resource. */
public function index() : View
{
    return view('products.index', [
    'products' => Product::latest()->paginate(4)
    ]);
}

/* Show the form for creating a new resource. */
public function create() : View
{
    return view('products.create');
}


/* Store a newly created resource in storage. */
public function store(StoreProductRequest $request) : RedirectResponse
{
Product::create($request->validated());
    return redirect()->route('products.index')->withSuccess('New product is added successfully.');
}

/* Display the specified resource. */
public function show(Product $product) : View
{
    return view('products.show', compact('product'));
}

/* Show the form for editing the specified resource. */
public function edit(Product $product) : View
{
    return view('products.edit', compact('product'));
}

/* Update the specified resource in storage. */
public function update(UpdateProductRequest $request, Product $product) : RedirectResponse
{
    $product->update($request->validated());
    return redirect()->back()->withSuccess('Product is updated successfully.');
}

/* Remove the specified resource from storage. */
public function destroy(Product $product) : RedirectResponse
{
$product->delete();
    return redirect()->route('products.index')->withSuccess('Product is deleted successfully.');
}
}
```


### 9. Update Code in Product Store and Update Requests

```bash
app\Http\Requests\StoreProductRequest.php
```

```php
<?php
namespace App\Http\Requests;
use Illuminate\Foundation\Http\FormRequest;
class StoreProductRequest extends FormRequest
{
/* Determine if the user is authorized to make this request. */
public function authorize(): bool
{
    return true;
}

/* Get the validation rules that apply to the request.  
@return array<string,\Illuminate\Contracts\Validation\ValidationRule|array<mixed>|string>
*/
public function rules(): array
{
return [
        'code' => 'required|string|max:50|unique:products,code', 'name' =>
        'required|string|max:250',
        'quantity' => 'required|integer|min:1|max:10000', 'price' =>
        'required',
        'description' => 'nullable|string'
        ];
    }
}
```

```bash
app\Http\Requests\UpdateProductRequest.php
```
```php
<?php
namespace App\Http\Requests;
use Illuminate\Foundation\Http\FormRequest;
class UpdateProductRequest extends FormRequest
{
/*
* Determine if the user is authorized to make this request.
*/
public function authorize(): bool
{
return true;
}

/* Get the validation rules that apply to the request. 
@return array<string, \Illuminate\Contracts\Validation\ValidationRule|array<mixed>|string> */
public function rules(): array
{
return [
    'code' => 'required|string|max:50|unique:products,code,'.$this->product->id,
    'name' => 'required|string|max:250',
    'quantity' => 'required|integer|min:1|max:10000', 'price' =>
    'required',
    'description' => 'nullable|string'

    ];
}
}
```
### 10. Enable Bootstrap 5 in AppServiceProvider

```bash
App\Providers\AppServiceProviders.php
```
```php
<?php
namespace App\Providers;
use Illuminate\Support\ServiceProvider;
use Illuminate\Pagination\Paginator;
class AppServiceProvider extends ServiceProvider
{
/* Register any application services. */
public function register(): void
{

}
/* Bootstrap any application services. */
public function boot(): void
{
    Paginator::useBootstrapFive();
}
}
```

### 11. Create Layout and Product Resource Blade View Files

```bash
php artisan make:view layouts.app
php artisan make:view products.index
php artisan make:view products.create
php artisan make:view products.edit
php artisan make:view products.show
```

```bash
resources/views/layouts/app.blade.php
```
```php
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial- scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<title>Simple Laravel 11 CRUD Application Tutorial</title>

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css">
</head>

<body>
    <div class="container">
    <h3 class=" mt-3">Simple Laravel CRUD Application Tutorial</h3>
    @yield('content')
    <div class="row justify-content-center text-center mt-3">
    <div class="col-md-12">
    <p>
    Return to Website: <a href="https://www.usjr.edu.ph/"><strong>University of San Jose - Recoletos</strong></a>
    </p>
    </div>
    </div>
    </div>
</body>
</html>
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
```

```bash
resources/views/products/create.blade.php
```
```php
@extends('layouts.app')
@section('content')
<div class="row justify-content-center mt-3">
    <div class="col-md-8">
        <div class="card">
            <div class="card-header">
                <div class="float-start"> Add New Product </div>
                <div class="float-end">
                    <a href="{{ route('products.index') }}" class="btn btn-primary btn-sm">&larr; Back</a>
                </div>
            </div>
            <div class="card-body">
                <form action="{{ route('products.store') }}" method="post">
                    @csrf

                    <div class="mb-3 row">
                        <label for="code" class="col-md-4 col-form-label text-md-end text-start">Code</label>
                        <div class="col-md-6">
                            <input type="text" class="form-control @error('code') is-invalid @enderror" id="code" name="code" value="{{ old('code') }}">
                            @error('code')
                                <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                    </div>

                    <div class="mb-3 row">
                        <label for="name" class="col-md-4 col-form-label text-md-end text-start">Name</label>
                        <div class="col-md-6">
                            <input type="text" class="form-control @error('name') is-invalid @enderror" id="name" name="name" value="{{ old('name') }}">
                            @error('name')
                                <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                    </div>

                    <div class="mb-3 row">
                        <label for="quantity" class="col-md-4 col-form-label text-md-end text-start">Quantity</label>
                        <div class="col-md-6">
                            <input type="number" class="form-control @error('quantity') is-invalid @enderror" id="quantity" name="quantity" value="{{ old('quantity') }}">
                            @error('quantity')
                                <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                    </div>

                    <div class="mb-3 row">
                        <label for="price" class="col-md-4 col-form-label text-md-end text-start">Price</label>
                        <div class="col-md-6">
                            <input type="number" step="0.01" class="form-control @error('price') is-invalid @enderror" id="price" name="price" value="{{ old('price') }}">
                            @error('price')
                                <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                    </div>

                    <div class="mb-3 row">
                        <label for="description" class="col-md-4 col-form-label text-md-end text-start">Description</label>
                        <div class="col-md-6">
                            <textarea class="form-control @error('description') is-invalid @enderror" id="description" name="description">{{ old('description') }}</textarea>
                            @error('description')
                                <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                    </div>

                    <div class="mb-3 row">
                        <input type="submit" class="col-md-3 offset-md-5 btn btn-primary" value="Add Product">
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
@endsection

```

```bash
resources/views/products/edit.blade.php
```
```php
@extends('layouts.app')
@section('content')
<div class="row justify-content-center mt-3">
    <div class="col-md-8">

        @if (session('success'))
            <div class="alert alert-success" role="alert">
                {{ session('success') }}
            </div>
        @endif

        <div class="card">
            <div class="card-header">
                <div class="float-start">Edit Product</div>
                <div class="float-end">
                    <a href="{{ route('products.index') }}" class="btn btn-primary btn-sm">&larr; Back</a>
                </div>
            </div>
            <div class="card-body">
                <form action="{{ route('products.update', $product->id) }}" method="post">
                    @csrf
                    @method("PUT")

                    <div class="mb-3 row">
                        <label for="code" class="col-md-4 col-form-label text-md-end text-start">Code</label>
                        <div class="col-md-6">
                            <input type="text" class="form-control @error('code') is-invalid @enderror" id="code" name="code" value="{{ $product->code }}">
                            @error('code')
                                <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                    </div>

                    <div class="mb-3 row">
                        <label for="name" class="col-md-4 col-form-label text-md-end text-start">Name</label>
                        <div class="col-md-6">
                            <input type="text" class="form-control @error('name') is-invalid @enderror" id="name" name="name" value="{{ $product->name }}">
                            @error('name')
                                <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                    </div>

                    <div class="mb-3 row">
                        <label for="quantity" class="col-md-4 col-form-label text-md-end text-start">Quantity</label>
                        <div class="col-md-6">
                            <input type="number" class="form-control @error('quantity') is-invalid @enderror" id="quantity" name="quantity" value="{{ $product->quantity }}">
                            @error('quantity')
                                <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                    </div>

                    <div class="mb-3 row">
                        <label for="price" class="col-md-4 col-form-label text-md-end text-start">Price</label>
                        <div class="col-md-6">
                            <input type="number" step="0.01" class="form-control @error('price') is-invalid @enderror" id="price" name="price" value="{{ $product->price }}">
                            @error('price')
                                <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                    </div>

                    <div class="mb-3 row">
                        <label for="description" class="col-md-4 col-form-label text-md-end text-start">Description</label>
                        <div class="col-md-6">
                            <textarea class="form-control @error('description') is-invalid @enderror" id="description" name="description">{{ $product->description }}</textarea>
                            @error('description')
                                <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                    </div>

                    <div class="mb-3 row">
                        <input type="submit" class="col-md-3 offset-md-5 btn btn-primary" value="Update Product">
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
@endsection

```


```bash
resources/views/products/index.blade.php
```
```php
@extends('layouts.app')
@section('content')
<div class="row justify-content-center mt-3">
    <div class="col-md-12">

        @if (session('success'))
            <div class="alert alert-success" role="alert"> 
                {{ session('success') }} 
            </div>
        @endif

        <div class="card">
            <div class="card-header">Product List</div>
            <div class="card-body">
                <a href="{{ route('products.create') }}" class="btn btn-success btn-sm my-2">
                    <i class="bi bi-plus-circle"></i> Add New Product
                </a>

                <table class="table table-striped table-bordered">
                    <thead>
                        <tr>
                            <th scope="col">S#</th>
                            <th scope="col">Code</th>
                            <th scope="col">Name</th>
                            <th scope="col">Quantity</th>
                            <th scope="col">Price</th>
                            <th scope="col">Action</th>
                        </tr>
                    </thead>
                    
                    <tbody>
                        @forelse ($products as $product)
                        <tr>
                            <th scope="row">{{ $loop->iteration }}</th>
                            <td>{{ $product->code }}</td>
                            <td>{{ $product->name }}</td>
                            <td>{{ $product->quantity }}</td>
                            <td>{{ $product->price }}</td>
                            <td>
                                <form action="{{ route('products.destroy',$product->id) }}" method="post">
                                    @csrf
                                    @method('DELETE')
                                    <a href="{{ route('products.show',$product->id) }}" class="btn btn-warning btn-sm">
                                        <i class="bi bi-eye"></i> Show
                                    </a>
                                    <a href="{{ route('products.edit',$product->id) }}" class="btn btn-primary btn-sm">
                                        <i class="bi bi-pencil-square"></i> Edit
                                    </a>
                                    <button type="submit" class="btn btn-danger btn-sm" 
                                        onclick="return confirm('Do you want to delete this product?');">
                                        <i class="bi bi-trash"></i> Delete
                                    </button>
                                </form>
                            </td>
                        </tr>
                        @empty
                        <tr>
                            <td colspan="6">
                                <span class="text-danger">
                                    <strong>No Product Found!</strong>
                                </span>
                            </td>
                        </tr>
                        @endforelse
                    </tbody>
                </table>
                {{ $products->links() }}
            </div>
        </div>
    </div>
</div>
@endsection

```


```bash
resources/views/products/show.blade.php
```
```php
@extends('layouts.app')
@section('content')
<div class="row justify-content-center mt-3">
    <div class="col-md-8">
        <div class="card">
            <div class="card-header">
                <div class="float-start">Product Information</div>
                <div class="float-end">
                    <a href="{{ route('products.index') }}" class="btn btn-primary btn-sm">&larr; Back</a>
                </div>
            </div>
            <div class="card-body">

                <div class="row mb-3">
                    <label for="code" class="col-md-4 col-form-label text-md-end text-start"><strong>Code:</strong></label>
                    <div class="col-md-6" style="line-height:35px;">
                        {{ $product->code }}
                    </div>
                </div>

                <div class="row mb-3">
                    <label for="name" class="col-md-4 col-form-label text-md-end text-start"><strong>Name:</strong></label>
                    <div class="col-md-6" style="line-height:35px;">
                        {{ $product->name }}
                    </div>
                </div>

                <div class="row mb-3">
                    <label for="quantity" class="col-md-4 col-form-label text-md-end text-start"><strong>Quantity:</strong></label>
                    <div class="col-md-6" style="line-height:35px;">
                        {{ $product->quantity }}
                    </div>
                </div>

                <div class="row mb-3">
                    <label for="price" class="col-md-4 col-form-label text-md-end text-start"><strong>Price:</strong></label>
                    <div class="col-md-6" style="line-height:35px;">
                        {{ $product->price }}
                    </div>
                </div>

                <div class="row mb-3">
                    <label for="description" class="col-md-4 col-form-label text-md-end text-start"><strong>Description:</strong></label>
                    <div class="col-md-6" style="line-height:35px;">
                        {{ $product->description }}
                    </div>
                </div>

            </div>
        </div>
    </div>
</div>
@endsection

```