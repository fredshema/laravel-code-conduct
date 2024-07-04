# Laravel Project Coding Standards

## 1. General Standards

-   ### Redundancy Checks

    Ensure no redundant code blocks or logic duplications.

    `Example`: Refactor repetitive code into a reusable function or service.

    ```php
    // Old implementation
    public function index()
    {
        $users = User::all();
        return view('users.index', compact('users'));
    }

    // Refactored implementation
    public function index()
    {
        $users = $this->service->getAllUsers();
        return view('users.index', compact('users'));
    }
    ```

    Regularly refactor code to eliminate redundancy and improve maintainability.

-   ### Completeness Checks

    Verify all code and features are fully implemented and thoroughly tested.

    `Example`: Ensure all endpoints have corresponding tests.

    ```php
    public function testIndex()
    {
        $response = $this->get('/users');
        $response->assertStatus(200);
    }
    ```

    Conduct code reviews and use checklists to ensure completeness.

-   ### Code Formatting

    Use the Prettier package for code formatting.

    `Example`: Configure Prettier in your project.

    ```bash
    npm install --save-dev prettier
    echo {}> .prettierrc.json
    ```

    Format code every time changes are made to ensure consistency.

-   ### Indentation

    Use 4 spaces for indentation across all files.

-   ### Line Length

    Limit lines to 120 characters.
    Wrap lines that exceed this limit to maintain readability.

    ```php
    public function exampleFunction()
    {
        $longVariableName = "This is a very long string that exceeds the line length limit and needs to be wrapped.";
    }
    ```

## 2. Directory Structure

Follow the standard Laravel directory structure.
Group related classes, traits, and interfaces into appropriate subdirectories under the app directory.

## 3. Class Naming Conventions

-   ### Controllers

    Use PascalCase and suffix with Controller.

    Example: `UserController`

-   ### Models

    Use singular PascalCase.

    Example: `User`, `Order`

-   ### Factories

    Suffix with Factory.

    Example: `UserFactory`

-   ### Migrations

    Use PascalCase for migration files matching the model name for easy identification.
    Example: `CreateUserTable`

## 4. Method Naming Conventions

Use camelCase for method names.

Use descriptive names for methods that convey their purpose.

Example: `getActiveUsers`, `calculateTotalAmount`

## 5. Variable Naming Conventions

Use camelCase for variable names.

Use meaningful and descriptive names for variables.

Example: `$userEmail`, `$totalAmount`

Use Enums for listing constant variables to avoid mistakes.

```php
enum UserTypeEnum: string {
    case ADMIN = 'admin';
    case RESTAURANT_OWNER = 'restaurant_owner';
    // More cases...
}

// Accessing:
UserTypeEnum::ADMIN->value
```

## 6. Controller Standards

-   ### Thin Controllers

    Keep controllers thin by delegating logic to service classes and traits for repetitive methods.

    ```php
    // OrderController
    public function store(OrderRequest $request)
    {
        $data = CreateOrderData::from($request->validated());
        $result = $this->service->createOrder($data);
        return $this->successfulResponse($result, __('Order created successfully'));
    }
    ```

-   ### Resource Controllers

    Use resource controllers for CRUD operations.

    ```php
    Route::resource('users', UserController::class);
    ```

-   ### Method Order

    Follow the order: `constructor`, `middleware`, `public methods`, and `private methods`.

-   ### Service Injection

    Inject service classes into controllers.

    ```php
    // OrderController
    public function __construct(private OrderService $service) {}
    ```

## 7. Model Standards

-   ### Eloquent Models

    Use Eloquent models for database interactions.

    ```php
    $user = User::find(1); // Find user by ID

    $users = User::where('email', 'john@test.com')->get(); // Find users by email
    ```

-   ### Fillable Properties

    Define `$fillable` properties instead of `$guarded`.

    ```php
    protected $fillable = ['name', 'email', 'password'];
    ```

-   ### Relationships

    Define relationships using appropriate methods.

    ```php
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
    ```

## 8. Migration Standards

-   ### Descriptive Names

    Use descriptive names for migration files.

    `2024_06_27_154523_add_email_column_to_users_table.php`
    `2024_06_27_183012_update_posts_table_add_status_column.php`

-   ### Schema Methods

    Use up and down methods to define schema changes clearly and revert changes if necessary.

    ```php
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('users');
    }
    ```

## 9. Database Standards

-   ### Table Naming

    Use singular PascalCase for table names.

    Example: `User`, `Order`

-   ### Column Naming

    Use snake_case for column names.

    Example: `first_name`, `created_at`

-   ### Indexes

    Define indexes where necessary to optimise query performance.

    ```php
    $table->index('email');
    ```

## 10. Route Standards

-   ### RESTful Routes

    Use RESTful routes for resource controllers.

    ```php
    Route::resource('users', UserController::class);
    ```

-   ### Naming Conventions

    Use kebab-case for route names.

    Example: `/user-profile`, `/order-items`

-   ### Main ID Route
    Use `/orders/{id}` instead of `orders?id={id}`.

## 11. Service and Repository Standards

-   ### Service Classes

    Encapsulate business logic in service classes.

    ```php
    class OrderService
    {
        public function createOrder(CreateOrderData $data)
        {
            // Business logic here
        }
    }
    ```

-   ### Repositories

    Use repositories for database interactions beyond basic CRUD operations.

-   ### Service Injection

    Inject services into controllers.

    ````php
    // OrderController
    public function \_\_construct(private OrderService $service) {}
    ```3
    ````

## 12. Validation

-   ### Form Requests

    Use Form Request classes for validating incoming data for every `POST` and `PUT` API.

    ```php
    class CreateUserRequest extends FormRequest
    {
        public function rules()
        {
            return [
                'name' => 'required|string',
                'email' => 'required|email',
                // More rules...
            ];
        }
    }
    ```

-   ### Spatie Data Package

    Use the Spatie Data package to convert FormRequest data into listed attributes.

    ```php
    use Spatie\LaravelData\Data;

    class CreateUserData extends Data
    {
        public string $name;
        public string $email;
    }
    ```

-   ### Custom Validation Rules

    Create custom validation rules where needed.

    ```php
    <?php

    namespace App\Rules;

    use Illuminate\Contracts\Validation\Rule;

    class PhoneNumber implements Rule
    {
        public function passes($attribute, $value, $arguments = [])
        {
            $pattern = '/^\d{3}-\d{3}-\d{4}$/';
            return preg_match($pattern, $value);
        }
        public function message($attribute, $parameters)
        {
            return 'The :attribute must be a valid phone number';
        }
    }
    ```

## 13. Error Handling

-   ### Try-Catch Blocks

    Use try-catch blocks to handle exceptions gracefully and log errors.

    ```php
    try {
        // Code that may throw an exception
    } catch (Exception $e) {
        Log::error([
            "message" => $e->getMessage(),
            "line" => $e->getLine(),
            "trace" => $e->getTrace(),
            "timestamp" => now()
        ]);
    }
    ```

-   ### Custom Exceptions
    Create custom exceptions for domain-specific errors.
    ```php
    class CustomException extends Exception
    {
        // Custom exception logic
    }
    ```

## 14. Testing Standards

-   ### Testing Framework

    Use Pest for testing.

-   ### Test Naming

    Use descriptive names for test methods.

-   ### Test Coverage

    Ensure high test coverage for critical parts of the application.

-   ### Test Organization

    Organise tests into appropriate directories.

## 15. Security Standards

-   ### Input Sanitization

    Sanitise and validate all input data.

-   ### XSS Protection

    Use Blade’s {{ }} to escape output.

-   ### CSRF Protection

    Ensure all forms include CSRF tokens.

    ```html
    <form method="POST" action="{{ route('user.store') }}">
        @csrf // your codes
    </form>
    ```

## 16. Documentation

-   ### DocBlocks

    Use PHPDoc for methods and classes.

    ```php
    class User {
        /**
        * Get the user's full name.
        * @return string The user's full name.
        */
        public function getFullName(): string {
            return $this->firstName . ' ' . $this->lastName;
        }
    }
    ```

-   ### README

    Maintain a README.md with setup instructions and usage examples.

-   ### API Documentation

    Use tools like Swagger or Postman to document APIs.

## 17. Version Control

-   ### Git Branches

    Use a branching strategy (e.g., Git Flow).

-   ### Commit Messages

Use meaningful commit messages.

## 18. Environment Configuration

-   ### Environment Variables

    Use .env for configuration.

-   ### Config Files

    Store environment-specific settings in config files in the config folder.

## 19. Consistent Input Parameters

Use common naming conventions for input parameters.

## 20. Query Optimization

-   ### Eager Loading

    Follow eager loading in queries.

    ```php
    $users = User::with('posts')->get();
    ```

-   ### Business Logic Separation

Separate business logic from the controller.

-   ### Client-Side Validation

    Manage validation at the client-side before sending data.

```js
$("#myForm").validate({
    rules: { name: "required", email: { required: true, email: true } },
    messages: {
        name: "Please enter a name.",
        email: "Please enter a valid email address.",
    },
});
```

## 21. Use of Traits

Use traits for methods instead of applying queries directly in controllers.

## 22. Use of Soft Deletes

Add softdeletes trait in models to prevent permanent deletes

```php
class ModelName extends Model
{
    use SoftDeletes;
}
```

Add deleted_at column in migrations to support soft deletes

```php
Schema::create(‘table_name’, function (Blueprint $table) {
    // table columns
    $table->softDeletes();
});
```

## 23. API Response Format

Standardise API responses using methods from `app/Http/Controllers/Controller.php` like:

```php
return response()->json([
   'success' => false,
   'message' => $message,
   'errors' => $errors,
   'timestamp' => now()->format('Y-m-d, H:i:s'),
   'execution_time' => (microtime(true) - START_EXECUTION_TIME) * 1000 . ' ms',
   'cached' => PROCESS_CACHED
], Response::HTTP_UNPROCESSABLE_ENTITY);
```

## 24. URL Conventions

-   ### Resource Routes

    Use `Route::resource` methods for all resource routes.

    Example:

    ```php
    Route::resource(‘users', UserController::class);
    ```

-   ### Kebab Casing

    Use kebab casing for URLs.

    Example: `/user-profile`, `/order-items`

-   ### Named Routes

    Use the name method for all URLs.

    ```php
    Route::get('/users/{id}', [UserController::class, ‘show’])->name('user.show');
    ```

## 25. Logging and Activity Logs

-   ### Request Logging
    Log each request to the controller.
-   ### Activity Logs

    Create activity logs for each endpoint/action.

-   ### Error Logging

    Log errors with the method name.

## 26. Use of Lang Facade

Use the Lang facade to support multiple languages.

```php
$welcomeMessage = Lang::get('welcome'); //or
$welcomeMessage = trans('welcome');
```

## 27. Security Enhancements

-   ### Additional Security Measures
    Implement additional security measures for API requests.
-   ### Login Attempt Limits
    Implement login attempt limits and account suspension.

## 28. Comments and Descriptions

Add comments and descriptions before creating any new function.

```php
/**
*  This function retrieves all active users from the database.
**/

function getActiveUsers()
{
    // ... function logic
}
```

## 29. GET Endpoint Guidelines

Every GET endpoint returning a list should be paginated.

Use helper methods for consistent responses created in `app/Http/Controllers/Controller.php`. Like:

```php
function jsonResponseWithPagination($data, $total)
{
    return response()->json([
        'data' => $data,
        'total' => $total,
        'timestamp' => now()->format('Y-m-d, H:i:s'),
        'execution_time' => (microtime(true) - START_EXECUTION_TIME) * 1000 . ' ms'
    ]);
}
```

Every list endpoint should include search capabilities and status check.

Implement search capabilities with necessary columns and add a status column to check the status of the model (the status column should be nullable)
