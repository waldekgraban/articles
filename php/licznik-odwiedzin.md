---
title: Licznik odwiedzin
category: php
thumbnail: licznik-odwiedzin-php.png
published: 2012-07-26T00:00:00Z
---
**Licznik odwiedzin** prezentuje się fajnie na wszystkich stronach, niezależnie od jej popularności i wielkości. Duże serwisy potrzebują statystyk aby analizować ruch w danych miesiącach i popularność odpowiednich podstron. Małe strony używają ich raczej do ozdoby, jako fajny bajer i funkcjonalność. **Liczniki odwiedzin** obrazują także użytkownikom serwisu o jego popularności, jeżeli odwiedzin jest mało nie świadczy to zbyt dobrze o stronie (przynajmniej patrząc z perspektywy użytkownika).

<!--more-->

## Liczniki odwiedzin

**Liczniki odwiedzin** mogą być zaawansowane i proste. Mogą być zbudowane na **plikach tekstowych** oraz na **bazie danych MySQL**. Mogą być **odporne na odświeżanie** lub zwiększać się co każde kliknięcie. Można także zliczać ilość kliknięć w dany link (np. w dziale download).

Pełnią one funkcje statystyczną. Doskonale obrazują popularność strony. Pamiętaj, że **umieszczanie na stronie licznika odwiedzin** w pewnych wypadkach może mieć skutki negatywne, szczególnie w przypadku młodych stron, które na początku będą miały po 5 odwiedzin dziennie.

### Licznik odwiedzin na plikach

Zaprezentowany niżej licznik jest **odporny na odświeżenia strony**. Zapisuje ciastko o nazwie nasza-strona.pl. Możemy przedłużyć żywotność ciastka np. do 24h. Jeżeli użytkownik odwiedza stronę pierwszy raz, pobierana jest aktualna wartość licznika, zwiększana o 1 a następnie nadpisywana. **Skrypt licznika** musi znajdować się na samej górze strony (jeszcze przed tekstem) ponieważ wymaga tego funkcja *SetCookie*.

Funkcja *flock* zabezpiecza plik licznika na czas kiedy prowadzone są operacje odczytu i zapisu. Gdyby funkcja nie została użyta wtedy wartość pliku została by utracona gdy stronę otworzą dwie osoby w tej samej chwili.

	<?php
	    //ob_start();
	    
	    if(!$_COOKIE['naszastrona']=="1")
	    {
	        $plik="licz.txt";
	        
	        //odczytujemy aktualną wartość z pliku
	        $file=fopen($plik, "r");
	        flock($file, 1);
	        $liczba=fgets($file, 16);
	        flock($file, 3);
	        fclose($file);
	        $liczba++; //zwiększamy o 1
	        
	        //zapisujemy nową wartość licznika
	        $file=fopen($plik, "w");
	        flock($file, 2);
	        fwrite($file, $liczba++);
	        flock($file, 3);
	        fclose($file); 
	        
	        setcookie("naszastrona","1");
	        //ob_end_flush();
	    }
	?>

Aby skrypt działał poprawnie, należy utworzyć plik licznika *licz.txt* i nadać mu prawa do odczytu i zapisu. Aby wyświetlić stan licznika wystarczy w dowolnym miejscu na stronie zaincludować plik *licz.txt*. Co prawda ładniej i bezpieczniej było by wczytać tą wartość za pomocą *fgets*, tak jak w kodzie wyżej. Wybór pozostawiam Tobie`.`

	<?php include ('licz.txt'); ?>

### Licznik odwiedzin na bazie MySQL

**Licznik oparty o bazę danych** daje nam większe możliwości. Zaprezentuję w jaki sposób napisałem licznik na tej stronie. Pokazuje on liczbę unikalnych odwiedzin oraz dodatkowo możemy wyświetlić liczbę odwiedzin z dzisiejszego dnia. Skrypt musi zostać umieszczony na samej górze dokumentu, z powodu ustawienia ciasteczka.

Do poprawnego działania skryptu potrzebujemy tabeli o nazwie **counter** z dwoma polami. Pierwsze pole nosi nazwę **data** i jest typu *date*. Drugie pole nosi nazwę **licznik** i jest typu *int*. Ponieważ pole przechowujące datę nie będzie się powtarzać możemy ustawić indeksy, przyśpieszy to przeszukiwanie tabeli. Poniżej znajduje się przykładowa komenda do utworzenia tabeli:

	CREATE TABLE IF NOT EXISTS `counter` (
	    `data` date NOT NULL DEFAULT '0000-00-00',
	    `licznik` int(11) NOT NULL DEFAULT '1',
	    PRIMARY KEY (`data`)
	) ENGINE=MyISAM DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;

Przyjmuję, że połączenie z bazą MySQL zaimplementujesz na własną rękę, gdzieś na początku dokumentu (jeszcze przed skryptem licznika).

	<?php
	    //ob_start();
	    function licznik_dzis()
	    {
	        $sql = "SELECT `licznik` FROM `counter` ORDER BY `data` DESC LIMIT 0,1";
	        $licznik = mysql_result(mysql_query($sql), 0, 0);
	        return $licznik;
	    }
	    
	    function licznik_all()
	    {
	        $sql = "SELECT sum(`licznik`) FROM `counter`";
	        $licznik = mysql_result(mysql_query($sql), 0, 0);
	        return $licznik;
	    }
	    
	    function licznik_last()
	    {
	        $sql = mysql_query("SELECT `data` FROM `counter` ORDER BY `data` DESC LIMIT 0,1");
	        $data = mysql_result($sql, 0, 0);
	        return $data;
	    }
	    
	    if(!$_COOKIE['naszastrona']=="1")
	    {
	        $data = date('Y-m-d');
	        if (licznik_last()==$data)
	            $sql = mysql_query("UPDATE `counter` SET `licznik`=`licznik`+1 WHERE `data` = '$data';");
	        else
	            $sql = mysql_query("INSERT INTO `counter` (`data`, `licznik`) VALUES ('$data',1)");
	    
	        setcookie("naszastrona","1");
	        //ob_end_flush();
	    }
	?>

Kod jest długi, ponieważ dołączyłem do niego 3 funkcje. Ułatwią nam one pracę i kod jest dzięki temu bardziej przejrzysty. Ja osobiście funkcje trzymam w osobnych plikach aby nie robić bałaganu, Ty zrobisz jak uważasz.

Skrypt za pomocą ciastka sprawdza czy odwiedzamy stronę pierwszy raz. **Jeżeli odwiedzamy stronę pierwszy raz, wartość pola *licznik* przy dzisiejszej dacie w tabeli zwiększa się o jeden**. Jeżeli funkcja sprawdzająca najnowszą datę w tabeli (*licznik_last()*), wykryje że w tabeli nie ma jeszcze dzisiejszej daty, wtedy zostanie ona dodana i ma przypisaną wartość 1. Każda następna osoba w tym dniu zwiększy wartość licznika przy dzisiejszej dacie. Tak wygląda tabela po kilku dniach pracy licznika:

	2010-01-01    42
	2010-01-02    24
	2010-01-03    12
	2010-01-04    251

Obok dni mamy ilość odwiedzin w danym dniu. Jeżeli jakiś dzień nie pojawi się w tabeli, oznacza to że nie było wtedy żadnych odwiedzin. Aby wyświetlić skrypt w dowolnym miejscu na stronie skorzystamy z dołączonych wcześniej funkcji`:`

	<?php
	    Odwiedzin dziś: <?php echo licznik_dzis(); ?><br>
	    Odwiedzin wszystkich: <?php echo licznik_all(); ?><br>
	?>

Efektem działania powyższego skryptu będzie:

	Odwiedzin dziś: 42
	Odwiedzin wszystkich: 812

## Błędy w działaniu licznika

Ten akapit dopisałem, ponieważ wiele osób skarży na źle działający licznik. Objawia się to brakiem odporności na odświeżanie strony.

Jeżeli licznik nie jest odporny na odświeżanie to prawdopodobnie pliki Cookies nie zostają ustawione. **Ważna informacja**: Pliki cookies muszą zostać ustawione od razu po wczytaniu strony jeszcze w chwili gdy nic nie zostało wyświetlone.

Jeżeli zechcesz ustawić plik cookies gdzieś w środku strony to prawdopodobnie zobaczysz komunikat: *Warning: Cannot modify header information headers already sent by (output started at..)*.

Komunikat nie musi zostać wyświetlony, ponieważ można go wyłączyć w configu serwera, a więc wszystko zależy od hostingu. Mimo wszystko jeżeli jesteś pewny, że pliki cookies zostają ustawiane w poprawnym miejscu (czyli na samej górze dokumentu), problem może tkwić w kodowaniu znaków. Większość kodowań wstawia różne niewidzialne znaki-śmiecie, które lubią generować powyższe błędy z buforem. **Polecam użyć kodowania "UTF-8 bez BOOM"**.

Jeżeli posiadasz dokument w innym kodowaniu, możesz go przekonwertować do **UTF-8 bez BOM** np. za pomocą programu Notepad++.

Ostatnim wyjściem na naprawienie licznika jest użycie funkcji **ob_start()**, która czyści bufor w tym miejscu, w którym chcemy go wyczyścić. W kodzie źródłowym liczników umieściłem wywołania funkcji **ob_start()** oraz **ob_end_flush()**. Są one aktualnie objęte komentarzami. Jeżeli spotkasz błąd opisany wyżej, lub licznik nie działa " po prostu usuń komentarze lub zmień kodowanie dokumentu.

## Podsumowanie

Obecnie, pisanie własnych liczników jest rzadkością. Wszystko za sprawą różnych systemów zarządzania treścią, które **liczniki odwiedzin mają wbudowane**. Pisanie własnego licznika ma sens tylko w przypadku pisania własnej strony od zera, bez korzystania z żadnego gotowego silnika.