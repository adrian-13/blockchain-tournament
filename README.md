# Dokumentácia projektu: Blockchain Turnajový Systém

## **1. Úvod a účel projektu**
Blockchain Turnajový Systém je navrhnutý ako decentralizované riešenie pre organizáciu a správu turnajov prostredníctvom blockchain technológie. Projekt zabezpečuje spravodlivé podmienky pre hráčov, transparentnosť v distribúcii výhier a efektívne riadenie viacerých turnajov súčasne.

---

## **2. Vlastnosti a funkcionality**

### **2.1 Viac turnajov naraz**
- Každý turnaj má jedinečné identifikačné číslo (ID), čo umožňuje súbežné organizovanie viacerých turnajov.
- Turnaje môžu byť v rôznych stavoch, ako napríklad:
  - Otvorené na registráciu.
  - Aktívne (v priebehu hry).
  - Ukončené (s distribúciou výhier).

### **2.2 Flexibilné parametre turnaja**
Organizátor môže pri vytváraní turnaja nastaviť:
- **Názov turnaja:** Viditeľné označenie turnaja pre hráčov.
- **Časové parametre:**
  - Čas začiatku registrácie.
  - Čas začiatku turnaja.
  - Čas ukončenia turnaja.
- **Poplatok za registráciu:** Výška vstupného poplatku pre hráčov.
- **Maximálny počet hráčov:** Obmedzenie počtu účastníkov turnaja.
- **Počet výhercov:** Počet hráčov, ktorí dostanú výhry.
- **Rozdelenie výhier:** Percentuálne podiely z prize poolu pre jednotlivé pozície.
- **Provízia organizátora:** Percentuálny podiel pre tvorcu turnaja z prize poolu.

### **2.3 Distribúcia výhier**
- **Automatická distribúcia:** Výhry sú rozdelené automaticky po ukončení turnaja.
- **Fallback mechanizmus:** Ak automatická distribúcia zlyhá, hráči si môžu výhry manuálne vyzdvihnúť cez funkciu `claimReward`.
- **Nevyzdvihnuté výhry:** Po 30 dňoch sa presunú do globálneho prize poolu.

### **2.4 Globálny prize pool**
- Uchováva nevyzdvihnuté výhry.
- Používa sa na financovanie budúcich turnajov alebo môže byť spravovaný tvorcom kontraktu.

### **2.5 Frontend integrácia**
- Podpora dynamického nastavovania parametrov turnajov cez užívateľské rozhranie.
- Prehľad o aktuálnych turnajoch, registráciách hráčov a distribúcii výhier.

---

## **3. Funkcie smart kontraktu**

### **3.1 Organizácia turnajov**

#### **Vytváranie turnaja (`createTournament`)**
- Funkcia na inicializáciu nového turnaja s definovanými parametrami.
```solidity
function createTournament(
    string memory _name,
    uint256 _releaseTime,
    uint256 _startTime,
    uint256 _endTime,
    TournamentParameters memory _params
) public;
```

#### **Registrácia hráčov (`registerForTournament`)**
- Hráči sa môžu registrovať na turnaj zaplatením poplatku, ak je povinný.
```solidity
function registerForTournament(uint256 _tournamentId) public payable;
```

#### **Uloženie skóre (`submitScore`)**
- Počas aktívneho turnaja môžu hráči nahrávať svoje skóre.
```solidity
function submitScore(
    uint256 _tournamentId,
    uint256 _score,
    uint256 _lines,
    string memory _playerName
) public;
```

#### **Spustenie turnaja (`startTournament`)**
- Funkcia, ktorú volá organizátor na aktiváciu turnaja.
```solidity
function startTournament(uint256 _tournamentId) public onlyTournamentCreator(_tournamentId);
```

#### **Ukončenie turnaja (`endTournament`)**
- Po ukončení turnaja sa vypočítajú a prerozdelia výhry, prípadne sa pridajú do `pendingRewards`.
```solidity
function endTournament(uint256 _tournamentId) public nonReentrant onlyTournamentCreator(_tournamentId);
```

### **3.2 Správa výhier**

#### **Vyzdvihnutie výhier (`claimReward`)**
- Ak automatická distribúcia zlyhá, hráči môžu manuálne vyzdvihnúť svoje výhry.
```solidity
function claimReward() public nonReentrant;
```

#### **Nevyzdvihnuté výhry (`reclaimUnclaimedRewards`)**
- Nevyzdvihnuté výhry po 30 dňoch sú presunuté do globálneho prize poolu.
```solidity
function reclaimUnclaimedRewards() public nonReentrant;
```

#### **Globálny prize pool (`useGlobalPrizePool`)**
- Tvorca kontraktu môže použiť globálny prize pool na financovanie ďalších turnajov.
```solidity
function useGlobalPrizePool(uint256 _tournamentId) public onlyOwner;
```

---

## **4. Práva majiteľa kontraktu**
- Vytváranie a úprava turnajov.
- Správa globálneho prize poolu:
  - Použitie na ďalšie turnaje.
  - Výber nadbytočných prostriedkov (ak je to povolené).
- Úpravy systému (napr. pridávanie nových funkcií alebo pravidiel).

---

## **5. Globálny prize pool**
1. **Nevyzdvihnuté výhry:** Po uplynutí 30-dňovej lehoty sa presúvajú do globálneho prize poolu.
2. **Použitie:** Prostriedky z poolu sa môžu pridať do prize poolu budúcich turnajov.
3. **Správa:** Tvorca môže obmedzene vyberať nadbytočné prostriedky (napr. nad stanovenú hranicu).

---

## **6. Frontend integrácia**
- Používatelia môžu vytvárať a spravovať turnaje prostredníctvom formulárov.
- Zobrazenie prehľadného dashboardu s aktuálnymi, budúcimi a ukončenými turnajmi.
- Možnosť registrácie hráčov a ukladania skóre v reálnom čase.
- Prepojenie s Web3 peňaženkami (napr. MetaMask) na priamu interakciu s kontraktom.

---

## **7. Bezpečnostné odporúčania**
1. **Validácia parametrov:** Na frontende overovať správnosť údajov (napr. súčet `prizeDistribution`, správne časové intervaly).
2. **Testovanie na škálovanie:** Simulovať rôzne scenáre, ako je veľký počet hráčov alebo turnajov.
3. **Ochrana proti útokom:** Pravidelne auditovať kontrakt a implementovať ochranu pred reentrancy útokmi.

---

## **8. Záver**
Blockchain Turnajový Systém poskytuje robustnú a flexibilnú platformu pre organizovanie turnajov na sieti Polygon. Vďaka svojej transparentnosti a decentralizovanému prístupu je ideálny na široké použitie a možno ho ďalej rozširovať podľa potrieb organizátorov a hráčov.

