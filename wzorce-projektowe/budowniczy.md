---
title: Budowniczy
category: wzorce-projektowe
thumbnail: budowniczy.png
published: 2018-03-25T00:00:00Z
---
**Budowniczy to jeden ze wzorców projektowych** używanych w programowaniu obiektowym. Zalicza się on do rodziny wzorców konstrukcyjnych. Dzięki użyciu budowniczego **oddzielamy proces tworzenia obiektu od jego reprezentacji**. Jest to dość prosty wzorzec, który jednak sprawia problemy, ze względu na różne warianty w jakich występuje. W tym artykule przedstawię dwie podstawowe implementacje tego wzorca oraz opiszę różnice, jakie między nimi zachodzą.

<!--more-->

## Różne odmiany budowniczego

Na samym początku należy zdać sobie sprawę z tego, że istnieją dwie popularne odmiany tego wzorca, jedna opisana przez GoF (ang. *Gang of Four*) a druga (będąca modyfikacją pierwszej) opisana przez Joshue Blocha. W większości przypadków **mówiąc o wzorcu budowniczy ma się na myśli jego pierwszą odmianę** - w tym artykule także będę się do niej odnosił. Wersja statyczna budowniczego zostanie opisana w jednym z akapitów poniżej.

## Po co używać wzorca budowniczy?

Budowniczy ma za zadanie rozwiązać pewien powtarzający się problem programistyczny - konkretniej zapewnia **oddzielenie procesu inicjalizacji obiektu od jego reprezentacji**. Decydując się na użycie tego wzorca można osiągnąć następujące korzyści:

- logika mówiąca o tym jak obiekt ma być zbudowany **będzie oddzielona od implementacji** tej logiki
- spełnia zasadę **otwarty na rozbudowę, zamknięty na modyfikacje** (*open/closed principle*) - łatwo dodać do kodu nowych budowniczych
- spełnia zasadę **odwrócenia zależności** (*dependency inversion principle*)
- sprzyja samodokumentującemu się kodowi (widząc poszczególnych budowniczych wiemy, co dostarczą)
- daje doskonałą kontrolę nad etapami budowania kompozytu (np. hermetyzacja obsługi błędów dla danego kroku)

Rzadko zdarza się aby za pomocą wzorca budowniczy inicjalizować proste klasy DTO. Częściej natomiast, gdy złożony model składa się z wielu typów referencyjnych na zasadzie kompozycji.

## Kiedy używać budowniczego

Budowniczy najczęściej używany jest wtedy, **gdy istnieje potrzeba zbudowania złożonego obiektu** (tzw. kompozytu). Złożony obiekt może oznaczać tworzenie "super klasy" - odpowiedzialnej za zbyt wiele rzeczy, a więc złamanie pierwszej zasady SOLID. W pierwszej kolejności należy więc spróbować uprościć złożony model, jednak jeżeli okaże się to niemożliwe, należy zastanowić się nad implementacją wzorca budowniczego. Szczególnie, jeżeli nasz złożony kompozyt ma być **budowany na wiele różnych sposobów** i stoi za tym jakaś logika.

Należy rozważyć użycie wzorca budowniczy gdy:

- obiekt, który tworzymy jest złożony i nie da się go uprościć
- nie da się utworzyć instancji obiektu **poprzez jednorazową operację** (wieloetapowa inicjalizacja)
- obiekt, którzy tworzymy, **będzie budowany wiele razy w różny sposób**

Spójrzmy na taki, niezbyt fajny, kod:

	var smallCar = new Car();

	if (budget > 4500)
	{
	    smallCar.Wheels = "Aluminium rims 17 inches";
	}
	else
	{
	    smallCar.Wheels = "Steel rims 15 inches";
	}

	smallCar.Addons = new List<string>();
	smallCar.Addons.Add("CD radio with MP3");
	smallCar.Addons.Add("CD radio");

	smallCar.Engine = EngineFactory.CreateEngine("120 HP engine");

W powyższym kodzie widzimy inicjalizację obiektu klasy `Car`. Prawdopodobnie chodzi o jakąś uboższą wersję, ponieważ tak możemy wywnioskować z nazwy zmiennej `smallCar`. Kod przeplatany jest logiką, w pewnych miejscach zostały użyte instrukcje warunkowe a gdzie indziej **wzorzec prostej fabryki**.

Kod jest i tak stosunkowo prosty, ponieważ brakuje w nim obsługi błędów, czyli instrukcji `try/catch`.  Reużycie kodu np. w celu zbudowania większego samochodu polegałoby na skopiowaniu tego kodu w całości i zmianie poszczególnych parametrów. Byłoby to **podejście bardzo imperatywne, raczej mało obiektowe**. W dużym projekcie wielu programistów konstruowałoby instancje klasy `Car` na swój sposób. Chcąc zmienić proces ich budowy, należałoby to zrobić w wielu miejscach.

Tak mogłaby wyglądać prosta implementacja budowniczego dla powyższego kodu:

	// kierownik - logika inicjalizacji obiektu
	class CarDirector
	{
	    public void ConstructCar(ICarBuilder builder)
	    {
	        builder.BuildWheels();
	        builder.BuildAddons();
	        builder.BuildEngine();
	    }
	}
	
	// budowniczy - interfejs implementacji
	interface ICarBuilder
	{
	    void BuildWheels();
	    void BuildAddons();
	    void BuildEngine();
	    Car GetResult();
	}
	
	// konkretny budowniczy - implementacja 
	class SmallCarBuilder : ICarBuilder
	{
	    private Car car = new Car();
	    private EngineFactory engineFactory = new EngineFactory();
	    
	    public void BuildWheels()
	    {
	        car.Wheels = "Steel rims 15 inches";
	    }
	    
	    public void BuildAddons()
	    {
	        car.Addons = new List();
	        car.Addons.Add("CD radio with MP3");
	        car.Addons.Add("CD radio");
	    }
	    
	    public void BuildEngine()
	    {
	        car.Engine = engineFactory.CreateEngine("120 HP engine");
	    }
	    
	    public Car GetResult()
	    {
	        return car;
	    }
	}
	
	static void Main(string[] args)
	{
	    CarDirector carDirector = new CarDirector();
	    ICarBuilder smallCarBuilder = new SmallCarBuilder();
	    
	    // buduj według logiki CarDirector używając implementacji z SmallCarBuilder
	    carDirector.ConstructCar(smallCarBuilder);
	    
	    Car smallCar = smallCarBuilder.GetResult();
	}

Dzięki użyciu wzorca główna metoda programu znacząco się uprościła. Uzyskaliśmy jednolity interfejs polimorficzny służący do tworzenia instancji samochodu.

### Rola poszczególnych elementów wzorca

Oto jego podstawowe elementy wzorca, które zostały użyte w powyższym przykładzie:

- *budowniczy (CarBuilder)* - dostarcza abstrakcyjny interfejs służący do budowania finalnego produktu
- *konkretny budowniczy (SmallCarBuilder)* - dostarcza implementację metodom budowniczego (implementacja)
- *kierownik (CarDirector)* - konstruuje obiekt z wykorzystaniem jakiegoś budowniczego (logika)
- *produkt (product)* - finalny złożony obiekt, dostarczony przez kierownika, zbudowany za pomocą jakiegoś budowniczego

Widząc pierwszy raz **implementację wzorca budowniczy** można odnieść wrażenie, że tych klas jest trochę za dużo. To naturalne, szczególnie jeśli **rozumie się zasadę działania wzorca fabryki abstrakcyjnej**. Poniżej przedstawię swoją krótką analizę tego wzorca, która być może rozwieje kilka wątpliwości:

- najważniejszymi elementami wzorca jest *kierownik* oraz *konkretny budowniczy*. Kierownik skupia logikę budowania, konkretny budowniczy skupia implementację tej logikii
- *budowniczy* jest nic nieznaczącym interfejsem (w przykładzie klasa abstrakcyjna) aby zapewnić wspólny interfejs polimorficzny dla konkretnych implementacji budowniczych (spełnienie 4 zasady SOLID **odwrócenie zależności**)
- nie możemy zrezygnować z *kierownika*, chociaż wydaje się nadmiarowy. Niektóre implementacje tego wzorca rezygnują z niego jednak nie jest to wtedy wzorzec budowniczy zaprezentowany przez GoF.
- dzięki oddzieleniu logiki od implementacji, możemy zmienić logikę budowania obiektu w jednym miejscu (*kierownik*) i zostanie ona zmieniona w całym systemie - w każdym budowniczym, a one nie będą tego świadome
- można dojść do wniosku, że połączenie *kierownika* z *konkretnym budowniczym* bardzo przypominałoby fabrykę abstrakcyjną

### Różnica pomiędzy budowniczym a fabryką

Fabryka i budowniczy to dwa podstawowe wzorce kreacyjne - moim zdaniem najczęściej używane. Niosą ze sobą praktycznie takie same korzyści. Jeżeli zaczniesz się nad tym zastanawiać, **możesz dojść do wniosku, że nie wiesz, który wzorzec użyć**.

Podstawowa różnica w tych dwóch wzorcach polega na tym, że **budowniczy służy do budowania bardziej skomplikowanych obiektów - kompozytów**. Fabryka natomiast odpowiada za utworzenie instancji prostszych obiektu, a jej logikę da się zahermetyzować w jednej funkcji. Dodatkowo, fabryka tworzy instancję od razu, a z budowniczego pobieramy ją na żądanie.

Jeżeli musisz utworzyć jakiś niefajny, skomplikowany obiekt, to:

1. Raczej odrzuć pomysł implementacji prostej fabryki..
2. Spróbuj zaimplementować metodę fabrykującą.
3. Jeżeli inicjalizacja obiektu jest zbyt złożona dla metody fabrykującej rozważ fabrykę abstrakcyjną lub budowniczego.

W fabrykę abstrakcyjną warto pójść, jeżeli tworzymy rodzinę obiektów, a implementacje obiektów da się zahermetyzować w jednej funkcji. Budowniczy to ostateczność, **kiedy budowanie obiektu jest zbyt złożone dla fabryki abstrakcyjnej** lub gdy czujesz, że warto pewne kroki wydzielić jako osobne metody.

## Obsługa błędów

Obsługa błędów potrafi rodzić pewną konsternację wynikającą z tego, że **jest wiele miejsc na ich obsłużenie**. Zasada jest jednak ta sama co wszędzie - błędy należy zgłaszać tak szybko, jak tylko jest to możliwe. Oznacza to, że wyjątki powinny być rzucane na etapie wywoływań poszczególnych kroków budowniczego.

W statycznym budowniczym (o którym napisałem w akapicie niżej) pewne błędy **muszą zostać obsłużone w metodzie budującej**. Wynika to z faktu, że programista projektant danego rozwiązania nie ma panowania nad tym, które metody płynnego interfejsu zostaną wywołane i w jakiej kolejności. Oznacza to, że **niektóre walidacje możemy wykonać w poszczególnych krokach budowniczego**, jednak główną walidację obiektu **musimy przeprowadzić w metodzie budującej** zwracającej instancję produktu. Dzieje się tak np. gdy chcemy sprawdzić czy zbudowany obiekt samochód został (w ogóle) wyposażony w silnik.

## Statyczny budowniczy

Myślę, że w wystarczający sposób opisałem **cechy i zalety wzorca budowniczy**, który został zaproponowany przez GoF. W programowaniu obiektowym częściej jednak korzysta z się z prostszej wersji budowniczego, która niestety jest nazywana tym samym terminem. Z tego względu czasem mogą pojawiać się pewne niezrozumienia, szczególnie - przykładowo - na rozmowach rekrutacyjnych.

### Płynny interfejs

Statyczny budowniczy **polega na użyciu metody zwanej płynnym interfejsem** (z ang. *fluent interface*). Polska nazwa brzmi dość głupio, polecam zapamiętać tę angielską.

Podejście płynnego interfejsu polega na takim projektowaniu metod w paradygmacie programowania obiektowego, aby **każda z nich zwracała instancję klasy**, w obrębie której się znajduje. Standardowy przykład płynnego interfejsy może wyglądać następująco:

	class Calc
	{
	    private int result = 0;
	    
	    public Calc Add(int number)
	    {
	        Console.WriteLine("dodaje");
	        return this;
	    }
	    public Calc Sub(int number)
	    {
	        Console.WriteLine("odejmuje");
	        return this;
	    }
	    public int GetResult() {
	        return this.result;
	    }
	}
	
	static void Main(string[] args)
	{
	    int result = new Calc().Add(5).Add(10).Sub(8).GetResult();
	}

Wywołanie poszczególnych metod płynnego interfejsu w danej kolejności zwróci wynik *7*. Teraz zapewne widzisz, skąd wzięła się nazwa **płynny interfejs**. W ten sposób bardzo często projektowane są przeróżne API (z ang. *fluent API*) oraz właśnie **statyczny budowniczy**. Nie ma znaczenia ile razy zostaną wywołane metody, za każdym razem zwracana jest instancja tej samej klasy więc możemy wywołać je ponownie.

## Po co korzystać ze statycznego budowniczego

Statyczny budowniczy został po raz pierwszy zaproponowany przez Josha Blocha, w pewnej książce związanej z programowaniem w języku Java. Zauważył on, że dzięki konstrukcji **budowniczego z wykorzystaniem płynnego interfejsu** można osiągnąć mechanizm dający wiele korzyści:

- uproszczenie trudnych konstruktorów
- budowanie skomplikowanych obiektów (ale już bez oddzielenia logiki od implementacji!)
- tworzenie obiektów niemutowalnych (ang. *immutable*)
- długi/wieloetapowy proces inicjalizacji obiektu końcowego

Pozostałe zalety wzorca budowniczy opisane w akapitach wyżej, nie występują we wzorcu budowniczego statycznego opisanego Blocha.

## Jak stworzyć statyczny budowniczy

Zasady tworzenia statycznego budowniczego są takie, że nie ma zasad. Dlaczego? Budowniczy opisany przez GoF jest **sklasyfikowany jako wzorzec konstrukcyjny, a ze względu na jego zakres jako wzorzec obiektowy**. Oznacza to, że po pierwsze, skupia się na procesie inicjalizacji obiektów, a po drugie, bazuje na powiązaniu klas poprzez kompozycje (mimo, że dziedziczenie także występuje).

Statyczny budowniczy nie jest oficjalnym wzorcem projektowym, jest podejściem do tworzenia budowniczego korzystając przy tym z płynnego interfejsu. Jest to bardziej metoda, niżeli przepis na daną architekturę. Przez to, **bardzo trudno formalnie opisać jak powinien wyglądać**, a co ważniejsze **jego implementacja silnie zależy od języka**.

Oto przykładowy kod:

	// budowniczy
	class CarBuilder
	{
	    EngineFactory engineFactory = new EngineFactory();
	    Car car = new Car();
	    
	    public CarBuilder AddEngine()
	    {
	        if (car.Engine != null)
	            throw new InvalidOperationException("Engine already added!");
	        
	        car.Engine = engineFactory.CreateEngine("120HP");
	        return this;
	    }
	    
	    public CarBuilder AddAddon(string addon)
	    {
	        car.Addons.Add(addon);
	        return this;
	    }
	    
	    public Car Build()
	    {
	        if (car.Engine == null)
	            throw new InvalidOperationException("Car must have an engine!");
	        
	        return car;
	    }
	}
	
	static void Main(string[] args)
	{
	    Car car = new CarBuilder()
	        .AddEngine()
	        .AddAddon("CD radio")
	        .AddAddon("Navigation")
	        .Build();
	}

Jak widzisz kod jest naprawdę przejrzysty. Warto zwrócić uwagę na to, że **budowniczy posiada logikę walidującą poszczególne etapy inicjalizacji** modelu wewnątrz obiektu budującego. Model jest więc w dalszym ciągu jest pozbawiony zbędnej logiki.

W różnych językach podejście do statycznego budowniczego może być inne. Ostatnio tworząc budowniczego na platformie Angular2 stworzyłem dwie osobne klasy, gdzie jedna była wstrzykiwalnym serwisem zarządzanym przez mechanizm DI Angulara, a druga była budowniczym. Musiałem tak zrobić, **ponieważ mój budowniczy w znacznej mierze miał po prostu uprościć konstruktor** i zabezpieczyć logikę inicjalizacji obiektu, a tworzenie klas zagnieżdżonych w TypeScript jest zabronione.

Oczywiście, mogłem użyć tzw. funkcji fabrykującej zwracającej nowy kontekst, jednak wybrałem inną metodę: z osobnymi klasami. Zdecydowałem się na to, ponieważ samo nazwanie klasy przedrostkiem *Builder* w pewien sposób dokumentuje kod dla przyszłego programisty. Nikt nie będzie się musiał zastanawiać dlaczego użyłem funkcji fabrykujących, a nazwa *Builder* sama przedstawia intencje twórcy (czyli moje).

## Podsumowanie

Wzorzec budowniczy jest fajny, statyczny budowniczy też. Dobrze jest rozumieć podstawowe różnice między nimi. Poniekąd **są to wzorce podobne, jednak pewne konsekwencje ich stosowania są zupełnie inne**. Budowniczy nie ma poważnych wad, a jedną z największych może być jego nadużywanie tam, gdzie nie jest potrzebny - jednak to dotyczy każdego innego wzorca.

Kod dostępny na github: <https://github.com/p-programowanie/wzorce-projektowe/tree/master/budowniczy>