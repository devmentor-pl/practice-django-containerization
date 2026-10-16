my> :white_check_mark: *Jeśli będziesz mieć problem z rozwiązaniem tego zadania, poproś o pomoc na odpowiednim kanale na Slacku, tj. `s9e05-django-containerization` (dotyczy [mentee](https://devmentor.pl/mentoring-javascript/)) lub na ogólnodostępnej i bezpłatnej [społeczności na Discordzie](https://devmentor.pl/discord). Pamiętaj, aby treść Twojego wpisu spełniała [odpowiednie kryteria](https://devmentor.pl/jak-prosic-o-pomoc/).*

&nbsp;

# `#04` Docker konteneryzacja


Twoim zadaniem jest uruchomienie aplikacji **Django** oraz **PostgreSQL** w **oddzielnych kontenerach** z użyciem Docker Compose.
**To zadanie jest całkowicie niezależne** — wykonujesz je w osobnym katalogu/środowisku i **nie** korzystasz z rozwiązań ani plików z innych ćwiczeń.

To ćwiczenie pozwoli Ci przećwiczyć:

* definiowanie wielokontenerowego środowiska w pliku `docker-compose.yml`,
* konfigurację sieci i zależności między usługami (Django ↔ baza),
* utrwalanie danych bazy w wolumenie.

## Zakres zadania

1. W nowym katalogu projektu przygotuj minimalną strukturę:

   * plik `docker-compose.yml` (dwie usługi: `web` i `db`),
   * plik `Dockerfile` dla usługi `web` (Django),
   * plik `requirements.txt` (ma zawierać zależności potrzebne do uruchomienia Django + sterownik do PostgreSQL),
   * opcjonalnie plik `.env` na hasła i parametry połączeniowe (nie commituj haseł).

2. Skonfiguruj usługę **`db`** (PostgreSQL):

   * ustaw **czytelną nazwę** kontenera, np. `postgres-db`,
   * włącz **trwałość danych** (wolumen na katalog danych bazy),
   * zdefiniuj podstawowe zmienne środowiskowe (użytkownik, hasło, nazwa bazy),
   * zapewnij, że **port bazy** jest dostępny w sieci compose (mapowanie portu na host opcjonalne).

3. Skonfiguruj usługę **`web`** (Django):

   * użyj obrazu bazowego Pythona (lub własnego przez `Dockerfile`),
   * zainstaluj zależności z `requirements.txt`,
   * ustaw **katalog roboczy** i skopiuj kod aplikacji,
   * przygotuj **polecenie startowe**, które uruchamia serwer developerski na `0.0.0.0:8000`,
   * dodaj **zależność** od `db` (Django ma wystartować po dostępności bazy),
   * skonfiguruj zmienne środowiskowe do połączenia z bazą.

4. W pliku ustawień Django skonfiguruj **connection string** do PostgreSQL, używając **nazwy usługi Compose jako hosta** (np. `HOST = "db"`).

   * upewnij się, że `ALLOWED_HOSTS` dopuszcza ruch z przeglądarki,
   * wykonaj migracje bazy przed pierwszym uruchomieniem aplikacji (zapewnij krok w procesie startu lub wykonaj je ręcznie w kontenerze).

5. Uruchom środowisko Compose i sprawdź, że:

   * oba kontenery działają,
   * aplikacja Django jest dostępna pod adresem **`http://localhost:8000`**,
   * tabela migracji powstała w bazie (wystarczy, że migracje się powiodły i aplikacja się uruchamia).

## Wymagania funkcjonalne

### Oczekiwane rezultaty

| Akcja                          | Oczekiwany rezultat                                                           |
| ------------------------------ | ----------------------------------------------------------------------------- |
| Definicja `docker-compose.yml` | Dwie usługi: `web` (Django) i `db` (PostgreSQL)                               |
| Trwałość danych bazy           | Zdefiniowany wolumen dla Postgresa, dane nie znikają po zatrzymaniu kontenera |
| Połączenie Django ↔ PostgreSQL | Aplikacja startuje i łączy się z bazą po nazwie hosta wskazującej usługę `db` |
| Uruchomienie środowiska        | Oba kontenery mają status „Up”                                                |
| Dostęp przez przeglądarkę      | `http://localhost:8000` zwraca stronę Django (lub stronę startową projektu)   |

## Wskazówki

* **Nazwy proste i jednoznaczne:** użyj `web` (Django) i `db` (PostgreSQL); nazwy kontenerów i usług trzymaj spójne z tymi nazwami.
* **Zależności:** w Compose skorzystaj z mechanizmu zależności tak, by `web` czekał na `db`; rozważ prosty „healthcheck” bazy albo skrypt „wait-for-db”.
* **Wolumen:** przypnij katalog danych Postgresa (np. `/var/lib/postgresql/data`) do wolumenu nazwowego, aby przetrwać restart.
* **Zmienne środowiskowe:** trzymaj hasła i parametry w `.env` i odwołuj się do nich w `docker-compose.yml`.
* **Migracje:** przed pierwszym startem aplikacji potrzebne są migracje — zaplanuj je w komendzie startowej `web` lub wykonaj w oddzielnym kroku wewnątrz kontenera.
* **Porty:** wystaw tylko port aplikacji web (8000) na hosta; port bazy nie musi być wystawiony na zewnątrz, o ile łączy się z nią tylko Django.

## Testowanie

Przygotuj **zrzuty ekranu** pokazujące:

1. uruchomione usługi w Compose (lista działających kontenerów),
2. logi startowe `web` z informacją o podłączeniu do bazy / wykonanych migracjach,
3. stronę aplikacji pod adresem `http://localhost:8000`,
4. ponowne uruchomienie środowiska po zatrzymaniu — baza zachowuje dane (wolumen działa).

## Twoje rozwiązanie powinno zawierać

* plik **`docker-compose.yml`** z dwiema usługami i wolumenem dla bazy,
* plik **`Dockerfile`** dla usługi `web`,
* plik **`requirements.txt`** z zależnościami (Django + sterownik do PostgreSQL),
* ewentualnie plik **`.env`** z parametrami połączenia,
* **zrzuty ekranu** z działania: uruchomione kontenery, logi startowe, strona w przeglądarce.


&nbsp;

> :no_entry: *Jeśli nie posiadasz materiałów do tego zadania tj. **PDF, projekt + Code Review**, znajdziesz je na stronie [devmentor.pl](https://devmentor.pl/workshop-django-containerization)*

> :arrow_left: [*poprzednie zadanie*](./../03) | [*następne zadanie*](./../05) :arrow_right:
