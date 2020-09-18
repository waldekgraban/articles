---
title: Resolver routingu i animacja wczytywania danych
category: angular
thumbnail: resolver-routingu.png
published: 2018-09-13T00:00:00Z
---
W Angularze istnieje kilka sposobów na wczytanie danych do komponentu. Jednym z mało znanych i **rzadziej stosowanych mechanizmów jest tzw. resolver**. Tego wyrazu, w moim odczuciu, nie da się przetłumaczyć sensownie na język polski, dlatego taką nazwą będę posługiwać się w całym artykule. Z tego wpisu dowiesz się jakie wady i zalety ma stosowanie resolvera, jakie są alternatywy, oraz jak w prosty sposób zaimplementować animację wczytywania danych.

<!--more-->

### Asynchroniczne doczytywanie danych

Aplikacje Angularowe wyposażone są we własny mechanizm routingów, **czyli mechanizm wiązania poszczególnych komponentów aplikacji z adresami internetowymi**. Podczas załadowania komponentu bardzo często istnieje konieczność doczytania danych pochodzących z zewnętrznych źródeł . Może to być np. profil użytkownika lub słowniki użyte w formularzu.

Aplikacje projektowane w Angularze to tzw. strony SPA (ang. *single page application*). Cechują się one m.in. dynamicznym doczytywaniem danych na żądanie. Oznacza to, że żądania wysyłane do serwera API są typu XHR (ang. *Asynchronous Javascript and XML*). Dawniej do obsługi tych żądań należało użyć technologii AJAX. Platforma programistyczna Angular dostarcza programistom gotowe serwisy, które znaczenie upraszczają doczytywanie danych z API. Jest to m.in. serwis *$http* oraz (od wersji Angulara >=4.3.0) jego nowszy odpowiednik *$httpClient*. Są one typowymi fasadami nałożonymi na technologię AJAX.

### Doczytanie danych do komponentu

Podczas ładowania komponentu powiązanego z danym routingiem mamy dwie opcje doczytania danych. Pierwszą jest **użycie resolvera**, a drugą bezpośrednie **odpytanie serwisu danych** np. w konstruktorze lub metodzie *OnInit*. Użycia serwisów *$http* lub *$httpClient* w klasie komponentu w ogóle nie bierzemy pod uwagę, ponieważ warstwa źródła danych powinna być niezależna od logiki biznesowej.

#### Utworzenie serwisu danych

Niezależnie jaką opcję wybierzesz, niezbędny jest serwis zwracający dane. W większości przypadków będzie on **odpytywał backend lub jakiekolwiek inne API** o dane, które następnie przekaże do komponentu. Jeżeli rozważamy architekturę Angulara jako wzorzec MVVM (ang. *model view view-model*) wtedy serwisy należy postrzegać jako warstwę modelu. Powinna ona spełniać następujące cechy:

- może pobierać dane i nimi manipulować
- nie może nic wiedzieć o widoku
- powinna hermetyzować logikę aplikacji (ale już niekoniecznie logikę biznesową)

Dlatego też, serwisy Angularowe powinny pobierać dane, przetwarzać je i zwracać do innych warstw aplikacji bez jakiejkolwiek ingerencji w widok. Na potrzeby tego artykułu utworzę prosty moduł symulujący pobieranie danych z zewnętrznego API:

	@Injectable({
	    providedIn: 'root',
	})
	export class BookService {
	    private booksList = [
	        { id: 1, name: "Harry Potta" },
	        { id: 2, name: "C# secrets" }
	    ];
	    
	    getAll(): Observable<Book[]> {
	        return of(this.booksList).pipe(
	            delay(800)
	        );
	    }
	}

Za pomocą operatorów *of* oraz *delay* można w wygodny sposób zasymulować zwrócenie statycznych danych w postaci strumienia z pewnych opóźnieniem - zupełnie tak jak zachowuje się odpytywanie zewnętrznego API.

#### Odpytanie serwisu danych

Najbardziej popularną metodą na wczytanie danych jest odpytanie serwisu zwracającego dane **bezpośrednio w konstruktorze** danego komponentu. Jest to rozwiązanie proste i często stosowane. Przykładowa implementacja może wyglądać następująco:

	@Component({
	    selector: "app-root",
	    templateUrl: "./app.component.html",
	})
	export class AppComponent implements OnInit {
	    private bookList: Book[];
	    
	    constructor(private bookService: BookService) { }
	    
	    ngOnInit(): void {
	        this.bookService.getAll().subscribe((result) => {
	            this.bookList = result;
	        });
	    }
	}

Kod jest stosunkowo prosty, w konstruktorze klasy wstrzykujemy instancję serwisu, następnie w metodzie *ngOnInit* ładujemy dane subskrybując się do strumienia danych zwracanego przez metodę *getAll()*.

Warto wspomnieć, że **wczytanie danych w konstruktorze komponentu byłoby błędem**, ponieważ konstruktor powinien być wykorzystywany tylko do pozyskiwania instancji serwisów z kontenera IoC (ang. *inversion of control*) Angulara. Nie służy on absolutnie do manipulacji danymi. Wczytanie lub manipulowanie danymi musi odbywać się w cyklu życia aplikacji Angularowej czyli np. w zdarzeniu *ngOnInit*. Konstruktor nie jest elementem platformy Angular tylko elementem języka TypeScript, nie możemy w nim np. uzyskać uchwytu do danych wejściowych i wyjściowych komponentu (*@Input* oraz *@Output*).

Co jest nie tak z powyższym kodem? Metoda *getAll()* jest asynchroniczna, oznacza to, że **zwróci dane w pewnym momencie w przyszłości**, jednak na pewno nie w chwili, w której zostanie wykonana. Być może strumień danych zostanie zwrócony za 10ms lub kilka sekund, lecz na pewno wystąpi jakieś opóźnienie.

Ponieważ, nie wiemy kiedy metoda zostanie wykonana, nie możemy w sposób bezpieczny używać zmiennej *bookList* w szablonie komponentu. Zmienna **została zadeklarowana jednak nie została zainicjalizowana**. Próba jakiejkolwiek manipulacji na zmiennej w szablonie spowoduje natychmiastowy błąd aplikacji. Jak się przed tym bronić? Mamy trzy wyjścia:

- zainicjalizowanie zmiennej aby była przykładowo pustą tablicą (sama deklaracja powoduje, że ma ona wartość *undefined*)
- otoczenie użycia zmiennej w dyrektywy *ngIf="bookList"*
- użycie operatora bezpiecznej nawigacji *?.*

Wszystkie powyższe rozwiązania działają, ale są złe. Poniżej opiszę dlaczego.

#### Konsekwencje pobierania danych w komponentach

Próba wyświetlenia długości zmiennej lub iterowania po niej spowoduje błąd aplikacji. Wystarczy jedna prosta linijka *{{bookList.length}}* a konsola zapełni się czerwonymi błędami. Dzieje się tak ponieważ szablon komponentu zostanie wyrenderowany szybciej niż zdąży się wykonać asynchroniczna metoda doczytująca dane.

Co prawda zainicjalizowanie zmiennej pustą tablicą w pełni zabezpieczy aplikację, jednak spowoduje &#8222;mryganie&#8221; danych w szablonie komponentu. Użytkownikowi przed ułamek sekundy ukaże się napis &#8222;0&#8221; a następnie &#8222;1&#8221; symbolizujący długość tablicy. **W przypadku próby iterowania po zmiennej efekt będzie jeszcze gorszy**, ponieważ na początku strona będzie pusta a po jakimś czasie zostanie wyrenderowana jej treść. W przypadku skomplikowanego szablonu i wolniejszego komputera wszystkie obiekty będą przez chwilę skakać.

Otaczanie całych stron instrukcjami warunkowymi **ngIf* jest niewydajne i samo w sobie powinno wywoływać u Ciebie niepokój. Ta dyrektywa nie została stworzona po to, aby obsługiwać nią moment doczytania danych. Poza tym, dalej pozostaje problem mrygającej strony.

Użycie operatora bezpiecznej nawigacji *?.* wydaje się **najbardziej sensowne**. Jego użycie polega na tym, aby **odwoływać się nim do wszelkich atrybutów zamiast użycia standardowej kropki**. Przykładowo zamiast napisać *{{bookList.length}}*, co spowoduje wyrzucenie wyjątku gdy zmienna jest niezdefiniowana, można napisać *{{bookList?.length}}*. Atrybut zostanie doczytany w momencie kiedy tylko będzie to możliwe. Skutkiem używania tego operatora często są konstrukcje w stylu *{{person?.attributes?.weight}}*. Fajnie, że dodali taki operator, jednak z mojego doświadczenia wynika, że **w bardziej zaawansowanych aplikacjach są z nim problemy**. Nigdy nie wiemy co i kiedy jest puste, co jest niezdefiniowane. Aplikacja nie zgłasza błędów, a jednak coś gdzieś się nie wyświetla. Na dodatek problem mrygania strony dalej pozostaje nierozwiązany.

Na szczęście, są rozwiązania znacznie bardziej eleganckie.

### Resolver jako jedyne słuszne rozwiązanie

Resolver jest mechanizmem umożliwiającym **doczytanie danych do komponentu, zanim ten zostanie załadowany**. Można w nim wykonać kod asynchroniczny lub zwrócić dane statyczne. W celu utworzenie resolvera wystarczy utworzyć klasę, zaimplementować generyczny interfejs *Resolve\<T>* oraz udekorować ją dekoratorem *@Injectable()*. Aby resolver działał należy połączyć go z danym komponentem w definicjach routingu.

W poprzednim akapicie opisałem wiele problemów związanych z doczytywaniem danych. Jeżeli te argumenty Cię nie przekonały przeanalizujemy zalety jakie niesie ze sobą używanie resolverów. Przede wszystkim **tworzą one dodatkową, abstrakcyjną warstwę pomiędzy komponentem a routingiem**. Dzięki nim, logika odpowiedzialna za dostarczenie danych nie znajduje się bezpośrednio ani w definicji routingu, ani w definicji komponentu. Co za tym idzie?:

- **komponent jest bardziej bezstanowy**, być może w innym miejscu aplikacji re-użyjesz go i prześlesz dane w inny sposób (np. za pomocą *@Input*). Bez resolvera - obarczając komponent logiką ładowania danych - będziesz musiał używać instrukcji warunkowych aby tę logikę ominąć.
- do każdego komponentu może być podpięta dowolna ilość resolverów, dopóki wszystkie nie zostaną rozwiązane komponent nie załaduje się. Dzięki temu nie trzeba otaczać ciała komponentu instrukcjami warunkowymi ani używać operatora bezpiecznej nawigacji.
- **resolvery można reużywać w wielu innych komponentach** i jest to zabieg bardzo często stosowany. Jeżeli doczytujesz jakieś słowniki w komponencie tworzenia dokumentu, prawdopodobnie będziesz ich potrzebował podczas wyświetlania i edycji.
- pozwalają zaimplementować animację wczytywania danych w bardzo prosty sposób (np. animowany spinner lub loading-bar) - jest to nieodłączony element każdej strony SPA.
- jeżeli chcesz pokryć aplikację testami, łatwiej napisać test do resolvera i do komponentu osobno, niż do klasy scyzoryka-szwajcarskiego, która wczytuje i przetwarza dane

W prosty sposób można utworzyć resolver obsługujący serwis utworzony w poprzednim akapicie:

	@Injectable({
	    providedIn: 'root',
	})
	export class BooksResolver implements Resolve<Book[]> {
	    constructor(private bookService: BookService) { }
	    
	    resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<Book[]> {
	        return this.bookService.getAll();
	    }
	}

Budowa resolvera jest bardzo prosta. Składa się z jednej metody zwracającej dane, które zostaną później przekazane do komponentu. Dane statyczne zostają przekazane natychmiast, dane asynchroniczne (obserwator) zostają najpierw rozwiązane a później przekazane do komponentu.

W powyższym przypadku zwracany jest obserwator, jednak dzięki operatorowi unii w deklaracji interfejsu, metoda może zwrócić jedną z trzech wartości: *Observable\<T> | Promise\<T> | T*.

#### Związanie resolvera z komponentem

Związanie resolvera z komponentem odbywa się poprzez routing zdefiniowany w *RouterModule*. Przykładowe związanie może wyglądać nastęująco:

	imports: [
	    BrowserModule,
	    RouterModule.forRoot([
	        {
	            path: 'books',
	            component: BooksList,
	            resolve: {
	                books: BooksResolver
	            }
	        }
	    ])
	],

Jest to fragment kodu wyciągnięty z głównego modułu aplikacji (*app.module.ts*). Mówi on, że dla routingu */books* powinien zostać załadowany komponent *BooksList*oraz powinien zadziałać resolver *BooksResolver*. Dane z resolvera będą dostępne pod atrybutem *books*.

Jak odebrać dane z resolvera? Należy użyć *ActivatedRoute*:
	
	@Component({
	    selector: "books-list-component",
	    templateUrl: "./books-list.component.html",
	})
	export class BooksList implements OnInit {
	    private booksList: Book[];
	    
	    constructor(private activatedRoute: ActivatedRoute) { }
	    
	    ngOnInit(): void {
	        this.booksList = this.activatedRoute.snapshot.data.books;
	    }
	}

W tym momencie lista książek jest wypełniona wartościami zwróconymi przez serwis. Komponent nie uruchomi się do czasu rozwiązania wszystkich resolverów. Wszystkie **resolvery wykonują się jednocześnie (niesekwencyjnie)**, nie istnieje żadna kolejność.

Doczytywanie danych w ten sposób, pozwala w bardzo prosty sposób wdrożyć animację wczytywania danych charakterystyczną dla stron SPA.

### Animacja wczytywania danych

Jeżeli korzystasz w aplikacji z resolverów, w bardzo prosty sposób możesz zaimplementować serwis odpowiedzialny za animację wczytywania danych. Założenia są bardzo proste:

- animacja to zwykły komponent (np. *spinner.component.ts*)  wyświetlany na całej szerokości ekranu (przykrywający całą zawartość strony). Można to łatwo osiągnąć za pomocą CSS
- aby ręcznie nie sterować zachowaniem komponentu ani nie powielać jego występowania najlepiej uzależnić go od dodatkowego serwisu (np. *spinner.service.ts*)
- aby zautomatyzować pokazywanie/ukrywanie komponentu ładowania należy wpiąć się w zdarzenia emitowane przez *Router*

Zacznijmy od serwisu. Serwis sterujący animacją musi mieć dostęp do serwisu *Router* - więc wstrzykujemy do w konstruktorze. **Następnie należy zasubskrybować się do jego zdarzeń i względem nich sterować odpowiednią flagą**. Zdarzenia, które nas interesują to:

- *NavigationStart* - w tym momencie pokazujemy animację wczytywania
- *NavigationEnd*, *NavigationCancel*, *NavigationError* - w tym momencie ukrywamy animację wczytywania

Pojawia się ostatnie pytanie: jak powiadomić komponenty z poza serwisu, że zmienia się w nim wartość flagi (np. *isLoading*)? W czystym JavaScripcie musielibyśmy użyć funkcji zwrotnej (ang. *callback*). W Angularze oczywiście lepiej skorzystać z dobrodziejstw RxJs oraz klasy *Subject*. Subject tak jak *EventEmitter* pozwala emitować wartości do wszystkich nasłuchujących subskrybentów. Przykładowy kod:

	@Injectable()
	export class SpinnerService {
	    private _isLoading$ = new BehaviorSubject<boolean>(false);
	    
	    get isLoading() {
	        return this._isLoading$.asObservable();
	    }
	    
	    set loading(value: boolean) {
	        this._isLoading$.next(value);
	    }
	    
	    constructor(private router: Router) {
	    		this.eventHandler(this.router.events);
	    }
	    
	    private eventHandler(events: Observable<Event>) {
	        events.subscribe((event) => {
	            if (event instanceof NavigationStart) {
	                this._isLoading$.next(true);
	            } else if (
	                event instanceof NavigationEnd ||
	                event instanceof NavigationCancel ||
	                event instanceof NavigationError
	            ) {
	                this._isLoading$.next(false);
	            }
	        });
	    }
	}

Przeanalizujmy powyższy kod. Po pierwszy zamiast klasy *Subject* użyłem klasy *BehaviourSubject*. Niczym się one nie różnią **oprócz tego, że ten drugi można zainicjować domyślną wartością**. Po drugie *Subject* i wszelkie jego odmiany muszą być prywatne, aby ktoś z zewnątrz nie mógł ich zepsuć, zresetować i obsłużyć w zły sposób. Z tego powodu wartość *Subjecta* pobieramy za pomocą właściwości i zwracamy jako obserwator.

W konstruktorze serwisu przesyłamy zdarzenia *Routera* do funkcji *eventHandler*. Logika obsługi zdarzeń powinna być wydelegowana do innej metody a nie upchana w konstruktorze. Ostatnią kwestią jest dodanie settera umożliwiającego ręcznie sterować stanem animacji ładowania. Zapytasz po co? Mechanizm jest dzięki temu bardziej reużywalny. Oprócz automatycznego działania z resolverami można go uruchamiać ręcznie np. w metodzie komponentu przy asynchronicznych operacjach (np. wykonanie zapytania z zapisem danych do back-endu).

Obsłużenie animacji jest bardzo proste, wystarczy utworzyć dowolny komponent, wystylizować go odpowiednie za pomocą CSS, a następnie sterować jego widocznością w zależności od stanu serwisu *SpinnerService*.

	<div class="spinner" [ngClass]="{'hidden': !(spinnerService.isLoading | async)}">
	    <i class="fas fa-spin fa-spinner"></i>
	</div>

To właściwie tyle jeżeli chodzi o automatyczną animację wczytywania danych. Aby całość działała należy jeszcze tylko umieścić komponent animacji gdzieś w głównym pliku szablonu naszej aplikacji *<spinner></spinner>*. Serwis zostanie do niego wstrzyknięty automatycznie, na komponent odpowiedzialny za animację zostanie automatycznie nakładana klasa *.hidden* w zależności od stanu serwisu. Stan będzie zmieniał się automatycznie w zależności od zdarzeń *Routera* lub manualnej ustawienia settera przez programistę.

Przykładowe działanie mechanizmu:

{% include parts/postPicture.html page=page img="spinner" ext="gif" %}
