---
hidden: true
---

# WebStrike Lab

هنفتح الفايل ب Wireshark ونبدأ نحلل الفايل اول حاجه لاحظتها ان في عمليه Three-Way Handshake حصلت بين ال IP: 117.11.88.124 , IP: 24.49.63.79 ف اول 3 packets ال IP : 117.11.88.124 بعتSYN لل IP : 24.49.63.79 ورد عليه ال IP: 24.49.63.79 ب SYN, ACK بعدين رد عليه ال IP : 117.11.88.124 ب ACK وبكدا تكون تمت عمليه ال Three-Way Handshake

هنا عشان اعرف ف اي IP تانيه ولا لا فتحت قايمه statistics ثم IPv4statistics ثم All Addresses هنلاقي مش موجود غير ال 2 IP بس ف الفايل دا وبكدا نكون عرفنا ان ال IP: 117.11.88.124 ده هو الAttacker من خلال Three-Way Handshake

![Screenshot 2024-12-15 234027.png](<.gitbook/assets/Screenshot_2024 12 15_234027 (1).png>)

![Screenshot 2024-12-15 234040.png](<.gitbook/assets/Screenshot_2024 12 15_234040 (1).png>)

دلوقتي هفلتر ال Packets اللي ال Attacker كان بيبعتها للسيرفر عن طريق بروتوكول HTTP ونشوف ردود السيرفر عليه :

اول حاجه هنلاحظ ان ال Attacker كان بيعمل **Enumeration** أو استكشاف النظام عشان يعرف تركيب الملفات والداتا اللي عليه.

هنشوف انه كان بيحاول يفتح مسارات وملفات معينه زي

* `/products/`
* `/reviews/`
* `/admin/`

هنلاحظ ان في ردود 404 Not Found ظهرت علي بعض المسارات دي ، وده معناه أن ال Attacker كان بيحاول يكتشف إذا كانت الملفات أو المسارات دي موجودة ولا لأ.

تاني حاجه لاحظتها وهي ان ال Attacker كان بيحاول يرفع ملفات للسيرفر وغالبا هتكون ملفات malicious .

وده لاحظته من خلال ان في طلب **POST** اتبعت لـ `/reviews/upload.php` ونوع الملف كان `application/x-php`، وده امتداد ملفات PHP.

الطلب ده اتكرر أكتر من مرة، وده معناه إن الهاكر كان بيحاول **يرفع ملف malicious** (زي شيل أو سكربت PHP) عشان ياخد تحكم في السيرفر.

لو ال Attacker نجح ف رفع الملف دا يبقا هيقدر ينفذ اوامر ع السيرفر وده اسمه (Remote Code Execution).

تالت حاجه لاحظتها ان ال Attacker كان بيبعت طلبات زي

* `GET /admin/`
* `GET /admin/uploads/`

ده بيدل إن ال Attacker كان بيحاول يوصل لـ صفحة الأدمن أو ملفات حساسة زي اللي في `/uploads/` عشان يستغلها.

رابع حاجه كانت **Redirect :**

في طلب لـ `/uploads/` ظهر فيه **301 Redirect**، وده معناه إن المسار ممكن يكون حقيقي، والمهاجم ممكن يحاول يستخدمه أو يتلاعب بيه عشان يوصل لحاجة مفيدة.

![Screenshot 2024-12-17 104742.png](<.gitbook/assets/Screenshot_2024 12 17_104742 (1).png>)

لما اتعمقت اكتر ف التحليل وبدأت افتح ال Packets لقيت ان ال Attacker قدر فعلا يرفع الملف ال malicious عن طريق بورت 8080 بعد محاولات ورفعه ك صوره وكان الاسم الخاص بالملف image.jpg.php وبكدا ال Attacker قدر يستغل الثغره اللي بتسمح برفع ملفات malicious ع السيرفر ب أنه يوصل ل Remote Code Execution .

لما فتحت الرد اللي راجع من السيرفر بعد م ال Attacker رفع الملف لقيت حاجه خطيره جدا

المهاجم بعت **POST Request** للـ (Root Directory) `/` وبيستخدم الـ `Content-Type: application/x-www-form-urlencoded` مع بيانات معينة.

في البيانات المبعوتة، بيطلب قراءة ملف حساس من النظام:

/etc/passwd

*   **ملف** /etc/passwd:

    الملف ده موجود في أنظمة **Linux/Unix**، وبيحتوي على معلومات المستخدمين المسجلين على النظام.

    لو المهاجم قدر يقراه، ده معناه إنه نجح في تنفيذ أمر خبيث على السيرفر.

والدليل ان الهجوم دا نجح

ال File Data ف الجز السفلي من الرد بتبين ان الPath الخاص بالملف /etc/passwd عدي ف ال POST request بنجاح .

دي عادة علامة على إن ال **Remote Code Execution (RCE)** أو **File Inclusion** نجح، لأن المهاجم دلوقتي قادر يطلب ملفات النظام ويقراها.

![Screenshot 2024-12-17 115713.png](<.gitbook/assets/Screenshot_2024 12 17_115713 (1).png>)

وكمان قدرت اوصل stream packet رقم 13 كان فيها الاوامر اللي نفذها ال Attacker :

![Screenshot 2024-12-17 135214.png](<.gitbook/assets/Screenshot_2024 12 17_135214 (1).png>)

ومن ال الريكوست اللي كان بيبعته ال Attacker عرفت ال User-Agent اللي بيستخدمه ال Attacker ومعلومات كمان زي Full Path اللي ال Attacker رفع عليه ال shell script :

User-Agent: Mozilla/5.0 (X11; Linux x86\_64; rv:109.0) Gecko/20100101 Firefox/115.0

![Screenshot 2024-12-17 121711.png](<.gitbook/assets/Screenshot_2024 12 17_121711 (1).png>)

ف خطوه كنت عملتها ف الاول ممكن تكون مش مهمه لان ال Attackers بيستخدموا VPN ,VPS, Proxy ,وهي تحديد مكان ال Attacker بال IP :

ودي بعض المعلومات عن ال IP واللي ربما تكون حقيقيه :

![Screenshot 2024-12-17 130918.png](<.gitbook/assets/Screenshot_2024 12 17_130918 (1).png>)

### **نصائح لتأمين السيرفر ضد رفع الـ Web Shell أو الملفات الخبيثة:**

## File Upload Security

المشكلة الرئيسية كانت إن المهاجم قدر يرفع **ملف خبيث** (web shell) على السيرفر.

عشان نمنع المشكله دي هنعمل الخطوات دي :

* **التحقق من نوع الملف:**
  * اسمح فقط برفع أنواع معينة من الملفات (زي الصور فقط - `.jpg`، `.png`، `.gif`).
  * استخدم التحقق على مستوى **الـ MIME Type**، مش بس الامتداد.
* **تغيير اسم الملفات:**
  * عند رفع ملف، **غير اسمه تلقائيًا** في السيرفر، زي توليد اسم عشوائي، عشان يمنع المهاجم من الوصول المباشر للملف.
* **التحقق من محتوى الملف:**
  * اعمل فحص للملفات اللي بترفع عشان تتأكد إنها مش تحتوي على أكواد خبيثة.
  * استخدم أدوات زي **Antivirus scanners** أو **ClamAV**.

***

## File Permissions

المهاجم غالبًا استغل أذونات خاطئة سمحت بتشغيل الـ Web Shell بعد رفعه.

عشان نمنع المشكله دي هنعمل الخطوات دي :

*   الملفات اللي بترفع لازم تكون أذوناتها:

    * **644** (Read/Write للمالك، Read للباقي).

    وده مصدر بيشرح ال [linux-file-permissions](https://www.redhat.com/en/blog/linux-file-permissions-explained)
* **المجلدات (Directories):**
  * المجلدات اللي بيرفع عليها الملفات ما يكونش فيها **تنفيذ Scripts** (Disable Execution).
  *   اعمل إعدادات خاصة زي:

      ```
      <Directory "/uploads">
        Options -ExecCGI
        AllowOverride None
      </Directory>
      ```

***

### **استخدام Web Application Firewall (WAF):**

WAF بيقدر يكشف ويمنع أي **طلبات خبيثة** زي رفع الملفات الخبيثة.

* استخدم **WAF** زي **ModSecurity**، أو أي Cloud WAF زي **Cloudflare**.
* فعّل قواعد الـ **OWASP ModSecurity CRS** عشان تقدر تكشف أنواع الهجمات المشهورة.

***

### **فحص الترافيك وتحليل السجلات (Traffic Monitoring & Logs):**

لو كنت بتراقب الترافيك بشكل صحيح، كان ممكن تلاحظ النشاط المشبوه.

* راقب **طلبات POST** اللي بترفع ملفات.
* حلل اللوجات باستخدام أدوات زي:
  * **ELK Stack (Elasticsearch, Logstash, Kibana).**
  * **Splunk**.
* ابحث عن أنشطة غير طبيعية، زي ملفات مش معروفة أو طلبات لامتدادات مش مسموحة.

***

### **تفعيل Authentication وAuthorization قوي:**

المهاجم قدر يستخدم صفحة رفع ملفات بدون قيود أو صلاحيات قويه.

* تأكد إن صفحة رفع الملفات محمية ببعض الآليات:
  * **تسجيل دخول إجباري (Login Authentication)**.
  * تحقق من هوية المستخدم **(Authorization)** وصلاحياته قبل السماح بالرفع.
  * استخدام **Token** (مثل CSRF Tokens) لتأكيد الطلب.

***

### **تعطيل تنفيذ الأكواد في مجلدات الرفع (Disable Code Execution):**

لو المهاجم رفع Web Shell كملف **PHP**، السيرفر شغّله، وده الخطير.

* امنع تنفيذ الأكواد في مجلد الرفع عن طريق إعدادات السيرفر:
  *   **Apache (httpd.conf):**

      ```
      <Directory "/var/www/html/uploads">
        Options -Indexes -ExecCGI -FollowSymLinks
        AllowOverride None
      </Directory>
      ```
  *   **Nginx:**

      ```
      location /uploads/ {
        default_type text/plain;
        autoindex off;
        deny all;
      }
      ```

***

### **تحديث السيرفر والتطبيقات بشكل دوري:**

بعض الهجمات بتعتمد على ثغرات قديمة في الـ **Web Server** أو التطبيق.

* حدث الـ **Web Server** (Apache/Nginx) والـ **PHP** لأحدث نسخة.
* راقب أي ثغرات جديدة وتطبيق التحديثات.

***

### **تفعيل قيود على الرفع من خلال الـ .htaccess:**

ملف **.htaccess** بيسمح لك تضيف إعدادات أمنية إضافية.

*   أضف قاعدة لمنع تنفيذ أي **PHP أو Scripts** في مجلد الرفع:

    ```
    <FilesMatch "\.(php|pl|py|jsp|asp|sh|cgi)$">
      Deny from all
    </FilesMatch>
    ```

***

### **الخلاصة:**

النصايح دي هتمنع المهاجم من إنه يقدر يرفع **Web Shell** أو ملف خبيث بسهولة تاني:

1. تأمين آلية رفع الملفات.
2. ضبط أذونات الملفات والمجلدات.
3. استخدام Web Application Firewall (WAF).
4. مراقبة الترافيك وتحليل اللوجات.
5. تفعيل Authentication وصلاحيات قويه.
6. تعطيل تشغيل الأكواد في مجلدات الرفع.
7. تحديث التطبيقات والسيرفر دوريًا.
8. تفعيل إعدادات الحماية في **.htaccess**.

لو طبقت النصايح دي، هتقلل بشكل كبير فرص نجاح الهجوم ده تاني.

**References:**

[https://www.redhat.com/en/blog/linux-file-permissions-explained](https://www.redhat.com/en/blog/linux-file-permissions-explained)

[https://portswigger.net/web-security/file-upload](https://portswigger.net/web-security/file-upload)

[https://owasp.org/search/?searchString=file+upload+%26+RCE](https://owasp.org/search/?searchString=file+upload+%26+RCE)
