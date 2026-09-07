# Kubernetes Network & Security Lab — Ściąga z komend

> **Cel:** szybka ściąga z komend używanych podczas budowy projektu.
>
> Dokument jest rozwijany wraz z projektem i obejmuje polecenia związane z:
> WSL, Linux, Git, Python, Flask, Docker, Docker Networking, Kubernetes,
> troubleshootingiem oraz dokumentacją projektu.

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
10. [Docker Networking](#10-docker-networking)
11. [Kubernetes](#11-kubernetes)
12. [Pliki i dokumentacja projektu](#12-pliki-i-dokumentacja-projektu)
13. [Troubleshooting i komendy pomocnicze](#13-troubleshooting-i-komendy-pomocnicze)

---

# 1. Nawigacja po systemie

## Sprawdzenie bieżącego katalogu

```bash
pwd
```

Pokazuje pełną ścieżkę katalogu, w którym aktualnie pracujemy.

---

## Przejście do katalogu projektu

```bash
cd ~/projects/kubernetes-network-security-lab
```

---

## Przejście do katalogu Kubernetes

```bash
cd k8s
```

---

## Wyświetlenie zawartości katalogu

```bash
ls
```

---

## Szczegółowe wyświetlenie zawartości katalogu

```bash
ls -la
```

- `-l` — szczegółowe informacje,
- `-a` — również pliki ukryte.

---

## Wyświetlenie zawartości katalogu `app`

```bash
ls -la app
```

---

# 2. WSL i Ubuntu

> Polecenia `wsl` wykonujemy w PowerShell lub terminalu Windows.

## Sprawdzenie stanu WSL

```powershell
wsl --status
```

---

## Lista dystrybucji WSL

```powershell
wsl -l -v
```

Pokazuje zainstalowane dystrybucje oraz informację, czy działają w WSL1 czy WSL2.

---

## Całkowite zatrzymanie WSL

```powershell
wsl --shutdown
```

Zatrzymuje wszystkie działające dystrybucje WSL.

Przydatne podczas rozwiązywania problemów z integracją Docker Desktop.

> Po wykonaniu tej komendy może być konieczne ponowne uruchomienie Docker Desktop.

---

## Aktualizacja listy pakietów Ubuntu

```bash
sudo apt update
```

---

## Aktualizacja zainstalowanych pakietów

```bash
sudo apt upgrade -y
```

---

## Sprawdzenie aktualnego użytkownika

```bash
whoami
```

---

## Informacje o systemie

```bash
uname -a
```

```bash
cat /etc/os-release
```

---

# 3. Cursor

## Otwarcie bieżącego katalogu w Cursorze

```bash
cursor .
```

Otwiera aktualny katalog jako projekt w Cursorze.

---

# 4. Git

## Inicjalizacja repozytorium

```bash
git init
```

---

## Zmiana nazwy głównej gałęzi na `main`

```bash
git branch -m main
```

---

## Sprawdzenie aktualnej gałęzi

```bash
git branch --show-current
```

---

## Sprawdzenie stanu repozytorium

```bash
git status
```

Jedna z najczęściej używanych komend w projekcie.

Pokazuje m.in.:

- pliki zmodyfikowane,
- pliki nieśledzone (`untracked`),
- pliki znajdujące się w staging area.

---

## Dodanie konkretnego pliku do staging area

```bash
git add <ścieżka-do-pliku>
```

Przykłady użyte w projekcie:

```bash
git add .gitignore
git add k8s/deployment.yaml
git add k8s/service.yaml
git add docs/tests.md
git add docs/images/tests
```

---

## Utworzenie commita

```bash
git commit -m "Opis zmian"
```

Przykładowe commity wykonane podczas projektu:

```bash
git commit -m "Add Kubernetes deployment manifest"
git commit -m "Add Kubernetes Service manifest"
git commit -m "Add test screenshots documentation"
git commit -m "Add project test documentation"
```

---

## Sprawdzenie różnic w konkretnym pliku

```bash
git diff -- <plik>
```

Przykład:

```bash
git diff -- docs/tests.md
```

---

## Sprawdzenie konfiguracji użytkownika Git

```bash
git config --global --get user.name
git config --global --get user.email
```

---

# 5. Struktura projektu

## Utworzenie podstawowych katalogów

```bash
mkdir app k8s docs
```

---

## Utworzenie katalogu wraz z brakującymi katalogami nadrzędnymi

```bash
mkdir -p k8s
```

Opcja `-p` zapobiega błędowi, jeśli katalog już istnieje.

---

## Utworzenie podstawowych plików

```bash
touch README.md .gitignore
```

---

## Utworzenie placeholderów dla pustych katalogów

```bash
touch app/.gitkeep docs/.gitkeep k8s/.gitkeep
```

Git nie śledzi pustych katalogów, dlatego `.gitkeep` może służyć jako placeholder.

---

## Utworzenie plików aplikacji

```bash
touch app/app.py
touch app/requirements.txt
```

---

## Utworzenie manifestu Deploymentu

```bash
touch k8s/deployment.yaml
```

---

## Utworzenie manifestu Service

```bash
touch k8s/service.yaml
```

---

## Utworzenie dokumentacji testów

```bash
touch docs/tests.md
```

---

## Usunięcie `.gitkeep`

```bash
rm app/.gitkeep
```

---

# 6. Python i virtual environment

## Sprawdzenie wersji Pythona

```bash
python3 --version
```

---

## Sprawdzenie modułu `venv`

```bash
python3 -m venv --help
```

---

## Instalacja obsługi `venv`

```bash
sudo apt install python3.12-venv
```

---

## Utworzenie środowiska wirtualnego

```bash
python3 -m venv .venv
```

---

## Aktywacja środowiska

```bash
source .venv/bin/activate
```

Po aktywacji terminal powinien zawierać:

```text
(.venv)
```

---

## Wyjście ze środowiska

```bash
deactivate
```

Kończy korzystanie z virtual environment w aktualnym terminalu, ale nie usuwa katalogu `.venv`.

---

## Usunięcie virtual environment

```bash
rm -rf .venv
```

> ⚠️ `rm -rf` usuwa katalog bez pytania o potwierdzenie. Przed wykonaniem zawsze należy sprawdzić ścieżkę.

---

# 7. Flask

## Instalacja zależności

```bash
python -m pip install -r app/requirements.txt
```

---

## Sprawdzenie zainstalowanego Flaska

```bash
python -m pip show Flask
```

---

## Uruchomienie aplikacji

```bash
python app/app.py
```

Aplikacja projektu nasłuchuje na porcie:

```text
8080
```

---

## Zatrzymanie aplikacji

```text
Ctrl+C
```

---

# 8. HTTP i diagnostyka

## Test głównego endpointu

```bash
curl http://localhost:8080/
```

Oczekiwana odpowiedź:

```text
Kubernetes Network & Security Lab
```

---

## Test endpointu health

```bash
curl http://localhost:8080/health
```

Oczekiwana odpowiedź:

```json
{"status":"healthy"}
```

---

## Sprawdzenie, czy port `8080` nasłuchuje

```bash
ss -ltnp | grep 8080
```

Przydatne podczas diagnozowania problemów z dostępem do aplikacji.

---

## Wyświetlenie zawartości pliku

```bash
cat <plik>
```

Przykłady:

```bash
cat .gitignore
cat docs/commands.md
cat k8s/service.yaml
```

---

# 9. Docker

## Sprawdzenie wersji Docker CLI

```bash
docker --version
```

Pokazuje wersję klienta Docker.

---

## Pełna weryfikacja klienta i Docker Engine

```bash
docker version
```

Pokazuje osobno informacje o:

- `Client`,
- `Server`.

Komenda została wykorzystana przy diagnozowaniu integracji Docker Desktop z WSL.

---

## Informacje o Docker Engine

```bash
docker info
```

---

## Sprawdzenie Docker Compose

```bash
docker compose version
```

---

## Test instalacji Dockera

```bash
docker run --rm hello-world
```

Pobiera obraz testowy, uruchamia kontener i automatycznie usuwa go po zakończeniu.

---

## Budowanie obrazu aplikacji

```bash
docker build -t kubernetes-network-security-lab:0.1 .
```

- `-t` — nazwa i tag obrazu,
- `.` — bieżący katalog jako build context.

---

## Lista obrazów

```bash
docker image ls
```

---

## Utworzenie i uruchomienie kontenera aplikacji

```bash
docker run --name kns-lab -p 8080:8080 kubernetes-network-security-lab:0.1
```

`docker run`:

```text
docker create + docker start
```

czyli tworzy nowy kontener i od razu go uruchamia.

---

## Uruchomienie kontenera w tle

```bash
docker run -d --name kns-lab -p 8080:8080 kubernetes-network-security-lab:0.1
```

Opcja:

```text
-d
```

uruchamia kontener w trybie detached.

---

## Lista działających kontenerów

```bash
docker ps
```

---

## Lista wszystkich kontenerów

```bash
docker ps -a
```

Pokazuje również kontenery zatrzymane.

---

## Zatrzymanie kontenera

```bash
docker stop kns-lab
```

---

## Uruchomienie istniejącego kontenera

```bash
docker start kns-lab
```

W przeciwieństwie do `docker run`, nie tworzy nowego kontenera.

---

## Usunięcie kontenera

```bash
docker rm kns-lab
```

> Usunięcie kontenera nie usuwa obrazu Docker.

---

## Wyświetlenie logów kontenera

```bash
docker logs kns-lab
```

---

## Śledzenie logów na żywo

```bash
docker logs -f kns-lab
```

`Ctrl+C` przerywa obserwowanie logów, ale nie zatrzymuje kontenera.

---

## Wejście do działającego kontenera

```bash
docker exec -it kns-lab sh
```

Przykładowe komendy wewnątrz kontenera:

```bash
pwd
ls -la
hostname
hostname -I
```

Wyjście:

```bash
exit
```

---

## Diagnostyka uprawnień Docker Engine

```bash
ls -l /var/run/docker.sock
```

---

```bash
id
```

Pokazuje użytkownika oraz jego aktywne grupy.

---

```bash
getent group docker
```

Pokazuje członków grupy `docker`.

---

```bash
docker context ls
```

Pokazuje dostępne konteksty Docker CLI.

---

## Tymczasowe odświeżenie członkostwa w grupie `docker`

```bash
newgrp docker
```

Uruchamia nową powłokę z aktywnym członkostwem w grupie `docker`.

---

# 10. Docker Networking

## Lista sieci Dockera

```bash
docker network ls
```

Domyślne sieci obejmują m.in.:

```text
bridge
host
none
```

---

## Inspekcja domyślnej sieci bridge

```bash
docker network inspect bridge
```

Pokazuje m.in.:

- subnet,
- gateway,
- podłączone kontenery,
- adresy IPv4.

---

## Utworzenie własnej sieci

```bash
docker network create kns-network
```

Tworzy własną sieć Docker typu `bridge`.

---

## Utworzenie sieci izolowanej

```bash
docker network create kns-isolated
```

Sieć została użyta podczas testu izolacji kontenerów.

---

## Podłączenie istniejącego kontenera do sieci

```bash
docker network connect kns-network kns-lab
```

---

```bash
docker network connect kns-network kns-client
```

Kontener może być jednocześnie podłączony do więcej niż jednej sieci Docker.

---

## Inspekcja własnej sieci

```bash
docker network inspect kns-network
```

Pokazuje m.in.:

- subnet,
- gateway,
- podłączone kontenery,
- ich adresy IP.

---

## Tymczasowy klient do testów HTTP

```bash
docker run --rm \
  --name kns-client \
  --network kns-network \
  curlimages/curl:latest \
  http://kns-lab:8080
```

Kontener:

1. zostaje utworzony,
2. wykonuje `curl`,
3. po zakończeniu zostaje automatycznie usunięty dzięki `--rm`.

---

## Tymczasowy klient z cichym `curl`

```bash
docker run --rm \
  --name kns-client \
  --network kns-network \
  curlimages/curl:latest \
  -s http://kns-lab:8080
```

Opcja `-s` ukrywa pasek postępu `curl`.

---

## Stały kontener klienta do testów sieciowych

```bash
docker run -dit \
  --name kns-client \
  --network kns-isolated \
  --entrypoint sh \
  curlimages/curl:latest
```

Pozwala utrzymać klienta uruchomionego i wykonywać w nim kolejne testy.

---

## Wykonanie `curl` wewnątrz działającego klienta

```bash
docker exec kns-client \
  curl -sS --connect-timeout 3 \
  http://kns-lab:8080
```

Opcja:

```text
--connect-timeout 3
```

ogranicza czas oczekiwania na połączenie do około 3 sekund.

---

## Test komunikacji bezpośrednio po IP

```bash
docker exec kns-client \
  curl -sS --connect-timeout 3 \
  http://172.18.0.2:8080
```

Użyte podczas sprawdzania, czy problem komunikacji wynika wyłącznie z DNS.

---

## Odczyt IP kontenera w konkretnej sieci

```bash
docker inspect -f '{{(index .NetworkSettings.Networks "kns-network").IPAddress}}' kns-lab
```

W wykonanym teście zwróciło:

```text
172.18.0.2
```

Adres IP może być inny po ponownym utworzeniu środowiska.

---

# 11. Kubernetes

## Sprawdzenie wersji klienta `kubectl`

```bash
kubectl version --client
```

W projekcie użyto:

```text
kubectl v1.36.1
```

---

## Wyświetlenie skonfigurowanych kontekstów

```bash
kubectl config get-contexts
```

Kontekst określa klaster, z którym komunikuje się `kubectl`.

W projekcie używany jest:

```text
docker-desktop
```

---

## Sprawdzenie node'ów klastra

```bash
kubectl get nodes
```

Stan:

```text
Ready
```

oznacza, że node jest gotowy do pracy.

---

## Zastosowanie manifestu Deploymentu

Z katalogu `k8s`:

```bash
kubectl apply -f deployment.yaml
```

Z katalogu głównego projektu:

```bash
kubectl apply -f k8s/deployment.yaml
```

---

## Zastosowanie manifestu Service

```bash
kubectl apply -f k8s/service.yaml
```

---

## Lista Podów

```bash
kubectl get pods
```

Pokazuje m.in.:

- nazwę Poda,
- `READY`,
- `STATUS`,
- liczbę restartów,
- wiek zasobu.

---

## Lista Deploymentów

```bash
kubectl get deployments
```

---

## Sprawdzenie konkretnego Deploymentu

```bash
kubectl get deployment kns-lab
```

---

## Lista ReplicaSetów

```bash
kubectl get replicasets
```

Pokazuje warstwę zarządzającą Podami:

```text
Deployment
    ↓
ReplicaSet
    ↓
Pod
```

---

## Ręczne usunięcie Poda

```bash
kubectl delete pod <nazwa-poda>
```

Przykład użyty podczas testu self-healing:

```bash
kubectl delete pod kns-lab-7b78dd8fd-fb8zr
```

Jeżeli Pod jest zarządzany przez ReplicaSet, Kubernetes automatycznie utworzy nową replikę.

---

## Skalowanie Deploymentu do 3 replik

```bash
kubectl scale deployment kns-lab --replicas=3
```

---

## Powrót do 1 repliki

```bash
kubectl scale deployment kns-lab --replicas=1
```

---

## Lista Service

```bash
kubectl get services
```

Pokazuje m.in.:

- nazwę,
- typ Service,
- `ClusterIP`,
- port.

---

## Sprawdzenie EndpointSlice dla Service

```bash
kubectl get endpointslices \
  -l kubernetes.io/service-name=kns-lab-service
```

Pokazuje endpointy backendowe powiązane z danym Service.

---

## Tymczasowy Pod klienta do testu Service

```bash
kubectl run kns-test-client --rm -it \
  --image=curlimages/curl:latest \
  --restart=Never \
  -- curl -sS http://kns-lab-service:8080
```

Komenda:

1. tworzy tymczasowy Pod,
2. uruchamia kontener z `curl`,
3. wykonuje żądanie HTTP do Service,
4. po zakończeniu usuwa Pod.

Opcje:

- `--rm` — usuwa Pod po zakończeniu,
- `-it` — uruchamia sesję interaktywną,
- `--image` — określa obraz kontenera,
- `--restart=Never` — tworzy pojedynczy Pod.

---

# 12. Pliki i dokumentacja projektu

## Utworzenie katalogu na screenshoty testów

```bash
mkdir -p docs/images/tests
```

---

## Utworzenie pliku dokumentacji testów

```bash
touch docs/tests.md
```

---

## Wyświetlenie plików znajdujących się w katalogu

```bash
find docs/images/tests -type f
```

---

## Wyszukiwanie paczki ZIP w systemie Windows z poziomu WSL

```bash
find /mnt/c/Users \
  -type f \
  -name 'kns-test-screenshots.zip' \
  2>/dev/null
```

---

## Sprawdzenie istnienia i rozmiaru pliku

```bash
ls -lh /mnt/c/Users/macie/Downloads/kns-test-screenshots.zip
```

---

## Rozpakowanie wyłącznie plików PNG

```bash
unzip -j \
  /mnt/c/Users/macie/Downloads/kns-test-screenshots.zip \
  '*.png' \
  -d docs/images/tests
```

- `-j` — pomija strukturę katalogów z archiwum,
- `'*.png'` — wypakowuje tylko obrazy PNG,
- `-d` — określa katalog docelowy.

---

## Sprawdzenie odwołań do screenshotów w `tests.md`

```bash
grep -o 'images/tests/[^)]*' docs/tests.md | while read -r file; do
  test -f "docs/$file" || echo "BRAK: $file"
done
```

Jeżeli polecenie nic nie wypisze, wszystkie wskazane screenshoty istnieją.

---

# 13. Troubleshooting i komendy pomocnicze

## Wyszukiwanie katalogu kosza w WSL

```bash
find ~ -type d -path '*/Trash/files' 2>/dev/null
```

---

## Wyszukiwanie screenshotów i `Zone.Identifier`

```bash
find ~ -type f \
  \( -name 'test-*.png*' -o -name '*Zone.Identifier*' \) \
  2>/dev/null
```

---

## Usunięcie plików `Zone.Identifier`

```bash
find docs/images -name '*:Zone.Identifier' -delete
```

> Polecenie usuwa znalezione pliki. Przed użyciem warto najpierw wykonać analogiczne `find` bez `-delete`.

---

## Wyszukiwanie konkretnego pliku ZIP w katalogu Downloads

```bash
find /mnt/c/Users/macie/Downloads \
  -maxdepth 1 \
  -type f \
  -iname '*kns*test*screenshot*.zip' \
  2>/dev/null
```

---

## Sprawdzenie konkretnego pliku

```bash
ls -lh <ścieżka-do-pliku>
```

---

# Zasada prowadzenia pliku

Każda nowa istotna komenda użyta podczas rozwoju projektu powinna zostać zapisana wraz z:

- samą komendą,
- krótkim opisem zastosowania,
- informacją, kiedy była używana,
- ostrzeżeniem, jeśli może usuwać lub modyfikować dane.

Dokument ma pełnić jednocześnie rolę:

1. **ściągi technicznej podczas pracy nad projektem,**
2. **dziennika nauki,**
3. **pomocy przy odtwarzaniu procedur testowych,**
4. **materiału pomocniczego podczas przygotowywania pracy inżynierskiej.**
