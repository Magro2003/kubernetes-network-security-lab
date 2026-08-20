# Kubernetes Network & Security Lab — Ściąga z komend

> **Cel:** szybka ściąga z komend używanych podczas budowy projektu.
> Plik aktualizujemy wraz z kolejnymi etapami: Docker, Kubernetes, networking, troubleshooting i security.

---

## Spis treści

1. [Nawigacja po systemie](#1-nawigacja-po-systemie)
2. [WSL i Ubuntu](#2-wsl-i-ubuntu)
3. [Cursor](#3-cursor)
4. [Git](#4-git)
5. [Struktura projektu](#5-struktura-projektu)
6. [Python i virtual environment](#6-python-i-virtual-environment)
7. [Flask](#7-flask)
8. [HTTP i diagnostyka](#8-http-i-diagnostyka)
9. [Docker](#9-docker)
10. [Kubernetes](#10-kubernetes)

---

## 1. Nawigacja po systemie

### Sprawdzenie bieżącego katalogu

```bash
pwd
```

Pokazuje pełną ścieżkę do katalogu, w którym aktualnie pracujemy.

### Przejście do katalogu projektu

```bash
cd ~/projects/kubernetes-network-security-lab
```

### Wyświetlenie zawartości katalogu

```bash
ls -la
```

Opcja `-a` pokazuje także pliki ukryte, a `-l` szczegółowe informacje.

### Wyświetlenie zawartości katalogu `app`

```bash
ls -la app
```

---

## 2. WSL i Ubuntu

### Sprawdzenie stanu WSL

```powershell
wsl --status
```

### Lista dystrybucji WSL

```powershell
wsl -l -v
```

### Aktualizacja listy pakietów Ubuntu

```bash
sudo apt update
```

### Aktualizacja zainstalowanych pakietów

```bash
sudo apt upgrade -y
```

### Sprawdzenie aktualnego użytkownika

```bash
whoami
```

### Informacje o systemie

```bash
uname -a
```

```bash
cat /etc/os-release
```

### Całkowite zatrzymanie WSL

```powershell
wsl --shutdown
```

Zatrzymuje wszystkie aktualnie działające dystrybucje WSL.

Przydatne przy pełnym odświeżeniu środowiska WSL.

> Jeśli Docker Desktop korzysta z integracji WSL, po wykonaniu tej komendy może być konieczne ponowne uruchomienie Docker Desktop.

---

## 3. Cursor

### Otwarcie bieżącego katalogu w Cursorze

```bash
cursor .
```

---

## 4. Git

### Inicjalizacja repozytorium

```bash
git init
```

### Zmiana nazwy głównej gałęzi na `main`

```bash
git branch -m main
```

### Sprawdzenie aktualnej gałęzi

```bash
git branch --show-current
```

### Sprawdzenie stanu repozytorium

```bash
git status
```

### Dodanie konkretnego pliku do staging area

```bash
git add .gitignore
```

### Sprawdzenie konfiguracji użytkownika Git

```bash
git config --global --get user.name
git config --global --get user.email
```

---

## 5. Struktura projektu

### Utworzenie głównych katalogów

```bash
mkdir app k8s docs
```

### Utworzenie podstawowych plików

```bash
touch README.md .gitignore
```

### Placeholdery dla pustych katalogów

```bash
touch app/.gitkeep docs/.gitkeep k8s/.gitkeep
```

Git nie śledzi pustych katalogów, dlatego `.gitkeep` może służyć jako placeholder.

### Utworzenie plików aplikacji

```bash
touch app/app.py
touch app/requirements.txt
```

### Usunięcie `.gitkeep` z katalogu `app`

```bash
rm app/.gitkeep
```

---

## 6. Python i virtual environment

### Sprawdzenie wersji Pythona

```bash
python3 --version
```

### Sprawdzenie modułu `venv`

```bash
python3 -m venv --help
```

### Instalacja obsługi `venv`

```bash
sudo apt install python3.12-venv
```

### Utworzenie środowiska wirtualnego

```bash
python3 -m venv .venv
```

### Aktywacja środowiska

```bash
source .venv/bin/activate
```

Po aktywacji terminal powinien zaczynać się od:

```text
(.venv)
```

### Wyjście ze środowiska

```bash
deactivate
```

Nie usuwa `.venv`. Kończy tylko korzystanie z niego w aktualnym terminalu.

### Usunięcie środowiska

```bash
rm -rf .venv
```

> ⚠️ **Uwaga:** `rm -rf` usuwa wskazany katalog bez pytania o potwierdzenie. Zawsze sprawdź ścieżkę przed wykonaniem.

---

## 7. Flask

### Instalacja zależności

```bash
python -m pip install -r app/requirements.txt
```

### Sprawdzenie zainstalowanego Flaska

```bash
python -m pip show Flask
```

### Uruchomienie aplikacji

```bash
python app/app.py
```

Nasza aplikacja nasłuchuje na porcie:

```text
8080
```

### Zatrzymanie serwera

```text
Ctrl+C
```

---

## 8. HTTP i diagnostyka

### Test endpointu `/`

```bash
curl http://localhost:8080/
```

Oczekiwana odpowiedź:

```text
Kubernetes Network & Security Lab
```

### Test endpointu `/health`

```bash
curl http://localhost:8080/health
```

Oczekiwana odpowiedź:

```json
{"status":"healthy"}
```

### Sprawdzenie, czy port `8080` nasłuchuje

```bash
ss -ltnp | grep 8080
```

Przydatne podczas diagnozowania problemów z połączeniem do aplikacji.

### Wyświetlenie `.gitignore`

```bash
cat .gitignore
```

---

## 9. Docker

### Sprawdzenie wersji Docker CLI

```bash
docker --version
```

Pokazuje zainstalowaną wersję klienta Docker.

### Informacje o Docker Engine

```bash
docker info
```

Pokazuje szczegółowe informacje o kliencie i Docker Engine.

### Sprawdzenie Docker Compose

```bash
docker compose version
```

Pokazuje wersję pluginu Docker Compose.

---

### Test instalacji Dockera

```bash
docker run --rm hello-world
```

Pobiera testowy obraz `hello-world`, uruchamia kontener i automatycznie usuwa go po zakończeniu.

Opcja:

```text
--rm
```

oznacza automatyczne usunięcie kontenera po jego zakończeniu.

---

### Budowanie obrazu aplikacji

```bash
docker build -t kubernetes-network-security-lab:0.1 .
```

Buduje obraz na podstawie `Dockerfile`.

- `-t` — nadaje obrazowi nazwę i tag,
- `kubernetes-network-security-lab` — nazwa obrazu,
- `0.1` — tag / wersja,
- `.` — bieżący katalog jako build context.

### Lista obrazów

```bash
docker image ls
```

Pokazuje obrazy dostępne lokalnie.

---

### Uruchomienie kontenera

```bash
docker run --name kns-lab -p 8080:8080 kubernetes-network-security-lab:0.1
```

Tworzy i uruchamia kontener na podstawie naszego obrazu.

- `--name kns-lab` — nadaje kontenerowi nazwę,
- `-p 8080:8080` — mapuje port hosta `8080` na port `8080` kontenera.

Schemat:

```text
HOST:CONTAINER
8080:8080
```

### Uruchomienie kontenera w tle

```bash
docker run -d --name kns-lab -p 8080:8080 kubernetes-network-security-lab:0.1
```

Opcja `-d` uruchamia kontener w trybie detached, czyli w tle.

---

### Działające kontenery

```bash
docker ps
```

Pokazuje tylko aktualnie uruchomione kontenery.

### Wszystkie kontenery

```bash
docker ps -a
```

Pokazuje również kontenery zatrzymane.

---

### Zatrzymanie kontenera

```bash
docker stop kns-lab
```

Zatrzymuje działający kontener.

### Ponowne uruchomienie istniejącego kontenera

```bash
docker start kns-lab
```

Uruchamia ponownie wcześniej utworzony kontener.

### Usunięcie kontenera

```bash
docker rm kns-lab
```

Usuwa zatrzymany kontener.

> Usunięcie kontenera nie usuwa obrazu, z którego został utworzony.

---

### Wyświetlenie logów

```bash
docker logs kns-lab
```

Pokazuje zapisane logi kontenera.

### Śledzenie logów na żywo

```bash
docker logs -f kns-lab
```

Opcja `-f` (`follow`) śledzi nowe logi na bieżąco.

`Ctrl+C` kończy śledzenie logów, ale nie zatrzymuje kontenera.

---

### Wejście do wnętrza kontenera

```bash
docker exec -it kns-lab sh
```

Uruchamia interaktywną powłokę `sh` wewnątrz działającego kontenera.

Przydatne komendy wewnątrz kontenera:

```bash
pwd
ls -la
hostname
hostname -I
```

Wyjście z kontenera:

```bash
exit
```

---

### Lista sieci Dockera

```bash
docker network ls
```

Pokazuje sieci zarządzane przez Docker Engine.

Domyślne sieci to m.in.:

```text
bridge
host
none
```

### Szczegóły sieci `bridge`

```bash
docker network inspect bridge
```

Pokazuje konfigurację domyślnej sieci bridge, m.in.:

- subnet,
- gateway,
- podłączone kontenery,
- ich adresy IPv4.

W naszym środowisku zobaczyliśmy m.in.:

```text
Subnet:  172.17.0.0/16
Gateway: 172.17.0.1
kns-lab: 172.17.0.2
```

---

### Diagnostyka uprawnień Docker Engine

```bash
ls -l /var/run/docker.sock
```

Pokazuje właściciela i uprawnienia socketu Docker Engine.

```bash
id
```

Pokazuje użytkownika i jego aktywne grupy.

```bash
getent group docker
```

Pokazuje członków grupy `docker`.

```bash
docker context ls
```

Pokazuje dostępne konteksty Docker CLI.

### Tymczasowe odświeżenie grupy `docker`

```bash
newgrp docker
```

Uruchamia nową powłokę z aktywnym członkostwem w grupie `docker`.

> Komenda była potrzebna podczas pierwszej konfiguracji. Po pełnym ponownym uruchomieniu WSL nie powinna być wymagana przy normalnej pracy.

---

## 10. Kubernetes

> ☸️ **Do uzupełnienia w kolejnych etapach projektu.**

Tutaj będziemy zapisywać komendy związane m.in. z:

- `kubectl`,
- Podami,
- Deploymentami,
- Service,
- probes,
- networkingiem,
- NetworkPolicy,
- troubleshootingiem,
- security.

---

## Zasada prowadzenia pliku

Każda nowa istotna komenda będzie zawierała:

- samą komendę,
- krótkie wyjaśnienie,
- informację, kiedy jej używamy,
- ostrzeżenie, jeśli może usuwać lub modyfikować dane.

Ten plik ma być jednocześnie **ściągą do projektu** oraz **dziennikiem nauki**.
