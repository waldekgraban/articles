---
title: Właściwości i akcesory get set
category: c-sharp
thumbnail: get-set.png
published: 2013-12-05T00:00:00Z
---
Z **akcesorów get** i **set** korzysta każdy kto programuje w C#. Stanowią one duże udogodnienie w programowaniu obiektowym. Zapewniają wygodę, bezpieczeństwo i znacząco skracają kod. **Akcesory** są ściśle związane z **właściwościami**, dlatego długo zastanawiałem się nad odpowiednim tytułem dla tego artykułu. Są też związane z językiem C# i nie spotkamy ich np. w Javie.

<!--more-->

## Czym są właściwości

**Właściwość** to konstrukcja charakterystyczna m.in. dla języka C#. Zapewnia dostęp do **pól klasy** posługując się przy tym **akcesorami get i set**. Główną funkcjonalnością właściwości jest możliwość zapisywania i odczytywania **prywatnych pól klasy**, tak jak by były publiczne.

Kiedy usłyszałem o **właściwościach** po raz pierwszy, w ogóle ich nie rozumiałem i nie byłem co do nich przekonany. Z biegiem czasu uświadomiłem sobie, że używanie ich w rozbudowanych projektach jest koniecznością, szczególnie jeżeli nad projektem pracuje wiele różnych osób.

## Poprawne projektowanie klas

Jednym z podstawowych filarów programowania obiektowego jest **hermetyzacja**. Mówi ona aby ukrywać składniki klasy, tak aby nie było dostępne z zewnątrz. Nigdy nie wiemy kto będzie pracował na napisanej przez nas klasie. Nie jest dobrym zwyczajem, a wręcz błędem, aby ktoś mógł edytować zmienne naszej klasy z zewnątrz.

Dlatego też **wszelkie zmienne dostępne wewnątrz naszej klasy powinny być prywatne**. Dostęp do najważniejszych pól, które mogą być dostępne z zewnątrz i **świadomie taki dostęp zapewnimy**, dostarczamy poprzez napisanie odpowiednich metod:

	class Osoba
	{
	    private int _wiek;
	    
	    public int PobierzWiek()
	    {
	        return _wiek;
	    }
	    
	    public void UstawWiek(int wiek)
	    {
	        _wiek = wiek;
	    }
	}
	
	class Program
	{
	    static void Main(string[] args)
	    {
	        Osoba Karol = new Osoba();
	        
	        Karol.UstawWiek(21);
	        System.Console.WriteLine(Karol.PobierzWiek());
	        
	        System.Console.ReadKey();
	    }
	}

Jak widzisz, trzymamy się zasad **poprawnego** programowania obiektowego. Przykład jest dość trywialny. Pole _wiek_ jest zmienną prywatną, aby ją zmodyfikować możemy posłużyć się jedynie metodami, które udostępnia klasa. Metody te są nazywane "setterami" i "getterami".

Prywatna zmienna oraz przypisane do niej **metody do odczytu i zapisu** dają nam pewność, że inny programista pracujący na naszej klasie, będzie trzymał się naszych założeń. Przykładowo możemy rozbudować metodę aby akceptowalny wiek był z przedziału 1..99:

	class Osoba
	{
	    private int _wiek;
	    
	    public int PobierzWiek()
	    {
	        return _wiek;
	    }
	    
	    public void UstawWiek(int wiek)
	    {
	        if (wiek >= 1 && wiek <=99)
	        {
	            _wiek = wiek;
	        }
	    }
	}

Gdyby *_wiek* był publiczny, nie posiadalibyśmy żadnej kontroli nad tym jaka będzie jego wartość.

Biedni programiści Javy nie mają **konstrukcji nazwanej właściwościami**. Oznacza to, że każdą zmienną w klasie muszą obudowywać takimi funkcjami jak w przykładzie wyżej. Oczywiście w rozbudowanych przykładach i dużych klasach setterów i getterów są dziesiątki, co tworzy niepotrzebny bałagan.

Ponieważ **język C#** był lekko wzorowany na Javie, jego projektanci uznali, że warto wprowadzić do języka konstrukcję, która zapewni bardziej praktyczny dostęp do funkcji setterów i getterów.

## Używanie właściwości

**Właściwość deklarujemy w sposób podobny do zmiennej**, jednak w jej wnętrzu należy obsłużyć **akcesory get oraz set**. Używając właściwości, nie będziemy musieli sami pisać poszczególnych metod do każdego pola.

Przeróbmy przykład znajdujący się wyżej, tak aby zamiast metod setterów i getterów używał **właściwości dostępnych w C#**:

	class Osoba
	{
	    private int _wiek;
	    
	    // właściwość
	    public int Wiek
	    {
	        get
	        {
	            return _wiek;
	        }
	        set
	        {
	            _wiek = value;
	        }
	    }
	}
	
	class Program
	{
	    static void Main(string[] args)
	    {
	        Osoba Karol = new Osoba();
	        
	        Karol.Wiek = 21;
	        System.Console.WriteLine(Karol.Wiek);
	        
	        System.Console.ReadKey();
	    }
	}

Obydwa programy zadziałają tak samo. W pierwszym użyliśmy zwykłych metod a w drugim **właściwości**.

W kodzie pojawiły się **akcesory get oraz set. **Pole *_wiek* nadal jest prywatne, natomiast właściwość *Wiek* jest publiczna. Zapewnia ona dostęp do prywatnego pola *_wiek*.

**Akcesor get** wywoływany jest w chwili gdy chcemy pobrać wartość właściwości.  
**Akcesor set** wywołany jest w chwili nadania wartości właściwości.  
Zwróć uwagę na słowo kluczowe *value* przy **akcesorze set**. Ma ono szczególną funkcję jedynie wewnątrz ciała właściwości - reprezentuje wartość przypisywaną do właściwości w akcesorze set.
{: .alert-info} 

Od tej chwili programista komunikuje się z polami klasy za pomocą **publicznych właściwości**. Mimo tego kod nadal jest stosunkowo długi. Na szczęście wprowadzona została deklaracja skrócona.

## Skrócona deklaracja właściwości

Bardzo przydatne jest używanie **skróconej wersji deklaracji właściwości**. Pozwala ona zredukować ilość kodu do absolutnego minimum w prostych programach.

Projektanci **języka C#** zdali sobie sprawę, że w przeważającej większości **właściwość nie będzie miała żadnych dodatkowych funkcji**, oprócz wyprowadzania/wprowadzania wartości dla pola prywatnego. Z tego powodu została wprowadzona możliwość **skróconej deklaracji właściwości**.

Znowu przerobię prosty programik znany z poprzednich przykładów:

	class Osoba
	{
	    public int Wiek { get; set; }
	}
	
	class Program
	{
	    static void Main(string[] args)
	    {
	        Osoba Karol = new Osoba();
	        
	        Karol.Wiek = 21;
	        System.Console.WriteLine(Karol.Wiek);
	        
	        System.Console.ReadKey();
	    }
	}

Środowisko automatycznie rozwinie **skróconą deklarację właściwości** do normalnej postaci  podczas kompilacji. Kompilator **niejawnie utworzy nawet pole prywatne przypisane do publicznej właściwości**. Dowodem tego jest analiza języka CIL po zdebugowaniu aplikacji.

Co sądzisz o tym zapisie? Program działa identycznie tak jak w poprzednich przykładach. Porównując teraz **właściwości w języku C#** do ich braku w Javie, widać jak bardzo są przydatne i jak bardzo potrafią skrócić kod.

Tworząc klasę *Osoba* o przykładowych właściwościach *Imie*, *Nazwisko*, *Wiek* w klasie posiadamy 3 linijki kodu. Pisząc tę samą klasę w Javie lub C++ i hermetyzując dane, będziemy mieli około 12 linijek kodu.

## Podsumowanie

Zgodnie z informacjami dostępnymi na MSDN, **nie powinno się umieszczać zaawansowanych fragmentów kodu we właściwościach**. Właściwości powinny w prosty sposób zwracać lub zapisywać wartości określonych pól. Dopuszczalne są jedynie proste operacje w stylu:

	get
	{
	    return 2013-wiek;
	}

Jeżeli pisałeś wcześniej w innym języku nie zniechęcaj się do funkcjonalności jakie zapewnia C#. **Używanie właściwości i akcesorów** pozwala znacznie zredukować kod a na dodatek narzuca schemat poprawnego programowania obiektowego.

Dodam na koniec, że niektóre źródła podają nazwę **własności** zamiast **właściwości**.