

INSTRUKCJA URUCHAMIANIA PROJEKTU DJANGO W KONTENERZE DOCKER (POSTGRESQL)

STRUKTURA KATALOGU:
* `app/` - kod źródłowy projektu Django
* `Dockerfile` - konfiguracja obrazu dla aplikacji Django
* `docker-compose.yml` - definicja usług Django + PostgreSQL
* `requirements.txt` - zależności Pythona
* `.env.example` - szablon zmiennych środowiskowych
* `screenshots/` - dokumentacja poprawnego działania

Wymagania wstępne
Do uruchomienia projektu wymagane są:
* **Docker**
* **Docker Compose**

Aplikacja docker jest dostępna do pobrania ze strony: https://www.docker.com

## Instrukcja uruchomienia krok po kroku

1. Zbudowanie i uruchamianie kontenerów

Zbuduj obrazy i uruchom usługi w tle za pomocą jednej komendy:

* docker-compose up --build -d

2. Migracje bazy danych 

Po uruchomieniu kontenerów sprawdź status komendą:

* docker-compose ps

Następnie należy przygotować strukturę bazy danych PostgreSQL:

* docker-compose exec web python manage.py migrate

3. Dostęp do aplikacji

Aplikacja jest teraz dostępna pod adresem: http://localhost:8000

4. Zarządzanie aplikacją

* Aby wyłączyć kontenery użyj komendy: docker-compose stop.

* Aby całkowicie usunąć kontenery z pamięci użyj komendy: docker-compose down.

* UWAGA: Aplikacja domyślnie zajmuje port 8000, jeśli jest on zajęty należy zmienić mapowanie w pliku docker-compose.yml (sekcja ports w usłudze web).

5. Rozwiązywanie problemów

* Brak uprawnień: Na systemach Linux może być wymagane użycie sudo przed komendami Dockera.

* Czysty start: W razie problemów z konfiguracją spróbuj: docker-compose down -v (usunie to również wolumeny bazy danych) i spróbuj ponownie.