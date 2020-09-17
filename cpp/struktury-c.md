---
title: Struktury
category: cpp
thumbnail: struktury.png
published: 2013-01-16T00:00:00Z
---
**Struktury** to złożone typy danych pozwalające przechowywać różne informacje. Za pomocą struktur możliwe jest grupowanie wielu zmiennych o różnych typach w jeden **obiekt.** Strukturę można nazywać obiektem lub **pojemnikiem** czy też **rekordem.** Dzięki strukturom można w prosty sposób organizować zbiory danych, bez konieczności korzystania z tablic (np. bardzo niewygodnie tworzyć bazę danych na tablicach).

<!--more-->

## Deklaracja struktury w C++

Utworzę przykładową strukturę reprezentującą osobę. Dzięki utworzeniu takiej struktury, będzie można przechowywać informacje takie jak imię, nazwisko i wiek w jednym pojemniku.

	struct osoba {
	    string imie;
	    string nazwisko;
	    int wiek;
	};

Słowo kluczowe **struct** informuje kompilator, że zostanie zadeklarowana struktura. Na drugim miejscu znajduje się **nazwa struktury**. Następnie w klamerkach występują kolejno po sobie **pola struktury** czyli zmienne.

	struct osoba {
	    string imie;
	    string nazwisko;
	    int wiek;
	} jan, artur, zenon;

W powyższym przykładzie, oprócz deklaracji struktury zostały także utworzone jej obiekty. Nazwy nowych obiektów struktury wypisuje się po przecinku po bloku z polami.

Do struktury można dołączyć także funkcje jednak nie jest to dobry zabieg. Struktury w C mogły zawierać tylko **pola**. W C++ wprowadzono także możliwość dołączania **metod do struktury** czyli funkcji.

**Metoda** - to inaczej funkcja w strukturze<br />**Pole** - to inaczej zmienna w strukturze
{: .alert-info} 

Struktura w C++ działa na takiej samej zasadzie jak **klasa**, to właśnie klasa powinna organizować różne metody oraz pola. Używając struktur w naszym programie, chcąc poszerzyć ją o **metody**, warto rozważyć po prostu użycie klas.

W internecie często będziesz spotykał nieco inną deklarację struktury **z użyciem rozkazu _typedef_**. Przykład takiej deklaracji to np:

	#include <iostream>
	#include <string.h>
	#include <cstdlib>
	
	using namespace std;
	
	typedef struct osoba {
	    string imie;
	    string nazwisko;
	    int wiek;
	} Osoba;
	
	int main()
	{
	    Osoba listonosz;    // styl C++
	    struct osoba strazak;   // styl C
	    
	    listonosz.imie = "Jan";
	    strazak.imie = "Piotr";
	    
	    cout << "Imie listonosza: " << listonosz.imie << endl;
	    cout << "Imie strazaka: " << strazak.imie << endl;
	    
	    system("pause >nul");
	    
	    return 0;
	}

Taka forma deklaracji była używana w języku C. W języku C deklarując strukturę trzeba było za każdym razem jej typ poprzedzać słowem _**struct**_ nawet w głównej funkcji programu. Dlatego programiści tworzyli typ złożony za pomocą rozkazu _**typedef**_. Dzięki temu zamiast pisać za każdym razem _struct osoba ktos_, można było używać struktur tak jak w teraz w C++ czyli _osoba ktos_.

## Wczytywanie danych do struktury

Posiadamy zadeklarowaną strukturę _osoba_. Wykorzystajmy ją w programie. Na początku utworzymy obiekt struktury, następnie wypełnimy danymi i wyświetlimy dowolne pole:

	#include <iostream>
	#include <string.h>
	
	using namespace std;
	
	// deklaracja struktury
	struct osoba {
	    string imie;
	    string nazwisko;
	    int wiek;
	};
	
	int main()
	{
	    osoba jan; // tworzenie obiektu struktury o nazwie jan
	    
	    jan.imie = "Jan";
	    jan.nazwisko = "Kowalski";
	    jan.wiek = 55;
	    
	    cout << "Twoje imie to: " << jan.imie << endl;
	    
	    return 0;
	}

Jest to prosty przykład na to, jak działa wypełnianie struktur danymi. Zauważ, że chcąc korzystać ze zmiennych struktury odwołujemy się do nich najpierw podając **nazwę obiektu struktury**, następnie **nazwę pola po kropce**. Mamy dostęp do wszystkich pól struktury, bo domyślnie są mają one atrybut **public.** 

Warto dodać, że jest to właśnie jedna z różnic pomiędzy strukturą a klasą. Klasa domyślnie posiada atrybut **private** na wszystkie swoje metody i pola.

## Tablica struktur

**Tablica struktur** doskonale sprawdza się podczas tworzenia prostych programów bazodanowych. Bez korzystania ze struktur trzeba było by użyć tablicy dwuwymiarowej, pierwszy wymiar odpowiadał by indeksie osoby, a drugi zawierał by odpowiednie informacje o niej. Oczywiście tablica posiada wszystkie elementy jednego typu co jest nie wygodne.

Tak samo jak tworzy się tablice z typów podstawowych, tak samo robi się to używając struktur:

	#include <iostream>
	#include <string.h>
	#include <cstdlib>

	using namespace std;

	// deklaracja struktury
	struct osoba {
	    string imie;
	    string nazwisko;
	    int wiek;
	};

	int main()
	{
	    osoba uczniowie[5]; // tablica ze struktury
	    
	    for (int i = 0; i<5; i++)
	    {
	        // pobieramy dane
	        cout << "Podaj imie ucznia numer " << i+1 << endl;
	        cin >> uczniowie[i].imie;
	    }
	    
	    for (int i = 0; i<5; i++)
	    {
	        // wyświetlamy tablicę
	        cout << uczniowie[i].imie << endl;
	    }
	    
	    system("pause >nul");
	    
	    return 0;
	}

## Struktury dynamiczne

Struktury w C++ często są tworzone w sposób dynamiczny, czyli przy użyciu wskaźników. Należy pamiętać aby zawsze zwalniać pamięć po nie używanych obiektach, które utworzyliśmy.

Uwaga! W strukturze statycznej odwołując się do pól używaliśmy kropki (*.*). W strukturze dynamicznej odwołując się do pól korzystamy z operatora strzałki (*->*).
{: .alert-info} 

Poniższy przykład demonstruje inicjację struktury, użycie operatora *->*, oraz zwolnienie pamięci:

	#include <iostream>
	#include <string.h>
	#include <cstdlib>

	using namespace std;

	struct osoba {
	    string imie;
	    int wiek;
	};

	int main()
	{
	    osoba * sklepowy = new osoba; // wskaźnik *sklepowy na strukturę
	    
	    sklepowy->imie = "Andrzej"; 
	    
	    cout << sklepowy->imie << endl;
	    
	    delete sklepowy; // usuwamy obiekt
	    
	    system("pause >nul");
	    
	    return 0;
	}

**Operator _->_** ułatwia nam sprawę. Zauważ, że nazwa zmiennej strukturalnej jest wskaźnikiem. Chcąc posłużyć się kropką tak jak przy strukturze statycznej, musiało by wyglądać to tak:

	osoba * sklepowy = new osoba; // wskaźnik *sklepowy na strukturę
	
	(*sklepowy).imie = "Andrzej"; // to to samo (tu brzydko)
	
	sklepowy->imie = "Andrzej";  // to to samo (tu ładnie)
	
	delete sklepowy; // usuwamy obiek