
Projekt wykorzystuje dwa kontenery Docker:

- **Django** – aplikacja internetowa uruchamiana na porcie `8000`.
- **PostgreSQL** – baza danych przechowywana w trwałym wolumenie Docker.

Do uruchomienia projektu wymagany jest Docker z obsługą Compose. Nie jest konieczne osobne instalowanie Pythona, Django ani PostgreSQL na komputerze użytkownika. Wszystkie zależności aplikacji instalowane są automatycznie podczas budowania obrazu Docker.

### Kroki niezbędne do uruchomienia

1. Ściągnij i zainstaluj oprogramowanie Docker (niezbędne oprogramowanie Docker Compose zainstaluje sie automatycznie).
2. Uruchom Docker (w systemie Windows ma pojawić się ikona wieloryba w tray).
3. Otwórz terminal (np. PowerShell) i sprawdź poprawność instalacji. Oba polecenia powinny wyświetlić informacje o zainstalowanych wersjach.

```powershell
docker --version
docker compose version  
```

4. Wypakuj folder o nazwie `05` z pliku .zip do wybranego katalogu na komputerze.
5. Utwórz plik `.env` ze swoimi zmiennymi środowiskowymi na podstawie pliku `.env.example` (alternatywnie zmień nazwę pliku `.env.example` na `.env`).

```
Do zmiennej POSTGRES_USER przypisz nazwę użytkownika bazy danych np. db_user
Do zmiennej POSTGRES_PASSWORD przypisz hasło użytkownika bazy danych np. my_password
Do zmiennej POSTGRES_DB przypisz nazwę bazy danych np. my_db
```

6. Otwórz w terminalu folder `05`.
7. Zbuduj obraz i uruchom kontenery za pomocą polecenia `docker compose up -d --build`
8. Sprawdź czy są aktywne dwa kontenery poleceniem `docker compose ps`
9. Wykonaj jednorazowo migrację bazy danych poleceniem `docker compose exec web python manage.py migrate`
10. Możesz sprawdzić czy aplikacja działa wpisując w przeglądarce adres `http://127.0.0.1:8000/`
11. W celu zatrzymania i usunięcia kontenerów użyj polecenia `docker compose down`

W razie potrzeby usunięcia volumenu użyj polecenia `docker compose down -v`
# Najczęstsze problemy

### Docker nie jest uruchomiony

Jeżeli pojawia się błąd połączenia z silnikiem Docker, uruchom Docker Desktop i upewnij się, że jego silnik działa.
### Port 8000 jest zajęty

* W przypadku blokowania portu 8000 przez inną aplikację ustaw inny port np. 8080 w pliku `docker-compose.yml`, czyli zamiast:

```
ports:
- "8000:8000"
```

zamień na:

```
ports:
- "8080:8000"
```

i użyj polecenia `docker compose up -d --build` do przebudowania i uruchomienia kontenera.

Sprawdzić czy aplikacja działa wpisując w przeglądarce adres `http://127.0.0.1:8080/`

### Kontener aplikacji nie uruchamia się

Sprawdź stan kontenerów:

```powershell
docker compose ps -a
```

Następnie sprawdź komunikaty diagnostyczne:

```powershell
docker compose logs web
```





