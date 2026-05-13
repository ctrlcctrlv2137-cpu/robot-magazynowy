#robot przemysłowy

### Schemat blokowy systemu (Flowchart)

```mermaid
graph TD
    %% Aktorzy
    Operator((Operator magazynu))
    RobotAct((Robot))
    Technik((Technik serwisant))

    %% Procesy Operatora
    Operator --> UC1[Uruchomienie/wyłączenie systemu]
    UC1 -.->|ext| UC2[Awaryjne zatrzymanie]
    Operator --> UC3[Odkładanie paczki]
    Operator --> UC4[Odłożenie paczki do bezpiecznej strefy]

    %% Procesy Robota
    RobotAct --> UC5[Pobranie paczki z taśmy]
    UC5 -->|inc| UC6[wybór odpowiedniej palety]
    UC6 -->|inc| UC3
    
    UC7[Zgłaszanie błędów - nieudany chwyt] -.->|ext| UC5
    UC7 -->|inc| UC8[Raport błędu]
    UC8 -->|inc| UC4

    %% Procesy Technika
    Technik --> UC9[Diagnostyka robota]
    UC10[Konserwacja i naprawa] -.->|ext| UC9
    UC11[Aktualizacja oprogramowania] -.->|ext| UC9

    %% Stylizacja dla czytelności (opcjonalnie)
    style UC2 fill:#fff4dd,stroke:#d4a017
    style UC7 fill:#fff4dd,stroke:#d4a017
    style UC10 fill:#fff4dd,stroke:#d4a017
    style UC11 fill:#fff4dd,stroke:#d4a017
