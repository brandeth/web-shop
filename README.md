### **1. Installing Laravel Application**

Reference: [Laravel Installation Documentation](https://laravel.com/docs/11.x/installation)

#### **Steps:**

1. **Install Laravel using Composer:**

   ```bash
   composer create-project laravel/laravel web-shop
   ```

   - Creates a new Laravel project named `web-shop`. Composer handles all dependencies automatically.

2. **Navigate to the project directory:**

   ```bash
   cd web-shop
   ```

   - Change your working directory to the newly created Laravel project.

3. **Start the development server:**

   ```bash
   php artisan serve
   ```

   - Starts the local development server at `http://localhost:8000`.

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

   - Installs Jetstream, which provides scaffolding for authentication and features like teams and API support.

2. **Install the Livewire stack:**

   ```bash
   php artisan jetstream:install livewire
   ```

   - Installs the Livewire stack for dynamic, reactive front-end interactions without needing a full SPA framework.

3. **Run the migrations:**

   ```bash
   php artisan migrate
   ```

   - Applies database changes based on migration files to set up tables and relationships.

4. **Install frontend dependencies:**

   ```bash
   npm install && npm run dev
   ```

   - Installs Node.js packages and compiles frontend assets using Laravel Mix.

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

   - Generates model, migration, and factory files for each entity.

2. **Define the table columns in the migration files:**

   - **products table:**
     ```php
     Schema::create('products', function (Blueprint $table) {
         $table->id();
         $table->string('name');  // Product name, used to identify the item.
         $table->decimal('price', 8, 2);  // Product price with two decimal places, used for pricing.
         $table->text('description')->nullable();  // Detailed description of the product.
         $table->timestamps();  // Tracks creation and update times.
     });
     ```

   - **product_variants table:**
     ```php
     Schema::create('product_variants', function (Blueprint $table) {
         $table->id();
         $table->foreignId('product_id')->constrained()->cascadeOnDelete();  // Links the variant to a specific product.
         $table->string('color')->nullable();  // Color attribute for product variations.
         $table->string('size')->nullable();  // Size attribute for product variations.
         $table->timestamps();  // Tracks creation and update times.
     });
     ```

   - **carts table:**
     ```php
     Schema::create('carts', function (Blueprint $table) {
         $table->id();
         $table->foreignId('user_id')->nullable();  // Stores the ID of the user who owns the cart, if logged in.
         $table->string('session_id')->nullable();  // Unique identifier for guest users without accounts.
         $table->timestamps();  // Tracks creation and update times.
     });
     ```

   - **cart_items table:**
     ```php
     Schema::create('cart_items', function (Blueprint $table) {
         $table->id();
         $table->foreignId('cart_id')->constrained()->cascadeOnDelete();  // Links the item to a specific cart.
         $table->foreignId('product_variant_id')->constrained()->cascadeOnDelete();  // Specifies which variant of the product is added to the cart.
         $table->integer('quantity');  // Quantity of the product variant added to the cart.
         $table->timestamps();  // Tracks creation and update times.
     });
     ```

   - **images table:**
     ```php
     Schema::create('images', function (Blueprint $table) {
         $table->id();
         $table->foreignId('product_id')->constrained()->cascadeOnDelete();  // Associates the image with a specific product.
         $table->string('path');  // File path to the image resource.
         $table->boolean('featured')->default(false);  // Indicates if the image is the featured image.
         $table->timestamps();  // Tracks creation and update times.
     });
     ```

3. **Define the model factories:**

   - Each model's factory is responsible for generating sample data for seeding the database.
   - Factories define default values for the fields and relationships of the models.

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
               'name' => $this->faker->word,
               'price' => $this->faker->randomFloat(2, 10, 1000),
               'description' => $this->faker->paragraph,
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
               'product_id' => App\Models\Product::factory(),  // Create and link a product
               'path' => $this->faker->imageUrl(),  // Generate a random image URL
               'featured' => $this->faker->boolean(20),  // 20% chance to be featured
           ];
       }
   }
   ```

   - Update each factory to reflect relationships, such as linking `product_id` fields in related models.

4. **Define the relationships in models:**

   Example for `Product` model:
   ```php
   namespace App\Models;

   use Illuminate\Database\Eloquent\Factories\HasFactory;
   use Illuminate\Database\Eloquent\Model;

   class Product extends Model
   {
       use HasFactory;

       protected $fillable = ['name', 'price', 'description'];

       // Define relationship with images
       public function images()
       {
           return $this->hasMany(Image::class);
       }

       // Define relationship with product variants
       public function variants()
       {
           return $this->hasMany(ProductVariant::class);
       }
   }
   ```

5. **Define the seeder logic:**

   Example for `DatabaseSeeder`:
   ```php
   namespace Database\Seeders;

   use App\Models\Product;
   use App\Models\ProductVariant;
   use App\Models\Cart;
   use App\Models\CartItem;
   use App\Models\Image;
   use Illuminate\Database\Seeder;

   class DatabaseSeeder extends Seeder
   {
       public function run()
       {
           // Seed products with variants and images
           Product::factory(10)->create()->each(function ($product) {
               // Add variants to each product
               ProductVariant::factory(3)->create(['product_id' => $product->id]);
               
               // Add images to each product
               Image::factory(2)->create(['product_id' => $product->id]);
           });

           // Seed carts with items
           Cart::factory(5)->create()->each(function ($cart) {
               CartItem::factory(2)->create(['cart_id' => $cart->id]);
           });
       }
   }
   ```

6. **Run the seeders:**

   ```bash
   php artisan db:seed
   ```

   - Populates the database with sample data based on the defined factories and relationships.

---

### **Troubleshooting Tips:**

- If you encounter issues with `npm install`, ensure you have Node.js installed by running `node -v`.
- Check `.env` for correct database configuration before migrating.
- If a migration fails, check the migration files for errors or missing relationships.
- Use `php artisan tinker` to test relationships and data generation from factories.

---

