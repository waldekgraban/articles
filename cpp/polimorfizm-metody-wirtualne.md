---
title: Polimorfizm i metody wirtualne
category: cpp
thumbnail: polimorfizm_funkcje_wirtualne.png
published: 2014-03-11TT00:00:00Z
---
**Polimorfizm** jest filarem **programowania obiektowego**, nie tylko jeżeli chodzi o język C++. Daje on programiście dużą elastyczność podczas pisania programu. **Polimorfizm** jest ściśle związany z **metodami wirtualnymi**. Złe operowanie mechanizmem polimorfizmu może znacznie spowolnić działanie aplikacji i doprowadzić do poważnych błędów.

<!--more-->

## Czym jest polimorfizm?

**Polimorfizm** (wielopostaciowość) jest to cecha programowania obiektowego, umożliwiająca różne zachowanie tych samych **metod wirtualnych** (funkcji wirtualnych) w czasie wykonywania programu.
{: .alert-info} 

**Polimorfizm** jest słowem zaczerpniętym do informatyki stosunkowo niedawno, podczas rozwoju języków programowania. W języku **C++** możemy korzystać z tego mechanizmu za pomocą **metod wirtualnych**. Dzięki niemu mamy pełną kontrolę nad wykonywanym programem, nie tylko w momencie kompilacji (**wiązanie statyczne**) ale także podczas działania programu (**wiązanie dynamiczne**) &#8211; niezależnie od różnych wyborów użytkownika.

Ponieważ **C++** jest językiem hybrydowym, nie mamy konieczności korzystania z **polimorfizmu**. Zostanie on automatycznie włączony podczas zadeklarowania przynajmniej jednej **metody wirtualnej** w danej klasie.

## Metody wirtualne

W internecie często używana jest nazwa **funkcji wirtualnej**, jednak jest ona dość mylna i nie do końca zgodna z konwencją **programowania obiektowego**. Funkcja wirtualna **musi być** funkcją składową danej klasy, a więc generalnie jest **metodą**. Zwykłej funkcji nie da się zadeklarować jako wirtualna.

#### Krótkie przypomnienie

- Podczas dziedziczenia **obiekt klasy pochodnej** może być wskazywany przez wskaźnik typu **klasy bazowej**.
- **Typem statycznym** obiektu wskazywanego przez wskaźnik jest typ tego wskaźnika. **Typem dynamicznym** obiektu wskazywanego przez wskaźnik jest typ na jaki dany wskaźnik wskazuje.

Dwa powyższe fakty dobrze zobrazuje ten kod:

	class Bazowa {
	public:
	    int a;
	};
	
	class Pochodna : public Bazowa {
	public:
	    int b;
	};
	
	int main()
	{
	    // typ statyczny: Bazowa
	    // typ dynamiczny: Pochodna
	    Bazowa *bazowa = new Pochodna();
	    
	    // typ statyczny: Pochodna
	    // typ dynamiczny: Pochodna
	    Pochodna *pochodna = new Pochodna();
	    
	    system("pause");
	    return 0;
	}

Dzięki tym informacjom możemy napisać zwięzłą definicję **metody wirtualnej**:

**Metoda wirtualna** jest to funkcja składowa klasy poprzedzona słowem kluczowym *virtual*, której sposób wywołania zależy od **typu dynamicznego** zmiennej, a nie od **typu statycznego**.
{: .alert-info} 

Definicja wydaje się trudna do zrozumienia, ale schemat jest bardzo prosty. Zobaczymy jak wygląda mechanizm **przesłaniania funkcji** podczas dziedziczenia, bez użycia **metod wirtualnych** i **polimorfizmu**. Aby funkcje zostały **przesłonięte** muszą mieć taką samą nazwę, argumenty oraz typ zwracany:

	class Bazowa {
	public:
	    void fun() { cout << "Bazowa \n"; }
	};
	
	class Pochodna : public Bazowa {
	public:
	    void fun() { cout << "Pochodna \n"; }
	};
	
	int main()
	{
	    
	    Bazowa *bazowa = new Pochodna();
	    Pochodna *pochodna = new Pochodna();
	    
	    bazowa->fun();  //wyswietli: bazowa
	    pochodna->fun();//wyswietli: pochodna
	    
	    bazowa = new Bazowa();
	    
	    bazowa->fun();  //wyswietli: bazowa
	    
	    system("pause");
	    return 0;
	}

Sytuacja nas nie zaskakuje. To, która metoda zostanie wywołana zależy od **typu wskaźnika** na obiekt. Jest to wspomniane wcześniej **wiązanie statyczne**. Kompilator już podczas kompilacji programu wie, jakiego **typu statycznego** są obiekty i jakie metody mają zostać wywołane.

Dzięki dodaniu do naszego kodu **metod wirtualnych**, uruchomimy **mechanizm polimorfizmu**. Wczesne **wiązanie statyczne** nie będzie miało wtedy żadnego znaczenia, ponieważ to która funkcja zostanie wywołana będzie zależało od późnego **wiązania dynamicznego**.

	class Bazowa {
	public:
	    virtual void fun() { cout << "Bazowa \n"; }
	};
	
	class Pochodna : public Bazowa {
	public:
	    void fun() { cout << "Pochodna \n"; }
	};
	
	int main()
	{
	    
	    Bazowa *bazowa = new Pochodna();
	    Pochodna *pochodna = new Pochodna();
	    
	    bazowa->fun();  //wyswietli: pochodna
	    pochodna->fun();//wyswietli: pochodna
	    
	    system("pause");
	    return 0;
	}

Słowo kluczowe *virtual* dobrze spełniło swoje zadanie. Widzimy jak wywołania metod są zależne od **typu dynamicznego**. Słowo *virtual* wystarczy dodać jedynie w klasie bazowej, nie ma konieczności powtarzania go w klasach pochodnych.

Zastanawiasz się pewnie, **do czego potrzebny jest polimorfizm** oraz **metody wirtualne**? Bez używania polimorfizmu, programista musiał już na etapie pisania programu, wiedzieć jak będzie się on zachowywał. To za sprawą **wczesnego wiązania**, które musi być dostarczone kompilatorowi w momencie kompilacji i linkowania.

W przypadku użycia polimorfizmu dostajemy nieograniczone możliwości projektowania aplikacji, gdzie zachowanie programu może się ciągle zmieniać.

## Przykład zastosowania polimorfizmu

Oto prosty przykład zastosowania **polimorfizmu**: Posiadamy klasę bazową *Pojazd* oraz trzy klasy pochodne: *Samochod*, *Rower* i *Rolki*. Wszystkie klasy mają zdefiniowaną prostą metodę *zatrzymaj()* odpowiedzialną za zatrzymanie pojazdu danego typu.

Deklarujemy **tablicę wskaźników** na obiekty klasy *Pojazd* (możemy ponieważ jest to klasa bazowa dla wszystkich innych klas). Dla przejrzystości kodu utworzyłem tylko 3 obiekty klas pochodnych i zapisałem wskaźniki na obiekty do poszczególnych indeksów tablicy. Dzięki użyciu **polimorfizmu** możemy zatrzymać wszystkie pojazdy w jednej pętli. <code class="cpp"></code>

	#include <iostream>
	#include <cstdlib>
	
	using namespace std;
	
	class Pojazd {
	public:
	    virtual void zatrzymaj() {
	        cout << "zatrzymuje pojazd..?\n";
	    }
	};
	
	class Samochod : public Pojazd {
	public:
	    void zatrzymaj() {
	        cout << "zatrzymuje samochod\n";
	    }
	};
	
	class Rower : public Pojazd {
	public:
	    void zatrzymaj() {
	        cout << "zatrzymuje rower\n";
	    }
	};
	
	class Rolki : public Pojazd {
	public:
	    void zatrzymaj() {
	        cout << "zatrzymuje rolki\n\n";
	    }
	};
	
	int main()
	{
	    Pojazd **tablica = new Pojazd*[3];
	    
	    tablica[0] = new Samochod();
	    tablica[1] = new Rower();
	    tablica[2] = new Rolki();
	    
	    for (int i = 0; i<3; i++) {
	        tablica[i]->zatrzymaj();
	    }
	    
	    system("pause");
	    return 0;
	}

Tak jak pisałem wcześniej, o wywołaniu odpowiedniej **przesłoniętej** metody zadecydowało **późne wiązanie**. Uzyskaliśmy ten efekt dzięki zadeklarowaniu **funkcji wirtualnej** w klasie bazowej. A więc zadziałał **polimorfizm**. Gdybyśmy usunęli słowo *virtual*, trzy razy zostałaby wywołana funkcja klasy bazowej &#8211; zostałby trzy razy wyświetlony napis: *zatrzymuje pojazd..?*.

Przykład jest dość trywialny ale w chwili obecnej wydawał mi się najprostszy do ukazania zalet **polimorfizmu** a przede wszystkim jego praktycznej implementacji. Oczywiście wyobraź sobie sytuację, że w programie istnieje kilkaset obiektów a nie tylko trzy. Wtedy wygoda jaką zyskaliśmy jest nieoceniona.

## Nie należy przesadzać

**Polimorfizm** kosztuje. Działa to w ten sam sposób jak deklaracja zmiennych statycznych i dynamicznych (stos jest szybszy od sterty). Gdy używamy **polimorfizmu** program aż do czasu uruchomienia nie wie jak będzie działał, ponieważ obiekt na jaki wskazuje wskaźnik może zmienić się 10 razy w ciągu minuty, zależnie od działania użytkownika.

	#include <iostream>
	#include <cstdlib>
	
	using namespace std;
	
	class Klasa1 {
	    int a,b,c;
	    
	    virtual void fun() {
	        cout << "test";
	    }
	};
	
	class Klasa2 {
	    int a,b,c;
	    
	    void fun() {
	        cout << "test";
	    }
	};
	
	int main()
	{
	    Klasa1 *klasa1 = new Klasa1;
	    Klasa2 *klasa2 = new Klasa2;
	    
	    cout << sizeof(*klasa1) << "\n";  //wyswietli 16
	    cout << sizeof(*klasa2) << "\n";  //wyswietli 12
	    
	    system("pause");
	    return 0;
	}

Klasy **polimorficzne** zajmują więcej miejsca w pamięci, ponieważ kompilator automatycznie dodaje do nich wskaźnik *vptr* wskazujący na tablicę *vtab*. Dla każdej klasy musi istnieć osobny wskaźnik i osobna tablica. Tablica jest generowana automatycznie i zawiera wskaźniki do funkcji, wygenerowane przez kompilator. Nie wiem na jakiej zasadzie odbywa się generowanie zawartości tablicy i nie umiem nic więcej na ten temat napisać.

Sama tablica w budowie nie przypomina niczego nadzwyczajnego. Jej podgląd umożliwia m.in. środowisko Visual Studio po dopisaniu do kompilatora odpowiednich parametrów (zostanie wtedy wygenerowana automatycznie w formie pliku tekstowego).

Traktuj ten artykuł jako wstęp, opisałem w nim związek pomiędzy **metodami wirtualnymi a polimorfizmem**. Jeżeli temat dalej Cię interesuje, zapraszam do bardziej szczegółowego wpisu przedstawiającego [Polimorfizm w C#](/c-sharp/metody-wirtualne-abstrakcyjne-i-polimorfizm/).