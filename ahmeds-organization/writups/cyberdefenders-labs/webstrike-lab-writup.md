---
cover: ../../.gitbook/assets/download.jpeg
coverY: 0
---

# WebStrike Lab Writup

We will open the file using **Wireshark** and begin analyzing it. The first thing I noticed is that a **Three-Way Handshake** occurred between **IP: 117.11.88.124** and **IP: 24.49.63.79** in the first three packets. The **IP: 117.11.88.124** sent a **SYN** to **IP: 24.49.63.79**, which replied with a **SYN, ACK**. Then **IP: 117.11.88.124** responded with an **ACK**, completing the **Three-Way Handshake** process.

To verify if there are any other IP addresses in the file, I opened the **Statistics** menu, navigated to **IPv4 Statistics**, and selected **All Addresses**. We can see that only these two IP addresses exist in this file. From this, we can conclude that **IP: 117.11.88.124** is the attacker based on the Three-Way Handshake.

<figure><img src="../../.gitbook/assets/Screenshot_2024 12 15_234027 (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot_2024 12 15_234040 (1).png" alt=""><figcaption></figcaption></figure>

**Now I will filter the packets sent by the attacker to the server over the HTTP protocol and analyze the server's responses:**

1.  **Enumeration or System Exploration:**\
    The first observation is that the attacker was performing **enumeration** to explore the system structure and discover files and directories.

    * The attacker attempted to access specific paths and files such as:
      * `/products/`
      * `/reviews/`
      * `/admin/`

    We noticed that some of these requests returned **404 Not Found** responses, which indicates that the attacker was probing to check if these files or directories existed on the server.
2.  **File Upload Attempts:**\
    The second observation is that the attacker attempted to **upload files** to the server, most likely malicious files.

    * This was evident from a **POST request** sent to `/reviews/upload.php`.
    * The file type was **application/x-php**, which is a **PHP file extension**.

    This request was repeated multiple times, indicating that the attacker was likely trying to upload a **malicious file** (e.g., a shell or PHP script) to gain control of the server.

    If the attacker succeeded in uploading the file, they would be able to execute commands on the server, which is known as **Remote Code Execution (RCE)**.
3.  **Admin Panel and Sensitive Files Access Attempts:**\
    The third observation is that the attacker sent requests such as:

    * `GET /admin/`
    * `GET /admin/uploads/`

    This suggests that the attacker was attempting to access the **admin panel** or sensitive files within the `/uploads/` directory to exploit or manipulate them.
4. **301 Redirect Response:**\
   A **301 Redirect** response was observed for a request to `/uploads/`. This indicates that the path might actually exist. The attacker might try to use or manipulate it further to gain access to useful resources.



<figure><img src="../../.gitbook/assets/Screenshot_2024 12 17_104742 (1).png" alt=""><figcaption></figcaption></figure>

Upon diving deeper into the analysis and inspecting the packets, I found that the attacker successfully uploaded the malicious file through port 8080 after multiple attempts. The file was uploaded as an image with the name `image.jpg.php`. This allowed the attacker to exploit the vulnerability that permits uploading malicious files to the server, leading to **Remote Code Execution (RCE)**.

When I examined the server's response after the attacker uploaded the file, I discovered something extremely dangerous:

* The attacker sent a **POST request** to the **root directory** `/` using the **Content-Type: application/x-www-form-urlencoded** with specific data.
* In the data sent, the attacker requested to **read a sensitive file** from the system:
  * `/etc/passwd`

**What is `/etc/passwd`?**\
The `/etc/passwd` file exists on Linux/Unix systems and contains information about the users registered on the system.

If the attacker successfully reads this file, it means they managed to execute a malicious command on the server.

**Evidence of the attack's success:**

* In the **File Data** section at the bottom of the server's response, the path to the file `/etc/passwd` was successfully passed through the **POST request**.

This is typically a strong indication that **Remote Code Execution (RCE)** or **File Inclusion** has succeeded because the attacker is now able to request and read system files.



<figure><img src="../../.gitbook/assets/Screenshot_2024 12 17_115713 (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot_2024 12 17_135214 (1).png" alt=""><figcaption></figcaption></figure>

From the requests sent by the attacker, I was able to identify the **User-Agent** being used by the attacker, along with additional information such as the **Full Path** where the attacker uploaded the shell script.

User-Agent: Mozilla/5.0 (X11; Linux x86\_64; rv:109.0) Gecko/20100101 Firefox/115.0



<figure><img src="../../.gitbook/assets/Screenshot_2024 12 17_121711 (1).png" alt=""><figcaption></figcaption></figure>

One of the initial steps I performed, which might not be very significant since attackers often use **VPNs**, **VPSs**, and **Proxies** to hide their identities and locations, was determining the attacker's location using the IP address.

Here is some information about the IP, which may or may not be accurate:



<figure><img src="../../.gitbook/assets/Screenshot_2024 12 17_130918 (1).png" alt=""><figcaption></figcaption></figure>

#### **Tips to Secure the Server Against Web Shell or Malicious File Uploads**

The main issue here is that the attacker was able to upload a malicious file (web shell) to the server.

To mitigate this problem, follow these steps:

***

#### **1. File Upload Security**

* **Validate File Types**:
  * Allow only specific file types (e.g., images like `.jpg`, `.png`, `.gif`).
  * Use **MIME type verification**, not just file extensions.
* **Rename Uploaded Files**:
  * Automatically rename uploaded files on the server using random names to prevent the attacker from accessing the file directly.
* **Scan File Content**:
  * Check uploaded files for malicious code.
  * Use tools like **Antivirus scanners** or **ClamAV**.

***

#### **2. File Permissions**

The attacker likely exploited incorrect file permissions that allowed the uploaded web shell to execute.

* **File Permissions**:\
  Uploaded files should have:
  * **644**: (Read/Write for the owner, Read for others).
*   **Directories Permissions**:\
    Prevent script execution in upload directories.\
    Configure settings like:

    **Apache Configuration**

    ```apache
     <Directory "/uploads">
       Options -ExecCGI
       AllowOverride None
    </Directory>
    ```

***

#### **3. Use a Web Application Firewall (WAF)**

* A **WAF** can detect and block malicious requests, including file uploads.
* Use tools like **ModSecurity** or cloud-based WAFs like **Cloudflare**.
* Enable **OWASP ModSecurity CRS** rules to detect common attack patterns.

***

#### **4. Traffic Monitoring & Log Analysis**

If traffic monitoring is done correctly, suspicious activities can be detected early.

* Monitor **POST requests** involving file uploads.
* Analyze logs using tools such as:
  * **ELK Stack** (Elasticsearch, Logstash, Kibana)
  * **Splunk**
* Look for unusual activities, such as unknown file names or requests with unauthorized extensions.

***

#### **5. Strong Authentication and Authorization**

The attacker exploited a file upload page with weak or no access restrictions.

* Secure the file upload page with:
  * **Mandatory Login Authentication**
  * **Authorization** to validate user permissions before file uploads.
  * Use **Tokens** (e.g., CSRF Tokens) to validate upload requests.

***

#### **6. Disable Code Execution in Upload Directories**

If the attacker uploads a web shell as a PHP file, it will execute unless code execution is disabled.

* Prevent code execution in the upload folder with the following configurations:

**Apache (httpd.conf)**

```apache
<Directory "/var/www/html/uploads">
   Options -Indexes -ExecCGI -FollowSymLinks
   AllowOverride None
</Directory>
```

**Nginx Configuration**

```nginx
location /uploads/ {
   default_type text/plain;
   autoindex off;
   deny all;
}
```

***

#### **7. Regular Server and Application Updates**

Some attacks exploit outdated web server or application vulnerabilities.

* Regularly update:
  * **Web Server** (Apache/Nginx)
  * **PHP** and other software to the latest versions.
* Monitor for new vulnerabilities and apply patches promptly.

***

#### **8. Restrict Uploads via `.htaccess`**

The `.htaccess` file can provide additional security settings.

* Prevent execution of PHP or scripts in the upload folder:

```apache
<FilesMatch "\.(php|pl|py|jsp|asp|sh|cgi)$">
   Deny from all
</FilesMatch>
```

***

#### **Summary**

Implementing the above recommendations will significantly reduce the chances of an attacker successfully uploading a **web shell** or malicious file:

1. Secure the file upload mechanism.
2. Configure proper file and folder permissions.
3. Use a Web Application Firewall (WAF).
4. Monitor traffic and analyze logs.
5. Enforce strong authentication and authorization.
6. Disable code execution in upload directories.
7. Regularly update applications and servers.
8. Enable security settings using `.htaccess`.

***

#### **References**

* [Linux File Permissions Explained - Red Hat](https://www.redhat.com/en/blog/linux-file-permissions-explained)
* [File Upload Vulnerabilities - PortSwigger](https://portswigger.net/web-security/file-upload)
* [OWASP File Upload Security](https://owasp.org/search/?searchString=file+upload+%26+RCE)
