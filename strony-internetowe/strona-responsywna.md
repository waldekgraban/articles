---
title: Strona responsywna
category: strony-internetowe
thumbnail: strona-responsywna.png
published: 2014-08-14T00:00:00Z
---
**Strona responsywna** to nowość jeżeli chodzi o strony przeznaczone dla urządzeń mobilnych. Tworzenie stron określonych mianem **responsywnych** daje nam bardzo dużą elastyczność i wiele możliwości. **Strony responsywne** same dostosowują się do rozdzielczości urządzenia, na którym są przeglądane, niezależnie czy jest to PC, urządzenie mobilne czy telewizor.

<!--more-->

## Strony przystosowane do telefonów

Od czasu pojawienia się systemu Android oraz telefonów komórkowych, **użytkownicy mobilni stanowią bardzo duży procent odwiedzających**. Napisanie strony, która nie jest przystosowania do telefonów jest w dzisiejszych czasach nieodpowiedzialne. Ryzykując w ten sposób musimy liczyć się z faktem stracenia dużej liczby potencjalnych odbiorców i klientów. Nie będę się rozpisywał za wiele na temat **stron mobilnych**, ponieważ napisałem już o tym artykuł [mobilna wersja strony na telefon](/html-css/mobilna-wersja-strony-na-telefon/)

W poprzednim artykule omawiam rozwiązanie, polegające na tworzeniu stron mobilnych w tradycyjny sposób. Zwykle polegało to na przygotowaniu drugiej wersji serwisu, i podmienianiu w zależności od wykrytego systemu. Czasem podmieniany był cały szablon a w innych wypadkach przekierowanie na inną domenę serwisu.

## Czym jest strona responsywna?

Obecnie jedynym słusznym rozwiązaniem jest **wdrożenie strony responsywnej**. Strona responsywna to po prostu **responsywny szablon**.

**Strona responsywna** lub ang. **responsible web design** (**RWD**) - to strona, która dopasowuje swoją szerokość i układ elementów do rozdzielczości na jakiej jest wyświetlana. Bardzo dobrze współpracują z *flat design* aczkolwiek nie jest zasadą ich łączenie.
{: .alert-info} 

Strony responsywne "narodziły" się od wersji CSS3 (arkusza stylów w wersji 3) ponieważ mocno rozbudowano możliwości **Media Queries**. Został on wtedy poszerzone o nowe możliwości *min-width* oraz *max-width*, dzięki którym możliwe jest rozpoznawanie rozdzielczości okna użytkownika. W zależności od wykrytych rozdzielczości, wczytujemy różne arkusze stylów, które tę samą strukturę HTML wyświetlają w całkiem inny sposób. Niektóre elementy są przesuwane, niektóre (te mniej ważne) całkowicie znikają.

### Flat design

Jak zostało napisane wyżej, koncepcja **stron responsywnych** oraz **flat design** narodziły się prawie równocześnie i są często łączone. Wynika to z faktu, że responsywność najłatwiej osiągnąć właśnie w nieskomplikowanych szablonach graficznych - w których nie ma nadmiaru grafiki.

**Flat design** - koncepcja projektowania szablonów stron, której **głównym założeniem jest prostota**. Elementy strony są **kwadratowe** lub prostokątne, nie występują Cienie ani dodatkowe ozdoby. Strona jest uproszczona do maksimum, co przekłada się na większą czytelność i przejrzystość szablonu.
{: .alert-info} 

Najważniejsze **zalety stron responsywnych** to:

- brak konieczności aktualizacji treści w dwóch różnych miejscach (istnieje tylko jedna wersja serwisu)
- brak zamieszania z przekierowaniem domen, przełączaniem szablonów oraz detekcją urządzeń
- nie trzeba się martwić o *duplicate-content*
- jest to sposób oficjalnie **rekomendowany przez Google**
- dostosowuje się nie tylko do różnych urządzeń, ale także do różnych rozdzielczości ekranu (jest najbardziej elastycznym rozwiązaniem)

<del>Jeżeli składasz zamówienie na stronę to strona responsywna będzie kosztować Cię więcej. Jeżeli wykonasz ją sam, wszystko będziesz miał za darmo.</del>

**Aktualizacja maj 2019:**  
Strony responsywne to obecnie standard na rynku. Każda firma, która zajmuje się projektowaniem stron **powinna dostarczyć responsywny szablon**, a więc taki, który będzie pasował do każdego urządzenia. W jednym z oświadczeń Google potwierdził, że od roku 2018 strony, które nie są responsywne będą systematycznie tracić zasięgi oraz pozycję w wyszukiwarce.

Warto też wspomnieć, że w chwili obecnej użytkownicy mobilni stanowią 40% wszystkich czytelników mojego bloga - jest to bardzo dużo. Gdyby nie responsywna strona, nie byliby oni w stanie czytać tego bloga na urządzeniach innych niż komputer.

## Tworzenie strony responsywnej

Niezależnie czy chcesz **zrobić stronę responsywną** od początku, czy chcesz **przerobić stary szablon na szablon responsywny**, musisz wiedzieć o kilku najważniejszych rzeczach. Pierwszym krokiem zawsze jest dodanie odpowiednich wpisów *meta*. Zapewniają one poprawne wyświetlanie strony na urządzaniach mobilnych, m.in. **zapobiegają skalowaniu**.

	<meta name="viewport" content="width=device-width, initial-scale=1.0" />

Meta-tag *viewport* zapewnia poprawne dostosowanie szerokości oraz brak powiększenia. Jeżeli zapomnisz go umieścić, strona będzie prezentować się źle.

Kolejnym najważniejszym krokiem jest **zaplanowanie kompozycji szablonu**. Musisz zdecydować jakie elementy mają być wyświetlane na pełnej rozdzielczości, a jakie na telefonie. W standardowej konfiguracji, na pełnej rozdzielczości wyświetlana jest **część główna strony oraz boczne menu, co daje razem dwie kolumny**. Oczywiście niektóre strony mogą wcale nie mieć menu, lub mieć aż 2 boczne kolumny + środkową będącą treścią strony.

Musisz mieć świadomość, że **każda strona dopasowana do urządzań mobilnych musi być zbudowana "na jednej kolumnie"**. Jedna kolumna jest jedynym rozwiązaniem, jakie zapewnia nam poprawne skalowanie. Kiedy szablon ma jedną kolumnę, ustawiamy jego szerokość na *width:100%*, dzięki temu szerokość kolumny dopasuje się do ekranu niezależnie od jego wielkości.

Takiej samej zasady trzymam się na tym blogu. Wdrożyłem proste rozwiązanie szablonu responsywnego. W pełnej okazałości strona posiada 2 kolumny, po zmianie wielkości okna cała prawa kolumna menu znika. Wiąże się to oczywiście z ukryciem niektórych informacji **ale nie ma innego wyjścia**. Musisz przemyśleć, jakie informacje zostaną ukryte dla użytkowników urządzeń mobilnych. Ja telefonom wyświetlam tylko:

- logo
- menu nawigacyjne
- treść strony.

Znika ankieta, wtyczka facebooka, linki oraz informacje o autorze. Niestety na ekranie *320x480* **nie zmieścimy** tyle co na *1600x900*. Gdybym się uparł, mógłbym zmieścić jeszcze jeden moduł w układzie jednej kolumny, nad menu lub pod menu, jednak nie są to informacje niezbędne więc nic nie stoi na przeszkodzie aby je ukryć.

### Arkusz stylów

Jeżeli zastanowisz się nad kompozycją, czas wziąć się za **arkusz stylów**.

	/*najwieksza rozdzielczosc laptopów*/
	@media only screen and (max-width: 1000px) {
	    #prawe_menu { display: block; }
	}
	
	/*średni telefon lub IPad*/
	@media only screen and (max-width: 680px) {
	    #prawe_menu { display: none; }
	}

Jak widzisz są tu tylko dwie **reguły media queries**. To ile ich dodasz zależy od Twojego serwisu. Umieszczenie tego kodu w arkuszu stylów powoduje, że dla urządzeń z szerokością ekranu w granicach *1-680px* prawe menu znika, natomiast dla urządzeń z rozdzielczością większą niż *680px* menu się pojawia. Osobiście na blogu stosuję 3 takie reguły i więcej mi nie potrzeba, używam **flat design** więc blog posiada mało elementów. Rozbudowane serwisy posiadają po 5-7 reguł, które **znacząco zmieniają wygląd strony nawet przy nieznacznych** zmianach rozdzielczości.

Istnieje ważna zasada podczas planowania kompozycji. Możesz a nawet **musisz ukrywać odpowiednie elementy na mniejszych rozdzielczościach** używając *display: none*. **Nie wolno natomiast utworzyć dwóch osobnych menu**, jednego dla telefonów drugiego dla komputerów. Nie można pokazywać ich i ukrywać w zależności od wykrytej rozdzielczości. Jest to powielanie treści, przez co strona może być nieciekawa dla wyszukiwarki, a w ekstremalnym wypadku grozi banem *duplicate-content*.

Na stronie muszą znajdować się pojedyncze elementy, a ich położeniem sterujemy za pomocą arkusza stylów.

### Responsywne menu

Strona responsywna to też responsywne menu oraz wszystkie elementy znajdujące się na niej. **Budowa responsywnego menu** może pochłonąć dużo czasu. Menu responsywne charakteryzuje się tym, że zawsze jest pionowe i rozwija się dopiero po kliknięciu.

Podstawowe menu można wykonać za pomocą **zwykłego stylu CSS bez żadnych dodatkowych bibliotek**. Ważne jest, aby zbudować menu na znacznikach *ul* *li*. Są to HTMLowe znaczniki listingu, i bardzo wygodnie można zmieniać ich orientację pionową/poziomą. Cała sztuczka polega na tym, aby dla dużych rozdzielczości ekranu nadać elementom listy atrybut *float: left* a dla małych *float: none*. Dokładnie taki mechanizm zastosowałem na blogu więc zmieniając szerokość okna przeglądarki możesz zobaczyć jak działa.

{% include parts/postPicture.html page=page img="responsywne-menu" %}

Istnieją gotowe narzędzia służące do zaprojektowania responsywnego menu np. SlickNav. Skrypt ten jest na tyle popularny, że bardzo często spotykamy go **w płatnych szablonach**. Dodatkową zaletą jest to, że SlickNav jest **całkowicie darmowy także do zastosowań komercyjnych**. Przerobienie menu na **menu responsywne** to dwie linijki kodu.

Jedynym warunkiem do użycia tego skryptu jest posiadanie menu zbudowanego w hierarchii *ul li*.

### Obrazki

Ważnym aspektem, na który należy zwrócić szczególną uwagę są obrazki. W **szablonie responsywnym** obrazki muszą się automatycznie skalować wraz z szerokością strony. Aby osiągnąć taki efekt, można dodać do arkusza stylów takie globalne ustawienie dla obrazków:

	img{
	    max-width: 100%;
	    height: auto;
	    width: auto;
	}

Oczywiście reguła sprawdzi się tylko dla obrazków będących statyczną częścią treści strony np. zdjęć w artykule strony.

### Elementy graficzne szablonu

Jeżeli korzystasz z koncepcji *flat-design* a więc uproszczenia strony do minimum - budowa szablonu nie będzie skomplikowana. Tak jak w przypadku obrazków, szerokość elementów graficznych (np. logo lub tytuł menu) powinna być podana w wartościach procentowych.

Jeżeli nie jest możliwe ustalenie wartości procentowych, należy ustawić wartości procentowe **dla pojemnika będącego rodzicem**, a następnie podmieniać element graficzny (np. logo) na mniejsze gdy braknie na nie miejsca - w zależności od wykrytej rozdzielczości.

**Tworząc szablon responsywny** możemy ustalać wysokość elementów graficznych stosując bezwzględną jednostkę *px*. Wynika to z faktu, że **strona responsywna musi dostosować się do urządzenia tylko poziomo**, pionowo nigdy miejsca nam nie braknie, najwyżej strona zacznie się przewijać.

#### Strony ze skomplikowaną szatą graficzną

Jeżeli jesteś właścicielem firmy, ze skomplikowanym motywem graficznym (np. restauracje), **niezbędna podczas przeróbki będzie pomoc grafika**, który wykonywał szablon strony. W takim wypadku dla większości elementów graficznych nie można ustalić wymiarów procentowych, ponieważ w przypadku zmiany rozmiaru okna grafika będzie niewyraźna (skalowanie).

Niestety, w takim scenariuszu, wszystkie elementy graficzne trzeba ukrywać i zastępować mniejszymi odpowiednikami. Często skomplikowany szablon graficzny trzeba po prostu lekko uprościć.

### Procentowe rozmiary elementów

W szablonie responsywnym powinniśmy używać jednostek procentowych. **Priorytetem jest nadanie procentowej szerokości**, aby mogła się zmieniać wraz ze zmianą rozdzielczości. Nie powinniśmy ustalać sztywnych rozmiarów żadnych obrazków, one także powinny skalować się razem z szablonem.

Wartości stałe powinniśmy natomiast nadawać czcionkom, trudno przewidzieć jak będzie wyglądać czcionka, której rozmiar zależny jest od innych elementów. Ja na blogu stosuję rozmiar *15-17px* i jest to jednostka stała. Czcionki dla komputerów PC są średnio o *1-2px* większe niż czcionki dla urządzeń mobilnych.

### Względne rozmiary czcionek

Ciekawym rozwiązaniem, które warto znać, jest używanie dla czcionek tzw. jednostek względnych. W CSS są to np. jednostki *em* oraz *rem*. Jeżeli jakiś obiekt ma ustawioną czcionkę o wartości *font-size: 2.5em*, oznacza to, że czcionka ma być 2.5 razy większa niż wartość odziedziczona z rodzica. **Cały mój responsywny szablon na tym blogu** oparty jest właśnie o jednostki *em*. Jedynym miejscem gdzie nadaję wartość bazową w jednostce bezwzględnej jest główny element strony czyli *body*.

Jaka jest korzyść tego rozwiązania? Dla różnych rozdzielczości ekranu zmieniam tylko i wyłączanie rozmiar czcionki elementu *body*. Wszystkie **inne elementy strony dziedziczą z niego** i rozmiary czcionek zmieniają się automatycznie.

## Podgląd responsywny strony

Bardzo dobry dodatek do **testowania naszego szablonu** udostępnia najnowsza wersja przeglądarki Firefox. Pozwala ona z opcji włączyć *Narzędzia dla twórców witryn* > *Widok responsywny*. Dzięki temu możemy podglądać jak strona responsywna zachowuje się w różnych rozdzielczościach:

{% include parts/postPicture.html page=page img="testowanie-strony-responsywnej" %}

Taki sam dodatek posiada wbudowany przeglądarka Google Chrome. Aby go włączyć należy uruchomić narzędzia deweloperskie klawiszem *F12* a następnie widok responsywny skrótem klawiaturowym *CTRL+SHIFT+M*.

## Prosty szablon responsywny

Nie przeczę, że zbudowanie zaawansowanej strony w sposób **responsywny** może być trudne i czasochłonne. Jednak prosty szablon na potrzeby bloga, każdy da radę zrobić samodzielnie. Przykładem jestem ja ponieważ dałem radę najpierw napisać dla siebie szablon w koncepcji **flat design**, a po kilku miesiącach przerobić go na **szablon responsywny**. Pokażę Ci jak łatwo stworzyć **prosty responsywny szablon**.

Na potrzeby artykułu stworzyłem mało skomplikowany szkielet szablonu. Jego kod HTML jest następujący:

	<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
	"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"W>
	<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="pl" lang="pl">
	
	<head>
	    <meta http-equiv="content-type" content="text/html; charset=utf-8" />
	    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
	    <link rel="stylesheet" href="style.css" />
	    <title>Szablon responsywny!</title>
	</head>
	
	<body>
	    <div id="header">
	        <div id="logo"><h1>Logo strony</h1></div>
	    </div>
	    
	    <div id="wrapper">
	        <div id="content">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Proin nibh augue, suscipit a, scelerisque sed, lacinia in, mi. Cras vel lorem. Etiam pellentesque aliquet tellus. Phasellus pharetra nulla ac diam. Quisque semper justo at risus. Donec venenatis, turpis vel hendrerit interdum, dui ligula ultricies purus, sed posuere libero dui id orci. Nam congue, pede vitae dapibus aliquet, elit magna vulputate arcu, vel tempus metus leo non est.<br><br> Etiam sit amet lectus quis est congue mollis. Phasellus congue lacus eget neque. Phasellus ornare, ante vitae consectetuer consequat, purus sapien ultricies dolor, et mollis pede metus eget nisi. Praesent sodales velit quis augue. Cras suscipit, urna at aliquam rhoncus, urna quam viverra nisi, in interdum massa nibh nec erat.</div>
	        <div id="menu">
	            <h5>Menu główne</h5>
	            <ul>
	                <li><a href="#">Strona główna</a></li>
	                <li><a href="#">Link 1</a></li>
	                <li><a href="#">Link 2</a></li>
	                <li><a href="#">Link 3</a></li>
	            </ul>
	        </div>
	    </div>
	    </body>
	</html>

Posiada też kod CSS:

	/* ogólne ------------------ */
	* {margin:0px; padding: 0px;}
	body { font: 16px Verdana; line-height:1.6; background: #EDE6DE; }
	#wrapper { width:90%; overflow:hidden; margin: 0 auto; }
	a {color: #354458;}
	
	/* logo ------------ */
	#header { height:100px;    background: #354458; margin: 0 0 10px 0; }
	#header h1 {padding: 20px; color: #E9E0D6;}
	
	/* tresc ------------ */
	#content { width:75%; background: #EB7260; float:left; padding:1%; overflow:hidden; }
	
	/* menu ------------ */
	#menu {    background: #548BC2; width:20%; float:right; padding:1%; overflow:hidden; }
	#menu h5 {padding:3px 3px 15px 3px; font-size:14px;}
	#menu ul li {list-style-type:none; padding:0 0 0 10px;}

Szablon na **na wszystkich rozdzielczościach wygląda tak samo**. Jedyną jego zaletą jest szerokość wyrażona w procentach, więc dostosowuje się do szerokości okna przeglądarki. Oto efekt:

{% include parts/postPicture.html page=page img="szablon" %}

Nie ma co analizować kodu ponieważ jest to podstawowy szkielet szablonu, najprostszy jaki mógł powstać. W kodzie HTML *#content* znajduje się wcześniej niż *#menu*. Z jednej strony jest to lepsze rozwiązanie ze względu na SEO, z drugiej gorsze jeżeli będziemy brać pod uwagę np. osoby niepełnosprawne.

Chcemy **aby szablon był responsywny** i aby prawe menu przesuwało się na górę i zamieniało w menu poziome. W tym celu musimy dodać do arkusza stylów znacznik **media queries** zmieniający kod CSS poniżej określonej rozdzielczości. Ja użyję rozdzielczości progowej *900px*.

**Aby menu przeskoczyło na górę**, trzeba nadać mu atrybut *position: absolute*. To przez to, że w kodzie HTML menu jest w kodzie jako drugie. Skoro będzie to menu poziome, należy ustawić jego szerokość na *width: 100%*. Należy także zwiększyć szerokość treści strony, ponieważ obecnie zajmuje ona tylko *75%*. Po przeskoczeniu menu powinna zajmować *width: 98%*.

Aby **linki w menu układały się poziomo** wystarczy zmienić im atrybut *display* i ustawić go na *inline*. To już wszystko. Ostatnim zabiegiem kosmetycznym jest ukrycie napisu *Menu główne* oraz dodanie poziomych kresek po każdym elemencie menu. Cały opisany kod wygląda następująco:

	@media only screen and (max-width: 900px) {
	    #menu {width:88%; position:absolute; height:30px;}
	    #menu h5 {display: none;}
	    #menu ul li {display: inline; }
	    #menu ul li:after {content: " |";}
	    #content {width: 98%;position:relative; top: 30px;margin-top:5%;}
	}

Dodaję go na końcu arkusza stylów, szablonu zaprezentowanego wyżej. Oto efekt podczas zmiany wielkości okna:

{% include parts/postPicture.html page=page img="szablon1" %}

{% include parts/postPicture.html page=page img="szablon2" %}

{% include parts/postPicture.html page=page img="szablon3" %}

Mimo, że dodałem tylko jeden element **media queries **zmieniający orientację menu, szablon będzie poprawnie wyświetlany na wszystkich urządzeniach mobilnych oraz na wszystkich rozdzielczościach. W kodzie CSS brakuje skalowania obrazków, jednak te nie zostały użyte w przykładzie.


Myślę, że **tworzenie stron w sposób responsywny **to nie dodatkowa umiejętność i nowa technologia dla bogatych, tylko standard, którego **trzeba się trzymać podczas projektowania strony lub bloga**. Jak widzisz, prosty zabieg dopasowania strony do rozdzielczości, wymaga tylko kilku dodatkowych linijek.

## Testowanie strony responsywnej

W dość łatwy sposób możesz przetestować działanie responsywności na dowolnej stronie internetowej. Firma **Google udostępnia narzędzie o nazwie "test optymalizacji mobilnej"**, które pozytywny wynik zwróci tylko dla poprawnie działającej strony responsywnej.

Narzędzie dostępne jest pod adresem: (https://search.google.com/test/mobile-friendly)

{% include parts/postPicture.html page=page img="test-strony-responsywnej" %}

Przeskanowanie strony jest bardzo proste i **nie wymaga żadnej specjalistycznej wiedzy**. Jeżeli wszystko działa poprawnie, Twoim oczom powinien się ukazać taki tekst jaki widoczny jest na powyższym zdjęciu.

Na pewno warto użyć tego narzędzia, jeżeli odbieramy stronę od jakiś firm trzecich. Da to poglądowy obraz tego, czy usługa została wykonana fachowo. Podkreślam jeszcze raz, **strony responsywne to już standard**.

## Biblioteka Bootstrap

W dzisiejszych czasach wszystkie dostępne szablony stron internetowych są responsywne. Dotyczy to zarówno WordPressa jak i innych systemów. Jeżeli decydujesz się na **zaprojektowanie strony responsywnej własnoręcznie od zera**, koniecznie skorzystaj z gotowych narzędzi.

Jednym z najpopularniejszych narzędzi będzie w tym wypadku biblioteka Bootstrap. Jest to zestaw stylów CSS, które są w dość mądry sposób poukładane i ustandaryzowane. Za pomocą tej biblioteki możesz tworzyć zaawansowane szablony stron internetowych, które od razu będą w całości responsywne. Przykłady stron opartych o Bootstrap znajdziesz na stronie (https://getbootstrap.com/docs/4.3/examples/).

Dosłownie **nikomu nie doradziłbym tworzenia własnej strony responsywnej bez użycia Bootstrapa**. Ja na moim blogu go nie posiadam, ale wynika to z faktu, że dawniej to narzędzie nie było aż tak popularne jak teraz.

## Podsumowanie

Artykuł wyszedł dość długi, z tego powodu dodam krótkie, treściwe podsumowanie:

- strona responsywna to już nie tylko fajny dodatek - **to konieczność, bo inaczej stracimy pozycję w Google**
- projektując stronę responsywną zawsze **oprzyj ją na bibliotece Bootstrap**
- jeżeli jakaś firma zajmuje się Twoją stroną, zweryfikuj ją w narzędziu Google
- strona responsywna bardzo dobrze współpracuje z prostym, przejrzystym projektem graficznym (*flat design*)
- szerokość wszystkich elementów **musi być procentowa**, wysokość może być już statyczna
- rozmiary czcionek opieraj o relatywne jednostki *em* i *rem*
- jakieś elementy strony będziesz musiał ukryć na mniejszych rozdzielczościach - to normalne
