# Εβδομάδα 4: Προχωρημένες Τεχνολογίες Ανάπτυξης Εφαρμογών Διαδικτύου


## Άσκηση 1: Basic Flask Application Setup

**Στόχος:** Δημιουργία βασικού Flask application ως single file.

**Απαιτήσεις:**
1. Δημιουργήστε ένα `app.py` με Flask instance
2. Υλοποιήστε 3 routes:
   - `/` - Επιστρέφει "Welcome to Flask Lab"
   - `/about` - Επιστρέφει πληροφορίες για το app, όπως name, version,human description, environment
   - `/status` - Επιστρέφει JSON με `{"status": "ok"}`
3. Τρέξτε το app σε debug mode

**Σκελετός κώδικα:**
<details> 

```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/')
def index():
    # TODO: Implement
    pass

if __name__ == '__main__':
    app.run(debug=True)
```
</details> 

**Έλεγχος:**
```bash
curl http://localhost:5000/
curl http://localhost:5000/about
curl http://localhost:5000/status
```

---

## Άσκηση 2: Configuration Management

**Στόχος:** Δημιουργία configuration system με διαφορετικά environments.

**Απαιτήσεις:**
1. Δημιουργήστε `config.py` με τρεις classes:
   - `DevelopmentConfig` (DEBUG=True, DATABASE_URI=sqlite)
   - `ProductionConfig` (DEBUG=False, DATABASE_URI from env)
   - `TestingConfig` (TESTING=True)
2. Κάθε config να έχει:
   - `SECRET_KEY`
   - `DATABASE_URI` (δημιουργήστε fake τύπου sqlite)
   - `DEBUG` (on/off)
3. Τροποποιήστε το `app.py` να φορτώνει config από environment variable

**Σκελετός κώδικα:**
<details> 

```python
# config.py
import os

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'base-secret-key'
    # TODO: Add more base config

class DevelopmentConfig(Config):
    # TODO: Implement
    pass

class ProductionConfig(Config):
    # TODO: Implement
    pass

config = {
    'development': DevelopmentConfig,
    'production': ProductionConfig,
    'testing': TestingConfig,
    'default': DevelopmentConfig
}
```
</details> 

**Έλεγχος:**
```python
# Στο Python shell
from app import app
print(app.config['DEBUG'])
print(app.config['DATABASE_URI'])
```

---


## Άσκηση 3: Application Factory Pattern

**Στόχος:** Μετατροπή single-file app σε application factory.

**Απαιτήσεις:**
1. Δημιουργήστε τη δομή αρχείων:
   ```
   myapp/
   ├── app/
   │   └── __init__.py
   ├── config.py
   └── run.py
   ```
2. Στο `app/__init__.py` υλοποιήστε `create_app(config_name)` function
3. Το `create_app` να:
   - Δημιουργεί Flask instance
   - Φορτώνει configuration
   - Επιστρέφει το app
4. Το `run.py` να καλεί το `create_app()` και να τρέχει το app

**Σκελετός κώδικα:**
<details> 

```python
# app/__init__.py
from flask import Flask
from config import config

def create_app(config_name='development'):
    app = Flask(__name__)
    app.config.from_object(config[config_name])
    
    # TODO: Register routes της προηγούμενης άσκησης
    
    return app

# run.py
import os
from app import create_app

config_name = os.environ.get('FLASK_ENV', 'development')
app = create_app(config_name)

if __name__ == '__main__':
    app.run()
```
</details> 

**Έλεγχος:**
```bash
export FLASK_ENV=development
python run.py
```

---

## Άσκηση 4: First Blueprint - Main Module

**Στόχος:** Δημιουργία πρώτου blueprint για main routes.

**Απαιτήσεις:**
1. Δημιουργήστε blueprint structure:
   ```
   app/
   ├── __init__.py
   └── main/
       ├── __init__.py
       └── routes.py
   ```
2. Στο `main/__init__.py` δημιουργήστε blueprint με όνομα 'main'
3. Στο `main/routes.py` υλοποιήστε:
   - `/` - Home page
   - `/health` - Health check endpoint (JSON)
4. Register το blueprint στο `create_app()`

**Σκελετός κώδικα:**
<details> 


```python
# app/main/__init__.py
from flask import Blueprint

bp = Blueprint('main', __name__)

from app.main import routes

# app/main/routes.py
from app.main import bp
from flask import jsonify

@bp.route('/')
def index():
    # TODO: Implement
    pass

@bp.route('/health')
def health():
    # TODO: Return JSON with status, timestamp
    pass

# app/__init__.py
def create_app(config_name='development'):
    app = Flask(__name__)
    # ... config ...
    
    from app.main import bp as main_bp
    app.register_blueprint(main_bp)
    
    return app
```

</details> 

**Έλεγχος:**
```bash
curl http://localhost:5000/health
```

---

## Άσκηση 5: API Blueprint με URL Prefix

**Στόχος:** Δημιουργία API blueprint με versioned endpoints.

**Απαιτήσεις:**
1. Δημιουργήστε `app/api/` blueprint
2. Register με URL prefix `/api/v1`
3. Υλοποιήστε endpoints:
   - `GET /api/v1/info` - API information
   - `GET /api/v1/echo?message=hello` - Echo service
4. Όλα τα responses να είναι JSON format

**Σκελετός κώδικα:**
<details> 

```python
# app/api/__init__.py
from flask import Blueprint

bp = Blueprint('api', __name__)

from app.api import routes

# app/api/routes.py
from app.api import bp
from flask import jsonify, request

@bp.route('/info')
def api_info():
    return jsonify({
        'version': '1.0',
        'endpoints': ['/info', '/echo']
    })

@bp.route('/echo')
def echo():
    # TODO: Get 'message' from query params
    # Return JSON with echoed message
    pass
```

</details> 

**Έλεγχος:**
```bash
curl http://localhost:5000/api/v1/info
curl "http://localhost:5000/api/v1/echo?message=hello"
```
