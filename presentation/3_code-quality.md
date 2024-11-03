# Python Clean Code és Kódellenőrzés

---

## PEP8 alapok

* Python Enhancement Proposal 8
* Python kód stílus útmutató
* Fontos szabályok:
  * 4 szóköz indentálás
  * Maximum 79 karakter sorhossz
  * Import rendezési szabályok
  * Whitespace használata
  * Naming conventions

---

## PEP8 példák

❌ Nem PEP8 kompatibilis:
```python
def calculate_something( x,y ):
    return x+y

class myClass:
    def __init__(self,name):
        self.name=name
```

✅ PEP8 kompatibilis:
```python
def calculate_something(x, y):
    return x + y

class MyClass:
    def __init__(self, name):
        self.name = name
```

---

## Type Hints

* Python 3.5+ típus annotációk
* Jobb kód dokumentáció
* IDE támogatás
* Statikus típusellenőrzés

```python
def calculate_age(birth_year: int) -> int:
    current_year: int = 2024
    return current_year - birth_year

# Összetett típusok
from typing import List, Dict, Optional

def process_users(users: List[str]) -> Dict[str, int]:
    return {user: len(user) for user in users}

def find_user(user_id: int) -> Optional[str]:
    # ...
```

---

## Kódellenőrző eszközök

---

### AutoPEP8
* **Cél**: Automatikus kódformázó a PEP 8 stílus útmutató szerint
* **Főbb funkciók**:
  * Automatikusan javítja a behúzásokat
  * Beállítja az operátorok és vesszők körüli szóközöket
  * Sorhosszúságot a PEP 8 szabványhoz igazítja (79 karakter)
  * Normalizálja az üres sorokat
* **Használati eset**: Ideális a kódformázás automatikus tisztításához manuális beavatkozás nélkül
* **Parancs**: `autopep8 --in-place file.py`

---

### isort
* **Cél**: Python import utasítások rendszerezője
* **Főbb funkciók**:
  * Automatikusan rendezi és formázza az import utasításokat
  * Csoportosítja az importokat típus szerint (standard könyvtár, külső, helyi)
  * Ábécé sorrendbe rendezi a csoportokon belül
  * Eltávolítja a duplikált importokat
* **Használati eset**: Konzisztens import szervezés fenntartása a projektekben
* **Parancs**: `isort file.py`

---

### Pylint
* **Cél**: Átfogó Python kód elemző
* **Főbb funkciók**:
  * Ellenőrzi a kódolási szabványokat
  * Keresi a kód szagokat (code smells)
  * Megtalálja a lehetséges hibákat/bugokat
  * Refaktorálási javaslatokat tesz
  * Részletes kódminőségi jelentéseket készít pontszámokkal
* **Használati eset**: Mélyreható kódelemzés és magas kódminőségi szabványok fenntartása
* **Parancs**: `pylint file.py`

---

### Flake8
* **Cél**: Python kód stílus ellenőrző és linter
* **Főbb funkciók**:
  * Kombinálja a PyFlakes-t (statikus kódelemzéshez)
  * pycodestyle (PEP 8 ellenőrzés)
  * Mccabe (kód komplexitás ellenőrző)
  * Könnyebb súlyú, mint a Pylint
  * Gyorsabb futási idők
* **Használati eset**: Gyors kódminőség ellenőrzések fejlesztés közben
* **Parancs**: `flake8 file.py`

---

### Mypy
* **Cél**: Statikus típusellenőrző Pythonhoz
* **Főbb funkciók**:
  * Ellenőrzi a típus annotációkat
  * Típushibákat észlel futtatás előtt
  * Támogatja a fokozatos típusozást
  * Segít korán észlelni a hibákat
  * Javítja a kód dokumentációt
* **Használati eset**: Típusbiztonság hozzáadása Python projektekhez
* **Parancs**: `mypy file.py`

---

## Legjobb gyakorlat

Használjuk ezeket az eszközöket együtt egy CI/CD pipeline-ban:

1. Kód formázása AutoPEP8-cal
2. Importok rendezése isort-tal
3. Flake8 futtatása gyors stílus ellenőrzéshez
4. Pylint használata mélyebb elemzéshez
5. Típusok ellenőrzése Mypy-val

---

## Lint eszközök

### Pylint
* Átfogó kódelemzés
* Kód minőség értékelés
* Hibakeresés
```bash
pip install pylint
pylint file.py
```

---

### Flake8
* PEP8 ellenőrzés
* Szintaktikai ellenőrzés
* Komplexitás ellenőrzés
```bash
pip install flake8
flake8 file.py
```

---

## Mypy - Statikus típusellenőrzés

* Type hints ellenőrzése
* Típus kompatibilitás
* Korai hibafelderítés

```python
# example.py
def greet(name: str) -> str:
    return f"Hello, {name}"

result: str = greet(42)  # Mypy hiba!
```

```bash
pip install mypy
mypy example.py
```

---

## Kódlefedettség mérése

* Coverage.py használata
* Tesztek által érintett kód mérése
* HTML riport generálás

```bash
pip install coverage
coverage run -m pytest
coverage report
coverage html
```

Példa riport:
```
Name                 Stmts   Miss  Cover
----------------------------------------
myproject.py            20      4    80%
tests/test_myproject.py 15      0   100%
----------------------------------------
TOTAL                   35      4    89%
```

---

## Gyakorlati példa: Kód tisztítás

Eredeti kód:
```python
def calc(x,y,type):
    if(type=="add"):
        return x+y
    elif(type=="subtract"):
        return x-y
```

---

Tisztított kód:
```python
from enum import Enum
from typing import Union

class OperationType(Enum):
    ADD = "add"
    SUBTRACT = "subtract"

def calculate(
    x: Union[int, float],
    y: Union[int, float],
    operation: OperationType
) -> Union[int, float]:
    """Perform arithmetic operation on two numbers.
    
    Args:
        x: First number
        y: Second number
        operation: Type of arithmetic operation
    
    Returns:
        Result of the arithmetic operation
    """
    if operation == OperationType.ADD:
        return x + y
    elif operation == OperationType.SUBTRACT:
        return x - y
    raise ValueError(f"Unsupported operation: {operation}")
```

---

## CI/CD integráció

```yaml
# .github/workflows/python-check.yml
name: Python Code Quality
on: [push]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python
      uses: actions/setup-python@v2
    - name: Install dependencies
      run: |
        pip install flake8 mypy pylint isort
    - name: Run checks
      run: |
        flake8 .
        mypy .
        pylint src/
        isort --check .
```

---

## Pre-commit hooks

```yaml
# .pre-commit-config.yaml
repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v3.4.0
    hooks:
    -   id: trailing-whitespace
    -   id: end-of-file-fixer
    -   id: check-yaml
    -   id: check-added-large-files

-   repo: https://github.com/pycqa/isort
    rev: 5.10.1
    hooks:
    -   id: isort

-   repo: https://github.com/pycqa/flake8
    rev: 4.0.1
    hooks:
    -   id: flake8
```

---

## VS Code integráció

```json
{
    "python.linting.pylintEnabled": true,
    "python.linting.flake8Enabled": true,
    "python.linting.mypyEnabled": true,
    "python.formatting.provider": "autopep8",
    "editor.formatOnSave": true,
    "python.linting.enabled": true,
    "[python]": {
        "editor.codeActionsOnSave": {
            "source.organizeImports": true
        }
    }
}
```
