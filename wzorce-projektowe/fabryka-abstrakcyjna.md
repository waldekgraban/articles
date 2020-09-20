---
title: Fabryka abstrakcyjna - robisz to źle
category: wzorce-projektowe
thumbnail: fabryka-abstrakcyjna.png
published: 2017-08-12T00:00:00Z
---
**Fabryka abstrakcyjna** i wszystkie jej odmiany są rodziną konstrukcyjnych wzorców projektowych. Dzięki fabryce otrzymujemy interfejs, służący do generowania różnych obiektów, które go spełniają. **Fabryka abstrakcyjna** i **metoda wytwórcza **są bardzo często mylone ze sobą. W wielu źródłach zaprezentowane są ich błędne implementacje, a programiści często sami nie wiedzą, której wersji fabryki chcą użyć.

<!--more-->

## Programowanie bez fabryki

W normalnym podejściu do programowania obiektowego, **wszelkie instancje nowych obiektów** tworzone są za pomocą słowa kluczowego *new*. Aby utworzyć instancję musimy **związać się z jej konkretnym typem**, podać go zaraz za rozkazem *new*. Dodatkowo musimy znać i wypełnić konstruktor. Przy takim podejściu programista nie jest w stanie w łatwy sposób zmienić sposobu tworzenia danego obiektu.

**Fabryka i wszelkie jej odmiany służą temu**, aby odciąć się od podawania konkretnego typu. Zamiast tego, wykorzystana zostaje tzw. **metoda fabrykująca zwracająca instancję**, która nas interesuje. Krótko mówiąc, zamiast używania rozkazu *new* i przypisywania instancji do jakiejś zmiennej, wywołujemy metodę fabrykującą np. *CreateLoggerInstance()* i to ona jest odpowiedzialna, za zwrócenie instancji.

## Po co używać wzorca fabryki?

Dzięki użyciu fabryki programista zyskuje abstrakcyjną warstwę, odpowiedzialną za **tworzenie instancji obiektów w jakiś sposób powiązanych wspólnym interfejsem**. Uzyskujemy wtedy kod, który jest bardziej skalowalny, łatwiejszy na rozbudowę. Oto najważniejsze zalety używania fabryk:

- spełnia **zasadę odwrócenia zależności** (*dependency inversion principle*). Rodzina tworzonych obiektów spełnia wspólny interfejs, którym następnie się posługujemy. Wpływa to bardzo pozytywnie na testowalność kodu (przykład mechanizmu *IoC*). Przykładowo zamiast tworzyć nowe klasy *new Apple()*, *new Banana()* itd. posiadamy metodę fabrykującą zwracającą obiekty interfejsu *IFruit*.
- skupienie logiki w **metodzie fabrykującej**, dzięki czemu zmiany w kodzie można wprowadzić w jednym miejscu systemu.
- dostarcza dodatkową warstwę abstrakcji dzięki czemu **hermetyzuje (enkapsuluje) odpowiednią logikę wewnątrz fabryki**. Hermetyzacja jako filar programowania obiektowego niesie ze sobą szereg kolejnych korzyści m.in. uproszczenie kodu, brak powtarzalności, ukrycie logiki pomiędzy warstwami.
- upraszcza proces inicjalizacji skomplikowanych klas (w przypadku rozbudowanych konstruktorów).
- w łatwy sposób **pozwala na reużywalność kodu** (procesu inicjalizacji danej rodziny klas) w innym miejscu systemu
- spełnia zasadę **otwarty na rozbudowę, zamknięty na modyfikację** (*open/closed principle*). Do fabryki w dość łatwy sposób można dodać dodatkową klasę, mając przy tym pewność, że nie zepsujemy czegoś co aktualnie działa.

Powyższe zalety fabryk wynikają z bardzo szerokiego spojrzenia na ten wzorzec projektowy. Trzeba pamiętać, że fabryki nie są lekarstwem na całe zło programowania obiektowego. Często są nadużywane w miejscach, gdzie nie powinno ich być.

### Kiedy użyć wzorca fabryki

Zapamiętać należy zasadę: **fabryk używamy tam, gdzie chcemy odciąć się od tworzenia instancji klas posługując się konkretnym typem**. Może być to spowodowane np. skomplikowaną logiką tworzenia instancji. Np. wiemy, że chcemy uzyskać instancję spełniającą jakiś interfejs, ale jaki to będzie konkretny typ, zależy od dodatkowych parametrów - wtedy używamy fabryki. Powody za użyciem fabryk:

- klasa ma **skomplikowany, przeciążony wielokrotnie konstruktor**. Oznaczać to może oczywiście błędnie zaprojektowaną klasę, ale może też wskazywać na to, że utworzenie instancji klasy potrzebuje dodatkowej logiki.
- utworzenie instancji klasy jest **poprzedzone instrukcją warunkową** *if*. Z całą pewnością Taką logikę należy zahermetyzować wewnątrz fabryki
- w momencie pisania programu nie wiesz, której instancji potrzebujesz (bo np. jest to uzależnione od parametru zwracanego z API). Ponownie, logikę hermetyzujemy wewnątrz fabryki.

Fabryka abstrakcyjna (i jej odmiany) jest jednym z pierwszych wzorców projektowych, które poznaje programista (obok singletona). Z tego powodu, początkujący programiści często **próbują wrzucać fabryki tam, gdzie nie są one potrzebne**. Pojedyncza metoda zwracająca instancję jest natomiast bardziej antywzorcem, niż dobrym nawykiem.

Idealny przykład do użycia fabryki to kod, który wygląda mniej więcej tak:

	IManageArea manageArea;
	Employee employee = employeeService.Load("Managment", 42);
	
	if (employee.isManager)
	{
	    manageArea = new GlobalManageArea();
	}
	else
	{
	    manageArea = new LocalManageArea();
	}
	
	manageArea.FireEmployees(1000);
	// itd

Pomijając sensowność kodu, widać tutaj logikę, od której zależy utworzenie instancji klasy. Całość powinna zostać zahermetyzowana wewnątrz fabryki.

### Kiedy nie używać wzorca fabryki

W następujących sytuacjach użycie wzorca fabryki może okazać się błędne lub co najmniej nadmiarowe:

- nie tworzyć fabryki "na wszelki wypadek", bo może kiedyś będę chciał zainicjalizować obiekt w inny sposób (złamanie zasady *YAGNI).*
- nie tworzyć fabryki, jeżeli w jej wnętrzu znajduje się pojedynczy wielki *switch* bez logiki, zwracający instancję klas z domyślnym konstruktorem. Jest to redundancja i wprowadzenie nowej warstwy abstrakcji (na wszelki wypadek), tam gdzie nie jest ona potrzebna.
- nie obudowywać kontenerów *IoC* w fabrykę, bo przeważnie nie jest to konieczne (kontener *IoC* nie jest fabryką, ale spełnia podobne zadanie i daje podobne efekty)
- kiedy logika jest tak skomplikowana, że nie da się utworzyć instancji obiektu w jednej funkcji, wtedy **lepiej rozważyć użycie wzorca budowniczy**

Jak wielu programistów tyle opinii, czy aby na pewno słuszne jest założenie, aby **nie tworzyć fabryki na wszelki wypadek**, bo być może kiedyś będę potrzebował zainicjalizować obiekt w inny sposób. Pozornym argumentem popierającym tę tezę jest to, że zahermetyzowanie inicjalizacji w osobnej warstwie umożliwi nam w przyszłości zmianę sposobu inicjalizacji w jednym miejscu. Jest to też **jedna z zalet wzorca fabryk abstrakcyjnych**. Nie należy jednak pchać fabryki wszędzie i próbować rozwiązywać nią problemów, do których nie jest dedykowana.

Wykorzystywanie fabryk jako osobnego pojemnika do inicjalizacji obiektów na dłuższą metę okaże się zgubne. Języki obiektowe C#, Java (oraz pół-obiektowe) C++ **wymagają związanie klasy z typem** za pomocą słowa kluczowego *new* i nie zbyt wiele programista może z tym zrobić. Użycie prostej fabryki, nie "będzie" bardziej spełniać zasad SOLIDu, a dobry i testowalny kod da się osiągnąć innymi mechanizmami takimi jak **dziedziczenie, kompozycja czy generyczność**. Tworzenie prostych fabryk doprowadza w skrajności do sytuacji posiadania "super klasy" z dziesiątkami metod *CreateXXXInstance()* - co ostatecznie nie ma to nic wspólnego ze wzorcem fabryki.

## Prosta fabryka (simple factory)

Prosta fabryka jest **najczęściej używanym rodzajem fabryki**. Doskonale sprawdza się w nieskomplikowanych przypadkach. Jest prosta do zaimplementowania a jednocześnie daje programiście korzyści, które wynikają ze stosowania wzorca fabryk. Ta odmiana wzorca, moim zdaniem, ma szczególne upodobanie **wśród początkujących programistów**, którzy chcieliby gdzieś zastosować jakiś wzorzec, a *simple factory* jest jednym z prostszych do użycia. Oto przykładowy kod:

	enum ShapeType
	{
	    Square = 1,
	    Triangle = 2
	}
	
	interface IShape { }
	
	class Square : IShape { }
	class Triangle : IShape { }
	
	class ShapeFactory
	{
	    public IShape CreateShape(ShapeType shapeType)
	    {
	        switch (shapeType)
	        {
	            case ShapeType.Square:
	                return new Square();
	            
	            case ShapeType.Triangle:
	                return new Triangle();
	            
	            default:
	                throw new ArgumentException();
	        }
	    }
	}
	
	static void Main(string[] args)
	{
	    ShapeFactory shapeFactory = new ShapeFactory();
	    
	    IShape triangle = shapeFactory.CreateShape(ShapeType.Triangle);
	}

Kod jest trywialnie prosty. **Tworzenie poszczególnych instancji zostało zahermetyzowane w osobnej warstwie abstrakcji**, którą jest klasa fabryki. W akapicie wyżej napisałem, że nie ma sensu tworzyć fabryk, których jedyną zawartością jest wielki, nic nie wnoszący *switch*, jednak jest to tylko przypadek testowy.

Wadą prostej fabryki jest to, że nie spełnia drugiej zasady SOLID czyli *Open/closed principle*. Mimo wielu zalet jakie uzyskał programista decydując się na tę fabrykę, rozbudowa jej o nowe elementy niesienie ze sobą konieczność ingerencji w klasę fabryki. Konkretniej oprócz rozszerzenia interfejsu *IShape* o nowe klasy, należy rozbudować instrukcję *switch* wewnątrz klasy fabryki.

Można też zastosować wersję statyczną. Metoda zwracająca instancje może być oznaczona jako *static* wtedy **nie trzeba będzie za każdym razem tworzyć instancji fabryki**. Wybór oczywiście zależy od specyfikacji systemu, jednak **ja stanowczo trzymam się od klas statycznych z daleka**. Klas statycznych nie da mockować, wcześniej czy później są z nimi problemy (szczególnie platforma ASP.NET MVC). Użycie klas statycznych często oznacza brak możliwości wprowadzenia testów jednostkowych.

## Metoda fabrykująca (factory method)

Metoda fabrykująca to **najczęściej używany rodzaj fabryki**, wśród programistów, którzy dobrze rozumieją działanie poszczególnych rodzajów fabryk. Jest to odmiana najbardziej uniwersalna. Oprócz niesienia ze sobą wszystkich korzyści wynikających ze wzorca fabryki, spełnia też pierwszą zasadę SOLID czyli *open/closed principle*. Dlaczego? Ponieważ kod odpowiedzialny za tworzenie instancji został przeniesiony do klas potomnych poszczególnych fabryk. Oto przykładowy kod:

	interface IShape { }
	
	class Square : IShape { }
	class Triangle : IShape { }
	
	abstract class ShapeFactory
	{
	    public abstract IShape CreateShape();
	}
	
	class SquareFactory : ShapeFactory
	{
	    public override IShape CreateShape()
	    {
	        return new Square();
	    }
	}
	
	class TriangleFactory : ShapeFactory
	{
	    public override IShape CreateShape()
	    {
	        return new Triangle();
	    }
	}
	
	static void Main(string[] args)
	{
	    ShapeFactory shapeFactory = new SquareFactory();
	    
	    IShape square = shapeFactory.CreateShape();
	}

Jak widać w powyższym kodzie klasa fabryki jest teraz klasą abstrakcyjną. Logika umieszczona w *switchu* prostej fabryki **została zastąpiona mechanizmem polimorfizmu**. Proces tworzenia instancji klas został wydelegowany do klas potomnych, i to one decydują, jaką instancję zwrócić. Dzięki temu, przy rozbudowie powyższego kodu programista nie musi zmieniać klasy fabryki, co oznacza że spełniona jest zasada *open/closed principle*. Dopisanie nowego typu polega jedynie na dopisaniu nowej fabryki.

Powyższy przykład jest prosty, aby maksymalnie ukazać koncepcje metody fabrykującej. Dzięki temu, że korzystamy z metody fabrykującej mamy jednak kolejną ważną zaletę, której nie widać w przykładzie. Klasa abstrakcyjna może posiadać metody, które odziedziczą klasy potomne. Dzięki temu mamy większe pole możliwości niż w prostej fabryce, której sednem działania była instrukcja warunkowa *switch*.

#### Zalety metody fabrykującej

- spełnia zasadę SOLID *open/closed principle*, jest **łatwiejsza do późniejszych modyfikacji**.
- główny kod fabryki może być dystrybuowany w postaci osobnej, zamkniętej biblioteki DLL, mimo to programista i tak będzie miał możliwość jej rozbudowy.
- dodatkowa warstwa abstrakcji, która umożliwia **hermetyzację skomplikowanej logiki tworzenia instancji obiektu** w klasach pochodnych (w prostej fabryce wszystko byłoby w *switchu*).
- poszczególne **fabryki pochodne można w łatwy sposób testować jednostkowo**, należy tylko wprowadzić dodatkowy interfejs, które będą spełniać (oprócz klasy abstrakcyjnej). Prostej fabryki natomiast testować się nie da.

## Fabryka abstrakcyjna (abstract factory)

Fabryka abstrakcyjna jako ostatnia z odmian wzorca fabryk sprawia zawsze najwięcej kłopotów. Główna zasada mówi, że wzorzec fabryki abstrakcyjnej ma dostarczyć **interfejs do tworzenia rodziny obiektów**, konkretniej oddzielić interfejs od implementacji. Najważniejszym słowem kluczowym jest tutaj **rodzina obiektów**, która nie występuje w przypadku dwóch poprzednich fabryk.

Ale, że o co chodzi? Dwóch poprzednich fabryk, czyli fabryki prostej i metody fabrykującej używamy, gdy chcemy tworzyć **obiekty spełniające jeden interfejs**. W poprzednich akapitach tworzyliśmy figury geometryczne spełniające interfejs *IShape*. **Nie możemy przedstawić poprzedniego przykładu w formie fabryki abstrakcyjnej**, ponieważ figury geometryczne to pojedynczy typ, a nie rodzina obiektów.

Przykład z figurami geometrycznymi nie jest zbyt trafny, ponieważ ciężko na szybko wymyślić jakąś sensowną rodzinę obiektów. Załóżmy jednak, że chcemy tworzyć typy figur spełniające interfejs *IShape* oraz zbiory liczbowe spełniające interfejs *INumber*. Obydwa typy tworzą rodzinę obiektów *MathTest*, czyli naszą **fabryką abstrakcyjną będzie fabryka zwracająca test**. W momencie gdy zdefiniowaliśmy umowną rodzinę obiektów, możemy zamodelować fabrykę abstrakcyjną. *MathTest* będzie naszą fabryką abstrakcyjną, a konkretne fabryki abstrakcyjne będą jej różnymi odmianami.

Spójrzmy na przykładowy kod:

	// kształty
	interface IShape { }
	
	class Square : IShape { }
	class Triangle : IShape { }
	
	// liczby
	interface INumber { }
	
	class RealNumber : INumber { }
	class ComplexNumber : INumber { }
	
	// fabryka abstrakcyjna rodziny obiektów
	abstract class MathTestFactory
	{
	    public abstract IShape CreateShape();
	    public abstract INumber CreateNumber();
	}
			
	// konkretna fabryka rodziny obiektów
	class PrimarySchoolTestFactory : MathTestFactory
	{
	    public override IShape CreateShape()
	    {
	        return new Square();
	    }
	    
	    public override INumber CreateNumber()
	    {
	        return new RealNumber();
	    }
	}
	
	// konkretna fabryka rodziny obiektów
	class HighSchoolTestFactory : MathTestFactory
	{
	    public override IShape CreateShape()
	    {
	        return new Triangle();
	    }
	    
	    public override INumber CreateNumber()
	    {
	        return new ComplexNumber();
	    }
	}
	
	// klasa klienta (kontekst wykonania fabryki)
	class MathTest
	{
	    private MathTestFactory mathTestFactory;
	    
	    public MathTest(MathTestFactory mathTestFactory)
	    {
	        this.mathTestFactory = mathTestFactory;
	    }
	    
	    public void GenerateTest()
	    {
	        var shape = this.mathTestFactory.CreateShape();
	        var number = this.mathTestFactory.CreateNumber();
	        System.Console.WriteLine("Test wygenerowany");
	    }
	}
	
	static void Main(string[] args)
	{
	    MathTest mathTest;
	    
	    mathTest = new MathTest(new PrimarySchoolTestFactory());
	    mathTest.GenerateTest(); // łatwy test przy użyciu PrimarySchoolTestFactory()
	    
	    mathTest = new MathTest(new HighSchoolTestFactory());
	    mathTest.GenerateTest(); // trudny test przy użyciu PrimarySchoolTestFactory()
	}

Klasa *MathTestFactory* jest **fabryką abstrakcyjną definiującą rodzinę obiektów**. Za pomocą kompozycji definiujemy, że obydwa elementy są w jakiś sposób od siebie zależne. Na potrzeby artykułu przyjęliśmy, że tworzą "test". Następnie tworząc konkretne egzemplarze fabryk, możemy rodzinę obiektów stworzyć na wiele sposób i tak np. *PrimarySchoolTestFactory* tworzy rodzinę obiektów bardzo prostych (kwadrat i zbiór liczb rzeczywistych). Natomiast *HighSchoolTestFactory* tworzy rodzinę obiektów trudnych (trójkąt i zbiór liczb zespolonych).

Ostatnim najważniejszym elementem fabryki abstrakcyjnej jest klasa "klienta" (kontekstu wykonania fabryki). **Za pomocą kompozycji tworzymy klasę zawierającą odpowiednią fabrykę**. Często w klasie klienta istnieje też namiastka wzorca projektowego metody szablonowej, która wywołuje metody fabryki (w kodzie wyżej metoda *GenerateTest()*). Klasa klienta służy do obsługi logiki wymaganej przy tworzeniu rodziny obiektów, hermetyzuje inicjalizację rodziny obiektów oraz ich konfigurację. **Klient nie wie, jaka fabryka zostanie mu dostarczona i wywołana - ważne aby spełniała typ fabryki abstrakcyjnej**.

### Rola poszczególnych elementów fabryki abstrakcyjnej

- *fabryka abstrakcyjna* - definiuje umowną rodzinę obiektów.
- *konkretna fabryka abstrakcyjna* - implementuje fabrykę abstrakcyjną, dostarcza implementację jej metodom, zawiera logikę.
- *klient* - musi zawierać odniesienie do fabryki abstrakcyjnej (za pomocą kompozycji), zawiera metodę (na wzór metody szablonowej) wywołującą metody dostarczonej fabryki, **nie wie z jakiej fabryki będzie korzystał **(logika zahermetyzowana w innych warstwach).

Mimo zalet wzorca fabryki abstrakcyjnej, **niesie on ze sobą także wady**. Jest o wiele **trudniejszy do rozbudowy**, łamie zasadę *open/closed principle*. Dodając nowy produkt, **trzeba zmodyfikować wiele innych klas** np. konkretne fabryki, a dodając nowy typ do rodziny obiektów trzeba zmodyfikować klasę fabryki abstrakcyjnej. Czasem trzeba zmodyfikować także kod klasy klienta.

## Podsumowanie wzorca fabryki

Wszelkie odmiany fabryki abstrakcyjnej potrafią przysporzyć **wiele problemów nawet zaawansowanym programistom**. Osobiście wzorzec fabryki uważam **za najbardziej skomplikowany wzorzec** gangu czworga (*gank of fours* - ojcowie wzorców i paradygmatu OOP). Jego niezwykła komplikacja polega na wielu możliwościach implementacji, a niniejszy artykuł pokazał tylko małą jej część. Przykłady zawarte w artykule są ubogie, a poszczególne rodzaje fabryk **można znacząco modyfikować i łączyć z innymi wzorcami** np. wzorcem strategii i wzorcem polecenia. Mimo to, poniższe wskazówki pomogą Ci ogarnąć temat fabryk:

#### Prosta fabryka

- najprostsza możliwość oddzielenia implementacji od interfejsu.
- **brak dziedziczenia** i **brak kompozycji**.
- brak osobnej klasy klienta.

Prosta fabryka zawsze zwraca obiekt spełniający **jeden interfejs**. Jest **ciężka do testowania** i ciężka do rozbudowy. Czasem (niesłusznie) uważana za antywzorzec, bo bywa nadużywana tam gdzie wcale nie powinno być fabryki.

#### Metoda fabrykująca

- najbardziej uniwersalna możliwość oddzielenia implementacji od interfejsu (najlepiej spełnia *open/closed principle*).
- **zawsze występuje mechanizm dziedziczenia**.
- **brak kompozycji**.
- brak osobnej klasy klienta.

Metoda fabrykująca zawsze zwraca obiekt spełniający **jeden interfejs**. Jest bardzo skalowalna, **idealna do testowania i rozbudowy**. Zawsze korzysta z **mechanizmu dziedziczenia i polimorfizmu**, przenosząc odpowiedzialność na klasy pochodne (czyli konkretne fabryki).

#### Fabryka abstrakcyjna

- umożliwia tworzenie rodzin obiektów w jakiś sposób powiązanych ze sobą.
- **zawsze występuje mechanizm dziedziczenia**.
- **zawsze występuje mechanizm kompozycji**.
- **zawsze występuje dedykowana klasa klienta**.

Minusem fabryki abstrakcyjnej jest **mała skalowalność**, dodając nowy produkt trzeba przerobić klasę fabryki abstrakcyjnej oraz konkretnych fabryk. **Często nazywana jest fabryką fabryk**, ponieważ tak naprawdę korzystając z mechanizmu kompozycji **zwraca metody fabrykujące**. Zawsze występuje **mechanizm dziedziczenia i polimorfizm** (do konkretnych fabryk) oraz **mechanizm kompozycji** (do powiązania klienta z określoną fabryką). Jej sednem jest to, że klient wykonuje jakieś operacje, ale **nie wie z jakiej fabryk korzysta**. Bez osobnej klasy klienta jest po prostu metodą fabrykującą. Bez zwracania rodziny obiektów (tylko pojedynczego typu) jest po prostu metodą fabrykującą.

Kod dostępny na github: <https://github.com/p-programowanie/wzorce-projektowe/tree/master/fabryka>