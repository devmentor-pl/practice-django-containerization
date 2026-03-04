# Instrukcja uruchomienia aplikacji Django w Docker

## Wymagania wstępne

Zanim zaczniesz, upewnij się, że masz zainstalowane:
- **Docker Desktop** (na Windows) - zawiera zarówno Docker jak i Docker Compose

Aby sprawdzić, czy Docker jest zainstalowany, otwórz terminal (PowerShell lub CMD) i wpisz:
```bash
docker --version
```

Powinieneś zobaczyć numer wersji Docker.

---

## Krok 0: Rozpakowanie plików (jeśli otrzymałeś ZIP)

Jeśli otrzymałeś aplikację w formie pliku ZIP:

1. **Rozpakuj plik ZIP** w wybranym miejscu na dysku twardym
   - Na Windows: kliknij prawym przyciskiem myszy na plik ZIP → Rozpakuj wszystko
   - Lub użyj komendy w terminalu (zastąp `django-app.zip` rzeczywistą nazwą):
     ```bash
     Expand-Archive -Path django-app.zip -DestinationPath .
     ```

2. **Otwórz terminal/PowerShell** w rozpakowanym folderze
   - Na Windows: Shift + prawy klik w folderze → Otwórz PowerShell w tym miejscu

3. **Upewnij się, że widzisz te pliki w folderze:**
   - `Dockerfile`
   - `manage.py`
   - `requirements.txt`
   - `myproject/`
   - `README_SETUP.md`
---

## Krok 1: Budowanie obrazu Docker

Otwórz terminal w folderze z projektem (tym folderze, gdzie znajduje się `Dockerfile`).

Wpisz komendę:
```bash
docker build -t django-app .
```

Wyjaśnienie:
- `docker build` - komenda do budowania obrazu
- `-t django-app` - tworzy obraz z tagiem "django-app" (to nazwa obrazu)
- `.` - używa Dockerfile z bieżącego folderu

Czekaj aż proces się ukończy. Powinieneś zobaczyć komunikat: `Successfully tagged django-app:latest`

---

## Krok 2: Uruchomienie kontenera

Gdy obraz jest już zbudowany, uruchom kontener za pomocą komendy:

```bash
docker run -d -p 8000:8000 --name django-server django-app
```

Wyjaśnienie flagi:
- `docker run` - komenda do uruchomienia kontenera
- `-d` - uruchamia kontener w tle (detached mode)
- `-p 8000:8000` - mapuje port 8000 z kontenera na port 8000 komputera
- `--name django-server` - daje kontenerowi nazwę "django-server"
- `django-app` - nazwa obrazu, który chcemy uruchomić

Powinieneś zobaczyć ID kontenera, co oznacza, że kontener został uruchomiony.

---

## Krok 3: Dostęp do aplikacji w przeglądarce

Otwórz przeglądarkę internetową (Chrome, Firefox, Edge, itp.) i wpisz w pasek adresu:

```
http://localhost:8000
```

Powinieneś zobaczyć stronę Django (domyślną stronę powitalną lub aplikację).

Jeśli aplikacja nie załaduje się, czekaj 5-10 sekund - serwer może się jeszcze uruchamiać.

---

## Krok 4: Zatrzymanie aplikacji

Aby zatrzymać kontener, otwórz terminal i wpisz:

```bash
docker stop django-server
```

Wyjaśnienie:
- `docker stop` - komenda zatrzymująca kontener
- `django-server` - nazwa kontenera (tę samą, którą podaliśmy w kroku 2)

Jeśli chcesz całkowicie usunąć kontener, wpisz:
```bash
docker rm django-server
```

---

## Informacja o portach

### Port 8000

Aplikacja Django uruchamia się na porcie **8000**. Możesz zmienić port, zmieniając pierwszy numer w `-p XXX:8000`.

### Port jest zajęty

Jeśli zobaczysz błąd, że port 8000 jest już zajęty, masz dwie opcje:

**Opcja 1: Zatrzymaj istniejący kontener**
```bash
docker stop django-server
docker rm django-server
```

Następnie uruchom ponownie z kroku 2.

**Opcja 2: Użyj innego portu**
```bash
docker run -d -p 8001:8000 --name django-server django-app
```

W tym przypadku aplikacja będzie dostępna pod adresem: `http://localhost:8001`

(Zmień `8001` na dowolny inny port, który jest dostępny)

---

## Przydatne komendy

Sprawdzenie, czy kontener się uruchamia:
```bash
docker ps
```

Zobaczenie logów kontenera:
```bash
docker logs django-server
```

Lista wszystkich kontenerów (uruchomionych i zatrzymanych):
```bash
docker ps -a
```

Ponowne uruchomienie zatrzymanego kontenera:
```bash
docker start django-server
```
