# 📰 Noticias IA – Sistema de Scraping Automático con ML Supervisado

**Sistema completo de extracción de noticias, calificación automática y aprendizaje de máquina supervisado.**

Bot automático cada 6 horas + WebApp Flask privada para calificar noticias + Modelo ML que aprende según tus puntuaciones.

---

## ✨ Características

✅ **Bot Autómático (GitHub Actions)** – Se ejecuta cada 6 horas extrayendo noticias automáticamente  
✅ **WebApp Privada (Flask)** – Panel simple con acceso por clave única, sin usuarios  
✅ **Calificación de Noticias** – Puntúa del 1 al 10, el sistema aprende automáticamente  
✅ **Agregar Portales Dinámicamente** – Pega la URL, testea que sea extraíble, y guarda  
✅ **Machine Learning Supervisado** – TF-IDF + Ridge, modelo se reentren automaticamente  
✅ **Base de Datos Centralizada** – Bot y WebApp se comunican SOLO vía BD  
✅ **Sin Dependencias Externas** – SQLite por defecto, puedes cambiar a PostgreSQL  
✅ **Producción Ready** – Gunicorn, variables de entorno, logging  

---

## 🏗️ Estructura de Carpetas

```
noticias-ia-scraper/
├── bot/
│   ├── __init__.py
│   ├── scraper.py           # Lógica de extracción
│   ├── ml.py                # Entrenamiento del modelo
│   ├── run.py               # Punto de entrada del bot
│   └── utils.py             # Utilidades
│
├── webapp/
│   ├── __init__.py
│   ├── app.py               # Creación de app Flask
│   ├── auth.py              # Autenticación por clave
│   ├── routes.py            # Rutas de la aplicación
│   ├── forms.py             # Formularios WTF
│   ├── templates/
│   │   ├── base.html
│   │   ├── login.html
│   │   ├── home.html        # Panel de noticias
│   │   ├── portales.html    # Agregar/gestionar portales
│   │   └── noticias.html
│   └── static/
│       └── style.css
│
├── db/
│   ├── models.py            # Modelos SQLAlchemy
│   └── db_init.py           # Inicialización de BD
│
├── config/
│   └── config.py            # Configuración global
│
├── .github/workflows/
│   └── bot.yml              # GitHub Actions (cada 6h)
│
├── main.py                  # Punto de entrada de la WebApp
├── requirements.txt
├── .env.example
└── README.md (este archivo)
```

---

## 🚀 Instalación Rápida (5 minutos)

### 1. Clonar el repositorio

```bash
git clone https://github.com/TU_USUARIO/noticias-ia-scraper.git
cd noticias-ia-scraper
```

### 2. Crear variables de entorno

```bash
cp .env.example .env
# Edita .env con tus valores:
# SECRET_KEY = clave aleatoria segura
# ADMIN_PASSWORD = contraseña para acceder a la web
# DB_URL = sqlite:///noticias.db (o tu BD de producción)
```

### 3. Crear y activar entorno virtual

```bash
python3 -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate
```

### 4. Instalar dependencias

```bash
pip install -r requirements.txt
```

### 5. Inicializar base de datos

```bash
python db/db_init.py
```

### 6. Ejecutar la WebApp

```bash
python main.py
# Accede a http://localhost:5000
```

### 7. Ejecutar el Bot manualmente (opcional)

```bash
python bot/run.py
```

---

## 🎮 Uso de la WebApp

### 📧 Login
- URL: `http://localhost:5000`
- Introduce tu `ADMIN_PASSWORD` del archivo `.env`
- Sin usuarios, sin registros, sin correos

### 📰 Panel de Noticias
- Se cargan noticias automáticamente desde los portales activos
- **Califica del 1 al 10** haciendo clic en los números
- El sistema aprende según tus puntuaciones

### 🔗 Agregar Portales
1. Ve a la sección "Portales"
2. **Pega la URL** del portal (ej: https://www.eluniversal.com.ve)
3. Selecciona la **categoría**
4. Haz clic en **"TESTEAR EXTRACCIÓN"**
   - Si es exitoso → se muestra cantidad de títulos y ejemplos
   - Si falla → ve el motivo (anti-bot, JS dinámico, etc.)
5. Si el test es OK, haz clic en **"GUARDAR"**
6. El portal se agregará y el bot lo incluirá en la próxima ejecución

### 🗑️ Eliminar Portales
- En la lista de portales, haz clic en el botón "Eliminar"

---

## 🤖 Flujo del Bot Automático

Cada 6 horas (configurable en `.github/workflows/bot.yml`):

1. **Lee portales activos** desde la BD
2. **Extrae titulares** de cada portal
3. **Evita duplicados** checando títulos y enlaces
4. **Usa ML** para asignar score predictivo de relevancia (1-10)
5. **Guarda** noticias en la BD
6. **Reentren el modelo** si hay nuevas calificaciones tuyas

---

## 🧠 Machine Learning

- **Algoritmo**: TF-IDF (vectorización) + Ridge (regresión)
- **Entrada**: Título + Resumen de noticia
- **Salida**: Score predicho de relevancia (1-10)
- **Entrenamiento**: Supervisado con tus calificaciones manuales
- **Reentrenamiento**: Automático cada ciclo del bot (si hay nuevos votos)
- **Persistencia**: Modelo guardado en `bot/modelo.pkl`

---

## ⚙️ Configuración en Producción

### 1. Cambiar a PostgreSQL

```bash
# Instala el driver
pip install psycopg2-binary

# En .env:
DB_URL=postgresql://user:password@localhost:5432/noticias_ia
```

### 2. Deploy con Gunicorn

```bash
gunicorn main:app --workers 4 --bind 0.0.0.0:8000
```

### 3. Configurar GitHub Secrets

En `Settings > Secrets and variables > Actions`, agrega:

```
DB_URL = tu_url_bd_produccion
ADMIN_PASSWORD = tu_contrasena
SECRET_KEY = clave_aleatoria
```

### 4. Workflow automático en GitHub

El archivo `.github/workflows/bot.yml` ya está configurado para ejecutarse cada 6 horas.

---

## 📊 Base de Datos

### Tablas principales

**fuentes** (portales de noticias)
```
id (PK) | url | categoria | activa
```

**noticias** (titulares extraídos)
```
id (PK) | titulo | resumen | link | fecha | fuente (FK) | score_predicho | entrenada
```

**votos** (calificaciones manuales)
```
id (PK) | noticia_id (FK) | calificacion_1_a_10
```

**modelo_estado** (metadata del entrenamient)
```
id (PK) | fecha_entrenamiento
```

---

## 🔒 Seguridad

- ✅ Acceso por contraseña única (sin usuarios)
- ✅ Sesiones Flask seguras
- ✅ Variables de entorno (no hardcodeadas)
- ✅ CSRF protection en formularios
- ✅ SQL injection prevención (ORM SQLAlchemy)
- ⚠️ **En producción**: Usa HTTPS, cambiar `SECRET_KEY`, usar BD segura

---

## 🐛 Troubleshooting

### "No puedo acceder a la web"
- Verifica que `ADMIN_PASSWORD` en `.env` es correcto
- Checa que el puerto 5000 esté disponible

### "El bot no extrae noticias"
- Verifica que los portales están activos en la BD
- Revisa logs de la GitHub Actions
- Algunas webs tienen protección anti-bot

### "El modelo no mejora"
- Necesitas al menos 10-15 noticias calificadas para entrenar
- Verifica que estás calificando noticias (debe marcar "entrenada")

### "Error de conexión a BD"
- Checa que el archivo `noticias.db` exista
- Si usas PostgreSQL, verifica credenciales en `DB_URL`

---

## 📝 Ejemplo de uso completo

```bash
# 1. Clonar y setup
git clone TU_REPO && cd noticias-ia-scraper
cp .env.example .env
# Edita .env
pip install -r requirements.txt
python db/db_init.py

# 2. Ejecutar web
python main.py
# Accede a http://localhost:5000 con tu contraseña

# 3. Agregar portales
# - Ve a /portales
# - Pega: https://eluniversal.com.ve
# - Testea
# - Guarda

# 4. Ejecutar bot manualmente
python bot/run.py
# Las noticias aparecen en /home

# 5. Calificar noticias
# - Haz clic en números 1-10 para cada noticia
# - El modelo aprende automáticamente

# 6. GitHub Actions automático
# - Bot corre cada 6h sin que hagas nada
```

---

## 🛠️ API de Rutas (WebApp)

| Ruta | Método | Descripción |
|------|--------|-------------|
| `/` | GET/POST | Login |
| `/home` | GET | Panel de noticias para calificar |
| `/portales` | GET/POST | Gestionar portales |
| `/noticia/<id>/califica/<score>` | GET | Calificar noticia (1-10) |
| `/test_portal` | POST | Testear extracción de portal |
| `/agrega_portal` | POST | Guardar nuevo portal |
| `/elimina_portal/<id>` | POST | Eliminar portal |
| `/logout` | GET | Cerrar sesión |

---

## 📦 Dependencias Principales

- **Flask** – Framework web
- **SQLAlchemy** – ORM para BD
- **Scikit-Learn** – ML (TF-IDF + Ridge)
- **Newspaper3k** – Extracción de noticias
- **APScheduler** – Scheduling de tareas
- **Gunicorn** – Servidor WSGI producción

---

## 🌐 Portales Recomendados para Pruebas

```
Política: https://www.eluniversal.com.ve
Tecnología: https://www.techcrunch.com
Deportes: https://www.espn.com
Economía: https://www.bloomberg.com
Crimen: https://www.bbc.com
```

---

## 📄 Licencia

MIT License – Úsalo libremente

---

## 🤝 Contribuir

Encontraste un bug o tienes una idea? Abre un [Issue](https://github.com/TU_USUARIO/noticias-ia-scraper/issues) o un [Pull Request](https://github.com/TU_USUARIO/noticias-ia-scraper/pulls).

---

## 📞 Soporte

- Abre un Issue en GitHub
- Revisa la sección Troubleshooting
- Verifica los logs de GitHub Actions

---

**Hecho con ❤️ para scrapers y ML enthusiasts**
