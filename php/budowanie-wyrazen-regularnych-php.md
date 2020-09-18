---
title: Budowanie wyrażeń regularnych
category: php
thumbnail: budowanie-wyrazen-regularnych.png
published: 2013-09-22T00:00:00Z
---
**Wyrażenia regularne** pełnią istotną rolę w PHP. Umożliwiają one opisywanie i przetwarzanie długich ciągów znaków. Dzieje się to na zasadzie porównania danego ciągu znaków z **określonym wzorem**, ułożonym przez programistę.

<!--more-->

## Czym są wyrażenia regularne?

**Wyrażenia regularne** to bardzo przydatne narzędzie istniejące nie tylko w **języku PHP** (na którym dziś się skupimy). Dzięki nim, możliwe jest wykonanie wielu operacji na raz np. wyszukiwanie określonych elementów, walidacja adresów email, walidacja adresów URL oraz zamiana poszczególnych fragmentów strony.

Zadaniem programisty jest zaprojektowanie określonego wzorcu (ang. **pattern**). Następnie odpowiednie funkcje **wyrażeń regularnych** próbują dopasować do niego tekst. W artykule opiszę proces projektowania wzorców, które będziesz mógł następnie użyć w odpowiednich funkcjach **wyrażeń regularnych**.

## Operatory wyrażeń regularnych

<table class="table table-striped table-condensed">
	<thead>
		<tr>
			<th style="text-align: center;">
				Operator
			</th>
			
			<th style="text-align: center;">
				Opis
			</th>
			
			<th style="text-align: center;">
				Priorytet
			</th>
		</tr>
  </thead>
  <tr>
    <td style="text-align: center;">
      <code>^</code>
    </td>
    
    <td>
      Operator początku ciągu znaków.
    </td>
    
    <td style="text-align: center;">
      prawostronny
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>$</code>
    </td>
    
    <td>
      Operator końca ciągu znaków.
    </td>
    
    <td style="text-align: center;">
      lewostronny
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>.</code>
    </td>
    
    <td>
      Operator jednego dowolnego znaku.
    </td>
    
    <td style="text-align: center;">
      n/d
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>[a-z]</code>
    </td>
    
    <td>
      Dowolne litery od a do z.
    </td>
    
    <td style="text-align: center;">
      n/d
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>[A-Z]</code>
    </td>
    
    <td>
      Dowolne litery od A do Z.
    </td>
    
    <td style="text-align: center;">
      n/d
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>[0-9]</code>
    </td>
    
    <td>
      Dowolne cyfry od 0 do 9.
    </td>
    
    <td style="text-align: center;">
      n/d
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>[^ ]</code>
    </td>
    
    <td>
      Operator negacji zbioru.
    </td>
    
    <td style="text-align: center;">
      n/d
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>*</code>
    </td>
    
    <td>
      Operator powtórzenia 0 lub więcej razy.
    </td>
    
    <td style="text-align: center;">
      lewostronny
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>+</code>
    </td>
    
    <td>
      Operator powtórzenia 1 lub więcej razy.
    </td>
    
    <td style="text-align: center;">
      lewostronny
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>?</code>
    </td>
    
    <td>
      Operator powtórzenia 1 lub 0 razy.
    </td>
    
    <td style="text-align: center;">
      lewostronny
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>|</code>
    </td>
    
    <td>
      Operator alternatywy.
    </td>
    
    <td style="text-align: center;">
      obustronny
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>( )</code>
    </td>
    
    <td>
      Wyrażenie wewnątrz nawiasów jest <b>atomem</b> (rozpatrujemy je jako całość).
    </td>
    
    <td style="text-align: center;">
      n/a
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>{x}</code>
    </td>
    
    <td>
      Operator powtórzenia dokładnie ‚x’ razy.
    </td>
    
    <td style="text-align: center;">
      lewostronny
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>{x,y}</code>
    </td>
    
    <td>
      Operator powtórzenia minimum ‚x’ i maksimum ‚y’ razy.
    </td>
    
    <td style="text-align: center;">
      lewostronny
    </td>
  </tr>
</table>

Zaprezentowane operatory na pierwszy rzut oka mogą wydawać się niezrozumiałe. Zanim zaczniemy **budować wyrażenia regularne,** musisz poznać jeszcze kilka ważnych informacji. To czy operator jest lewostronny, prawostronny czy obustronny decyduje o rozmieszczeniu argumentu. Jeżeli operator jest lewostronny, to dotyczy atomu znajdującego się po jego lewej stronie np.: _Kar*ol_ &#8211; w tym wzorze operator gwiazdki dotyczy atomu litery _r_, _(Karol)*_ w tym wypadku dotyczy wyrażenia atomowego w nawiasie.

**Atom** - jest to najmniejsze wyrażenie we wzorze. Nie rozpatrywujemy go na mniejsze składowe. Z atomów zbudowany jest wzór wyrażenia regularnego. Atomem może być pojedyncza litera lub cyfra. Dzięki nawiasom okrągłym, możemy całe wyrażenie znajdujące się wewnątrz nich przedstawić jako atom.
{: .alert-info} 

Wydaje się to logiczne. Jeżeli dla operatora lewostronnego podamy cały nawias, wtedy argumentami są wszystkie atomy znajdujące się wewnątrz nawiasu (może być to np. cały napis).

W artykule będę posługiwał się przede wszystkim funkcją *preg_match()*. Funkcje wyrażeń regularnych rozpoczynające się przedrostkiem *preg* pochodzą z rodziny PCRE (język Perl) i są bardziej optymalne od funkcji z *ereg* z rodziny POSIX. Tak podaje dokumentacja i wiele innych źródeł dostępnych w internecie.

## Działanie funkcji Preg_match

Funkcja *Preg_match()* jest podstawową funkcją PHP dotyczącą wyrażeń regularnych. Przyjmuje ona 2 argumenty: *wzorzec* oraz *ciąg znaków*. Jeżeli ciąg znaków zostanie pomyślnie dopasowany do wzorca &#8211; zwraca wartość logiczną *true*, w przeciwnym razie *false*.

int *preg_match* (string *$wzorzec*, string *$ciag_znakow*)
{: .alert-info} 

**Przekazując wzorzec do funkcji** należy pamiętać aby umieścić go pomiędzy dwoma slashami. Przykładowe wywołanie funkcji może wyglądać następująco:

	<?php
	    if (preg_match('/abc/', 'abcdabcdabcd'))
	    {
	        echo "Wzorzec 'abc' pasuje do ciągu znaków 'abcdabcdabcd'";
	    }
	    else
	    {
	        echo "Błąd: Wzorzec nie pasuje do ciągu znaków";
	    }
	?>

## Praktyczne wykorzystanie operatorów

Przyjrzyjmy się jak działa kod umieszczony w poprzednim akapicie. Prosty wzorzec _abc_ pasuje do ciągu znaków _abcdabcdabcd_. Co więcej, został on dopasowany w wielu miejscach:

{% include parts/postPicture.html page=page img="php-regex-1" %}

Posłużmy się **operatorem początku ciągu znaków** (czyli _^_). Dopiszmy go do naszego wzorca _^abc_ i porównajmy do tego samego ciągu znaków:

{% include parts/postPicture.html page=page img="php-regex-2" %}

Tym razem wzorzec został dopasowany tylko jeden raz. Zamiast operatora  początku ciągu można także użyć **operator końca**. Można także użyć obydwu operatorów na raz. Po zmianie operatorów wzorzec nie zostanie dopasowany (chyba że zmienimy ciąg znaków). Przyjrzyjmy się obrazkowi:

{% include parts/postPicture.html page=page img="php-regex-3" %}

Operatory początku i końca dają małe możliwości. Aby lepiej zrozumieć **wyrażenia regularne**, zróbmy kilka testów z operatorem dowolnego znaku ‚<span style="color: #ff6600;">.</span>‚ i **operatorami powtórzeń**.

**Operator kropki** _._ zastępuje **jeden** dowolny znak. Nie można określić czy jest to operator lewostronny czy prawostronny, ponieważ nie przyjmuje on argumentów. Używając operatorów powtórzeń, należy pamiętać, że dotyczą one **atomu** znajdującego się po lewej stronie operatora. Spójrz na przykład:

{% include parts/postPicture.html page=page img="php-regex-4" %}

Czas na **operatory powtórzeń**. Użyję _*_, _?_ oraz _+_. Ich opis znajdziesz w tabelce na samej górze artykułu. Są to operatory lewostronne, czyli najważniejszy jest atom znajdujący się po lewej stronie operatora. Pamiętaj że atomem może być także cały nawias:

{% include parts/postPicture.html page=page img="php-regex-5" %}

Robi się coraz ciekawiej. Proponują przetestować nawiasy oraz grupy znaków _[a-z]_, _[A-Z]_ i _[0-9]_. Wszystko odbywa się w ten sam sposób, należy tylko zapamiętać, że nawias jest traktowany jako jeden znak czyli atom. Grupy znaków w nawiasach kwadratowych także traktujemy jako jeden atom.

{% include parts/postPicture.html page=page img="php-regex-6" %}

Na obrazku wyżej, ostatni przykład musi zaczynać się od przynajmniej jednej dużej litery. Następnie maksymalnie jeden raz może pojawić się litera _a_. Następnie wiele razy może się powtórzyć końcówka _rol_. Napis musi kończyć się końcówką _rol_.

Ostatnimi operatorami są **operatory szczegółowego powtarzania** (nawiasy klamrowe) oraz **operator alternatywy**. Alternatywa to **funkcja logiczna** wybierająca pomiędzy argumentami. Działanie jest bardzo proste:

{% include parts/postPicture.html page=page img="php-regex-7" %}

Wszystkie operatory zostały przedstawione. Teraz wystarczy trochę poćwiczyć, aby nabrać wprawy. Warto nauczyć się ich na pamięć.Dobrze opanowane **wyrażenia regularne** dają duże pole do popisu, każdemu kto programuje w **PHP.**

## Walidacja adresu email za pomocą wyrażeń regularnych

Pisanie długich wyrażeń regularnych najlepiej rozbić na kilka osobnych części. Na końcu każdą część zamykamy w nawias tworząc atom i łączymy je innym atomem bądź operatorem.

Najbardziej przydanym i najczęściej stosowanym przypadkiem używania **wyrażeń regularnych** jest **walidacja adresu email**. Każdy większy serwis sprawdza poprawność wprowadzonego adresu email. Poprawny adres ma format: _login@serwer.domena_.

Pisząc **wyrażenie regularne** **walidujące adres emailowy**, mamy na prawdę dużo możliwości. Możemy ustalić jakiej długości ma być login, czy **adres może zawierać cyfry** i czy może zaczynać się od cyfry. Możemy ustalić minimalną długość serwera albo dopuszczalne nazwy serwerów (np.: gmail, interia i o2). Możemy także ustalić domenę i dopuszczać w rejestracji tylko końcówki _.pl_.

Zacznijmy od loginu. Przyjmuję, że login w adresie email powinien mieć długość od 4 do 20 znaków. Dopuszczam w nim wszelkie znaki. Całość łączę w nawias:

**Wzorzec walidacji loginu:** {% raw %}_([a-z\|A-Z\|0-9]{4,20})_{% endraw %}

Między loginem a serwerem znajduje się małpa, ale ten znak dodamy na końcu łącząc poszczególne atomy. Czas na serwer. Przyjmijmy, że powinien mieć on długość od 2 do 10 znaków. Wszystko będzie analogicznie jak w przykładzie wyżej, zmieni się tylko ilość znaków:

**Wzorzec walidacji serwera:** _([a-z\|A-Z\|0-9]{2,10})_

Między loginem a domeną musi znajdować się kropka, dodamy ją na samym końcu podczas łączenia atomów. Ostatnim krokiem jest domena. Mogli byśmy ograniczyć ją od 2 do 3 znaków, jednak na potrzeby kursu przyjmuję, że chcemy adresy email Polaków, Niemców oraz domeny &#8222;.com&#8221;. Posłużymy się operatorem alternatywy:

**Wzorzec walidacji domeny:** _(pl\|gr\|com)_

Ostatnim krokiem jest połączenie wszystkich atomów w jeden wzorzec. **Nie można zapomnieć** o **znaku początku i końca ciągu** znaków. **Uwaga! Ponieważ kropka jest operatorem wyrażeń regularnych, a chcemy użyć znaku kropki jako oddzielenie serwera od domeny, dlatego poprzedzamy ją dwoma backslashami.** Ostateczna postać wyrażenia:

**Walidacja adresu email:** _/^([a-z\|A-Z\|0-9]{4,20})@([a-z\|A-Z\|0-9]{2,10})\\\\.(pl\|gr\|com)$/_

	<?php
	    if (isset($_GET['email']))
	    {
	        $email = $_GET['email'];
	        $wynik = preg_match('/^([a-z|A-Z|0-9]{4,20})@([a-z|A-Z|0-9]{2,10})\\.(pl|gr|com)$/', $email);
	        
	        if ($wynik)
	        {
	            echo "Wpisany adres jest poprawny! :)";
	        }
	        else echo "Błąd: proszę wpisać poprawny adres email!";
	    
	    }
	?>

Standardowo nie wklejam żadnych gotowców. Pełno **gotowych rozwiązań** można znaleźć w internecie.