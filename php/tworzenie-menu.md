---
title: Tworzenie menu
category: php
thumbnail: menu-php.png
published: 2013-01-09T00:00:00Z
---
Początkujący webmasterzy często mają problem ze zbudowaniem dynamicznego menu z prawdziwego zdarzenia. Trudność polega na tym, aby klikając odnośnik w menu zmieniała się tylko treść bez przeładowania całego dokumentu. Najlepszym wyjściem z tej sytuacji jest zbudowanie menu w **PHP** korzystając z konstrukcji *switch*.

<!--more-->

### Przestroga dla początkujących

Najgorszym możliwym rozwiązanie jest **brak menu dynamicznego**, kiedy klikając w odsyłacz przechodzimy po prostu do innego pliku .html. W takim wypadku mając stronę zbudowaną zaledwie z 20 podstron, trzeba poświęcić 20 minut na edytowanie wyglądu każdej z osobna. Kolejnym prawie najgorszym rozwiązaniem jest budowanie menu za pomocą **iframe** lub **javascript**. Iframe to totalny przeżytek w dzisiejszych czasach spotykany najwyżej w kursach HTML. Z kolei javascrip może zostać wyłączony przez użytkownika z poziomu przeglądarki, więc po co komu menu, które nie zawsze zadziała?

### Menu zbudowane na switch

Takie menu spotykane jest w praktycznie każdym serwisie. Do jego budowy wystarczy dowolny hosting z działającym PHP.

Dobrym nawykiem podczas tworzenia menu w HTML jest używanie wykresów *ul, li*. Dlaczego? Takim menu jest bardzo łatwo stylizować za pomocą arkusza stylów. Wystarczy kilka kliknięć aby z menu poziomego menu zamieniło się w menu pionowe.

Kod HTML przykładowego menu:

	<ul>
	    <li> <a href="index.php?menu=1">Pozycja pierwsza</a> </li>
	    <li> <a href="index.php?menu=2">Pozycja druga</a> </li>
	    <li> <a href="index.php?menu=3">Pozycja trzecia</a> </li>
	</ul>

Podczas kliknięcia w dowolny odnośnik ustawiamy wartość zmiennej *menu*. Zmienna ta przekazywana jest poprzez link, to znaczy że jest widoczna w pasku adresu. Taki sposób przekazywania danych to metoda *GET*. Dane te można odczytać za pomocą tablicy super globalnej *$*GET[‚menu’].* Pod kodem HTML dodajmy skrypt PHP:

	<?php
	    echo $_GET['menu'];
	?>

Teraz po kliknięciu w dowolny odsyłacz w menu na stronie zostanie wyświetlona wartość zmiennej z adresu. Będzie to argument który przekażemy do instrukcji *switch:*

	<?php
	    $arg = (int)$_GET['menu'];
	    switch ($arg)
	    {
	        case 1:
	            echo "Pozycja pierwsza";
	            break;
	        
	        case 2:
	            echo "Pozycja druga";
	            break;
	        
	        case 3:
	            echo "Pozycja trzecia";
	            break;
	        
	        default:
	            echo "Strona główna";
	            break;
	    }
	?>

Przeanalizujmy przykład. Do instrukcji *switch* przekazujemy wartość zmiennej z adresu URL. W zależności od numeru zostaną wykonane różne operacje (w tym wypadku wyświetlony zostanie tekst). Ostatnią pozycja *default* jest wartość domyślna, zostanie ona wykonana wtedy, kiedy zmienna *menu* nie jest określona (np. podczas pierwszego wejścia na stronę).

Zauważ, że w *switch* nie ma klamer, koniec określonego bloku oznacza rozkaz *break*. Jeżeli zgubisz gdzieś *break* to zostanie wykonanych kilka instrukcji na raz.

Menu jest praktycznie gotowe. Ostatnim krokiem jest wczytywanie podstron. W tym celu należy posłużyć się instrukcją *include()*. Ta funkcja dołącza do dokumentu inny dokument znajdujący się na serwerze. Kompletny kod może wyglądać tak:

	<ul>
	    <li> <a href="index.php?menu=1">Pozycja pierwsza</a> </li>
	    <li> <a href="index.php?menu=2">Pozycja druga</a> </li>
	    <li> <a href="index.php?menu=3">Pozycja trzecia</a> </li>
	</ul>
	
	<?php
	    $arg = (int)$_GET['menu'];
	    switch ($arg)
	    {
	        case 1:
	            echo "Pozycja pierwsza";
	            break;
	        
	        case 2:
	            echo "Pozycja druga";
	            break;
	        
	        case 3:
	            echo "Pozycja trzecia";
	            break;
	        
	        default:
	            echo "Strona główna";
	            break;
	    }
	?>

### Menu zagnieżdżone (złożone) zbudowane na switch

Jeżeli tworzymy większą stronę, rodzi się potrzeba zagnieżdżenia kilku *switchów*. Przykładowy kod HTML zagnieżdżonego menu wygląda tak`:`

	<ul>
	    <li> <a href="index.php?menu=1">Pozycja pierwsza</a> </li>
	    <li> 
	        <a href="index.php?menu=2">Pozycja druga</a>
	        <ul>
	            <li><a href="index.php?menu=2&art=1">artykul 1</a> </li>
	            <li><a href="index.php?menu=2&art=2">artykul 2</a> </li>
	        </ul>
	    </li>
	    <li> <a href="index.php?menu=3">Pozycja trzecia</a> </li>
	</ul>

Wszystko odbywa się tak jak poprzednio, jedyną różnicą jest zagnieżdżenie wykresu *ul* oraz *switcha.* Trzeba pamiętać aby przesłać dwie zmienne w adresie URL, jedna zmienna to *menu* decydująca o podstronie a druga zmienna to *art* decydująca o numerze artykułu jeżeli jest to podstrona numer 2. Zmienne łączymy w odsyłaczu za pomocą ampersandu (&).

	<?php
	    $arg = (int)$_GET['menu'];
	    $art = (int)$_GET['art'];
	    
	    switch ($arg)
	    {
	        case 1:
	            echo "Pozycja pierwsza";
	            break;
	            
	        case 2:
	            switch ($art)
	            {
	                case 1:
	                    echo "Pozycja druga - artykuł pierwszy";
	                    break;
	                    
	                case 2:
	                    echo "Pozycja druga - artykuł drugi";
	                    break;
	                    
	                default:
	                    echo "Pozycja druga";
	                    break;
	            }
	            break;
	        
	        case 3:
	            echo "Pozycja trzecia";
	            break;
	            
	        default:
	            echo "Strona główna";
	            break;
	    }
	?>
