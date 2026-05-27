# robot przemysłowy

### Schemat blokowy systemu (Flowchart)

```mermaid
graph TD
    %% Aktorzy
    Operator((Operator magazynu))
    RobotAct((Robot))
    Technik((Technik serwisant))

    %% Procesy Operatora
    Operator --> UC1[Uruchomienie/<br/>wyłączenie systemu]
    UC1 -->|ext| UC2[Awaryjne zatrzymanie]
    UC2 -.-> UC1

    %% Procesy Robota
    RobotAct --> UC5[Pobranie paczki<br/>z taśmy]
    UC5 -->|inc| UC6[Wybór odpowiedniej<br/>palety]
    UC6 -->|inc| UC3[Odkładanie paczki]

    UC7[Zgłaszanie błędów<br/>- nieudany chwyt] -->|ext| UC5
    UC7 -->|inc| UC8[Raport błędu]
    UC8 -->|inc| UC4[Odiożenie paczki<br/>do bezpiecznej strefy]

    %% Procesy Technika
    Technik --> UC9[Diagnostyka robota]
    UC10[Konserwacja<br/>i naprawa] -->|ext| UC9
    UC11[Aktualizacja<br/>oprogramowania] -->|ext| UC9

    %% Stylizacja dla czytelności
    style UC2 fill:#fff4dd,stroke:#d4a017
    style UC7 fill:#fff4dd,stroke:#d4a017
    style UC10 fill:#fff4dd,stroke:#d4a017
    style UC11 fill:#fff4dd,stroke:#d4a017
```

### Diagram stanów systemu (State Diagram)

```mermaid
stateDiagram-v2
    [*] --> Wyłączony
    
    Wyłączony --> Uruchomienie: Operator włącza system
    
    Uruchomienie --> Gotowy: System zainicjalizowany
    Uruchomienie --> Błąd: Inicjalizacja nie powiodła się
    
    Gotowy --> Czekanie: Robot czeka na paczkę
    Gotowy --> Awaryjne_zatrzymanie: Naciśnięty przycisk STOP
    
    Czekanie --> Pobieranie: Paczka dostarczona
    
    Pobieranie --> Selekcja_palety: Paczka pobrana
    Pobieranie --> Błąd_chwytu: Chwyt nie powiódł się
    
    Selekcja_palety --> Odkładanie: Paleta wybrana
    
    Odkładanie --> Czekanie: Paczka odłożona
    Odkładanie --> Błąd: Niemożliwość dostępu do palety
    
    Błąd_chwytu --> Zgłoszenie_błędu: Błąd zarejestrowany
    Zgłoszenie_błędu --> Bezpieczna_strefa: Paczka do strefy bezpieczeństwa
    Bezpieczna_strefa --> Czekanie: Odłożona, czekaj na następną
    
    Błąd --> Diagnostyka: Technik badania
    
    Diagnostyka --> Konserwacja: Wymagana naprawa
    Diagnostyka --> Gotowy: Brak problemu
    
    Konserwacja --> Aktualizacja_oprogramowania: Update dostępny
    Konserwacja --> Gotowy: Naprawa ukończona
    
    Aktualizacja_oprogramowania --> Gotowy: Update zainstalowany
    
    Awaryjne_zatrzymanie --> Wyłączony: System zatrzymany
    
    Wyłączony --> [*]
    
    %% Stylizacja
    classDef error fill:#fff4dd,stroke:#d4a017,stroke-width:2px
    classDef normal fill:#e1f5e1,stroke:#2ecc71,stroke-width:2px
    classDef warning fill:#ffe1e1,stroke:#e74c3c,stroke-width:2px
    
    class Błąd error
    class Błąd_chwytu error
    class Awaryjne_zatrzymanie warning
    class Gotowy normal
    class Czekanie normal
    class Odkładanie normal
```

# Analiza zastosowanych wzorców projektowych

## Zastosowane wzorce projektowe

### State (Stan)
**Cel wzorca:**  
Umożliwia zmianę zachowania obiektu w zależności od jego aktualnego stanu, bez stosowania rozbudowanych instrukcji warunkowych.

**Zastosowanie w systemie:**  
Robot magazynowy może znajdować się w kilku stanach, takich jak: bezczynność, praca, błąd oraz tryb serwisowy. Każdy stan definiuje własne zachowanie robota oraz reakcję na polecenia operatora i zdarzenia awaryjne.

**Korzyści:**  
- czytelna logika sterowania,
- łatwe dodawanie nowych stanów,
- ograniczenie złożoności kodu.

---

### Command (Polecenie)
**Cel wzorca:**  
Hermetyzuje żądanie jako obiekt, co umożliwia parametryzację, kolejkowanie oraz logowanie poleceń.

**Zastosowanie w systemie:**  
Polecenia takie jak uruchomienie systemu, pobranie paczki, odkładanie paczki czy awaryjne zatrzymanie są realizowane jako osobne obiekty typu Command.

**Korzyści:**  
- rozdzielenie nadawcy polecenia od wykonawcy,
- łatwe testowanie i rozbudowa systemu,
- możliwość rejestrowania operacji.

---

### Observer (Obserwator)
**Cel wzorca:**  
Zapewnia automatyczne powiadamianie wielu obiektów o zmianach stanu innego obiektu.

**Zastosowanie w systemie:**  
Robot powiadamia zarejestrowanych obserwatorów o wystąpieniu błędów, nieudanym chwycie lub zdarzeniach awaryjnych. Obserwatorami są m.in. panel operatora oraz moduł logowania błędów.

**Korzyści:**  
- luźne powiązania pomiędzy komponentami,
- łatwe dodawanie nowych modułów monitorujących,
- dobra skalowalność.

---

### Strategy (Strategia)
**Cel wzorca:**  
Umożliwia dynamiczną zmianę algorytmu działania bez ingerencji w kod klienta.

**Zastosowanie w systemie:**  
Strategie są wykorzystywane m.in. do wyboru palety lub sposobu obsługi wyjątków, w zależności od sytuacji operacyjnej.

**Korzyści:**  
- elastyczność systemu,
- możliwość łatwej modyfikacji algorytmów,
- poprawa czytelności kodu.

---

### Facade (Fasada)
**Cel wzorca:**  
Upraszcza dostęp do złożonego systemu poprzez jeden spójny interfejs.

**Zastosowanie w systemie:**  
Operator oraz technik korzystają z jednego interfejsu sterującego, który ukrywa szczegóły implementacyjne systemu robota.

**Korzyści:**  
- prostsze i bezpieczniejsze API,
- mniejsze ryzyko błędnego użycia systemu,
- lepsza separacja odpowiedzialności.

---

## Wzorce projektowe, które nie pasują do systemu

### Builder (Budowniczy)
**Dlaczego nie pasuje:**  
Wzorzec Builder służy do tworzenia złożonych obiektów krok po kroku. W analizowanym systemie nie występuje potrzeba konstruowania obiektów o skomplikowanej strukturze — kluczowa jest logika sterowania, a nie proces budowy obiektów.

---

### Abstract Factory (Abstrakcyjna fabryka)
**Dlaczego nie pasuje:**  
Wzorzec ten jest używany do tworzenia rodzin powiązanych obiektów. System robota nie wymaga dynamicznej wymiany całych rodzin komponentów, dlatego zastosowanie tego wzorca byłoby nieuzasadnione i nadmiernie skomplikowałoby architekturę.

---

## Podsumowanie
Zastosowane wzorce projektowe wspierają modularność, elastyczność oraz bezpieczeństwo sterowania robotem magazynowym. System koncentruje się na zarządzaniu zachowaniem i reakcją na zdarzenia, dlatego wzorce konstrukcyjne nie znajdują w nim praktycznego zastosowania.
``

### Diagram klas UML

```mermaid
classDiagram

%% =====================
%% FACADE
%% =====================
class RobotControlFacade {
  +startSystem()
  +stopSystem()
  +emergencyStop()
  +runDiagnostics()
}

RobotControlFacade --> RobotController

%% =====================
%% CONTROLLER
%% =====================
class RobotController {
  -currentState : RobotState
  +setState(state)
  +executeCommand(cmd)
}

%% =====================
%% STATE PATTERN
%% =====================
class RobotState {
  <<interface>>
  +handle()
}

class IdleState
class WorkingState
class ErrorState
class MaintenanceState

RobotState <|.. IdleState
RobotState <|.. WorkingState
RobotState <|.. ErrorState
RobotState <|.. MaintenanceState

RobotController --> RobotState

%% =====================
%% COMMAND PATTERN
%% =====================
class Command {
  <<interface>>
  +execute()
}

class StartSystemCommand
class StopSystemCommand
class PickPackageCommand
class PlacePackageCommand
class EmergencyStopCommand

Command <|.. StartSystemCommand
Command <|.. StopSystemCommand
Command <|.. PickPackageCommand
Command <|.. PlacePackageCommand
Command <|.. EmergencyStopCommand

RobotController --> Command

%% =====================
%% STRATEGY PATTERN
%% =====================
class PalletSelectionStrategy {
  <<interface>>
  +selectPallet()
}

class DefaultPalletStrategy
class PriorityPalletStrategy

PalletSelectionStrategy <|.. DefaultPalletStrategy
PalletSelectionStrategy <|.. PriorityPalletStrategy

RobotController --> PalletSelectionStrategy

%% =====================
%% OBSERVER PATTERN
%% =====================
class Subject {
  <<interface>>
  +attach()
  +detach()
  +notify()
}

class Observer {
  <<interface>>
  +update()
}

class Robot {
  +pickPackage()
  +placePackage()
  +reportError()
}

class ErrorLogger
class OperatorPanel

Subject <|.. Robot
Observer <|.. ErrorLogger
Observer <|.. OperatorPanel

Robot --> Observer

%% =====================
%% AKTORZY (logicznie)
%% =====================
class Operator
class Technician

Operator --> RobotControlFacade
Technician --> RobotControlFacade
```
