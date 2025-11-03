# Εβδομάδα 1: Προχωρημένες Τεχνολογίες Ανάπτυξης Εφαρμογών Διαδικτύου

## **Άσκηση 1: Environment Setup**
**Στόχος**: Εγκατάσταση Python environment και βασικές βιβλιοθήκες

### Βήματα:
1. **Python Installation Check**:
   ```bash
   python3 --version
   pip3 --version
   ```

2. **Virtual Environment Creation**:
   ```bash
   python3 -m venv http_lab
   source http_lab/bin/activate  # macOS/Linux
   # ή Scripts\activate.bat  # Windows
   ```

3. **Package Installation**:
   ```bash
   pip install requests beautifulsoup4 pandas matplotlib
   pip list  # Verify installation
   ```

4. **Test Script** (`test_setup.py`):
   ```python
   import requests
   print("Environment setup successful!")
   print(f"Requests version: {requests.__version__}")
   ```

**Αναμενόμενο αποτέλεσμα**: Εμφάνιση Version number και μηνύματος "Environment setup successful!" 

---

## **Άσκηση 2: First HTTP Request**
**Στόχος**: Πρώτο GET request σε public API

### Κώδικας:
```python
import requests

# Simple GET request
response = requests.get('https://httpbin.org/json')

print("=== HTTP Response Analysis ===")
print(f"Status Code: {response.status_code}")
print(f"Content-Type: {response.headers['content-type']}")
print(f"Response Time: {response.elapsed.total_seconds()} seconds")
print(f"Response Size: {len(response.content)} bytes")

# Pretty print JSON
import json
data = response.json()
print("\nJSON Response:")
print(json.dumps(data, indent=2))
```

### Ερωτήματα:
1. Τι σημαίνει ο status code 200;
2. Πόσο χρόνο χρειάστηκε το request;
3. Τι τύπου δεδομένα επιστρέφει το API;

**Αναμενόμενο αποτέλεσμα**: Successful JSON response με metadata analysis

---

## **Άσκηση 3: URL Parameters**
**Στόχος**: Χρήση query parameters σε GET requests

Εκτελέστε το πιο κάτω πρόγραμμα και παρατηρήστε τα αποτελέσματα.
+ Συγκρίνετε τα URLs που δημιουργούνται από τη “χειροκίνητη” μέθοδο (Method 1) με αυτά που παράγει η “έξυπνη” μέθοδος (Method 2).
+ Εντοπίστε τη διαφορά στην κωδικοποίηση ειδικών χαρακτήρων (π.χ. &, >, κενά).

**Ερωτήσεις Κατανόησης**
+ Ποια είναι τα πλεονεκτήματα της χρήσης του ορίσματος params αντί για χειροκίνητη σύνθεση του URL;
+ Πώς κωδικοποιούνται οι ειδικοί χαρακτήρες στα query strings (π.χ. “python & django”);
+ Πώς θα μπορούσατε να στείλετε λίστες ή πολλαπλές τιμές για ένα ίδιο κλειδί παραμέτρου;
+ Τι θα συμβεί αν αλλάξετε τη βάση (base_url) σε ένα endpoint που δεν δέχεται GET παραμέτρους;

**Επέκταση**. 
* Δημιουργήστε μία συνάρτηση make_request(base_url, params) που:
    * Δέχεται μια βάση URL και ένα λεξικό παραμέτρων.
    * Εκτελεί το αίτημα με requests.get().
    * Επιστρέφει το JSON αποτέλεσμα.

Καλέστε τη συνάρτηση με διαφορετικά σύνολα παραμέτρων (π.χ. άλλα ονόματα, πόλεις ή φίλτρα).
<details>
    <b>Κώδικας:</b>

    ```
    import requests

    # Test different parameter methods
    base_url = 'https://httpbin.org/get'

    # Method 1: Manual URL construction
    response1 = requests.get(f'{base_url}?name=Γιάννης&city=Αθήνα&age=25')

    # Method 2: Using params dictionary (preferred)
    params = {
        'name': 'Μαρία',
        'city': 'Θεσσαλονίκη', 
        'age': 30,
        'interests': ['programming', 'music', 'travel']
    }
    response2 = requests.get(base_url, params=params)

    print("=== Method 1 Results ===")
    print(f"Final URL: {response1.url}")
    print(f"Args received: {response1.json()['args']}")

    print("\n=== Method 2 Results ===")
    print(f"Final URL: {response2.url}")
    print(f"Args received: {response2.json()['args']}")

    # Special characters handling
    special_params = {
        'search': 'python & django',
        'filter': 'price>100',
        'sort': 'date desc'
    }
    response3 = requests.get(base_url, params=special_params)
    print(f"\n=== Special Characters ===")
    print(f"Encoded URL: {response3.url}")
    ```    
</details>

---

## **Άσκηση 4: Headers Customization**
**Στόχος**: Κατανόηση και χρήση HTTP headers

Εκτελέστε το πρόγραμμα και μελετήστε την έξοδο.  
+ Παρατηρήστε ποια headers στάλθηκαν από τον client και ποια σας επέστρεψε ο server.  
+ Εντοπίστε τις διαφορές μεταξύ Request Headers και Response Headers.  
+ Αλλάζει η απάντηση όταν χρησιμοποιείτε διαφορετικό User-Agent;

**Ερωτήσεις Κατανόησης**
+ Ποιος είναι ο ρόλος του πεδίου User-Agent; Πώς μπορεί να επηρεάσει την απάντηση ενός server;
+ Τι πληροφορίες μεταφέρει το header Accept-Language;
+ Τι σημαίνει ένα header που ξεκινά με X- (όπως X-Custom-Header ή X-Student-ID);
+ Ποιες πληροφορίες παρέχουν τα Response Headers content-type, server, date, content-length;
+ Τι αλλάζει αν αφαιρέσετε εντελώς τα custom headers από το αίτημα;

<details>
    <b>Κώδικας:</b>

    ```
    import requests

    # Custom headers example
    custom_headers = {
        'User-Agent': 'HTTPLabWorkshop/1.0 (Educational Purpose)',
        'Accept': 'application/json',
        'Accept-Language': 'el-GR,en-US;q=0.9',
        'X-Custom-Header': 'LabExercise4',
        'X-Student-ID': '12345'
    }

    response = requests.get('https://httpbin.org/headers', headers=custom_headers)

    print("=== Request Headers Sent ===")
    received_headers = response.json()['headers']
    for header, value in received_headers.items():
        print(f"{header}: {value}")

    print("\n=== Response Headers Received ===")
    interesting_headers = ['content-type', 'server', 'date', 'content-length']
    for header in interesting_headers:
        if header in response.headers:
            print(f"{header}: {response.headers[header]}")

    # Browser simulation
    browser_headers = {
        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
    }
    browser_response = requests.get('https://httpbin.org/user-agent', headers=browser_headers)
    print(f"\n=== Browser Simulation ===")
    print(f"Detected User-Agent: {browser_response.json()['user-agent']}")
    ```
</details>

---

## **Άσκηση 5: POST Requests με JSON**
**Στόχος**: Αποστολή δεδομένων με POST requests

Εκτελέστε το πρόγραμμα και μελετήστε την έξοδο.  
+ Παρατηρήστε πώς αλλάζουν τα headers όταν χρησιμοποιείτε το json= έναντι του data=.
+ Ελέγξτε πώς εμφανίζονται τα ελληνικά δεδομένα στον server (UTF-8 κωδικοποίηση).
+ Δοκιμάστε να “χαλάσετε” επίτηδες το JSON για να δείτε τι είδους σφάλμα παράγεται.

**Ερωτήσεις Κατανόησης**
+ Ποια είναι η διαφορά ανάμεσα στο requests.post(..., json=data) και στο requests.post(..., data=json_string);
+ Γιατί το requests ορίζει αυτόματα το header Content-Type: application/json όταν χρησιμοποιούμε το json=;
+ Πώς βοηθά η παράμετρος ensure_ascii=False κατά την κωδικοποίηση JSON με ελληνικούς χαρακτήρες;
+ Ποιο είναι το αποτέλεσμα της “λανθασμένης” αποστολής JSON (με μονά εισαγωγικά); Τι θα δείτε στην απάντηση του server;
+ Πώς θα μπορούσατε να προσθέσετε μηχανισμό ελέγχου σφαλμάτων (error handling) για περιπτώσεις που ο server δεν επιστρέφει έγκυρο JSON;

<details>
    <b>Κώδικας:</b>

    ```python
    import requests
    import json
    from datetime import datetime

    # Student data to send
    student_data = {
        'name': 'Ελένη Παπαδοπούλου',
        'email': 'eleni@university.gr',
        'student_id': 'CS2021123',
        'courses': ['Data Structures', 'Web Technologies', 'Databases'],
        'semester': 6,
        'gpa': 8.5,
        'enrollment_date': datetime.now().isoformat(),
        'is_active': True
    }

    # Method 1: Using json parameter (automatic header setting)
    print("=== Method 1: JSON Parameter ===")
    response1 = requests.post('https://httpbin.org/post', json=student_data)
    print(f"Status: {response1.status_code}")
    print(f"Content-Type sent: {response1.request.headers['content-type']}")
    print(f"Data received by server: {response1.json()['json']}")

    # Method 2: Manual JSON encoding
    print("\n=== Method 2: Manual JSON ===")
    json_string = json.dumps(student_data, ensure_ascii=False)  # Greek characters
    headers = {'Content-Type': 'application/json; charset=utf-8'}
    response2 = requests.post('https://httpbin.org/post', data=json_string, headers=headers)
    print(f"Manual encoding successful: {response2.status_code == 200}")

    # Error case - Invalid JSON
    print("\n=== Error Handling ===")
    try:
        invalid_data = "{'invalid': 'json syntax'}"  # Single quotes = invalid JSON
        response3 = requests.post('https://httpbin.org/post', data=invalid_data, 
                                headers={'Content-Type': 'application/json'})
        print("This might cause issues on the server side")
    except Exception as e:
        print(f"Error: {e}")
    ```
</details>

---

## **Άσκηση 6: Form Data POST**
**Στόχος**: Αποστολή form data (application/x-www-form-urlencoded)

Εκτελέστε το πρόγραμμα και μελετήστε την έξοδο.  
+ Πώς φαίνονται τα δεδομένα της φόρμας στο αποτέλεσμα του httpbin.org/post;
+ Πώς αλλάζει το Content-Type όταν προσθέτετε αρχεία;
+ Ποια πεδία θεωρούνται “form fields” και ποια “file uploads”;

**Ερωτήσεις Κατανόησης**
+ Ποια είναι η διαφορά μεταξύ των data= και files= παραμέτρων στο requests.post();
+ Γιατί το Content-Type αλλάζει αυτόματα σε multipart/form-data όταν ανεβάζουμε αρχεία;
+ Ποια πλεονεκτήματα έχει η μορφή multipart/form-data σε σχέση με την application/x-www-form-urlencoded;
+ Πώς θα μπορούσατε να στείλετε περισσότερα από ένα αρχεία στο ίδιο αίτημα;
+ Τι θα συμβεί αν προσπαθήσετε να στείλετε δυαδικό αρχείο (.jpg, .zip) χωρίς να καθορίσετε τύπο MIME;


<details>
    <b>Κώδικας:</b>

    ```python
    import requests

    # Traditional form data
    form_data = {
        'username': 'student123',
        'password': 'secretpassword',
        'email': 'student@university.gr',
        'remember_me': 'on',
        'terms_accepted': 'true'
    }

    print("=== Form Data POST ===")
    response = requests.post('https://httpbin.org/post', data=form_data)
    print(f"Status: {response.status_code}")
    print(f"Content-Type: {response.request.headers.get('content-type')}")
    print(f"Form data received: {response.json()['form']}")

    # File upload simulation
    print("\n=== File Upload Simulation ===")
    files = {
        'assignment': ('homework.txt', 'This is my homework content', 'text/plain'),
        'screenshot': ('screen.png', b'\x89PNG\r\n\x1a\n...', 'image/png')
    }
    additional_data = {
        'student_id': 'CS2021123',
        'course': 'Web Technologies'
    }

    upload_response = requests.post('https://httpbin.org/post', 
                                files=files, 
                                data=additional_data)

    print(f"Upload status: {upload_response.status_code}")
    print(f"Files received: {list(upload_response.json()['files'].keys())}")
    print(f"Form data: {upload_response.json()['form']}")

    # Compare Content-Types
    print("\n=== Content-Type Comparison ===")
    print(f"JSON POST Content-Type: application/json")
    print(f"Form POST Content-Type: application/x-www-form-urlencoded")
    print(f"File Upload Content-Type: multipart/form-data")
    ```
</details>


---

## **Άσκηση 7: Error Handling Basics**
**Στόχος**: Διαχείριση HTTP errors και exceptions

Εκτελέστε το πρόγραμμα και μελετήστε την έξοδο.  

**Ερωτήσεις Κατανόησης**
+ Ποια είναι η διαφορά ανάμεσα στα σφάλματα HTTPError και ConnectionError;
+ Τι σημαίνει το raise_for_status() και γιατί είναι χρήσιμο;
+ Πώς λειτουργεί η παράμετρος timeout;
+ Τι θα συμβεί αν αφαιρέσουμε τον έλεγχο try/except;
+ Τι είδους εξαιρέσεις χειρίζεται η requests.exceptions.RequestException;

<details>
    <b>Κώδικας:</b>

    ```python
    import requests
    from requests.exceptions import RequestException

    # Test different error scenarios
    test_urls = {
        'valid': 'https://httpbin.org/json',
        'not_found': 'https://httpbin.org/status/404',
        'server_error': 'https://httpbin.org/status/500',
        'timeout_test': 'https://httpbin.org/delay/10',
        'invalid_domain': 'https://this-domain-does-not-exist-12345.com'
    }

    def test_url(name, url):
        print(f"\n=== Testing {name.upper()} ===")
        try:
            response = requests.get(url, timeout=3)
            print(f"✅ Status: {response.status_code}")
            
            # Check for HTTP errors (4xx, 5xx)
            response.raise_for_status()
            print("✅ No HTTP errors")
            
            # Try to parse JSON
            try:
                data = response.json()
                print(f"✅ JSON parsed successfully: {len(data)} items")
            except ValueError:
                print("⚠️  Response is not valid JSON")
                
        except requests.exceptions.HTTPError as e:
            print(f"❌ HTTP Error: {e}")
            print(f"   Status Code: {response.status_code}")
            
        except requests.exceptions.ConnectionError:
            print(f"❌ Connection Error: Cannot reach server")
            
        except requests.exceptions.Timeout:
            print(f"❌ Timeout: Request took longer than 3 seconds")
            
        except requests.exceptions.RequestException as e:
            print(f"❌ Request Error: {e}")

    # Test all scenarios
    for name, url in test_urls.items():
        test_url(name, url)

    print(f"\n=== Summary ===")
    print("✅ = Success, ⚠️ = Warning, ❌ = Error")
    ```
</details>

---

## **Άσκηση 8: Timeout Configuration**
**Στόχος**: Κατανόηση και διαχείριση timeouts

Εκτελέστε το πρόγραμμα και μελετήστε την έξοδο.

**Ερωτήσεις Κατανόησης**
+ Ποια η διαφορά μεταξύ connect και read timeout;
+ Γιατί είναι επικίνδυνο να μην οριστεί timeout σε HTTP αιτήματα;
+ Πώς μπορούμε να μετρήσουμε τη διάρκεια ενός HTTP αιτήματος σε Python;
+ Τι τύπους εξαιρέσεων χειρίζεται η requests σε περίπτωση καθυστερήσεων;
+ Πώς οι σωστές ρυθμίσεις timeout βελτιώνουν την αξιοπιστία και απόδοση ενός client;

<details>
    <b>Κώδικας:</b>

    ```
    import requests
    import time
    from datetime import datetime

    def test_timeout_scenarios():
        scenarios = [
            ('No timeout (dangerous)', None),
            ('Short timeout (3s)', 3),
            ('Long timeout (30s)', 30),
            ('Tuple timeout (connect=2, read=10)', (2, 10)),
        ]
        
        test_url = 'https://httpbin.org/delay/5'  # 5 second delay
        
        for description, timeout_value in scenarios:
            print(f"\n=== {description} ===")
            start_time = datetime.now()
            
            try:
                if timeout_value is None:
                    print("⚠️  SKIPPING no-timeout test for safety")
                    continue
                    
                response = requests.get(test_url, timeout=timeout_value)
                end_time = datetime.now()
                duration = (end_time - start_time).total_seconds()
                
                print(f"✅ Success in {duration:.2f} seconds")
                print(f"   Status: {response.status_code}")
                
            except requests.exceptions.Timeout:
                end_time = datetime.now()
                duration = (end_time - start_time).total_seconds()
                print(f"❌ Timeout after {duration:.2f} seconds")
                
            except Exception as e:
                print(f"❌ Error: {e}")

    def demonstrate_best_practices():
        print(f"\n=== Best Practices Demo ===")
        
        # Good timeout configuration
        CONNECT_TIMEOUT = 3  # 3 seconds for connection
        READ_TIMEOUT = 10    # 10 seconds for reading
        
        urls = [
            'https://httpbin.org/json',           # Fast response
            'https://httpbin.org/delay/2',        # Medium response
            'https://httpbin.org/delay/8',        # Slow response
        ]
        
        for url in urls:
            try:
                start = time.time()
                response = requests.get(url, timeout=(CONNECT_TIMEOUT, READ_TIMEOUT))
                duration = time.time() - start
                print(f"✅ {url}: {response.status_code} in {duration:.2f}s")
            except requests.exceptions.Timeout:
                print(f"⏰ {url}: Timed out")

    test_timeout_scenarios()
    demonstrate_best_practices()
    ```
</details>

---

## **Άσκηση 9: JSON Processing**
**Στόχος**: Εργασία με JSON responses από APIs

**Αναπτύξτε ένα πρόγραμμα Python που:** 
+ Διαβάζει μονάδες χρήστη από το JSONPlaceholder API (/users/1).
+ Πλοηγείται με ασφάλεια σε nested JSON fields.
+ Διαχειρίζεται JSON arrays, υπολογίζει σύνολα, φιλτράρει δεδομένα και εμφανίζει στατιστικά.
+ Χρησιμοποιεί συναρτήσεις για ασφαλή πρόσβαση σε πεδία που μπορεί να λείπουν.

**Ερωτήσεις Κατανόησης**
+ Τι είναι nested JSON και πώς μπορούμε να πλοηγηθούμε σε αυτά τα πεδία με Python;
+ Ποιο το όφελος της συνάρτησης safe_get έναντι άμεσης πρόσβασης (user['company']['name']);
+ Πώς μπορούμε να φιλτράρουμε JSON arrays για συγκεκριμένες τιμές (π.χ. email domains);
+ Πώς μπορούμε να υπολογίσουμε στατιστικά ή σύνολα από JSON arrays (π.χ. unique cities);
+ Τι γίνεται αν ένα πεδίο λείπει στο JSON από το API;

<details>
    <b>Κώδικας:</b>

    ```python
    import requests
    import json

    def explore_json_api():
        print("=== JSON Placeholder API Exploration ===")
        
        # Fetch user data
        response = requests.get('https://jsonplaceholder.typicode.com/users/1')
        user = response.json()
        
        print("=== Basic User Information ===")
        print(f"Name: {user['name']}")
        print(f"Email: {user['email']}")
        print(f"Phone: {user['phone']}")
        
        # Navigate nested JSON
        print("\n=== Address Information ===")
        address = user['address']
        print(f"Street: {address['street']} {address['suite']}")
        print(f"City: {address['city']}")
        print(f"Zipcode: {address['zipcode']}")
        
        # Deeply nested data
        geo = address['geo']
        print(f"Coordinates: {geo['lat']}, {geo['lng']}")
        
        # Company information
        print("\n=== Company Information ===")
        company = user['company']
        print(f"Company: {company['name']}")
        print(f"Catchphrase: {company['catchPhrase']}")

    def safe_json_navigation():
        print("\n=== Safe JSON Navigation ===")
        
        def safe_get(data, *keys, default=None):
            """Safely navigate nested dictionaries"""
            for key in keys:
                if isinstance(data, dict) and key in data:
                    data = data[key]
                else:
                    return default
            return data
        
        response = requests.get('https://jsonplaceholder.typicode.com/users/1')
        user = response.json()
        
        # Safe navigation examples
        company_name = safe_get(user, 'company', 'name', default='Unknown Company')
        geo_lat = safe_get(user, 'address', 'geo', 'lat', default='0')
        website = safe_get(user, 'website', default='No website')
        
        print(f"Company: {company_name}")
        print(f"Latitude: {geo_lat}")
        print(f"Website: {website}")
        
        # Handle missing data
        missing_field = safe_get(user, 'social_media', 'twitter', default='Not provided')
        print(f"Twitter: {missing_field}")

    def work_with_json_arrays():
        print("\n=== Working with JSON Arrays ===")
        
        # Fetch all users
        response = requests.get('https://jsonplaceholder.typicode.com/users')
        users = response.json()
        
        print(f"Total users: {len(users)}")
        
        # Process array data
        print("\n=== Users Summary ===")
        for user in users[:3]:  # First 3 users
            print(f"ID: {user['id']}, Name: {user['name']}, City: {user['address']['city']}")
        
        # Data analysis
        cities = [user['address']['city'] for user in users]
        unique_cities = set(cities)
        print(f"\nUnique cities: {', '.join(unique_cities)}")
        
        # Filter data
        gmail_users = [user for user in users if 'gmail' in user['email'].lower()]
        print(f"Gmail users: {len(gmail_users)}")

    explore_json_api()
    safe_json_navigation()
    work_with_json_arrays()
    ```
</details>
