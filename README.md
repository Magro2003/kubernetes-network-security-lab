# Kubernetes Network & Security Lab

Projekt inżynierski poświęcony budowie i stopniowej rozbudowie praktycznego laboratorium wykorzystującego konteneryzację, Kubernetes, sieci komputerowe, troubleshooting oraz wybrane mechanizmy bezpieczeństwa.

Projekt rozwijany jest etapami. Wersja **v0.1** stanowi pierwszą działającą podstawę środowiska opartego na Dockerze i Kubernetesie. W kolejnych etapach laboratorium będzie rozszerzane o bardziej zaawansowane scenariusze sieciowe, diagnostyczne i bezpieczeństwa.

---

## Cel projektu

Głównym celem projektu jest stworzenie praktycznego laboratorium umożliwiającego naukę, testowanie oraz analizę mechanizmów związanych z konteneryzacją, Kubernetesem i komunikacją sieciową.

W obecnej wersji projekt obejmuje:

- konteneryzację prostej aplikacji webowej,
- budowanie obrazu Docker,
- uruchamianie aplikacji w kontenerze,
- komunikację pomiędzy kontenerami,
- analizę sieci Docker,
- wdrożenie aplikacji do Kubernetes,
- wykorzystanie obiektów Deployment, ReplicaSet, Pod oraz Service,
- testowanie mechanizmów DNS w Dockerze i Kubernetesie,
- obserwację mechanizmu self-healing,
- skalowanie Deploymentu,
- dokumentowanie testów oraz procedur diagnostycznych.

---

## Aktualny zakres — v0.1

Wersja v0.1 zawiera:

- prostą aplikację Python / Flask,
- Dockerfile służący do budowy obrazu aplikacji,
- uruchamianie aplikacji w kontenerze Docker,
- własne sieci Docker typu `bridge`,
- komunikację kontener–kontener,
- rozwiązywanie nazw kontenerów za pomocą Docker DNS,
- test izolacji pomiędzy sieciami Docker,
- lokalny klaster Kubernetes,
- Kubernetes Deployment,
- Kubernetes Service typu `ClusterIP`,
- komunikację z Service poprzez Kubernetes DNS,
- analizę EndpointSlice,
- test mechanizmu self-healing,
- skalowanie Deploymentu z 1 do 3 replik i ponownie do 1,
- dokumentację wykonanych testów,
- screenshoty wyników testów,
- dokumentację używanych komend.

---

## Architektura

### Przepływ ruchu w Kubernetes

W aktualnej wersji klient testowy komunikuje się z aplikacją za pośrednictwem Kubernetes Service.

```text
Klient testowy
      |
      | DNS: kns-lab-service
      v
Kubernetes Service
kns-lab-service
ClusterIP :8080
      |
      | TCP / HTTP :8080
      v
Pod
      |
      v
Kontener
      |
      v
Aplikacja Flask
port 8080
```

Service wybiera Pody aplikacji za pomocą etykiety:

```text
app=kns-lab
```

Dzięki temu klient korzystający z Service nie musi znać bezpośredniego adresu IP konkretnego Poda.

### Zarządzanie zasobami aplikacji

Za utrzymywanie odpowiedniej liczby Podów odpowiada hierarchia zasobów Kubernetes:

```text
Deployment
kns-lab
      |
      v
ReplicaSet
      |
      v
Pod
      |
      v
Kontener aplikacji
```

Deployment definiuje pożądany stan aplikacji, ReplicaSet utrzymuje wymaganą liczbę Podów, a w Podzie uruchamiany jest kontener z aplikacją Flask.

---

## Struktura projektu

```text
kubernetes-network-security-lab/
├── app/
│   ├── app.py
│   └── requirements.txt
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
├── docs/
│   ├── commands.md
│   ├── tests.md
│   └── images/
│       └── tests/
├── Dockerfile
├── .dockerignore
├── .gitignore
└── README.md
```

### Najważniejsze katalogi i pliki

- `app/` — kod źródłowy aplikacji Flask,
- `k8s/` — manifesty Kubernetes,
- `docs/tests.md` — szczegółowa historia wykonanych testów,
- `docs/commands.md` — ściąga z komend używanych podczas pracy nad projektem,
- `docs/images/tests/` — screenshoty dokumentujące wyniki testów,
- `Dockerfile` — definicja obrazu Docker aplikacji,
- `.dockerignore` — pliki pomijane podczas budowania obrazu Docker,
- `.gitignore` — pliki i katalogi pomijane przez Git.

---

## Wykorzystane technologie

W obecnej wersji projektu wykorzystywane są:

- Windows 11,
- WSL2,
- Ubuntu 24.04,
- Python,
- Flask,
- Docker,
- Docker Desktop,
- Kubernetes,
- `kubectl`,
- Git,
- Cursor.

---

## Wymagania środowiska

Wersja v0.1 była tworzona i testowana przy użyciu:

- Docker Desktop z integracją WSL,
- Kubernetes uruchomionego w Docker Desktop,
- Ubuntu działającego w WSL2,
- `kubectl`,
- Git.

Kontekst Kubernetes używany podczas pracy nad projektem:

```text
docker-desktop
```

---

## Budowanie obrazu Docker

Z katalogu głównego projektu należy wykonać:

```bash
docker build -t kubernetes-network-security-lab:0.1 .
```

Sprawdzenie utworzonego obrazu:

```bash
docker image ls
```

---

## Uruchomienie aplikacji w Dockerze

Aplikację można uruchomić bezpośrednio w kontenerze:

```bash
docker run --name kns-lab \
  -p 8080:8080 \
  kubernetes-network-security-lab:0.1
```

Po uruchomieniu aplikacja jest dostępna pod adresem:

```text
http://localhost:8080
```

Oczekiwana odpowiedź:

```text
Kubernetes Network & Security Lab
```

Działające kontenery można sprawdzić poleceniem:

```bash
docker ps
```

---

## Wdrożenie aplikacji do Kubernetes

Najpierw należy sprawdzić dostępność klastra:

```bash
kubectl get nodes
```

Node powinien znajdować się w stanie:

```text
Ready
```

Następnie należy zastosować manifest Deploymentu:

```bash
kubectl apply -f k8s/deployment.yaml
```

oraz manifest Service:

```bash
kubectl apply -f k8s/service.yaml
```

Sprawdzenie utworzonych zasobów:

```bash
kubectl get pods
kubectl get deployments
kubectl get services
```

Można również sprawdzić ReplicaSet:

```bash
kubectl get replicasets
```

---

## Test komunikacji przez Kubernetes Service

Service aplikacji dostępny jest wewnątrz klastra pod nazwą:

```text
kns-lab-service
```

Do sprawdzenia komunikacji można utworzyć tymczasowy Pod z narzędziem `curl`:

```bash
kubectl run kns-test-client --rm -it \
  --image=curlimages/curl:latest \
  --restart=Never \
  -- curl -sS http://kns-lab-service:8080
```

Oczekiwana odpowiedź:

```text
Kubernetes Network & Security Lab
```

Test pozwala sprawdzić:

- działanie Service,
- rozwiązywanie nazwy przez Kubernetes DNS,
- komunikację sieciową z aplikacją,
- poprawne przekazywanie ruchu do Poda.

---

## EndpointSlice

Backendy powiązane z Service można sprawdzić poleceniem:

```bash
kubectl get endpointslices \
  -l kubernetes.io/service-name=kns-lab-service
```

EndpointSlice przechowuje informacje o endpointach, do których Kubernetes może kierować ruch przeznaczony dla danego Service.

---

## Self-healing

Mechanizm self-healing został sprawdzony poprzez ręczne usunięcie Poda zarządzanego przez Deployment.

Przykład:

```bash
kubectl delete pod <nazwa-poda>
```

Po usunięciu Poda ReplicaSet automatycznie utworzył nowy Pod, aby ponownie osiągnąć zadeklarowaną liczbę replik.

---

## Skalowanie

Deployment został przetestowany poprzez zwiększenie liczby replik z 1 do 3:

```bash
kubectl scale deployment kns-lab --replicas=3
```

Następnie sprawdzono stan Deploymentu i Podów:

```bash
kubectl get deployment kns-lab
kubectl get pods
```

Po zakończeniu testu Deployment został ponownie zmniejszony do jednej repliki:

```bash
kubectl scale deployment kns-lab --replicas=1
```

---

## Wykonane testy

W ramach wersji v0.1 wykonano i udokumentowano następujące testy:

1. budowanie obrazu Docker i uruchomienie aplikacji w kontenerze,
2. komunikacja pomiędzy kontenerami Docker,
3. rozwiązywanie nazw kontenerów za pomocą Docker DNS,
4. izolacja pomiędzy osobnymi sieciami Docker,
5. uruchomienie i weryfikacja lokalnego klastra Kubernetes,
6. pierwsze wdrożenie aplikacji przy użyciu Deploymentu,
7. test mechanizmu Kubernetes self-healing,
8. test Service, DNS oraz EndpointSlice,
9. skalowanie Deploymentu z 1 do 3 replik i ponownie do 1.

Szczegółowe procedury, użyte komendy, wyniki oraz wnioski znajdują się w pliku:

```text
docs/tests.md
```

Screenshoty dokumentujące wykonane testy znajdują się w:

```text
docs/images/tests/
```

---

## Dokumentacja komend

Komendy używane podczas budowy i testowania projektu są zapisywane w:

```text
docs/commands.md
```

Dokument zawiera polecenia związane między innymi z:

- Linux i WSL,
- Git,
- Python,
- Flask,
- Docker,
- Docker Networking,
- Kubernetes,
- troubleshootingiem,
- dokumentacją projektu.

---

## Aktualny status

Wersja **v0.1** stanowi pierwszą działającą fazę projektu.

Obecna wersja umożliwia:

- zbudowanie aplikacji jako obrazu Docker,
- uruchomienie jej w kontenerze,
- testowanie komunikacji pomiędzy kontenerami,
- analizę izolacji sieci Docker,
- wdrożenie aplikacji do lokalnego klastra Kubernetes,
- udostępnienie aplikacji za pomocą Service,
- wykorzystanie Kubernetes DNS,
- obserwację mechanizmu self-healing,
- ręczne skalowanie liczby replik,
- analizę podstawowych mechanizmów sieciowych.

---

## Plan dalszego rozwoju

W kolejnych etapach projekt będzie stopniowo rozszerzany.

Planowane obszary rozwoju obejmują między innymi:

- bardziej rozbudowane scenariusze komunikacji Pod–Pod,
- Kubernetes NetworkPolicy,
- kontrolę i izolację ruchu sieciowego,
- scenariusze troubleshootingu sieciowego,
- celowo błędnie skonfigurowane zasoby Kubernetes i ich diagnostykę,
- `securityContext`,
- Secrets,
- RBAC,
- dodatkowe mechanizmy bezpieczeństwa,
- rozszerzenie monitorowania i diagnostyki.

Projekt będzie rozwijany modułowo, dzięki czemu poszczególne mechanizmy sieciowe i bezpieczeństwa będą mogły być niezależnie testowane i analizowane.
