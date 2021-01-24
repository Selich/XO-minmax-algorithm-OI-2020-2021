## XO - minmax algoritam - Operaciona Istrazivanja 2020/2021

Studenti:
- Dusan Urosevic IN 16/2017
- Nikola Selic IN 43/2017

### Sadrzaj

- Pokretanje
- Opis problema 
- Algoritam
- Implementacija
- Literatura

### Pokretanje
Potrebno je instalirati [node](https://nodejs.org/dist/v14.15.4/node-v14.15.4-x86.msi) za pokretanje Javascript koda.

Nakon instalacije, u root direktorijumu pokrenete
```bash
npm install  # instalira potrebne biblioteke
npm run start # pokrece program
```

### Opis problema 
**XO** (engleski Tic-tac-toe) je igra sa dva igraca X i O, u kojoj igraci naizmenicno zauzimaju pozicije na tabli dimenzije 3x3. Igra se zavrsava kada neki od igraca zauzme 3 pozicije koje formiraju horizontalnu, vertikalnu ili dijagonalnu liniju ili ukoliko su sve pozicije na tabli popunjene. U prvom slucaju onaj igrac koji je formirao liniju je pobednik, a u drugom je igra neresena.

XO spada u skup **resenih** igara odnosno ako pretpostavimo da su oba igraca racionalna mozemo u bilo kom trenutku (stanju table) odrediti krajnji rezultat igre. Dodatno, ukoliko su i X i O racionalni igra se uvek zavrsava neresenim rezultatom. Iz ovog razloga XO se pretezno koristi u pedagoske svrhe i kao primer problema u grani vestacke inteligencije koja se bavi pretragom stabla igre. **Stablo igre** je usmeren graf u kom cvorovi predstavljaju moguca stanja igre dok grane predstavljaju pojedinacne poteze koji dovode do prelaska iz jednog u drugo stanje. Koren ovog stabla je pocetno stanje igre a listovi su sva moguca zavrsna stanja igre.
![alt text](https://upload.wikimedia.org/wikipedia/commons/thumb/d/da/Tic-tac-toe-game-tree.svg/1024px-Tic-tac-toe-game-tree.svg.png)

### Algoritam
U ovom resenju problema, X (korisnik) bira proizvoljno svoj potez dok O uvek igra optimalan potez koji bira po **MinMax** algoritmu. Ovaj algoritam pretpostavlja da su oba igraca racionalna pri nalazenju optimalnog poteza. Osnovna ideja **MinMax** algoritma je da se za trenutno stanje odredi najbolji potez kroz rekurzivnu pretragu stabla stanja. Zavisno od toga da li je u trenutnom stanju na redu X ili O algoritam ce traziti maksimalnu/minimalnu vrednost medju svim stanjima koji se dobijaju kao rezultat dozvoljenog poteza za trenutno stanje. Primer pretrage stabla stanja je dat na sledecoj slici. Metrike zavrsnih stanja su sledece: +1 ako pobedi X, -1 ako pobedi O, 0 ako je igra neresena.

![alt text](https://miro.medium.com/max/700/1*lp2_TjdcFI4CtLjBsJFTRw.png)

U pocetnom stanju za koje procenjujemo najbolji potez igraca X postoje 3 moguca poteza. Prvi dovodi do pobede X dok druga 2 zavisno od poteza igraca O mogu dovesti ili do pobede O (2.0, 2.2) ili do pobede X (3.0, 3.1). 

Propagacija metrike kroz rekurziju se vrsi na sledeci nacin:
- zavrsna stanja vracaju odgovarajucu vrednost metrike (+1,-1,0)
- ukoliko je u trenutnom stanju na redu O bira se minimalna vrednost potomaka
- ukoliko je u trenutnom stanju na redu X bira se maksimalna vrednost potomaka
![alt_text](https://miro.medium.com/max/700/1*03whc5D5uXodVQRT2k22Qg.png)

Nakon analize citavog stabla MinMax ce vratiti vrednost +1 kao najbolji moguci ishod za igraca X. Ukoliko bi i X automatski igrao potez po Minmax algoritmu izabrao bi prvi potez zato sto je metrika rezultujuceg stanja jednaka najboljoj vrednosti koju je MinMax nasao.

### Implementacija
Algoritam i interfejs je implementiran u Javascript programskom jeziku. 

Sam algoritam je implementiram u src/MinMax.js datoteci.

Implementirane metode su:
```bash
checkWin(board)
minmax(board, isMaximizing)
bestMove(board)
aiTurn()
```
#### checkWin(board)
Namena ove funkcije je da se pronadje da li trenutno stanje table(board) odgovara sablonu koji predstavlja stanje pobede.

Stanja mogu biti:
```bash
O|O|O  O| |  O| | 
 | |   O| |   |O| 
 | |   O| |   | |O
```
Tj. da odgovaraju pobedi kada je prisutan vertikalni, horizontalni ili dijagonalni sablon.
#### bestMove(board)
Parametri funkcije:
- board - trenutno stanje table

Nadfunkcija koja se poziva da bismo trazili najbolji moguci potez za trenutno stanje table.
Iterise kroz sva polja na tabli i trazi slobodno polje
Postavljamo da je to prazno polje zauzeo AI(O) i zatim pozivamo minmax funkciju sa izmenjenim stanjem tabele.
Nakon rekurzivnog rada, minmax funkcija vraca score.
Proverava da li je vraceni score trenutno najbolji tj. bolji od prethodno najboljeg, i ako jeste, postavljamo ga kao novog najboljeg.
Takodje, pamtimo lokaciju datog polja kao indekse matrice(i,j)
Nakon izvrsavanja vracamo i,j koordinate tj indekse polja najboljeg poteza.


#### minmax(board, isMaximizing)
Parametri funkcije:
- board - trenutno stanje table
- isMaximizing - Boolean koji oznacava da li trazimo maksimizaciju/minimizaciju score-a (da li je na redu korisnik-X ili AI-O)

Pri pokretanju funkcije dolazi do provere da li je trenutno stanje tabele u stanju pobede, tj. da li je doslo u zavrsno stanje.
Ako jeste, upisujemo odgovarajucu vrednost iz niza SCORE (1-X pobedio,-1-O pobedio,0-nereseno).

Inicijalizujemo trazeni score (bestscore) na negativnu ili pozitivnu beskonacnost u zavisnosti da li trazimo maksimizaciju ili minimizaciju scora(isMaximizing).

Iterisemo kroz tablu i trazimo slobodna polja.
Proveravamo da li trenutno igra AI, korisnik(isMaximizing) i upisujemo u dato polje odigran potez.

Nakon toga, vrsimo rekurzivni poziv minmax algoritma sa:
- board - izmenjenim stanjem table 
- !isMaximizing - postavimo da je drugi igrac na potezu i da trazimo minimizaciju ili maksimizaciju scora

Nakon izlaska na povrsinu rekurzije, proveravamo da li dobijeni score optimalan.
Vracamo optimalan score.


#### aiTurn()
Igra najbolji potez za AI (O) koji dobija pozivom bestMove(board). Povecava counter broja odigranih poteza i proverava da li je tabela popunjena.



### Literatura
- [MinMax-TicTacToe](https://gsurma.medium.com/tic-tac-toe-creating-unbeatable-ai-with-minimax-algorithm-8af9e52c1e7d#:~:text=Minimax%20is%20a%20recursive%20algorithm,minimize%20the%20worst%20case%20scenario)
- [TicTacToe](https://en.wikipedia.org/wiki/Tic-tac-toe)
