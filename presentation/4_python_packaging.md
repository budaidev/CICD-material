# Python Alkalmazás és Library Csomagolás

---

## Python Alkalmazás vs Library

### Alkalmazás
* Közvetlenül futtatható
* Végfelhasználóknak készül
* Főprogram és belépési pont
* Tipikusan: CLI tools, web apps

### Library
* Más Python kódból importálható
* Fejlesztőknek készül
* Újrafelhasználható komponensek
* Tipikusan: SDK-k, keretrendszerek

---

## Csomagolási eszközök

* **setuptools**: Standard csomagolás
* **wheel**: Bináris disztribúció
* **pip**: Csomag telepítő
* **twine**: PyPI feltöltés
* **poetry**: Modern dependency management
* **pyproject.toml**: Új generációs konfiguráció

---

## Library projekt struktúra

```
my_library/
├── src/
│   └── my_library/
│       ├── __init__.py
│       ├── core.py
│       └── utils.py
├── tests/
│   ├── __init__.py
│   └── test_core.py
├── setup.py
├── pyproject.toml
├── README.md
└── LICENSE
```

---

## setup.py alapok

```python
from setuptools import setup, find_packages

setup(
    name="my-library",
    version="0.1.0",
    packages=find_packages(where="src"),
    package_dir={"": "src"},
    
    # Metaadatok
    author="Kovács János",
    author_email="kovacs.janos@example.com",
    description="Példa Python library",
    long_description=open("README.md").read(),
    long_description_content_type="text/markdown",
    
    # Függőségek
    install_requires=[
        "requests>=2.25.0",
        "pandas>=1.2.0"
    ],
    
    # Python verzió
    python_requires=">=3.7",
)
```

---

## Függőségek kezelése

### Install requires vs Extra requires

```python
setup(
    # ...
    install_requires=[
        "requests>=2.25.0",
    ],
    extras_require={
        'dev': [
            'pytest>=6.0',
            'flake8>=3.9.0',
        ],
        'docs': [
            'sphinx>=4.0.0',
        ],
    },
)
```

Telepítés:
```bash
pip install .[dev]  # Fejlesztői függőségekkel
```

---

## Nem Python fájlok kezelése

### MANIFEST.in használata

```
include README.md
include LICENSE
recursive-include src/my_library/templates *.html
recursive-include src/my_library/static *.css *.js
global-exclude *.pyc
```

### setup.py konfiguráció

```python
setup(
    # ...
    package_data={
        'my_library': [
            'templates/*.html',
            'static/*.css',
            'static/*.js',
        ],
    },
    include_package_data=True,
)
```

---

## CLI alkalmazás készítése

### Belépési pont definiálása

```python
# src/my_library/cli.py
import click

@click.command()
@click.option('--name', default='World',
              help='Who to greet')
def main(name):
    """Simple CLI tool example."""
    click.echo(f'Hello {name}!')

if __name__ == '__main__':
    main()
```

### setup.py konfiguráció

```python
setup(
    # ...
    entry_points={
        'console_scripts': [
            'my-tool=my_library.cli:main',
        ],
    },
)
```

---

## Package indexek

### PyPI (Python Package Index)
* Hivatalos Python csomag repository
* `pip install` alapértelmezett forrás

### TestPyPI
* Teszt környezet
* Gyakorláshoz, teszteléshez

---

## Twine használata

### Build és feltöltés

```bash
# Build
python setup.py sdist bdist_wheel

# Feltöltés TestPyPI-ra
twine upload --repository testpypi dist/*

# Feltöltés PyPI-ra
twine upload dist/*
```

### Konfiguráció (~/.pypirc)

```ini
[distutils]
index-servers =
    pypi
    testpypi

[pypi]
username = __token__
password = pypi-xxx...

[testpypi]
repository = https://test.pypi.org/legacy/
username = __token__
password = pypi-xxx...
```

---

## Modern csomagolás: pyproject.toml

```toml
[build-system]
requires = ["setuptools>=45", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "my-library"
version = "0.1.0"
description = "Példa Python library"
authors = [{name = "Kovács János", email = "kovacs.janos@example.com"}]
dependencies = [
    "requests>=2.25.0",
    "pandas>=1.2.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=6.0",
    "flake8>=3.9.0",
]

[project.scripts]
my-tool = "my_library.cli:main"
```

---

## Lokális fejlesztés

### Telepítés fejlesztői módban

```bash
# Projekt könyvtárában
pip install -e .
```

### Virtuális környezet használata

```bash
# Környezet létrehozása
python -m venv venv

# Aktiválás (Windows)
venv\Scripts\activate

# Aktiválás (Linux/Mac)
source venv/bin/activate
```

---

## Gyakori problémák és megoldások

❌ **Probléma**: Hiányzó `__init__.py`
✅ **Megoldás**: Minden package könyvtárba helyezzünk el egy (akár üres) `__init__.py` fájlt

❌ **Probléma**: Nem található a package
✅ **Megoldás**: Ellenőrizzük a `package_dir` és `packages` beállításokat

❌ **Probléma**: Nem működik a CLI parancs
✅ **Megoldás**: Telepítsük újra a csomagot `pip install -e .` paranccsal

---

## Best Practices

* Használjunk szemantikus verziózást
* Tartsuk naprakészen a függőségeket
* Dokumentáljuk a kódot és API-t
* Írjunk teszteket
* Használjunk CI/CD pipeline-t
* Kövessük a Python Packaging User Guide ajánlásait
* Használjunk type hinteket
* Készítsünk részletes README-t