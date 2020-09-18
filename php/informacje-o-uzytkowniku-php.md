---
title: Informacje o użytkowniku
category: php
thumbnail: informacje-php.png
published: 2013-01-05T00:00:00Z
---
Ciekawą funkcjonalnością na stronach są **informacje o użytkowniku**. Najczęściej pokazują one **adres IP**, **rodzaj systemu operacyjnego** oraz **wersję przeglądarki**. Takie informacje przydają się także do tworzenia statystyk. Dzięki nim autor strony wie ile osób używa danego systemu lub danej przeglądarki. Autorzy witryn prawie zawsze zapisują podstawowe statystyki o osobach odwiedzających stronę lecz nie zawsze informują o tym użytkowników.

<!--more-->

## Gotowe rozwiązania

Istnieje wiele gotowych skryptów i bibliotek oferujących **wyświetlanie informacji o użytkownikach**. Przeważnie są to duże, rozbudowane skrypty statystyk, które zapisują wszelkie informacje, tworzą wykresy i zapisują odwiedziny.

W internecie można znaleźć kilka darmowych serwisów oferujących monitorowanie naszej strony. Wystarczy się zarejestrować, wkleić do źródła strony odpowiedni skrypt i to wszystko. Od tej chwili wszelkie **informacje o naszej witrynie** będą rejestrowane.

Innym sposobem jest skorzystanie  z funkcji *get_browser()*. Dostarcza nam ona kilku informacji o **systemie operacyjnym** oraz **przeglądarce** użytkownika. Informacje pobiera z tablicy super globalnej *$_SERVER[HTTP_USER_AGENT']*. Wydaje się idealnym rozwiązaniem, lecz niestety, nie na wszystkich hostingach jest włączona.

## Własny skrypt PHP

Nie chcąc korzystać z zewnętrznych skryptów i dodatkowych klas możemy napisać własny skrypt rozpoznający **wersję przeglądarki i system operacyjny.** Tak jak robią wszystkie inne skrypty, informacje te pobieramy z tablicy *$_SERVER[HTTP_USER_AGENT']*.

Wyświetlmy i przeanalizujmy zawartość zmiennej *HTTP\*USER\*AGENT*:

	<?php
	    echo $_SERVER['HTTP_USER_AGENT'];
	?>

Wynik

	Mozilla/5.0 (Windows NT 5.1; rv:17.0) Gecko/20100101 Firefox/17.0

Z takiego ciągu znaku bezproblemowo odczytujemy system operacyjny i przeglądarkę:

{% include parts/postPicture.html page=page img="inf2" %}

Można tutaj odczytać także kilka innych rzeczy oraz wersję, jednak jest to w pewnym sensie bezcelowe i tylko zaśmieca &#8222;proste&#8221; statystyki. Aby wyciągnąć **nazwę systemu i przeglądarki** można posłużyć się np. funkcją php *strpos()*. Zwraca ona pozycję wystąpienia danej frazy w tekście. Jeżeli zwróci zero - oznacza to że fragment nie został odnaleziony, jeżeli zwróci liczbą większą od 0, oznacza to że znaleźliśmy nazwę przeglądarki.

Ostatnią potrzebną rzeczą są identyfikatory systemów operacyjnych oraz przeglądarek:

Systemy:

- **Windows 2000** - *NT 5.0*
- **Windows XP** - *NT 5.1*
- **Windows Server 2003** - *NT 5.2*
- **Windows Vista** - *NT 6.0*
- **Windows 7** - *NT 6.1*
- **Windows 8** - *NT 6.2*
- **Windows 8.1** - *NT 6.3*
- **Windows 10** - *NT 10*
- **Android** - *android*
- **iPhone** - *iphone*
- **Linux** - *Linux*

Przeglądarki:

- **Internet Explorer** - *MSIE*
- **Mozilla Firefox** - *Firefox*
- **Opera** - *Opera*
- **Chrome** - *Chrome*
- **Edge** - *Edge*
- **Safari** - *Safari*

Jeżeli zechcesz, do przeglądarek można w bardzo prosty sposób dopisać rozpoznawanie wersji. Tak wygląda kompletny skrypt:

	<?php
	    $agent = "X".$_SERVER['HTTP_USER_AGENT'];
	    
	    $system = array('Windows 2000' => 'NT 5.0', 'Windows XP' => 'NT 5.1','Windows Vista' => 'NT 6.0', 'Windows 7' => 'NT 6.1',
	        'Windows 8' => 'NT 6.2', 'Windows 8.1' => 'NT 6.3', 'Windows 10' => 'NT 10', 'Linux' => 'Linux', 'iPhone' => 'iphone' , 'Android' => 'android');
	    
	    $przegladarka = array('Internet Explorer' => 'MSIE', 'Mozilla Firefox' => 'Firefox','Opera' => 'Opera', 'Chrome' => 'Chrome', 'Edge' => 'Edge');
	    
	    foreach ($system as $nazwa => $id)
	        if (strpos($agent, $id)) $system = $nazwa;
	    
	    foreach ($przegladarka as $nazwa => $id)
	        if (strpos($agent, $id)) $przegladarka = $nazwa;
	    
	    echo "Twój IP: <b>".$_SERVER['REMOTE_ADDR']."</b><BR>";
	    echo "System operacyjny: <b>".$system."</b><BR>";
	    echo "Przegladarka: <b>".$przegladarka."</b><BR>";
	?>
