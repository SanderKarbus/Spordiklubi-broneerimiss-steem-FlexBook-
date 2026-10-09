# Tarkvararakenduse kavand: Spordiklubi broneerimissüsteem "FlexBook"

Siin on spordiklubi treeningute broneerimissüsteemi FlexBook arendusprotsessi, metoodika ja arhitektuuri ülevaade.

---

## 1. Tarkvara arendusprotsess

### Arendusprotsessi peamised etapid ja eesmärgid
1. **Nõuete analüüs:** Kaardistada spordiklubi ja treenijate vajadused, panna paika funktsionaalsus ja tehniline platvorm.
2. **Süsteemi disain ja arhitektuur:** Luua rakenduse loogiline ülesehitus, andmebaasi mudel ning kasutajaliidese esmased vaated.
3. **Programmeerimine (Teostus):** Kirjutada rakenduse kood, luua andmebaas ja siduda esiosa tagasüsteemiga.
4. **Testimine:** Kontrollida, kas süsteem töötab veatult (broneeringud ei kattu, valideerimised toimivad) ja parandada leitud vead.
5. **Kasutuselevõtt ja hooldus:** Paigaldada rakendus serverisse, teha see kasutajatele kättesaadavaks ning tagada jooksva tagasiside põhjal turvauuendused.

### Arendusprotsessi mudelite võrdlus
* **Koskmudel (Waterfall):** Lineaarne ja range. Järgmine etapp algab alles siis, kui eelmine on täielikult valmis. Paindlikkus on väga madal, riskid on suured, sest töötavat tarkvara näeb alles lõpus.
* **Iteratiivne mudel (Agile):** Tsükliline. Süsteemi arendatakse jupikaupa ja täiendatakse pidevalt. Paindlikkus on kõrge ja tagasisidet saab kiiresti integreerida.

### Valitud mudel ja põhjendus
Projekti jaoks on valitud **iteratiivne mudel**. Tänapäevased veebirakendused vajavad kiiret turule tulekut. Iteratiivne lähenemine võimaldab meil esimeses tsüklis luua valmis kõige kriitilisema osa (kalendri kuvamine ja broneeringu tegemine) ning järgmistes etappides lisada teavitused ja maksesüsteemid.

### Käitumine nõuete muutumisel keset arendust
Kuna kasutame iteratiivset metoodikat, ei tekita kliendi muudatused probleeme. Kui klient soovib keset arendust muudatust:
1. Hindame selle mõju praegusele arendustsüklile.
2. Vormistame uue nõude kasutajaloona ja lisame selle tööde nimekirja (*Product Backlog*).
3. Prioritiseerime selle koos kliendiga järgmise arendustsükli planeerimisel.

---

## 2. Arendusmetoodikad

Projekti juhtimiseks valiti **Scrum** metoodika, kuna see sobib ideaalselt fikseeritud pikkusega arendustsükliteks (sprintideks).

### Tööde nimekiri (Product Backlog) ja prioriteedid
* **US1 (Kriitiline):** Kliendina soovin näha treeningute kalenderaegu, et valida endale sobiv treening.
* **US2 (Kriitiline):** Kliendina soovin broneerida kohta treeningule, et tagada endale pääs trenni.
* **US3 (Kõrge):** Treenerina soovin lisada ja muuta uusi treeningaegu, et kliendid näeksid minu graafikut.
* **US4 (Kõrge):** Kliendina soovin broneeringu tühistada, kui mu plaanid muutuvad.
* **US5 (Keskmine):** Administraatorina soovin näha statistikat trennide täituvuse kohta, et optimeerida klubi kavasid.
* **US6 (Madal):** Kliendina soovin saada meeldetuletuse e-kirjaga 2 tundi enne trenni algust.

### Esimene arendusperiood (Sprint 1 - Kestvus 2 nädalat)
Sprindi eesmärk on saavutada rakenduse miinimumfunktsionaalsus (MVP) ehk kasutajalugude **US1** ja **US2** teostamine.
* **Tegevused:** Andmebaasi baasstruktuuri loomine, tagasüsteemi (API) otspunktide loomine treeningute pärimiseks ning lihtsa kalendrivaate programmeerimine kasutajaliideses.

### Projektihaldus
Töid hallatakse visuaalsel tahvlil (nt GitHub Projects või Trello), kus koodi kirjutamise ajal liikuvad tööd veergude vahel: *To-Do* (Tegemata) -> *In Progress* (Tegemisel) -> *Testing* (Testimisel) -> *Done* (Tehtud).

---

## 3. CASE-vahendid

Projekti kavandamiseks ja disainimiseks kasutatakse kahte tarkvaraarendust toetavat CASE-vahendit:
1. **PlantUML / draw.io (Modelleerimise vahend):** Kasutatakse süsteemi tarkvaraarhitektuuri ja andmevoogude visuaalseks joonistamiseks koodi või plokkskeemide abil.
2. **GitHub Projects (Projektihaldusvahend):** Kasutatakse kasutajalugude (backlogi) haldamiseks, prioriteetide seadmiseks ja sprindi arendustegevuste jälgimiseks.

### CASE-vahendite võrdlus

| Atribuut | PlantUML / draw.io | GitHub Projects |
| :--- | :--- | :--- |
| **Peamine eesmärk** | Süsteemi modelleerimine ja skeemide loomine. | Tööde planeerimine, haldus ja meeskonna koostöö. |
| **Toetatavad tegevused** | Arhitektuuri disain, andmebaasi mudelite ja UML-skeemide joonistamine. | Kasutajalugude kaardistamine, sprindi planeerimine, ülesannete staatuse jälgimine. |
| **Muudetavus** | Skeemid on reaalajas muudetavad tekstipõhise koodi või elementide lohistamise teel. | Ülesanded on lohistatavad tulpade vahel (Kanban tahvel). |

---

## 4. Tarkvaraarhitektuur

### Rakenduse tüüp ja ülesehitus
FlexBook on disainitud **veebirakendusena**, mis põhineb **kihilisel arhitektuuril** (Layered Architecture). See jagab süsteemi selgeteks vastutusaladeks, mis teeb selle hooldamise ja testimise lihtsaks.

### Arhitektuuri komponendid ja andmevoog
1. **Esitluskiht (Kasutajaliides / Front-end):** Veebibrauseris jooksev Reacti rakendus, mida klient näeb. Kui klient vajutab nuppu "Broneeri trenn", saadab esitluskiht HTTP-päringu rakendusserverile.
2. **Äriloogika kiht (Back-end / Rakendusserver):** Node.js/Express server.
   * **Controller:** Võtab päringu vastu ja suunab selle edasi.
   * **Service:** Kontrollib äriloogikat (nt kas saalis on veel vabu kohti ja kas kasutajal on kehtiv pääse).
3. **Andmepääsu kiht (Repository):** Suhtleb otse andmebaasiga ja käivitab SQL-päringuid.
4. **Andmebaasikiht:** PostgreSQL relatsiooniline andmebaas, kus hoitakse tabeleid `Kasutajad`, `Treeningud` ja `Broneeringud`.

### Valitud arhitektuuri eelised ja puudused võrreldes Monoliidiga

| Arhitektuur | Eelised | Puudused |
| :--- | :--- | :--- |
| **Kihiline arhitektuur (Valitud)** | Selge komponentide eraldatus; ühte kihti (nt andmebaasi) saab muuta ilma esiosa lõhkumata; lihtne testida. | Andmed peavad läbima mitu kihti, mis võib väga lihtsate päringute puhul tekitada minimaalset ülekulu. |
| **Traditsiooniline Monoliit** | Kõik koos ühes suures koodihunnikus; alguses väga kiire ja lihtne üles seada. | Kood muutub kiiresti segaseks ("spagett-kood"); ühe koodivea tõttu võib kogu rakendus alla kukkuda. |
