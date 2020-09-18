---
title: Tablice asocjacyjne
category: php
thumbnail: tablica-asocjacyjna.png
published: 2012-08-03T00:00:00Z
---
**Tablica asocjacyjna** jest nazywana także tablicą skojarzeniową. Przechowuje ona dane parami, każdej wartości przyporządkowuje odpowiedni klucz. Aby uzyskać dostęp do danej wartości trzeba znać nazwę jej klucza. W przypadku standardowych tablic dostęp do poszczególnych elementów zyskujemy za pomocą indeksu w tablicy.

<!--more-->

## Tworzenie tablic asocjacyjnych

Tworzenie tablicy asocjacyjnej jest bardzo proste. Możemy skorzystać z konstrukcji *array()*. Wszystko odbywa się praktycznie tak samo jak w przypadku standardowych tablic jednowymiarowych.

	<?php
	    $cena = array(
	        'jogurt' => '2 zł',
	        'maslo' => '1.50 zł',
	        'zapiekanka' => '4 zł',
	        'gazeta'  => '5 zł'
	    );
	?>

Oprócz tego możemy edytować wartość elementu odnosząc się bezpośrednio po kluczu:

	<?php
	    $cena['zapiekanka'] = "16 zł";
	    $cena['jogurt'] = "2 zł";
	?>

## Wyświetlanie tablic asocjacyjnych

Chcąc wyświetlić zwykłą tablicę przeważnie korzysta się z pętli *for*, która wyświetla po kolei jej wszystkie indeksy. Wyświetlanie tablicy asocjacyjnej jest nieco inne jednak działa w podobny sposób. Używa się do tego celu *foreach*. Jest to specyficzny rodzaj pętli, która służy do przeglądania zawartości **tablic asocjacyjnych** oraz obiektów:

	<?php
	    
	    $cena = array(
	        'jogurt' => '2 zł',
	        'maslo' => '1.50 zł',
	        'zapiekanka' => '4 zł',
	        'gazeta'  => '5 zł'
	    );
	    
	    foreach ($cena as $klucz => $wartosc)
	        echo $klucz." -> ".$wartosc."<br>\n";
	    
	?>

Wynik powyższego skryptu jest następujący:

	jogurt     -> 2 zł
	maslo      -> 1.50 zł
	zapiekanka -> 4 zł
	gazeta     -> 5 zł

W ten sposób z pomocą *foreach* wyświetlimy wszystkie elementy tablicy, ich klucze oraz przypisane im wartości. Kolejnym sposobem na wyświetlenie całej tablicy asocjacyjnej jest użycie konstrukcji językowej *print_r()*. Wyświetla ona tablicę wraz kluczami i wartościami. Należy zwrócić uwagę, że nie mamy żadnej możliwości modyfikacji tego co i w jaki sposób zwraca *print_r*. Jest to funkcja służąca raczej do debbugowania i szybkiego podglądnięcia działania skryptu.

	<?php
	    $cena = array(
	        'jogurt' => '2 zł',
	        'maslo' => '1.50 zł',
	        'zapiekanka' => '4 zł',
	        'gazeta'  => '5 zł'
	    );
	    
	    print_r($cena);
	?>

Wynik powyższego skryptu jest następujący:

	Array ( [jogurt] => 2 zł [maslo] => 1.50 zł [zapiekanka] => 4 zł [gazeta] => 5 zł )

## Sortowanie tablic asocjacyjnych

Sortowanie zwykłych tablic jednowymiarowych dokonujemy za pomocą **sort()**. Funkcja ta kompletnie nie sprawdza się w przypadku tablic asocjacyjnych. Do tablic asocjacyjnych używamy **asort()** oraz **ksort()**.

**asort()** - sortuje tablicę asocjacyjną według wartości  
**ksort()**- sortuje tablicę asocjacyjną według klucza
{: .alert-info} 

<?php
	$cena = array(
	    'jogurt' => '2 zł',
	    'maslo' => '1.50 zł',
	    'zapiekanka' => '4 zł',
	    'gazeta'  => '5 zł'
	);
	
	asort($cena);
	
	foreach ($cena as $klucz => $wartosc)
	    echo $klucz." kosztuje ".$wartosc."<br>\n";
?>

Wynikiem działania powyższego skryptu będzie posortowana tablica asocjacyjna (ceny od najmniejszej do największej):

	maslo kosztuje      1.50 zł
	jogurt kosztuje     2 zł
	zapiekanka kosztuje 4 zł
	gazeta kosztuje     5 zł
