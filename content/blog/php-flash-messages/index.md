+++
title = "Mastering Flash Messages in PHP: A Simple Guide"
date = 2024-11-28
description = "Flash messages are a simple yet powerful way to improve user experience in web applications. With the stdakov/flash package, implementing them becomes straightforward. Whether you're building a small project or a complex system, this technique is invaluable."
+++

![Image](php-flash-message.webp)

When building web applications, effective communication with users is essential. One technique widely used in frameworks like Laravel or Symfony is "flash messages." These are temporary messages displayed to users, such as success notifications or error alerts. This article dives into implementing flash messages in PHP with a straightforward approach.
<!-- more -->
## What Are Flash Messages?

Flash messages are a type of session-based message that persists only for the next user request. They are ideal for:

* Informing users of successful actions (e.g., "Profile updated successfully").
* Displaying error messages (e.g., "Invalid username or password").
* Guiding users through processes.

## Why Use Flash Messages?
1. User Feedback: Keep users informed without overwhelming the UI.
2. Temporary Data: Flash messages disappear after being displayed, reducing clutter in the session data.
3. Ease of Use: They fit seamlessly into workflows like login authentication or form validation.

## Building Flash Messages in PHP

Using the `stdakov/flash` package simplifies flash message management. Follow these steps to implement and use it:

### Step 1: Install the Package

Start by installing the package with Composer:
```bash
composer require stdakov/flash
```

This will download and set up the necessary files.

### Step 2: Setting Flash Messages
In your backend logic, set a flash message using the FM::set method:

```php
use Dakov\FM;

if (!$isAuthenticated) {
    FM::set("error", "Invalid username or password.");
    header("Location: /login.php");
    exit();
}
```

This stores the message temporarily in the session.

### Step 3: Displaying Flash Messages

On the redirected page, check for and display the message using `FM::exist` and `FM::flash`:

```html
<?php if (FM::exist("error")): ?>
    <div class="alert alert-danger">
        <?= FM::flash("error"); ?>
    </div>
<?php endif; ?>
```

### Putting It All Together
Here’s a complete example of login validation using flash messages:

Login Script (`login.php`):
```php
require 'vendor/autoload.php';
session_start();

use Dakov\FM;

if ($_POST['username'] !== 'admin' || $_POST['password'] !== 'password') {
    FM::set("error", "Invalid username or password.");
    header("Location: /index.php");
    exit();
}

header("Location: /dashboard.php");
```

Login Page (`index.php`):

```html
require 'vendor/autoload.php';
session_start();

use Dakov\FM;

?>

<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
</head>
<body>
    <?php if (FM::exist("error")): ?>
        <div class="alert alert-danger">
            <?= FM::flash("error"); ?>
        </div>
    <?php endif; ?>

    <form method="POST" action="login.php">
        <input type="text" name="username" placeholder="Username" required>
        <input type="password" name="password" placeholder="Password" required>
        <button type="submit">Login</button>
    </form>
</body>
</html>
```

Flash messages are a simple yet powerful way to improve user experience in web applications. With the `stdakov/flash` package, implementing them becomes straightforward. Whether you're building a small project or a complex system, this technique is invaluable.

Now, you're ready to master flash messages and make your PHP applications more user-friendly!