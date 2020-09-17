---
title: Klasy
category: cpp
thumbnail: klasy.png
published: 2013-12-14T00:00:00Z
---
**Klasy w C++** są bardzo ważnym narzędziem w rękach programisty. **Klasy** są fundamentem programowania obiektowego. Z pomocą **klas** będziesz mógł tworzyć lepszy kod, a co najważniejsze będzie on bardzo dobrze zorganizowany. Ucząc się **klas i programowania obiektowego**, powinieneś znać podstawy języka i umieć pisać proste programy proceduralne.

<!--more-->

## Informacje ogólne

<span class="d"><strong>Klasa</strong> grupuje pewne zmienne i funkcje w jedną całość. Klasa jest odpowiednikiem <strong>obiektu</strong> posiadającego pewne cechy. Tworząc klasę tworzymy również nowy typ danych.</span>

Klasy tworzy się używając słowa kluczowego **class**, nazwy klasy oraz ciała klasy zawartego w klamrach. Przykładowa definicja klasy wygląda następująco:

	class osoba {
	    // cialo klasy
	};

Cała wygoda w używaniu klas polega na tym, że możemy stworzyć klasę reprezentującą **dowolny obiekt**. Obiekt ten możemy scharakteryzować później dowolnymi zmiennymi lub funkcjami. Dzięki temu klasy zapewniają ogromny porządek w rozbudowanych programach. Dla przykładu stwórzmy klasy opisujące mebel i osobę. Każda z nich będzie miała inne składniki:

	class osoba {
	    string imie;
	    string nazwisko;
	    
	    int wiek;
	    int wzrost;
	    
	    void WypiszImie();
	};
	
	class mebel {
	    string nazwa;
	    
	    int ilosc_nog;
	    float waga;
	    
	    int szerokosc;
	    int wysokosc;
	    int dlugosc;
	};

Do tej pory posługiwaliśmy się błędną terminologią. Od tej części artykułu będę używał ogólnie przyjętych pojęć.

- **funkcje** w klasie **nazywamy metodami**
- **zmienne** w klasie **nazywamy polami**
- funkcje i zmienne w klasie nazywamy ogólnie **składnikami klasy**
{: .alert-info}

## Składniki klas

**Klasa w C++** jest bardzo podobna do struktury. Właściwie istnieją między nimi tylko dwie różnice: inne słowo kluczowe podczas deklaracji oraz **domyślny dostęp do składników**. Domyślnie w strukturze wszystkie jej składniki są publiczne, tzn. można się do nich odwołać z poza ciała struktury. W klasach wszystkie składniki są domyślnie prywatne, chyba że programista będzie chciał inaczej.

Definiując dostęp do składników klasy używamy 3 słów kluczowych:

- *public* - dostęp do składników klasy jest dozwolony wszędzie, nawet z poza ciała klasy
- *private* - dostęp do składników klasy jest zabroniony z poza ciała klasy
- *protected* - dostęp do składników klasy jest dozwolony tylko z ciała klasy (tak jak private) oraz w klasach pochodnych naszej klasy bazowej

Aby zastosować operatory dostępu, po prostu poprzedzamy nimi odpowiednie składniki:

	class osoba {
	public:
	    string imie;     //pole publiczne
	    string nazwisko; //pole publiczne
	    int wiek;        //pole publiczne
	    int wzrost;      //pole publiczne
	    void WypiszImie(); //metoda publiczna
	private:
	    int pesel;      //pole prywatne
	    int nr_dowodu;  //pole prywatne
	    void WypiszPesel(); //metoda prywatna
	};

Operatory dostępu służą jedynie do projektowania klas zgodnych z pewnymi założeniami programisty. Generalna zasada jest taka, że ważne dla klasy pola powinny być *prywatne*. Chcę podkreślić że *public, private* i *protected* w ogóle nie zabezpiecza kodu przed edycją pamięci, atakami hakerów itp.

## Tworzenie obiektów klas

Utworzenie **obiektu danej klasy** nie różni się wcale od tworzenie innych obiektów. Możemy utworzyć **obiekt statyczny** lub **dynamiczny alokując pamięć** i **używając wskaźników**. Generalnie nie ma prawie żadnej różnicy.

Tworząc obiekt statyczny, robimy to tak jak deklaracja zwykłej zmiennej:

	#include <cstdlib>
	#include <iostream>
	#include <string>
	
	using namespace std;
	
	class osoba {
	public:
	    int wiek;
	};
	
	int main()
	{
	    osoba Karol;
	    
	    Karol.wiek = 21;
	    cout << Karol.wiek; << endl
	    
	    system ("Pause");
	    return 0;
	}

Posługując się wskaźnikami sprawa wygląda identycznie. Obiekt tworzy się za pomocą operatora *new* i przypisuje do wskaźnika tego samego typu co klasa.

### Odwoływanie się do składników klasy dynamicznej

Do składników klasy dynamicznej najlepiej odwoływać się operatorem strzałki *->*. Został on stworzony w celu uproszczenia czytelności kodu.

Kolejną ważną kwestią jest fakt, że obiekty stworzone w sposób dynamiczny **zawsze trzeba ręcznie usunąć z pamięci** gdy nie są już używane (np. podczas zamykania programu). W C++ nie ma mechanizmy automatycznego oczyszczania pamięci tak jak np. w C#.

Zwróć uwagę na przykład niżej:

	#include <cstdlib>
	#include <iostream>
	#include <string>
	
	using namespace std;
	
	class osoba {
	public:
	    int wiek;
	};
	
	int main()
	{
	    osoba *Karol = new osoba;
	    
	    // bez operatora strzalki
	    
	    (*Karol).wiek = 21;
	    cout << (*Karol).wiek << endl;
	    
	    // z operatorem strzalki
	    
	    Karol->wiek = 21;
	    cout << Karol->wiek << endl;
	    
	    delete Karol;
	    system ("Pause");
	    return 0;
	}

Jak widzisz operator strzałki jest bardziej czytelny w przypadku **dynamicznej alokacji**. Posługując się standardowym operatorem kropki, należy dołączyć dodatkowe nawiasy. Wynika to z  faktu, że operator wyłuskania wskaźnika (gwiazdka) ma mniejszy priorytet niż operator kropki (odwołania się do składowej klasy).

## Funkcje w klasach

Funkcje w klasach (czyli **metody**) tworzymy na takiej samej zasadzie jak w innych miejscach programu. Metody także podlegają operatorom dostępu. Możemy więc tworzyć prywatne metody, które nie będą mogły zostać wywołane z poza ciała klasy.

Metode można zdefiniować albo wewnątrz klasy lub poza nią. Ważne aby jej deklaracja znajdowała się w ciele klasy:

	#include <cstdlib>
	#include <iostream>
	#include <string>
	
	using namespace std;
	
	class osoba {
	private:
	    string imie;    //pole prywatne
	    int wiek;       //pole prywatne
	public:
	    void UstawImie(string imie, int wiek) {
	        this->imie = imie;
	        this->wiek = wiek;  //definicja metody wewnątrz klasy
	    }
	    void WypiszImie();      //tylko deklaracja metody
	};
	
	// cialo metody poza klasą
	void osoba::WypiszImie() {
	    cout << "Imie: " << imie << "\nWiek: " << wiek << endl;
	}
	
	int main()
	{
	    osoba *Karol = new osoba;
	    
	    Karol->UstawImie("karol",21);    // zapisze dane do klasy
	    Karol->WypiszImie();             // wypisze imie i wiek
	    
	    delete Karol;
	    system ("Pause");
	    return 0;
	}

Zauważ, że nazwa argumentu funkcji _UstawImie()_, pokrywa się z **nazwą pola klasy**. Pole **klasy** zostało przysłonięte, dlatego wymagane jest użycie słowa kluczowego _**this**_. Zwraca ono wskaźnik na naszą klasę, dzięki temu _**this->imie**_ odnosi się do pola klasy a nie do argumentu. Jest to konstrukcja bardzo często używana, dlatego warto ją zapamiętać.

Pierwsza metoda została w całości zadeklarowana w klasie, natomiast druga poza nią &#8211; co nie jest żadnym problemem. Jeżeli metod jest dużo, warto ich deklarację trzymać poza klasą aby nie zaśmiecać kodu.

## Konstruktory w klasach

Jeżeli doszedłeś do tej części artykułu, pewnie zdążyłeś zauważyć jak wielką funkcjonalność niesie ze sobą **używanie klas**. Kiedy klasa jest inicjowana, zawsze istnieje konieczność wypełnienia jej odpowiednimi danymi. W tym celu nie trzeba posługiwać się osobnymi metodami, można skorzystać z **konstruktora**.

**Konstruktor** to metoda, która wykona się zawsze podczas tworzenia obiektu danej klasy. Jego zadaniem jest zainicjowanie obiektu i wypełnienie pól odpowiednimi danymi.

Jeżeli nie stworzymy konstruktora, kompilator zrobi to za nas niejawnie. Będzie to wtedy pusty konstruktor bezparametrowy.

#### Definiowanie konstruktora

- nazwa konstruktora **musi być taka sama** jak nazwa klasy
- nie podajemy typu zwracanego (nawet void)
- definicja konstruktora **musi** znajdować się w sekcji *public*

Przyjrzyjmy się poniższemu programowi:

	#include <cstdlib>
	#include <iostream>
	#include <string>
	
	using namespace std;
	
	class produkt {
	private:
	    string nazwa;
	    float cena;
	public:
	    produkt (string nazwa, float cena) {
	        this->nazwa = nazwa;
	        this->cena = cena;
	    }
	    
	    void WypiszInformacje ();
	};
	
	void produkt::WypiszInformacje() {
	    cout << "nazwa produktu: " << nazwa << endl;
	    cout << "cena: " << cena << endl;
	    cout << "---------" << endl;
	};
	
	int main()
	{
	    produkt *p1 = new produkt("laptop",2000);
	    produkt *p2 = new produkt("myszka",40);
	    produkt *p3 = new produkt("monitor",800);
	    
	    p1->WypiszInformacje();
	    p2->WypiszInformacje();
	    p3->WypiszInformacje();
	    
	    delete p1;
	    delete p2;
	    delete p3;
	    
	    system ("Pause");
	    return 0;
	}

Jak widzisz, dzięki użyciu **konstruktora** można w łatwy sposób wypełnić klasę już podczas jej tworzenia. Definicja konstruktora może znajdować się także poza klasą, tak jak w wypadku metod.

Więcej o **konstruktory w klasach** możesz przeczytać w artykule [konstruktory C++](/cpp/konstruktory/ "konstruktory C++").