# Cybersecurity_Session-Hijacking_Demo
HTTP Session Hijacking Demo

Session hijacking is a critical security issue where an attacker takes over a user's session by stealing or guessing the session token. This demonstration should be performed in a controlled environment with clear ethical considerations, ensuring that it’s only done with explicit permission and for educational purposes.

### Prerequisites
- **Two Virtual Machines**: One acting as the victim and the other as the attacker.
- **A Simple Web Application**: A basic PHP web application with session handling.
- **Wireshark**: For packet capture and inspection.

### Step-by-Step Guide

#### Step 1: Set Up the Web Application

1. **Create a Simple PHP Login Page:**
   - Set up a PHP web application that uses sessions to manage user authentication. Save the following code as `login.php` and `welcome.php` in your web server’s root directory (e.g., `htdocs` in XAMPP or MAMP).

   **`login.php`:**

   ```php
   <?php
   session_start();
   if ($_SERVER['REQUEST_METHOD'] == 'POST') {
       $username = $_POST['username'];
       $password = $_POST['password'];

       if ($username == 'admin' && $password == 'password') {
           $_SESSION['user'] = $username;
           header('Location: welcome.php');
       } else {
           echo "Invalid credentials!";
       }
   }
   ?>
   <!DOCTYPE html>
   <html>
   <body>
       <form method="POST" action="login.php">
           Username: <input type="text" name="username"><br>
           Password: <input type="password" name="password"><br>
           <input type="submit" value="Login">
       </form>
   </body>
   </html>
   ```

   **`welcome.php`:**

   ```php
   <?php
   session_start();
   if (!isset($_SESSION['user'])) {
       header('Location: login.php');
       exit();
   }
   ?>
   <!DOCTYPE html>
   <html>
   <body>
       <h1>Welcome, <?php echo $_SESSION['user']; ?>!</h1>
       <p>Your session ID: <?php echo session_id(); ?></p>
       <a href="logout.php">Logout</a>
   </body>
   </html>
   ```

   **`logout.php`:**

   ```php
   <?php
   session_start();
   session_destroy();
   header('Location: login.php');
   exit();
   ?>
   ```

2. **Run the Application:**
   - Start your local web server and navigate to `http://localhost/login.php` to ensure the login page is working.

#### Step 2: Demonstrate Session Hijacking

1. **Log in as a Victim:**
   - On the victim machine, log in to the web application using the credentials `admin/password`. This will create a session.

2. **Capture the Session ID:**
   - Open Wireshark on the attacker machine and start capturing traffic. Apply a filter like `http` or `cookie` to capture HTTP packets.
   - On the victim machine, after logging in, note that the session ID is transmitted as a cookie in the HTTP headers.
   - The attacker, using Wireshark, captures the session ID from the victim's HTTP request.

3. **Hijack the Session:**
   - On the attacker machine, open the same web application and log in.
   - After logging in, open the browser's developer tools (usually by pressing F12), and go to the "Application" or "Storage" tab, where cookies are listed.
   - Replace the attacker’s session ID with the victim’s session ID that was captured.
   - Refresh the page. The attacker now has access to the victim’s session, effectively hijacking it.

#### Step 3: Impact and Mitigation

1. **Impact:**
   - An attacker can impersonate a user by hijacking the session. This can lead to unauthorised access to sensitive data, unauthorised transactions, or complete control of a user's account.

2. **Mitigation Techniques:**
   - **Use HTTPS**: Encrypts the session ID during transmission, making it harder for attackers to capture it.
   - **Regenerate Session IDs**: Regenerate the session ID upon login and at regular intervals.
   - **Use Secure and HttpOnly Flags**: Mark cookies as `Secure` to ensure they are only sent over HTTPS and `HttpOnly` to prevent access via JavaScript.
   - **Session Expiration**: Implement short session expiration times and automatic logouts.

#### Step 4: Ethical Considerations

- **Controlled Environment**: Ensure that this demonstration is performed in a controlled environment, such as a lab setup, where all participants are aware of and have consented to the activity.
- **Legal Compliance**: Never perform session hijacking or any other attacks on live or production systems without explicit permission.

### Summary

This demonstrates the vulnerabilities of session handling in web applications and emphasises the importance of implementing robust security measures to prevent session hijacking. By understanding both the attack and the defense mechanisms, one can gain a deeper understanding of the importance of secure session management in cybersecurity.
