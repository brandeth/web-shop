### **1. Installing Laravel Application**

Reference: [Laravel Installation Documentation](https://laravel.com/docs/11.x/installation)

#### **Steps:**

1. **Install Laravel using Composer:**

   ```bash
   composer create-project laravel/laravel web-shop
   ```

   - Creates a new Laravel project named `web-shop`. Composer handles all dependencies automatically by downloading necessary packages.

2. **Navigate to the project directory:**

   ```bash
   cd web-shop
   ```

   - Changes the current working directory to the newly created Laravel project.

3. **Start the development server:**

   ```bash
   php artisan serve
   ```

   - Starts the built-in PHP development server at `http://localhost:8000` for testing and development.

4. **Verify installation:**

   - Open your browser and visit `http://localhost:8000`.
   - You should see the default Laravel welcome page, confirming successful installation.

---

### **2. Installing Jetstream with Livewire**

Reference: [Jetstream Installation Documentation](https://jetstream.laravel.com/installation.html#livewire)

#### **Steps:**

1. **Install Jetstream via Composer:**

   ```bash
   composer require laravel/jetstream
   ```

   - Installs the Jetstream package, which provides scaffolding for features like authentication and user profile management.

2. **Install the Livewire stack:**

   ```bash
   php artisan jetstream:install livewire
   ```

   - Configures Jetstream to use Livewire, enabling reactive front-end components.

3. **Run the migrations:**

   ```bash
   php artisan migrate
   ```

   - Creates necessary database tables by applying the migration files.

4. **Install frontend dependencies:**

   ```bash
   npm install && npm run dev
   ```

   - Installs Node.js packages and compiles front-end assets using Laravel Mix.

5. **Verify the setup:**

   - Visit `http://localhost:8000`.
   - You should see the Laravel Jetstream login and registration pages.

---

### **3. Creating Models with Artisan**

Reference: [Laravel Models Documentation](https://laravel.com/docs/11.x/eloquent#defining-models)

#### **Steps:**

1. **Create models with migrations and factories using Artisan:**

   ```bash
   php artisan make:model Product -m -f
   php artisan make:model ProductVariant -m -f
   php artisan make:model Cart -m -f
   php artisan make:model CartItem -m -f
   php artisan make:model Image -m -f
   ```

   - Generates models along with migration and factory files for each entity.

2. **Define the table columns in the migration files:**

   - **products table:**
     ```php
     Schema::create('products', function (Blueprint $table) {
         $table->id();  // Primary key
         $table->string('name');  // Name of the product
         $table->decimal('price', 8, 2);  // Price of the product with two decimal points for currency precision
         $table->text('description')->nullable();  // Optional product description
         $table->timestamps();  // Automatically manages created_at and updated_at timestamps
     });
     ```

   - **product_variants table:**
     ```php
     Schema::create('product_variants', function (Blueprint $table) {
         $table->id();
         $table->foreignId('product_id')->constrained()->cascadeOnDelete();  // Links to the parent product and deletes variants if the product is deleted
         $table->string('color');  // Product color variant
         $table->string('size');  // Product size variant
         $table->timestamps();
     });
     ```

   - **images table:**
     ```php
     Schema::create('images', function (Blueprint $table) {
         $table->id();
         $table->foreignId('product_id')->constrained()->cascadeOnDelete();  // Associates the image with a specific product
         $table->string('path');  // Path to the image file
         $table->boolean('featured')->default(false);  // Indicates if the image is a featured product image
         $table->timestamps();
     });
     ```

---

### **4. Define the model factories:**

   - Factories generate fake data for seeding the database.

   Example for `ProductFactory`:
   ```php
   namespace Database\Factories;

   use App\Models\Product;
   use Illuminate\Database\Eloquent\Factories\Factory;

   class ProductFactory extends Factory
   {
       protected $model = Product::class;

       public function definition()
       {
           return [
               'name' => fake()->word(),  // Generates a random product name
               'price' => fake()->randomFloat(2, 10, 1000),  // Random price between 10 and 1000 with two decimal places
               'description' => fake()->paragraph(),  // Random product description
           ];
       }
   }
   ```

   Example for `ProductVariantFactory`:
   ```php
   namespace Database\Factories;

   use App\Models\ProductVariant;
   use Illuminate\Database\Eloquent\Factories\Factory;

   class ProductVariantFactory extends Factory
   {
       protected $model = ProductVariant::class;

       public function definition()
       {
           return [
               'product_id' => Product::factory(),  // Automatically associates a product variant with a product
               'color' => fake()->safeColorName(),  // Random safe color name
               'size' => fake()->randomElement(['Small', 'Medium', 'Large']),  // Random size selection
           ];
       }
   }
   ```

   Example for `ImageFactory`:
   ```php
   namespace Database\Factories;

   use App\Models\Image;
   use Illuminate\Database\Eloquent\Factories\Factory;

   class ImageFactory extends Factory
   {
       protected $model = Image::class;

       public function definition()
       {
           return [
               'product_id' => Product::factory(),  // Automatically associates the image with a product
               'path' => fake()->imageUrl(),  // Generates a random image URL
               'featured' => fake()->boolean(20),  // 20% chance to be a featured image
           ];
       }
   }
   ```

---

### **5. Define the relationships in models:**

   Example for `Product` model:
   ```php
   namespace App\Models;

   use Illuminate\Database\Eloquent\Factories\HasFactory;
   use Illuminate\Database\Eloquent\Model;

   class Product extends Model
   {
       use HasFactory;

       protected $fillable = ['name', 'price', 'description'];  // Mass assignable attributes

       public function images()
       {
           return $this->hasMany(Image::class);  // Defines a one-to-many relationship with the Image model
       }

       public function variants()
       {
           return $this->hasMany(ProductVariant::class);  // Defines a one-to-many relationship with the ProductVariant model
       }
   }
   ```

---

### **6. Define the seeder logic:**

   Example for `DatabaseSeeder`:
   ```php
   namespace Database\Seeders;

   use App\Models\Product;
   use App\Models\ProductVariant;
   use App\Models\Image;
   use Illuminate\Database\Seeder;

   class DatabaseSeeder extends Seeder
   {
       public function run()
       {
           // Seed products with variants and images
           Product::factory(10)->create()->each(function ($product) {
               ProductVariant::factory(3)->create(['product_id' => $product->id]);
               Image::factory(2)->create(['product_id' => $product->id]);
           });
       }
   }
   ```

---

### **7. Run the seeders:**

   ```bash
   php artisan db:seed
   ```

   - Executes the `DatabaseSeeder` class to populate the database with sample data based on the defined factories.
