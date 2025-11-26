# Instalacion Rapida paso a paso

## Requisitos
- Python 3.8+
- Git
- Pip

## Pasos

1. git clone https://github.com/RandyCorrea/noticias-ia-scraper.git
2. cd noticias-ia-scraper
3. cp .env.example .env
4. Edita .env con tu contrasena
5. python3 -m venv venv
6. source venv/bin/activate (Mac/Linux) o venv\Scripts\activate (Windows)
7. pip install -r requirements.txt
8. python db/db_init.py
9. python main.py

Accede a http://localhost:5000 con tu contrasena

En otra terminal, ejecuta:
python bot/run.py

La webapp y bot ya estan listos para usar.
