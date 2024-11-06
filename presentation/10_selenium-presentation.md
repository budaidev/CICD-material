class: inverse, center, middle

# Selenium E2E Testing Python-nal

---

class: inverse, center, middle

# Mi a Selenium?

---

## Selenium áttekintés

* Nyílt forráskódú böngésző automatizációs eszköz
* End-to-End (E2E) teszteléshez
* Multi-browser támogatás
* Python, Java, C#, stb. nyelveken használható
* Valós böngésző interakciók szimulálása
* Cross-platform támogatás
* Beépíthető CI/CD pipeline-okba

---

## Pytest és Selenium setup

### Környezet előkészítés:
```bash
# Függőségek telepítése
pip install selenium pytest pytest-selenium

# WebDriver telepítése
pip install webdriver-manager
```

### Alapvető imports:
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
```

---

## WebDriver beállítás

### Chrome WebDriver inicializálás:
```python
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

def get_driver():
    service = Service(ChromeDriverManager().install())
    options = webdriver.ChromeOptions()
    options.add_argument('--headless')  # Headless mód
    driver = webdriver.Chrome(service=service, options=options)
    return driver
```

---

## Első Selenium teszt

```python
def test_google_search():
    # Driver inicializálás
    driver = get_driver()
    
    try:
        # Oldal betöltése
        driver.get("https://www.google.com")
        
        # Keresőmező megkeresése és kitöltése
        search_box = driver.find_element(By.NAME, "q")
        search_box.send_keys("Selenium testing")
        search_box.submit()
        
        # Várunk az eredményekre
        assert "Selenium testing" in driver.title
        
    finally:
        driver.quit()
```

---

## Pytest fixture-ök

### WebDriver fixture:
```python
import pytest

@pytest.fixture
def browser():
    driver = get_driver()
    yield driver
    driver.quit()

def test_with_fixture(browser):
    browser.get("https://example.com")
    assert "Example" in browser.title
```

### Page Object fixture:
```python
@pytest.fixture
def login_page(browser):
    return LoginPage(browser)
```

---

## DOM és WebElement

### Elem keresési stratégiák:
```python
# ID alapján
element = driver.find_element(By.ID, "login")

# CSS selector
element = driver.find_element(By.CSS_SELECTOR, ".login-form")

# XPath
element = driver.find_element(By.XPATH, "//button[@type='submit']")

# Név alapján
element = driver.find_element(By.NAME, "username")

# Class név alapján
element = driver.find_element(By.CLASS_NAME, "btn-primary")
```

---

## WebElement műveletek

### Interakciók:
```python
# Kattintás
element.click()

# Szöveg beírása
element.send_keys("test@example.com")

# Tartalom törlése
element.clear()

# Attribútum lekérése
value = element.get_attribute("value")

# CSS tulajdonság lekérése
color = element.value_of_css_property("background-color")
```

---

## Assertion példák

### WebElement assertek:
```python
# Elem láthatóság
assert element.is_displayed()

# Elem engedélyezettség
assert element.is_enabled()

# Szöveg tartalom
assert element.text == "Expected Text"

# Attribútum érték
assert element.get_attribute("class") == "active"

# Elem létezés
assert len(driver.find_elements(By.CLASS_NAME, "error")) == 0
```

---

## Várakozási stratégiák

### Explicit wait:
```python
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

# Várunk egy elem megjelenésére
element = WebDriverWait(driver, 10).until(
    EC.presence_of_element_located((By.ID, "myDynamicElement"))
)

# Várunk egy elem kattinthatóságára
element = WebDriverWait(driver, 10).until(
    EC.element_to_be_clickable((By.ID, "myButton"))
)
```

---

## Parametrizált tesztek

```python
@pytest.mark.parametrize("username,password", [
    ("user1", "pass1"),
    ("user2", "pass2"),
    ("user3", "pass3")
])
def test_login(browser, username, password):
    browser.get("https://example.com/login")
    
    # Username kitöltése
    username_input = browser.find_element(By.ID, "username")
    username_input.send_keys(username)
    
    # Password kitöltése
    password_input = browser.find_element(By.ID, "password")
    password_input.send_keys(password)
    
    # Form beküldése
    submit_button = browser.find_element(By.ID, "submit")
    submit_button.click()
    
    # Eredmény ellenőrzése
    assert browser.current_url == "https://example.com/dashboard"
```

---

## Page Object Pattern

```python
class LoginPage:
    def __init__(self, driver):
        self.driver = driver
        self.url = "https://example.com/login"
        
    def navigate(self):
        self.driver.get(self.url)
        
    def login(self, username, password):
        self.driver.find_element(By.ID, "username").send_keys(username)
        self.driver.find_element(By.ID, "password").send_keys(password)
        self.driver.find_element(By.ID, "submit").click()
        
    def get_error_message(self):
        return self.driver.find_element(By.CLASS_NAME, "error").text

def test_login_with_page_object(browser):
    login_page = LoginPage(browser)
    login_page.navigate()
    login_page.login("user", "pass")
    assert "Dashboard" in browser.title
```

---

## Python Web App tesztelése

### Komplett teszt példa:
```python
class TestWebApp:
    @pytest.fixture
    def app_url(self):
        return "http://localhost:5000"
    
    def test_home_page(self, browser, app_url):
        browser.get(app_url)
        assert "Welcome" in browser.title
        
    def test_create_item(self, browser, app_url):
        browser.get(f"{app_url}/create")
        
        # Form kitöltése
        name_input = browser.find_element(By.NAME, "name")
        name_input.send_keys("Test Item")
        
        # Form beküldése
        submit = browser.find_element(By.CSS_SELECTOR, "button[type='submit']")
        submit.click()
        
        # Eredmény ellenőrzése
        success_message = browser.find_element(By.CLASS_NAME, "alert-success")
        assert "Item created successfully" in success_message.text
```

---

## Best Practices

* Page Object Pattern használata
* Explicit wait preferálása
* Megfelelő elem lokátorok választása
* Hibakezelés és cleanup
* Test isolation
* Screenshot készítés hibák esetén
* Headless mód CI/CD-ben
* Logolás és riportolás

---

class: inverse, center, middle

# Kérdések?

