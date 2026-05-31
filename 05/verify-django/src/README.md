# Django Application with Docker Compose

Aplikacja Django uruchomiana w kontenerach Docker z bazą danych PostgreSQL.

## Wymagania wstępne

Aby uruchomić tę aplikację na czystej maszynie, potrzebujesz:

- **Docker** (wersja 20.10+) — [Instalacja](https://docs.docker.com/get-docker/)
- **Docker Compose** (wersja 1.29+) — zwykle instaluje się razem z Dockerem

### Weryfikacja instalacji

```bash
docker --version
docker-compose --version
```

---

## Szybki start

### 1. Przygotowanie zmiennych środowiskowych

Skopiuj plik `.env.example` do `.env`:

```bash
cp .env.example .env
```

Edytuj `.env` i dostosuj wartości do swoich potrzeb (opcjonalnie):

```env
SECRET_KEY=your-secret-key-here-change-this-in-production
DEBUG=1
ALLOWED_HOSTS=localhost,127.0.0.1,0.0.0.0

POSTGRES_DB=project-db
POSTGRES_USER=your-username
POSTGRES_PASSWORD=your-db-password
POSTGRES_HOST=db
```

### 2. Budowanie i uruchomienie aplikacji

Uruchom aplikację za pomocą Docker Compose:

```bash
docker-compose up -d
```

Flaga `-d` uruchamia kontenery w tle (detached mode).

### 3. Wykonanie migracji bazy danych

Po uruchomieniu aplikacji wykonaj migracje:

```bash
docker-compose exec web python manage.py migrate
```

### 4. Tworzenie superusera (opcjonalnie)

Aby uzyskać dostęp do panelu administratora:

```bash
docker-compose exec web python manage.py createsuperuser
```

---

## Dostęp do aplikacji

Po uruchomieniu aplikacja dostępna jest pod adresem:

- **Aplikacja główna:** http://localhost:8000
- **Panel administracyjny:** http://localhost:8000/admin

Zaloguj się przy użyciu superusera utworzonego w kroku powyżej.

---

## Zatrzymanie aplikacji

Aby zatrzymać kontenery:

```bash
docker-compose down
```

Jeśli chcesz usunąć również dane z bazy danych:

```bash
docker-compose down -v
```

Flaga `-v` usuwa również wolumeny (persistent data).

---

## Informacje o portach

### Używane porty:

- **8000** — aplikacja Django (web server)
- **5432** — baza danych PostgreSQL (db)

### Konflikt portów

Jeśli port jest już zajęty, możesz zmienić mapowanie w pliku `docker-compose.yml`:

```yaml
# Zmień tę linię:
ports:
  - "8000:8000"  # host_port:container_port

# Na przykład na:
ports:
  - "8080:8000"  # dostęp przez http://localhost:8080
```

Następnie uruchom ponownie:

```bash
docker-compose down
docker-compose up -d
```

---

## Zmienne środowiskowe

Opis wszystkich wymaganych i opcjonalnych zmiennych:

| Zmienna | Rola | Przykład | Wymagana |
|---------|------|---------|----------|
| `SECRET_KEY` | Klucz bezpieczeństwa Django | `django-insecure-...` | ✅ Tak |
| `DEBUG` | Tryb debugowania (1=włącz, 0=wyłącz) | `1` lub `0` | ✅ Tak |
| `ALLOWED_HOSTS` | Dozwolone hosty oddzielone przecinkami | `localhost,127.0.0.1,0.0.0.0` | ✅ Tak |
| `POSTGRES_DB` | Nazwa bazy danych | `project-db` | ✅ Tak |
| `POSTGRES_USER` | Użytkownik bazy danych | `your-username` | ✅ Tak |
| `POSTGRES_PASSWORD` | Hasło bazy danych | `your-db-password` | ✅ Tak |
| `POSTGRES_HOST` | Host bazy danych | `db` | ✅ Tak |

---

## Migracje bazy danych

### Wykonanie migracji

Migracje aplikują zmiany w strukturze bazy danych:

```bash
docker-compose exec web python manage.py migrate
```

### Tworzenie nowej migracji

Jeśli zmienisz modele w aplikacji, utwórz nową migrację:

```bash
docker-compose exec web python manage.py makemigrations
```

Następnie zastosuj ją:

```bash
docker-compose exec web python manage.py migrate
```

---

## Przydatne komendy

### Logowanie do kontenera web

```bash
docker-compose exec web bash
```

### Logowanie do bazy danych PostgreSQL

```bash
docker-compose exec db psql -U your-username -d project-db
```

### Wyświetlanie logów

```bash
docker-compose logs web      # Logi aplikacji
docker-compose logs db       # Logi bazy danych
docker-compose logs -f       # Podążanie za logami (live)
```

### Ponowne budowanie obrazu

```bash
docker-compose build --no-cache
```

---

## Najczęstsze problemy

### Problem 1: Port 8000 jest zajęty

**Objaw:** `Address already in use`

**Rozwiązanie:**
```bash
# Znajdź proces na porcie 8000
lsof -i :8000

# Wejdź do docker-compose.yml i zmień port:
# ports:
#   - "8080:8000"  # zamiast "8000:8000"

docker-compose down
docker-compose up -d
```

### Problem 2: Port 5432 jest zajęty

**Objaw:** Baza danych nie uruchamia się

**Rozwiązanie:**
```bash
# Zmień mapowanie portu bazy w docker-compose.yml:
# ports:
#   - "5433:5432"  # zamiast "5432:5432"

docker-compose down
docker-compose up -d
```

### Problem 3: Błąd uprawnień do gniazd Docker

**Objaw:** `permission denied while trying to connect to Docker daemon`

**Rozwiązanie (macOS/Linux):**
```bash
# Dodaj swoje konto do grupy docker
sudo usermod -aG docker $USER

# Załaduj nową grupę
newgrp docker

# Sprawdź, czy działa
docker ps
```

### Problem 4: Baza danych nie inicjalizuje się

**Objaw:** `FATAL: remaining connection slots are reserved for non-replication superuser connections`

**Rozwiązanie:**
```bash
# Usuń istniejące wolumeny i uruchom ponownie
docker-compose down -v
docker-compose up -d

# Ponownie wykonaj migracje
docker-compose exec web python manage.py migrate
```

### Problem 5: Błędy zależności Python

**Objaw:** `ModuleNotFoundError` lub `ImportError`

**Rozwiązanie:**
```bash
# Przebuduj obraz bez cache
docker-compose build --no-cache

# Uruchom ponownie
docker-compose down
docker-compose up -d
```

### Problem 6: Aplikacja nie łączy się z bazą danych

**Objaw:** `could not translate host name "db" to address`

**Rozwiązanie:**
```bash
# Sprawdź, czy usługa bazy jest uruchomiona
docker-compose ps

# Jeśli jest zatrzymana, uruchom ją
docker-compose up -d

# Sprawdź zmienne w .env
cat .env

# Upewnij się, że POSTGRES_HOST=db (dokładnie)
```

### Problem 7: Migracja zawiesza się

**Objaw:** Komenda `migrate` nie kończy się

**Rozwiązanie:**
```bash
# Przerwij komendę (Ctrl+C)
# Sprawdź logi
docker-compose logs db

# Spróbuj usunąć woluminy i zacząć od nowa
docker-compose down -v
docker-compose up -d
docker-compose exec web python manage.py migrate
```

---

## Wsparcie

Jeśli problem nie jest wymieniony wyżej:

1. Sprawdź logi:
   ```bash
   docker-compose logs
   ```

2. Upewnij się, że wszystkie zmienne w `.env` są ustawione

3. Przebuduj obrazy:
   ```bash
   docker-compose build --no-cache
   docker-compose up -d
   ```

---

## Architektura projektu

```
.
├── Dockerfile              # Definicja obrazu Docker
├── docker-compose.yml      # Konfiguracja usług (web + db)
├── .env.example           # Szablon zmiennych środowiskowych
├── .env                   # Zmienne środowiskowe (nie pushuj!)
├── manage.py              # Django management script
├── requirements.txt       # Python dependencies
├── project/               # Główny pakiet Django
│   ├── settings.py       # Konfiguracja Django
│   ├── urls.py           # Routing
│   ├── wsgi.py           # WSGI config
│   └── asgi.py           # ASGI config
└── README.md              # Ten plik
```

