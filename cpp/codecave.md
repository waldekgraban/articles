---
title: CodeCave
category: cpp
thumbnail: codecave.png
published: 2012-08-07T00:00:00Z
author: karol-trybulec
---
Każda uruchomiona aplikacja posiada swoją pamięć (pamięć procesu). Technika **DllInjection** pozwala programiście na wstrzyknięcie kodu do uruchomionego procesu, dzięki czemu można poszerzyć go o nowe funkcje, lub wywoływać istniejące funkcje procesu w dowolnym momencie. Jedną z wielu technik wstrzykiwania kodu jest korzystanie z **codecave**.

<!--more-->

## Codecave

Użycie codecave będzie potrzebna Ci potrzebna szczególnie po to, aby pisać funkcjonalne trainery do gier. Oprócz podstawowej modyfikacji pamięci możemy uruchamiać funkcje gry w dowolnym momencie. Jednak czym jest codecave?

**Codecave** jest to nieużywana pamięć uruchomionej aplikacji, do której można wstrzyknąć dowolny kod a 
następnie wykonać go. Podglądając pamięć procesu w debbugerze, będzie to fragment pamięci wypełniony 
instrukcjami *NOP* (ang. *no operation*).
{: .alert-info} 

Zaprezentuję prosty przykład działania codecave. Potrzebny będzie edytor pamięci np. **CheatEngine** oraz debbuger **OllyDbg**. Co prawda istnieją inne debbugery, nawet CheatEngine posiada swój wbudowany, jednak możliwości **OllyDbg** przerastają wszystkie inne programy tego typu. Nie trzeba umięć programować w assemblerze jednak trzeba znać jego najważniejsze rozkazy.

## Zbieranie informacji

Naszym zadaniem będzie taka modyfikacja kodu Sapera, aby po zmniejszeniu ilości min (licznik po lewej stronie) wyskakiwał komunikat z informacją.

{% include parts/postPicture.html page=page img="sp1" %}

Utwórz kopię gry Saper i wklej ją do jakiegoś folderu. Kopię pliku nazwij *saper.exe* i uruchom. Otwórz program **CheatEngine** i wczytaj proces sapera. Musimy odczytać adres zmiennej przechowującej aktualną ilość min, czyli *10*.

Wierzę, że umiesz to zrobić, zostało to opisane w artykule [edycja pamięci procesów](/cpp/edycja-pamieci-procesow). Znaleziony przeze mnie adres to *0x1005194*, Twój może się różnić jeżeli posiadasz inną wersję gry.

Posiadając adres zmiennej przechowującą ilość min, pozostaje nam znaleźć adres funkcji, która modyfikuje ilość min. Adres zmiennej a adres funkcji to zupełnie inne nie powiązane ze sobą wartości. Aby znaleźć adres funkcji klikamy na adres *0x1005194* prawym przyciskiem myszy i a następnie na pozycję *Find out what writes to this address..* Pokaże się komunikat informujący o konieczności użycia debbugera, klikamy *yes*. Został ustawiony breakpoint (pułapka) na adres zmiennej przechowującej ilość min, uaktywni się w momencie zapisania nowej wartości do zmiennej. Można także ustawiać pułapkę na odczytywanie informacji z pod adresu.

**Pułapka** (ang. *breakpoint*) - ustawia się ją za pomocą debbugera na określony adres w pamięci. Pułapki ustawia się na **zapis lub odczyt** z adresu. Pułapka przerywa działanie programu w momencie użycia zmiennej na którą została ustawiona, pozwalając programiście analizować kod.
{: .alert-info :}

Po ustawieniu pułapki na zapis, musimy sprawić aby szukana funkcja zapisała nową wartość do naszego adresu. Klikamy prawym przyciskiem myszy na dowolnym polu w saperze, ustawi to flagę na pole i zmniejszy ilość min o *1*. Oznacza to że szukana przez nas funkcja będzie musiała nadpisać zmienną z ilością min nową wartością. Na liście instrukcji powinna pojawić się taka pozycja:

{% include parts/postPicture.html page=page img="sp2" %}

Na czerwono zaznaczyłem **adres szukanej funkcji**, następnie widoczne są wartości odpowiadające heksadecymalne naszej funkcji, na razie nas to nie interesuje. Rozkaz _ADD_ jest odpowiedzialny za arytmetyczne dodawanie wartości, w tym wypadku dodawana jest wartość rejestru _EAX_ do zmiennej wyświetlającej ilość naszych min (kolor zielony).

Podsumowując, posiadamy dwa adresy:

- _0x1005194_ - adres zmiennej z ilością min
- _0x100346E_ - adres funkcji edytujący zmienną z ilością min

## Wstrzykiwanie kodu do gry Saper

Uruchom **OllyDbg** a następnie przeciągnij i upuść do niego plik *saper.exe*. Program wyświetlił nam kod maszynowy Sapera w języku Assembler. Aby **wstrzyknąć kod** musisz znaleźć wolny obszar pamięci który nie jest używany (**codecave**), czyli taki który nie jest wypełniony żadnymi instrukcjami asma. Wolny obszar pamięci to *DB 00*, czyli po prostu bajt. Puste instrukcje oznacza również *NOP* (ang. No operation) ale są one wykorzystywane w środku kodu i nie tworzą codecave.

Musisz znaleźć obszar wolnych bajtów, ja znalazłem na końcu programu (adres _01004A56_). Wygląda to tak:

{% include parts/postPicture.html page=page img="ol1" %} 

Wolne bajty widoczne wyżej nadpiszemy własnym kodem, następnie zamiast wywołania funkcji zmieniającej ilość min wywołamy nasz kod. Nasz kod wyświetli _MessageBox_, uaktualni ilość min a na końcu wykona skok do miejsca gdzie powinna znajdować się oryginalna funkcja. Koncepcja prezentuje się następująco:

{% include parts/postPicture.html page=page img="ol2" %} 
 

Zajmijmy się treścią komunikatu. Zaznaczamy około _15_ linii w codecave (jedna linia to jeden rozkaz to jeden bajt i jedna litera). Następnie klikamy prawym przyciskiem myszy na zaznaczeniu i wybieramy z menu _Binary > Edit_ (lub _CTRL+E_). Pokaże się okienko, wpisujemy tam treść komunikatu. Klikamy _OK_. Naciskamy na klawiaturze skrót _CTRL+A_, dzięki temu debbuger przetworzy sobie kod.

{% include parts/postPicture.html page=page img="ol3" %} 

Zapiszmy tytuł komunikatu, znów zaznaczamy dowolną ilość linii, edytujemy i wpisujemy tytuł. Klikamy OK a następnie na klawiaturze skrót _CTRL+A_. Teraz zajmiemy się wyświetlaniem komunikatu. Struktura funkcji wygląda następująco:

	int WINAPI MessageBox(
	    __in_opt  HWND hWnd,
	    __in_opt  LPCTSTR lpText,
	    __in_opt  LPCTSTR lpCaption,
	    __in      UINT uType
	);

Funkcja przyjmuje _4_ parametry, z czego pierwszy może wynosić 0 i ostatni 0 (struktura dostępna i opisana na MSDN, pamiętaj że wartości na stos trafiają w **odwrotnej kolejności** niż mówi o tym struktura funkcji). Przejdź do dowolnej pustej linii i kliknij dwukrotnie. Pokaże się okienko do wpisania instrukcji. Pierwszym argumentem może być 0, więc dodajemy __ do stosu wpisując w okienku _PUSH 0_. Drugim parametrem jest tytuł więc dodajemy do stosu adres (pierwsza kolumna) pod jakim zapisaliśmy wcześniej tytuł komunikatu, u mnie jest to adres _1004A67_. Wpisz w okienku _PUSH (adr\_tytuł\_komunikatu)_ np. _PUSH 1004A67_. Na stos dodajemy jeszcze adres treść komunikatu oraz parametr czwarty czyli. Na końcu dodajemy rozkaz _call user32.MessageBoxA_ &#8211; wywoła on komunikat. Całość powinna wyglądać tak:

{% include parts/postPicture.html page=page img="ol4" %} 
 

Różowymi strzałkami zaznaczyłem adresy **treści komunikatu** oraz **tytułu komunikatu** u Ciebie wszystkie adresy prawdopodobnie będą inne. Zapisz adres strzałki niebieskiej, wskazuje ona początek naszego kodu.

Naciśnij _CTRL+G_, otworzy się okienko pozwalające przenieść się pod dowolny adres. Wpisujemy tu **adres naszej funkcji** odpowiedzialnej za zapisywanie ilości min do zmiennej. Adres tej funkcji to: _100346E_. Skopiuj i zapisz w notatniku 4 linijki począwszy od początku funkcji pod którą przeniósł Cię debugger. Zauważ, że **debbuger** zaznacza funkcje czarną klamerką po lewej stronie. Widać, że funkcja zaczyna się pod adresem _0100346A_ a kończy na _01003479_. U mnie są to linijki:

{% include parts/postPicture.html page=page img="ol8" %} 

Kliknij dwukrotnie na pierwszą linijkę funkcji w debbugerze (adres _0100346A_). Pojawi się okienko pozwalające edytować istniejący rozkaz. Zastąp _MOV EAX,DWORD PTR SS:[ESP+4]_ skokiem do naszego kodu czyli _JMP 1004A6F_ (u ciebie adres jest inny i miałeś go zapisać! spójrz na poprzedni obrazek!). Teraz zamiast funkcji nadpisującej ilość min program wykona skok do naszego kodu, który wyświetli komunikat.

{% include parts/postPicture.html page=page img="ol5" %} 

Zauważ, że zostały dodane automatycznie rozkazy _NOP_. Zapisz adres ostatniego z nich na boku (u mnie _1003473_, widać na obrazku). Kliknij na pierwszą czerwoną linijkę (rozkaz _JMP_) i naciśnij _ENTER_. Zostałeś przeniesiony do naszego kodu. Zauważ, że zamiast wywołania funkcji nadpisującej ilość min, wywoływany jest skok na nasz kod w codecave. Oznacza to że ilość min przestanie być aktualizowana. Musimy naprawić aktualizację min czyli wywołać oryginalną funkcję, którą zastąpiliśmy skokiem (obrazek wyżej). Kazałem zapisać Ci _4_ pierwsze linijki przed nadpisaniem funkcji, teraz będą nam one potrzebne. Pierwsze dwie nadpisane linijki to:

	0100346A    /$>MOV EAX,DWORD PTR SS:[ESP+4]
	0100346E    |.>ADD DWORD PTR DS:[1005194], EAX

Pierwsza linijka (rozkaz _MOV_) dodaje do rejestru wartość ze wskaźnika stosu _SS:[ESP+4]_. Druga linijka odpowiedzialna jest za dodanie wartości z rejestru do zmiennej wyświetlającej ilość min. Jest to właśnie sedno naszej funkcji modyfikującej ilość min (pamiętasz adres _0100346E_ z 1 części artykułu?). W naszym kodzie po wywołaniu komunikatu (_CALL user32.messageboxa_) znajduje się puste pole _(DB 00)_. Kliknij na nie dwukrotnie i wklej rozkaz _MOV EAX,DWORD PTR SS:[ESP+4]_, następnie w nowej linii wklej _ADD DWORD PTR DS:[1005194],EAX_, dzięki temu licznik min odzyska swoją funkcjonalność. Następnie w kolejnej linii wykonaj skok na instrukcję _NOP_ (obrazek wyżej), której adres przed chwilą zapisałeś (u mnie _1003473_). W tym celu wpisz w okienku _JMP 1003473_ i kliknij _OK_. Cały nasz dodatkowy kod powinien wyglądać tak (Ty masz inne adresy):

{% include parts/postPicture.html page=page img="ol6" %} 

Klikamy prawym przyciskiem myszy w głównym oknie a następnie _Coppy to executable > All modyfications’_. Wyskoczy nowe okienko. W nowym okienku kliknij prawym przyciskiem myszy a następnie _Save File_ i zapisz plik pod dowolną nazwą.

Uruchom nowo zapisany plik. Teraz przy każdej zmianie ilości min powinien ukazać się komunikat. Jeżeli się nie ukazuje, oznacza że popełniłeś błąd.

{% include parts/postPicture.html page=page img="ol7" %} 