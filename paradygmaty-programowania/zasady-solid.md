---
title: Zasady SOLID
category: paradygmaty-programowania
thumbnail: zasady-solid.png
published: 2016-11-25T00:00:00Z
---
**Zasady SOLID** to termin jakim zostało nazwane pięć podstawowych zasad, którymi należy się kierować programując obiektowo. Skrót pochodzi od pierwszych liter poszczególnych zasad, są to: *single responsibility*, *open/closed*, *liskov substitution*, *interface segregation* oraz *dependency inversion*. **Zasady SOLID** zostały wymyślone przez znanego amerykańskiego programistę Roberta Martina. Słynie on ze swojego podejścia do czystego kodu, przyczynił się także do rozwoju manifestu zwinnego programowania.

<!--more-->

## Czym są zasady SOLID?

Zasady SOLID to **pięć podstawowych zasad podpowiadających jak pisać dobry kod** zorientowany obiektowo. Zaproponował je słynny Amerykański programista Robert Martin. Jest on także jednym z twórców manifestu zwinnego programowania *agile*. Do napisania artykułu opisującego zasady SOLID skłoniło mnie osobiste doświadczenie. Zauważyłem, że **wielu początkujących programistów często nie rozumie sensu poszczególnych zasad**.

Pytanie o SOLID często pojawia się podczas rozmów kwalifikacyjnych, w szczególności dla programistów z mniejszym stażem (a więc np. studentom). Nieumiejętność lub co gorsze niewiedza na temat niniejszych zasad wiele mówi o poziomie wiedzy danej osoby.

Ponadto, o SOLID można rozmawiać także z programistami doświadczonymi, jednak wtedy należy poruszyć szerszy kontekst i większy problem. Można zapytać przykładowo o to, jakie wzorce projektowe pozwalają na wdrożenie określonej zasady SOLID.

### Single responsibility

**Zasada pojedynczej odpowiedzialności** mówi o tym, aby każda klasa była **odpowiedzialna za jedną konkretną rzecz**. W szczególności powinien istnieć jeden konkretny powód do modyfikacji danej klasy. Stosowanie tej **zasady znacząco zwiększa ilość klas w programie**, a jednocześnie zmniejsza ilość klas typu scyzoryk szwajcarski. Takim mianem określa się wielkie kilkuset linijkowe klasy, skupiające za dużo funkcjonalności.

**Zasada pojedynczej odpowiedzialności** (ang. *single responsibility principle*) - każda klasa powinna być odpowiedzialna za jedną konkretną rzecz.
{: .alert-info} 

Jednym z podstawowych kroków każdej refaktoryzacji jest zawsze wydzielenie mniejszych klas z tych już istniejących. Budowanie dużych klas zawsze wcześniej czy później prowadzi do problemów.

Określenie szwajcarskiego scyzoryka zawsze pojawia się podczas opisywania zasady pojedynczej odpowiedzialności, ponieważ jest to przykład idealny. Powszechnie wiadomo, że jeżeli coś jest do wszystkiego to jest do niczego. W programowaniu to stwierdzenie ma podwójną moc. Oto przykład złej klasy:

	class Person
	{
	    public string Name { get; set; }
	    public string Lastname { get; set; }
	    public string City { get; set; }
	    public string Street { get; set; }
	    public int HouseNumber { get; set; }
	    public string Email { get; set; }
	    
	    public Person(string name, string lastname, string email)
	    {
	        Name = name;
	        Lastname = lastname;
	        Email = ValidateEmail(email);
	    }
	    
	    private string ValidateEmail(string email) 
	    {
	        if (!email.Contains("@") || !email.Contains("."))
	        {
	            throw new FormatException("Email address has a wrong format!");
	        }
	        
	        return email;
	    }
	}

Powyższa klasa zawiera w sobie metodę sprawdzającą poprawność adresu e-mail, a nie powinno leżeć to w obowiązku typu *Person*. Czy są jeszcze błędy? - tak. Klasa *Person* nie powinna zawierać atrybutów, które nie są z nią powiązane. W przyszłości ktoś będzie musiał tę klasę refaktoryzować lub powielać kod chcąc przechować sam adres zameldowania.

Poprawna struktura klasa powinna wyglądać następująco:

	class Address
	{
	    public string City { get; set; }
	    public string Street { get; set; }
	    public int HouseNumber { get; set; }
	}
	
	class Person
	{
	    public string Name { get; set; }
	    public string Lastname { get; set; }
	    public string Email { get; set; }
	    public Address PersonAddress { get; set; }
	    
	    public Person(string name, string lastname, string email)
	    {
	        Name = name;
	        Lastname = lastname;
	        Email = email;
	    }
	}
	
	class EmailValidator
	{
	    public void ValidateEmail(string email)
	    {
	        if (!email.Contains("@") || !email.Contains("."))
	        {
	            throw new FormatException("Email address has a wrong format!");
	        }
	    }
	}

Klasa została rozdrobniona aż na 3 mniejsze klasy. Czy wydaje Ci się, że kod jest poprawny? - jest nieźle, ale dalej można go ulepszyć.

W klasie *EmailValidator* występuje fragment kodu odpowiedzialny za rzucanie wyjątku. Zastanówmy się, czy walidator powinien rzucać wyjątkami? Raczej nie. Wyjątek to coś niespodziewanego, awaria programu. Walidator **powinien spełniać jedną, prostą funkcję** walidacji, bez żadnej dodatkowej logiki. W tym wypadku rzucenie wyjątku powinno zostać wyniesione wyżej:

	class Address
	{
	    public string City { get; set; }
	    public string Street { get; set; }
	    public int HouseNumber { get; set; }
	}
	
	class Person
	{
	    public string Name { get; set; }
	    public string Lastname { get; set; }
	    public string Email { get; set; }
	    public Address PersonAddress { get; set; }
	    
	    public Person(string name, string lastname, string email)
	    {
	        Name = name;
	        Lastname = lastname;
	        Email = email;
	    }
	}
	
	class EmailValidator
	{
	    public void ValidateEmail(string email)
	    {
	        if (!email.Contains("@") || !email.Contains("."))
	        {
	            throw new FormatException("Email address has a wrong format!");
	        }
	    }
	}

Jak widać pozostanie prosta zasada jaką jest pojedyncza odpowiedzialność, potrafi zaskoczyć nawet w prostych przykładach. W praktyce bardzo rzadko programiści rozdzielają swój kod na klasy w wystarczającym stopniu.

Nie miej wrażenia, że zbytnie rozdrabnianie kodu jest złe. Nawet w tak trywialnym przypadku jak klasa *Person*, zaszycie tam walidacji byłoby dużym błędem. Jakie problemy by wystąpiły?:

- aby zwalidować adres e-mail w innym miejscu programu, musiałby kopiować kod albo użyć klasy *Person*
- aby zwalidować adres e-mail bez rzucania wyjątku, musiałby kopiować kod (napisać nową metodę)
- aby użyć adresu w innej klasie, musiałby kopiować kod zamiast wykorzystać klasę *Address*

### Open/closed

Zasada otwarty/zamknięty powinna być zawsze rozwijana do postaci "**otwarty na rozbudowę, zamknięty na modyfikacje**". Dzięki temu, jest to praktycznie jej cała i kompletna definicja. Jest to bardzo ważna zasada, szczególnie w dużych projektach, nad którymi pracuje wielu programistów.

Każdą klasę powinniśmy pisać tak, aby możliwe było dodawanie nowych funkcjonalności, bez konieczności jej modyfikacji. Modyfikacja jest surowo zabroniona, ponieważ zmiana deklaracji jakiejkolwiek metody może spowodować awarię systemu w innym miejscu. Zasada ta jest szczególnie ważna dla twórców wszelkich wtyczek i bibliotek programistycznych.

**Zasada otwarty/zamknięty** (ang. *open/close principle*) - każda klasa powinna być otwarta na rozbudowę ale zamknięta na modyfikacje.
{: .alert-info} 

Istnieje pewna zależność, im bardziej trzymamy się **zasady pojedynczej odpowiedzialności**, tym bardziej musimy dbać o **zasadę otwarty na rozbudowę, zamknięty na modyfikacje**.

#### Użycie polimorfizmu

Polimorfizm jest jednym z fundamentów programowania obiektowego. Jest także podstawowym mechanizmem, który powoduje, że nasza klasa będzie możliwa na rozbudowę w przyszłości.

Rozważmy przykład:

	class Square
	{
	    public int A { get; set; }
	}
	
	class Rectangle
	{
	    public int A { get; set; }
	    public int B { get; set; }
	}
	
	class Calculator
	{
	    public int Area(object shape)
	    {
	        if (shape is Square)
	        {
	            Square square = (Square)shape;
	            return square.A * square.A;
	        }
	        else if (shape is Rectangle)
	        {
	            Rectangle rectangle = (Rectangle)shape;
	            return rectangle.A * rectangle.B;
	        }
	        
	        return 0;
	    }
	}

W powyższym przykładzie dodanie jakiejkolwiek nowej figury, wiąże się z koniecznością modyfikacji istniejącej klasy. Jest to ewidentne złamanie zasady otwarty/zamknięty, ponieważ klasa nie jest otwarta na rozbudowę.

Dzięki użyciu polimorfizmu można obarczyć implementacją metody liczącej pole figury każdą klasę reprezentującą figurę. Kod będzie dodatkowo o wiele prostszy.

Rozważmy przykład:

	abstract class Shape
	{
	    public abstract int Area();
	}
	
	class Square : Shape   
	{
	    public int A { get; set; }
	    
	    public override int Area()
	    {
	        return A * A;
	    }
	}
	
	class Rectangle : Shape
	{
	    public int A { get; set; }
	    public int B { get; set; }
	    
	    public override int Area()
	    {
	        return A * B;
	    }
	}
	
	class Calculator
	{
	    public int Area(Shape shape)
	    {
	        return shape.Area();
	    }
	}

Czy powyższy przykład przekonał Cię do konieczności trzymania się zasady otwarty/zamknięty? Być może nie. Aby rozumieć konieczność używania tej zasady, pomyśl o klasie *Calculator* jako klasie znajdującej się w osobnym pliku DLL, który jest udostępniony tysiącom klientów. Drobna poprawka w kodzie zmusza tysiące programistów do pobrania nowej wersji pliku DLL z nowszą wersją metody liczenia pola. Dlatego właśnie **klasa powinna być otwarta na modyfikacje bez konieczności jej edycji**.

#### Użycie dziedziczenia

Wyobraź sobie, że posiadasz prostą metodę *Drive*, która przyjmuje instancję klasy *Car*:

	void Drive(Car car) {
	    ...
	}

Jeżeli w przyszłości będziesz chciał przekazać do tej metody instancję klasy *Motorcycle*, wtedy narodzi się problem. Znowu, trzeba będzie złamać zasadę otwarty/zamknięty i zmienić istniejący kod. Zamiast tego, można użyć dziedziczenia, aby nasza metoda przyjmowała bardziej ogólny typ:

	void Drive(IVehicle vehicle) {
	    ...
	}

Dzięki takiemu mechanizmowi, w przyszłości będzie łatwo rozbudować kod o inny typy pojazdów. Tutaj warto zauważyć, że zastosowany mechanizm **spełnia także zasadę odwrócenia zależności** (opisana niżej w tym artykule).

#### Użycie wzorców projektowych

Aby przestrzegać zasady otwarty/zamknięty najlepiej jest użyć wzorców projektowych. Dzięki nim pośrednio skorzystamy także z dziedziczenia i polimorfizmu, a nasze rozwiązanie będzie zrozumiałe dla innych programistów.

Najlepszymi wzorcami, które mogą okazać się pomocne, są: metoda fabrykująca, budowniczy, metoda szablonowa, strategia i most. Wszystkie z nich pozwalają dopisać dodatkową implementację, bez zmiany klas bazowych.

### Liskov substitution

Zasada podstawienia Liskov jest w moim mniemaniu zasadą, którą najciężej zrozumieć, a **ludzie bardzo często mylą ją z wszelkimi innymi zasadami**. Jej nazwa pochodzi od nazwiska amerykańskiej programistki Barbary Liskov. W skrócie zasada polega na tym, że w miejscu klasy bazowej można zawsze użyć dowolnej klasy pochodnej. Oznacza to, że w całości musi być zachowana zgodność interfejsu i wszystkich metod.

**Zasada podstawienia Liskov** (ang. *liskov substitution principle*) - w miejscu klasy bazowej można użyć dowolnej klasy pochodnej (zgodność wszystkich metod).
{: .alert-info} 

Jeżeli posiadamy klasę bazową *Animal*, z której dziedziczą dwie klasy: *Dog* oraz *Cat*, to jakakolwiek funkcja przyjmująca w parametrze typ *Animal*, powinna obsłużyć także instancję *Dog* oraz *Cat*. Jeżeli potrzebne są sprawdzenia dodatkowych warunków, lub co gorsza rzucenie wyjątku w zależności od typu klasy, to jest to złamanie zasady Liskov.

Spójrzmy na przykład:

	abstract class Animal
	{
	    public string Name { get; set; }
	    public abstract void Run();
	}
	
	class Dog : Animal
	{
	    public override void Run()
	    {
	        Console.WriteLine("Dog runs");
	    }
	}
	
	class Fish : Animal
	{
	    public override void Run()
	    {
	        throw new NotImplementedException("Fish can not run!"); 
	    }
	}
	
	class Program
	{
	    static void Main()
	    {
	        List<Animal> animals = new List<Animal>();
	        
	        animals.Add(new Dog());
	        animals.Add(new Fish());
	        
	        animals.ForEach(o => o.Run());
	    }
	}

W powyższym przykładzie utworzyliśmy abstrakcję *Animal* jednak występuje tutaj **zjawisko źle przemyślanego mechanizmu dziedziczenia**. Ryba jest zwierzęciem, ale  została obarczona implementacją metody *Run()* znajdującej się w klasie bazowej. Ryba jak to ryba, nie może biegać i jest to złamanie zasady podstawienia Liskov. **Dziedziczenie należy zaplanować inaczej**, tak aby każda klasa pochodna mogła wykorzystać funkcje klasy bazowej.

Zasada podstawienia Liskov najczęściej łamana jest w przypadkach:

- kiedy programista źle rozplanował mechanizm dziedziczenia, interfejs polimorficzny jest zbyt ogólny
- zastosowane dziedziczenie bez mechanizmu polimorfizmu (mało efektywne i często prowadzi do złamania Liskov)
- klasy pochodne nadpisują metody klasy bazowej zastępując jej niepasującą logikę

W dobrze zaplanowanym mechanizmie dziedziczenia, klasy pochodne nie powinny nadpisywać metod klas bazowych. Mogą je ewentualnie rozszerzać, wywołując metodę z klasy bazowej (np. poprzez słowo kluczowe *base* będącym wskaźnikiem na klasę bazową). Spójrzmy na przykład:

	class CoffeeMachine
	{
	    public virtual void Brew()
	    {
	        Console.WriteLine("Pour coffee to the cup");
	        Console.WriteLine("Pour water to the cup");
	    }
	}
	
	class CoffeeLatteMachine : CoffeeMachine
	{
	    public override void Brew()
	    {
	        base.Brew();
	        Console.WriteLine("Pour milk to the cup");
	    }
	}
	
	class Program
	{
	    static void Main()
	    {
	        CoffeeMachine coffee;
	        
	        Console.WriteLine("Making normal coffee");
	        coffee = new CoffeeMachine();
	        coffee.Brew();
	        
	        Console.WriteLine("Making latte coffee");
	        coffee = new CoffeeLatteMachine();
	        coffee.Brew();
	    }
	}

Powyższy przykład idealnie przestrzega metode podstawienia Liskov. Nie dość że **obiekt klasy pochodnej można użyć w miejscu klasy bazowej**, to na dodatek mimo użycia polimorfizmu nie nadpisujemy metod klasy bazowej, tylko z nich korzystamy.

Należy się także wystrzegać wszelkich instrukcji warunkowych sprawdzających typ pochodny klasy przed wywołaniem danej funkcji. Przykładowo:

	static void Main()
	{
	    List<Vehicle> vehicleList = new List<Vehicle>();
	    
	    vehicleList.Add(new Car());
	    vehicleList.Add(new Bike());
	    vehicleList.Add(new Boat());
	    
	    foreach (Vehicle obj in vehicleList)
	    {
	        if (obj is Boat)
	            break; // boat does not have wheels 
	        
	        obj.ChangeTires();
	    }
	}

Ten kod także jest błędny, złe dziedziczenie powoduje konieczność dodawania dodatkowej logiki sprawdzającej typ pochodny, ponieważ nie wszystkie są w 100% możliwe do podstawienia pod typ bazowy.

### Interface segregation

Zasada segregacji interfejsów jest bardzo prosta, mówi aby nie tworzyć interfejsów z metodami, których nie używa klasa. Interfejsy powinny być konkretne i jak najmniejsze.

**Zasada segregacji interfejsów** (ang. *interface segregation principle*) interfejsy powinny być konkretne i jak najmniejsze.
{: .alert-info}

Do tworzenia typu bazowego przeważnie **lepiej użyć klasy abstrakcyjnej**. Może ona opisywać konkretny typ, zawierać odpowiednie atrybuty oraz metody, którymi następnie obarcza wszystkie klasy pochodne. Klasa bazowa definiuje model biznesowy, który potrzebujemy.

Interfejs natomiast jest bezstanowy, nie powinien definiować modelu biznesowego. Interfejs powinien zapewniać kontrakt, informujący programistę o zachowaniach danego typu. Przykładowy kod:

	interface IRaportable
	{
	    void PrintPdf();
	    void PrintExcel();
	}
	
	class SalaryRaport : IRaportable
	{
	    public void PrintPdf()
	    {
	        // print pdf
	    }
	    
	    public void PrintExcel()
	    {
	        // print excel
	    }
	}
	
	class HighSchoolExam : IRaportable
	{
	    public void PrintPdf()
	    {
	        // print Pdf here
	    }
	    
	    public void PrintExcel()
	    {
	        throw new NotImplementedException();
	    }
	}

Kod jest błędny, ponieważ nie każda metoda definiowana przez interfejs jest wykorzystana w klasach pochodnych. Zamiast głównego interfejsu *IRaportable* można utworzyć wiele mniejszych interfejsów. Przykładowy kod:

	interface IRepository<T>
	{
	    IEnumerable<T> GetAll();
	    T GetById(int id);
	}
	
	class BookRepository: IRepository<Book>
	{
	    public IEnumerable<Book> GetAll() { ... }
	    public Book GetById(int id) { ... }
	}
	
	class BookController {
	    private IRepository<Book> bookRepository;
	    
	    public BookController(IRepository<Book> bookRepository) 
	    {
	        this.bookRepository = bookRepository;
	    }
	}

Dzięki podzieleniu interfejsu na mniejsze, utrzymujemy porządek w interfejsie polimorficznym typu. Dzięki temu typy pochodne nie są związane kontraktami, które nie są im potrzebne.

Łamanie zasady segregacji interfejsów prowadzi do niemiłych sytuacji, kiedy iterując po liście typów bazowych ze wspólnym interfejsem polimorficznym rzucony zostaje wyjątek, ponieważ **któraś z klas nie implementuje metody** rozbudowanego interfejsu.

Jeżeli rozejrzysz się po interfejsach wbudowanych w język C#, to zobaczysz, że **większość z nich implementuję jedną lub dwie metody**. Rzadko daje się spotkać większą ilość. Przykłady:

- *IEquatable* - 1 metoda
- *ICloneable* - 1 metoda
- *IComparable* - 1 metoda
- *IEnumerable* - 1 metoda
- *IIterator* - 2 metody

Jest to piękny przykład zastosowania zasady segregacji interfejsów.

### Dependency inversion

Zasada odwrócenia zależności jest prostą i bardzo ważną zasadą. Polega ona na używaniu interfejsu polimorficznego wszędzie tam gdzie jest to możliwe, szczególnie w parametrach funkcji.

**Zasada odwrócenia zależności** (ang. *dependency inversion principle*) - wszystkie zależności powinny w jak największym stopniu zależeć od abstrakcji a nie od konkretnego typu.
{: .alert-info} 

Jeżeli mamy parametr funkcji, który przyjmuje figurę matematyczną, znaczenie lepszym rozwiązaniem będzie przyjęcie interfejsu lub klasy abstrakcyjnej figur matematycznych niż konkretnej figury.  Dzięki temu, nie uzależniamy pojedynczej metody od konkretnego typu, tylko od interfejsu, który mogą implementować duże grupy podtypów. Przykładowy kod jest załączony w tym artykule kilka akapitów wyżej i dotyczy *IVehicle* oraz *Car*.

Rozważmy inny przykładowy kod:

	class BookController 
	{
	    private BookRepository bookRepository;
	    
	    public BookController(BookRepository bookRepository) 
	    {
	        this.bookRepository = bookRepository;
	    }
	}

Powyższy kod jest błędny, ponieważ nasz kontroler jest związany ze zbyt specyficznym obiektem *BookRepository*. O wiele lepiej wprowadzić dodatkowy typ abstrakcyjny, aby bazować na czymś bardziej ogólnym. Dzięki takiemu zabiegowi **zmniejszamy powiązanie pomiędzy klasami** (ang. *tight coupling*) co jest głównym celem stosowania tej zasady.

Poprawiony kod może wyglądać tak:

	interface IRepository<T>
	{
	    IEnumerable<T> GetAll();
	    T GetById(int id);
	}
	
	class BookRepository: IRepository<Book>
	{
	    public IEnumerable<Book> GetAll() { ... }
	    public Book GetById(int id) { ... }
	}
	
	class BookController 
	{
	    private IRepository<Book> bookRepository;
	    
	    public BookController(IRepository<Book> bookRepository) 
	    {
	        this.bookRepository = bookRepository;
	    }
	}

#### Wstrzykiwanie zależności

Warto dodać, że często stosowany jest wzorzec wstrzykiwania zależności (ang. *dependency injection*). Dzięki temu wzorcowi, możemy **automatycznie wstrzykiwać instancje klas przez konstruktor**, zamiast tworzyć te instancje rozkazem *new*. Taką instancję za pomocą DI można byłoby wstrzyknąć w powyższym kodzie do klasy *BookController*.

Dzięki takiemu automatycznemu wstrzyknięciu klasa *BookController* nie wie nic o klasie *BookRepository*. Będzie na niej niejawnie operować, jednak nie bezpośrednio, a pośrednio poprzez interfejs *IRepository<T>*.

Najważniejszą rzeczą jaką musisz zapamiętać jest to, że zasada odwrócenia zależności pomaga nam w  większym stopniu pracować na abstrakcji. Natomiast wstrzykiwanie zależności do wzorzec projektowy, który możemy dodatkowo użyć, aby jeszcze bardziej zmniejszyć powiązania między klasami.

## Dlaczego warto przestrzegać zasad SOLID?

Zasady SOLID są niezłą bazą **dla każdego początkującego programisty**. W dużych projektach nie zawsze wszystkie zasady da się idealnie przestrzegać, jednak **powinniśmy dążyć do poprawy jakości kodu** i wdrażania zasad SOLID jeżeli tylko jest to możliwe. Zły kod w większości przypadków łamie kilka zasad SOLID jednocześnie.

Jeżeli po refaktoryzacji okaże się, że kod łamie już tylko jedną zasadę lub wcale, jest to ogromny sukces. Nawet jeżeli teraz nie dostrzeżesz tego sukcesu, dostrzeże go zapewne ktoś, kto będzie pracował na tym kodzie za kilka miesięcy lub lat.

Pisząc kod osobiście w większości przypadków wszystko się rozumie, **nawet gdyby kod był złej jakości**. Każdy, po prostu, rozumie to co sam napisał. Prawdziwy problem pojawia się, gdy obca osoba jest zmuszona przesiąść się do nieswojego projektu, i pisać w kodzie, którego nigdy wcześniej nie widziała.

Wtedy bardzo pomaga, to że:

- zamiast jednej klasy zawierającej 2000 linii kodu jest 20 małych klas, z której każda jest odpowiedzialna za jedną, małą, konkretną rzecz (*zasada pojedynczej odpowiedzialności*)
- autor klasy przewidział poszerzenie funkcjonalności jego klasy bez konieczności przerabiania jej kodu np. poprzez mechanizm dziedziczenia i polimorfizmu (*zasada otwarty/zamknięty*)
- korzystając z klas pochodnych mamy pewność, że implementują one wszystkie metody klas bazowych i nie musimy tego sprawdzać (*zasada liskov substitution*)
- system zbudowany jest z małych interfejsów (często tylko z jedną metodą), dzięki czemu jesteśmy w stanie zaimplementować w nowo dopisanej przez nas klasie 2 interfejsy których potrzebujemy i ani jednego więcej, bez niepotrzebnych metod (*interface segregation*)
- poprzedni programista używał typów abstrakcyjnych tam gdzie to tylko możliwe (np. w parametrach funkcji) więc możemy przekazać do funkcji każdą kolekcję implementującą interfejs *IEnumerable* a nie tylko listę. A interfejsy znajdujące się w projekcie mają sens i mogą zostać użyte, a nie są tylko sztuką dla sztuki, bo po co nam interfejsy, jeżeli wszystkie funkcje przyjmują jako parametry typy pochodne (*dependency inversion*)

Mam nadzieje, że po przeczytaniu tego artykułu zrozumienie zasad SOLID będzie dla Ciebie łatwiejsze. Jest to pierwszy krok na drodze do pisania czystszego kodu.