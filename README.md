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
         $table->string('name');  // Product name
         $table->decimal('price', 8, 2);  // Product price with two decimal places
         $table->foreignId('main_image_id')->nullable();  // Reference to the main image
         $table->text('description')->nullable();  // Product description
         $table->timestamps();  // Created and updated timestamps
     });
     ```

   - **product_variants table:**
     ```php
     Schema::create('product_variants', function (Blueprint $table) {
         $table->id();
         $table->foreignId('product_id')->constrained()->cascadeOnDelete();  // Foreign key to products table
         $table->string('color')->nullable();  // Variant color
         $table->string('size')->nullable();  // Variant size
         $table->timestamps();
     });
     ```

   - **carts table:**
     ```php
     Schema::create('carts', function (Blueprint $table) {
         $table->id();
         $table->foreignId('user_id')->nullable();  // Associated user (optional)
         $table->string('session_id')->nullable();  // Session identifier for guest users
         $table->timestamps();
     });
     ```

   - **cart_items table:**
     ```php
     Schema::create('cart_items', function (Blueprint $table) {
         $table->id();
         $table->foreignId('cart_id')->constrained()->cascadeOnDelete();  // Foreign key to carts table
         $table->foreignId('product_variant_id')->constrained()->cascadeOnDelete();  // Foreign key to product_variants table
         $table->integer('quantity');  // Number of items in the cart
         $table->timestamps();
     });
     ```

   - **images table:**
     ```php
     Schema::create('images', function (Blueprint $table) {
         $table->id();
         $table->foreignId('product_id')->constrained()->cascadeOnDelete();  // Foreign key to products table
         $table->string('path');  // Path to the image file
         $table->timestamps();
     });
     ```

3. **Run the migrations to create the tables:**

   ```bash
   php artisan migrate
   ```

   - Creates the tables in your database as defined in the migration files.

---

### **Troubleshooting Tips:**

- If you encounter issues with `npm install`, ensure you have Node.js installed by running `node -v`.
- Check `.env` for correct database configuration before migrating.
- If a migration fails, check the migration files for errors or missing relationships.

---

