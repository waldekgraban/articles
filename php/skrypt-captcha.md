---
title: Skrypt CAPTCHA
category: php
thumbnail: captcha-php.png
published: 2012-07-26T00:00:00Z
---
**CAPTCHA** to technika stosowana w skryptach, umożliwiająca wprowadzanie danych do formularzy tylko przez ludzi, odrzucając roboty sieciowe. Używanie CAPTCHY (potocznie kapeć) w dzisiejszych czasach jest koniecznością. Wszelkiego rodzaju skrypty i programy spamujące stały się plagą i nieodłączoną częścią internetu. Każdy niezabezpieczona strona widniejąca w wyszukiwarce google zostanie wcześniej czy później zasypana komentarzami oraz postami.

<!--more-->

## Czym jest skrypt CAPTCHA

**CAPTCHA spotykana jest w wielu formularzach** wymagających podawania danych przez użytkowników. Wyświetlana jest **w postaci obrazka lub działania matematycznego**, treść obrazka lub wynik działania należy przepisać w odpowiednie pole. Dzięki temu robot sieciowy **nie może zarejestrować nowego konta** lub dodać setek komentarzy w ciągu paru sekund.

**Skrypty CAPTCHA** są stale rozwijane i udoskonalane. Przyczyną tego są skrypty potrafiące łamać captchę, tzn. **potrafiące odczytywać tekst z obrazka** lub potrafiące obliczać działania matematyczne wyświetlone na stronie. Zaawansowane skrypty CAPTCHA mają nieczytelne litery, różne kolory, wszelkiego rodzaju kreski i losowe położenie cyfr i liter. Wszystko po to aby utrudnić odczyt CAPTCHY robotom.

## CAPTCHA obrazkowa

Jest to najskuteczniejszy rodzaj CAPTCHY spotykany na popularnych serwisach. Wymagane jest stworzenie skryptu PHP generującego losowy obrazek. Obrazek wyświetlamy w formularzu. Oprócz generowania obrazku skrypt musi zapisać treść z obrazka do zmiennej sesyjnej (np. *$_SESSION['captcha'];*).

Przykładowy kod:

	<?php
	    session_start();
	    $znaki = '0123456789';  // dozwolone znaki
	    $szerokosc = 170;       // szerokość obrazka
	    $wysokosc = 30;         // wysokość obrazka
	    $ilosc_znakow = 6;      // długość captchy
	    $str = '';              // zmienna pomocnicza
	    
	    // losowanie ciągu znkaów
	    for ($i = 0; $i < $ilosc_znakow; $i++)
	        $str .= substr($znaki, mt_rand(0, strlen($znaki) -1), 1);
	    
	    $string = $str;
	    $_SESSION['captcha'] = $string; // przypisanie do zmiennej sesyjnej
	    
	    // tworzenie obrazka o danych wymiarach
	    $im = imagecreate($szerokosc, $wysokosc);
	    
	    //kolory obrazka
	    $tlo     = imagecolorallocate($im,0,0,0);
	    $czcionka   = imagecolorallocate($im,255,255,255);
	    $siatka   = imagecolorallocate($im,78,78,78);
	    $ramka = imagecolorallocate ($im, 255, 0, 0);
	    
	    imagefill($im,1,1,$tlo); // wypełnienie tłem
	    
	    // losowanie siatki
	    for($i=0; $i<1600; $i++)
	    {
	        $rand1 = rand(0,$szerokosc);
	        $rand2 = rand(0,$wysokosc);
	        imageline($im, $rand1, $rand2, $rand1, $rand2, $siatka);
	    }
	    
	    // losowanie pozycji znaków
	    $x = rand(5, $szerokosc/(7/2));
	    
	    // dodawanie obramowania
	    imagerectangle($im, 0, 0, $szerokosc-1, $wysokosc-1, $ramka);
	    
	    // umieszczanie liter na obrazku
	    for($a=0; $a < 7; $a++)
	    {
	        imagestring($im, 6, $x, rand(4 , $wysokosc/5), substr($string, $a, 1), $czcionka);
	        $x += (5*3); // odstęp między literami
	    }
	    
	    // zwrócenie wygenerowanego obrazka, ustawienie typu mime na GIF
	    header("Content-type: image/gif");
	    imagegif($im);
	    imagedestroy($im);
	?>

Możesz zmienić wymiary i kolory generowanego obrazka. Plik *captcha.php* wyświetlamy teraz w formularzu tak jak każdy inny obrazek. Obok należy umieścić pole tekstowe na wpisanie treści z obrazka. Aby sprawdzić czy wpisany kod CAPTCHA jest poprawny, pobieramy metodą POST wartość wpisaną w polu tekstowym i porównujemy ze zmienną sesyjną o nazwie *captcha*.

Sprawdzanie poprawności captchy

	<?php
	    if ($_POST['kod']==$_SESSION['captcha'])
	    {
	        echo "Wpisałeś poprawny kod.";
	        //reszta skryptu
	    }
	    else
	    {
	        echo "Wpisany kod jest niepoprawny.";
	    }
	?>

## CAPTCHA tekstowa.

**Skuteczność tego rozwiązania jest mniejsza**, jednak bardzo dobrze sprawdza się to na małych stronach. Mniejsza skuteczność spowodowana jest łatwością napisania skryptu wykonującego podane przez nas działanie matematyczne. Jednak aby taka sytuacja zaistniała, **ktoś musiał by napisać skrypt specjalnie pod naszą stronę**, wycinający działanie matematyczne za pomocą wyrażeń regularnych.

Działanie jest identyczne jak w przykładzie graficznym. Zamiast generować obrazek, wystarczy wygenerować kilka cyfr za pomocą funkcji *rand*. Wynik zapisujemy do zmiennej sesyjnej, a działanie wyświetlamy w formularzu.

## Podsumowanie

Z mojego doświadczenia wynika, że wystarczy nawet proste &#8222;Wpisz wyraz KOT w polu obok&#8221; aby zablokować 99% robotów sieciowych i nie potrzeba do tego żadnej CAPTCHY. Jednak lepiej dmuchać na zimne niż któregoś dnia obudzić się z 10MB spamu w bazie danych.