---
title: Lista jednokierunkowa
category: cpp
thumbnail: lista-jednokierunkowa.png
published: 2013-01-15T00:00:00Z
---
**Lista jednokierunkowa** jest strukturą o dynamicznie zmieniającej się wielkości. Listę można opisać jako uszeregowany zbiór elementów. **Każdy element zawiera jakieś dane oraz wskazuje na swojego następcę**. Cechą listy jednokierunkowej jest to, że można przeglądać ją tylko w jedną stronę, od początku do końca.

<!--more-->

## Jak napisać listę jednokierunkową?

**Lista jednokierunkowa** w odróżnieniu od tablicy **jest rozrzucona po pamięci aplikacji**. Jej elementy nie występują kolejno po sobie. Element poprzedni wskazuje na element następny. Dlatego tak ważne jest przypisywanie odpowiednich wskaźników do nowych elementów.

Nie ma jednego rozwiązania dotyczącego zaimplementowania listy. Każda lista może być zbudowana w inny sposób, zależy to od programisty oraz programu.

Ja tworząc listę posługuję się taką metodą:

1. **tworzę strukturę odpowiadającą jednemu elementowi listy** - element zawiera różne informacje np: *imię*, *nazwisko*, *wiek* oraz **wskaźnik do następnego elementu**.
1. **tworzę strukturę główną** - jest to struktura bazowa dla wszystkich elementów listy. Zawiera on adres początku listy czyli pierwszego elementu. Oprócz tego może zawierać różne metody typu np. *dodawanie*, *sortowanie* i *usuwanie elementów*.

Tak wygląda zobrazowanie mojej koncepcji:

{% include parts/postPicture.html page=page img="lista1" %}

**Wszystkie ważne metody związane z listą umieszczam w strukturze głównej**, zaznaczonej kolorem żółtym na rysunku. Inny popularny sposób **tworzenia list jednokierunkowych w C++** mówi, aby funkcje związane z listą umieszczać **całkiem poza strukturami związanymi z listą**. Wtedy najważniejszym argumentem każdej funkcji operującej na liście jest wskaźnik do jej pierwszego elementu.

Nie będę zajmował się drugą metodą, uważam że nie jest tak wygodna jak metoda zaprezentowana na obrazku wyżej. W mojej osobistej opinii: trzymając się zasad programowania obiektowego powinniśmy wszystkie metody odpowiedzialne za listę zgrupować w jednej strukturze/klasie a nie rozrzucać po całym programie.

**Żadne rozwiązanie nie jest błędne** ani nie jest złe. Obydwa rozwiązania przedstawiają listy jednokierunkowe, **różnią się tylko implementacją oraz w następstwie sposobem inicjacji** i budowy listy, co jest logiczne. Gdy nabierzesz wprawy nie będziesz się nawet nad nimi zastanawiał, przyjmiesz własną koncepcję i będziesz się jej trzymał.

Jeżeli w programie będziesz używał kilku list, *wtedy wygodnie trzymać się powyższej metody* Jeżeli natomiast Twój program będzie korzystał z dziesiątek, setek list, wtedy warto operacje dotyczące list *zaimplementować osobno*, przekazując im jako argument wskaźnik na tę listę, na jakiej chcemy operować. Oszczędzi to miejsce w kodzie.
{: .alert-info} 

## Deklaracja struktury elementów oraz listy

Stworzę listę służącą jako **baza danych**, będzie ona przechowywać imiona, nazwiska oraz wiek osób. Będę posługiwał się moją metodą przedstawioną na pierwszym rysunku w artykule. Pierwszym krokiem będzie utworzenie **struktury elementu listy** na dane i wskaźnik do kolejnego elementu. Druga struktura będzie odpowiedzialna za początek listy oraz metody.

	struct osoba {
	    string imie;
	    string nazwisko;
	    int wiek;
	    osoba *nastepna; // wskaźnik na następny element
	    osoba(); // konstruktor
	};
	
	osoba::osoba() {
	    nastepna = 0; // konstruktor
	}
	
	struct lista {
	    osoba *pierwsza; // wskaźnik na początek listy
	    void dodaj_osobe(string imie, string nazwisko, int wiek);
	    void usun_osobe(int nr);
	    void wyswietl_liste();
	    lista();
	};
	
	lista::lista() {
	    pierwsza = 0; // konstruktor
	}

### Dodawanie elementów do listy

Posiadamy potrzebne struktury. Do struktury *lista*, dodałem metodę *dodaj*osobe*, dzięki niej będziemy mogli dodawać osoby do naszej bazy.

Co się dzieje gdy dodamy nową osobę (nowy element listy)? Ostatniemu elementowi listy przypisujemy wskaźnik na nasz nowo utworzony element i wypełniamy go danymi. Szczególną sytuacją jest moment kiedy dodajemy pierwszy element, wtedy nie można poprzednikowi ustawić wskaźnika, ponieważ poprzednika jeszcze nie ma. Przeanalizuj dokładnie poniższy kod ponieważ jego zrozumienie jest kluczowe:

	void lista::dodaj_osobe(string imie, string nazwisko, int wiek)
	{
	    osoba *nowa = new osoba;    // tworzy nowy element listy
	    
	    // wypełniamy naszymi danymi
	    nowa->imie = imie;
	    nowa->nazwisko = nazwisko;
	    nowa->wiek = wiek;
	    
	    if (pierwsza==0) // sprawdzamy czy to pierwszy element listy
	    {
	        // jeżeli tak to nowy element jest teraz początkiem listy
	        pierwsza = nowa;
	    }
	    else
	    {
	        // w przeciwnym wypadku wędrujemy na koniec listy
	        osoba *temp = pierwsza;
	        
	        while (temp->nastepna)
	        {
	            // znajdujemy wskaźnik na ostatni element
	            temp = temp->nastepna;
	        }
	        
	        temp->nastepna = nowa;  // ostatni element wskazuje na nasz nowy
	        nowa->nastepna = 0;     // ostatni nie wskazuje na nic
	    }
	}

Używanie **listy jednokierunkowej** czy nawet **listy dwukierunkowej** to tak naprawdę dobre zrozumienie wskaźników. Nie mamy iteratora aby odwoływać się do poszczególnych elementach tak jak w tablicy, do każdej operacji musimy znaleźć wskaźnik. Kiedy chciałem dodać nową osobę do bazy danych, musiałem za pomocą pętli *while* znaleźć wskaźnik do ostatniego elementu listy i dopiero wtedy dodać nowy element.

Aby przyśpieszyć niektóre operacje na liście, można zapisywać wskaźnik do jej ostatniego elementu w głównej strukturze odpowiedzialnej za hierarchie listy (teraz zapisujemy tylko początek).

### Wyświetlanie elementów listy

Aby wyświetlić dowolny element  listy musimy znaleźć wskaźnik.  Wędrówkę zaczynamy zawsze od początku listy.

	int main()
	{
	    lista *baza = new lista;    //tworzymy liste
	    
	    //dodajemy rekordy do bazy
	    baza->dodaj_osobe("maciej","pierwszy", 23);
	    baza->dodaj_osobe("arkadiusz","drugi", 44);
	    baza->dodaj_osobe("dariusz","trzeci", 19);
	    baza->dodaj_osobe("andrzej","czwarty", 21);
	    
	    // wyswietlamy 1 osobę - macieja
	    cout << baza->pierwsza->imie << endl;
	    
	    // wyswietlamy 2 osobę - arkadiusza
	    cout << baza->pierwsza->nastepna->imie << endl;
	    
	    // wyswietlamy wiek 3 osoby - dariusza
	    cout << baza->pierwsza->nastepna->nastepna->wiek << endl;
	    
	    // wyswietlamy wiek 4 osoby - andrzeja
	    cout << baza->pierwsza->nastepna->nastepna->nastepna->wiek << endl;
	    
	    delete (baza);
	    
	    return 0;
	}

Po uruchomieniu kodu strumień wyjścia wygląda następująco:

{% include parts/postPicture.html page=page img="lista2" %}

Powyższy kod to tylko demonstracja, w jaki sposób **poruszając się wskaźnikami po elementach listy** wydobywamy informacje. Nie powinno się wyświetlać elementów listy w ten sposób, powinna być od tego **osobna metoda** a wskaźniki najlepiej przewijać w pętli *while* tak jak jest to pokazane w załączonej metodzie wyżej.

Napiszmy proste ciało metody *wyswietl*liste()*. Sprawa jest trywialna, po wywołaniu metody, wyświetlamy w pętli wszystkie elementy listy przewijając wskaźnik w pętli:

	void lista::wyswietl_liste()
	{
	    // wskaznik na pierszy element listy
	    osoba *temp = pierwsza;
	    
	    // przewijamy wskazniki na nastepne elementy
	    while (temp)
	    {
	        cout << "imie: " << temp->imie << " nazwisko: " << temp->nazwisko << endl;
	        temp=temp->nastepna;
	    }
	}

### Usuwanie elementów listy

Usuwając elementy listy jednokierunkowej należy pamiętać o porządku we wskaźnikach:

- **jeżeli usuwasz pierwszy element (głowa/head)** - wystarczy ustawić nowy początek listy z elementu *n* na *n+1*
-  **jeżeli usuwasz środkowy element** - usuwając *n-ty* element trzeba uzyskać sytuację aby element *n-1* wskazywał na element *n+1*. Element *n-ty* można także usunąć z pamięci *free* lub *delete*
- **jeżeli usuwasz ostatni element (ogon/tail)** - przyjmując że *n-ty* element znajduje się na końcu listy, usuwając go wystarczy wyzerować wskaźnik elementu *n-1*.

Zależnie od rodzaju aplikacji programista decyduje co jest kryterium w usuwaniu elementów listy, może być to unikalny *id* lub inny atrybut np. *imie*. Przykładowy kod odpowiedzialny za usuwanie **elementu listy jednokierunkowej** może wyglądać następująco:

	void lista::usun_osobe (int nr)
	{
	    // jezeli to pierwszy element listy
	    if (nr==1)
	    {
	        osoba *temp = pierwsza;
	        pierwsza = temp->nastepna; //ustawiamy poczatek na drugi element
	        delete temp; // usuwamy stary pierwszy element z pamieci
	    }
	    // jeżeli nie jest to pierwszy element
	    else if (nr>=2)
	    {
	        int j = 1;
	        
	        // do usuniecia srodkowego elemetnu potrzebujemy wskaznika na osobe n-1
	        // wskaznik *temp bedzie wskaznikiem na osobe poprzedzajaca osobe usuwana
	        osoba *temp = pierwsza;
	        
	        while (temp)
	        {
	            // sprawdzamy czy wskaznik jest na osobie n-1 niz usuwana
	            if ((j+1)==nr) break;
	            
	            // jezeli nie to przewijamy petle do przodu
	            temp = temp->nastepna;
	            j++;
	        }
	        
	        // wskaznik *temp wskazuje teraz na osobe n-1
	        // nadpisujemy wkaznik n-1 z osoby n na osobe n+1
	        // bezpowrotnie tracimy osobe n-ta
	        
	        // jezeli usuwamy ostatni element listy
	        if (temp->nastepna->nastepna==0) {
	            delete temp->nastepna;
	            temp->nastepna = 0;
	        }
	        // jezeli usuwamy srodkowy element
	        else {
	            osoba *usuwana = temp->nastepna;
	            temp->nastepna = temp->nastepna->nastepna;
	            delete usuwana;
	        }
	    }
	}

Kryterium według którego usuwamy elementy jest ich numer na liście (numerując od *1..n* a nie od *0..n-1* tak jak tablice).

Co sądzisz o tej metodzie? Wydaje się lekko skomplikowana, szczególnie przewijanie wskaźników dla elementów środkowych. **Można to uprościć**. Swego czasu pisząc rozbudowany projekt z **listą jednokierunkową** stworzyłem specjalną metodę, która zwracała mi wskaźnik na element *n-1*. Kod znacząco się skrócił piszcząc poszczególne funkcjonalności listy, ponieważ nie musiałem przewijać wszystkich elementów w poszukiwaniu wskaźnika *n-1* &#8211; dostawałem go od razu po wywołaniu metody.

## Podsumowanie

**Lista jednokierunkowa** z prawdziwego zdarzenia powinna posiadać jeszcze inne metody np.  **dodawanie elementów na początek/koniec, sortowanie, wyszukiwanie.**. Reszta funkcji zależy od aplikacji jaką piszemy. W internecie jest wiele gotowych list z dołączonymi metodami. Ten artykuł miał pokazać na jakiej zasadzie **tworzyć i budować listę** dlatego przedstawiłem tylko podstawowe metody.

Uwaga! W artykule podczas usuwania elementów listy, po prostu pozbywałem się wskaźników natomiast nie usuwałem obiektów w pamięci na które wskazywały wskaźniki. **Może to powodować błędy memory-leak** w rozbudowanych aplikacjach. Pisząc projekt na zaliczenie, przypuszczam że nie ma to żadnego znaczenia. Nie usuwałem wskaźników z pamięci, ponieważ kod został by znacznie zaśmiecony.

Jeżeli chcesz skompilować plik dołączony przeze mnie na samym dole strony, rozważ na jakim środowisku go uruchamiasz. Na poszczególnych kompilatorach różnią się nieco biblioteki dyrektywy *#include*.

Microsoft Visual Studio:

	#include "stdafx.h"
	#include <iostream>
	#include <string>
	
	using namespace std;

Pozostałe (np. Code::Blocks):

	#include <iostream>
	#include <string>
	#include <cstdlib>
	
	using namespace std;

Kompletny kod jest dostępny na githubie: <https://github.com/p-programowanie/cpp/tree/master/lista-jednokierunkowa>