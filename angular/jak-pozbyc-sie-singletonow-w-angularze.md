---
title: Jak pozbyć się singletonów w Angularze?
category: angular
thumbnail: angular-singleton.png
published: 2018-03-28T00:00:00Z
---
Praktycznie **każdy serwis w Angularze jest domyślnie singletonem**. Od tej reguły istnieją pewne wyjątki jednak tylko w ściśle określonych przypadkach. Niestety, nie zawsze chcemy aby serwis, którego używamy, zachowywał się jak singleton. W moim przekonaniu Angular nie dostarcza prostego, intuicyjnego mechanizmu **aby programista mógł wybrać, jakiego rodzaju serwisu potrzebuje**. W tym artykule podzielę się z Tobą kilkoma sposobami, których używam aby osiągnąć cel - czyli instancyjność serwisów.

<!--more-->

##  Wstrzykiwanie zależności w Angularze

Angular jest wyposażony we **własny mechanizm wstrzykiwania zależności** i chwała mu za to. Jest to jedna z cech tego frameworka, za którą go pokochałem. Programując przykładowo w ASP.NET MVC bądź Node.JS programista sam musi zadbać o jakiś kontener IoC (ang. *inversion of control*). W .NET bardzo prosty i popularny jest Ninject, w JS można użyć przykładowo InversifyJS. Użyć, czyli w tym wypadku dodać do projektu, i odpowiednie skonfigurować. W bardziej zaawansowanych przypadkach **może to sprawić problemy początkującym programistom**, szczególnie, jeśli chcemy do mechanizmu DI (ang. *dependency injection*) dodać obsługę połączenia z bazą danych (np. wzorzec *Unit of work*). Pojawiają się wtedy problemy z transakcjami, zakresami transakcji, pulą połączeń itd.

Całe szczęście, zarówno AngularJS jak i Angular2+ załatwił sprawę z góry. Wszystkie serwisy są wstrzykiwalne poprzez wbudowany mechanizm DI. W AngularJS każdy serwis był singletonem i nie było na to lekarstwa. Wynika to z faktu, że posiadał on jeden globalny *injector*.

W Angular2+ może występować wiele *injectorów* a cały mechanizm DI jest hierarchiczny. DI w Angular2+ to **naprawdę rozbudowany temat** i na pewno napiszę o tym osobny artykuł. Z tego względu pominę wiele technicznych szczegółów i opiszę tylko jaki jest czas życia serwisów.

### Wstrzykiwalne serwisy

Aby serwis był wstrzykiwalny należy poprzedzić jego definicję dekoratorem *@Injectable*. Użycie tego dekratora jest sygnałem dla mechanizmu DI, że ma brać ten serwis pod uwagę. Drugim i ostatnim krokiem, jest dodanie serwisu do tablicy *providers* w module, w którym się znajdujemy. To wszystko. Zdefiniowaliśmy wstrzykiwalny serwis.

Aby pobrać instancję serwisu można użyć następujących sposobów:

	// domyślne
	constructor(private customService: CustomService) {
	    this.customService.testMethod();
	}

	// poprzez dekorator @Inject
	constructor(@Inject(CustomService) private customService) {
	    this.customService.testMethod();
	}

	// poprzez instancję injector'a
	constructor(private injector: Injector) {
	    this.customService = Injector.get(CustomService);
	}

W większości przypadków **korzysta się z podejścia domyślnego**. Czasem aby pobrać uchwyty do niestandardowych obiektów, które nie są serwisami (np. uchwyt OpaqueToken lub InjectionToken) trzeba użyć dekoratora. Czasem natomiast aby zapobiec błędowi *cicrular dependency* lub *cyclic dependency* (szczególnie podczas korzystania z *HTTP_INTERCEPTOR* i *HttpClient*) trzeba skorzystać z instancji *injectora*. Nie jest to jednak w tym artykule ważne.

Wszystkie wstrzyknięte w ten sposób (każda z powyższych metod) serwisy, **są singletonami**.

### Jeden injector dla wielu modułów

Komponenty i dyrektywy są zahermetyzowane wewnątrz modułów, w których zostały zadeklarowane, jednak **nie dotyczy to serwisów**. Wynika to z faktu, iż podczas fazy kompilowania wszystkie moduły Angulara są scalane w jeden główny moduł, a wszystkie serwisy lądują w **jednej, wspólnej tablicy dostępnych serwisów**. M.in. dlatego serwisów nie trzeba eksportować (w odróżnieniu do komponentów i dyrektyw) reużywając je pomiędzy modułami. Przez to nie jest też ważne, czy serwis użyjemy w tym samym module, w którym go zdefiniowaliśmy, czy w całkiem innym module aplikacji. **Serwis zawsze będzie dostępny wszędzie i będzie singletonem**.

Dzieje się tak ponieważ, wszystkie scalone moduły Angulara podczas fazy kompilacji **dostają jeden wspólny *injector***. Wiążą się z tym kolejne konsekwencje np. możliwość nadpisywania (przesłaniania) serwisów pomiędzy modułami, jednak to nie temat na ten wpis.

Kiedy próbujemy uzyskać uchwyt do serwisu poprzez konstruktor, odpytujemy *injector* o uchwyt do interesującego na serwisu. Jeśli zrobimy to pierwszy raz, **zostanie niejawnie utworzona jego instancja** (leniwie), a za każdym kolejnym razem **zostanie zwrócona ta sama, poprzednio utworzona instancja**. Pojawia się więcej pytań niż odpowiedzi, który *injector* odpytujemy?

### Hierarchiczne wstrzykiwanie zależności

W Angularze DI jest hierarchiczne, a tak naprawdę hierarchiczne są *injectory*. Podobny mechanizm zapewnia biblioteka InversifyJS, której można użyć w np. w NodeJS. Jeśli posiadamy kilka zdefiniowanych modułów, podczas kompilacji są one scalone w jeden i wyposażone w jeden wspólny *injector*. Konstruktor każdego komponentu i serwisu szuka tego *injectora* hierarchicznie.

{% include parts/postPicture.html page=page img="angular-dependency-injection" %}

Wyobraźmy sobie strukturę jak na powyższym obrazku. Serwis dostarczony w głównym module aplikacji **będzie singletonem w każdym komponencie w aplikacji**, bo cała aplikacja posiada wspólny *injector*. Użycie serwisu *CustomService* w każdym komponencie będzie oznaczało operowanie na tej samej instancji.

Jedynym sposobem na pozbycie się singletona jest utworzenie nowego *injectora*. Można tego dokonać na następujące sposoby:

- moduł, w którym deklarujemy serwis, **doczytać leniwie** (ang. *lazy loading*)
- dodać tablicę *providers* w dekoratorze komponentu,w którym chcemy utworzyć nowy *injector* (przełamać łańcuch DI). Następnie dodać do tablicy interesujący nas serwis.
- utworzyć serwis ręcznie operatorem *new* (bez korzystania z DI Angulara, bez dekoratora *@Injectable*)
- tworzyć ciągle nowe serwisy rozszerzające (ang. *extends*) bazowy serwis (dziedzicząc jego logikę i funkcje)

W poniższych akapitach opisze własne doświadczenia i przemyślenia na temat tworzenia nowych *injectorów*.

## Leniwie doczytany moduł

Angular udostępnia mechanizm **leniwego doczytywania modułów** poprzez odpowiednią konstrukcję routingów. Jakie niesie to ze sobą wady i zalety nie naddaje się do opisania w tym artykule. Ważne jest natomiast to, że każdy doczytany leniwie moduł posiada osobny *injector*. Jest to zrozumiałe, ponieważ w momencie kompilacji aplikacji Angularowej leniwie doczytany moduł fizycznie nie istnieje (nie jest doczytany), więc nie można uwzględnić jego serwisów podczas scalania modułów.

Leniwe doczytywane moduły są więc jednym ze sposobów osiągnięcia instancyjnych serwisów - wręcz narzuconym. Wygląda to następująco:

{% include parts/postPicture.html page=page img="angular-dependency-injection3" %}

W zależności od tego, czy będziemy chcieli użyć *CustomService* czy *CustomService2* mechanizm DI odpyta różne *injectory*. Albo główny, powstały w wyniki procesu kompilacji **poprzez scalenie wszystkich nie-leniwych modułów**, albo drugi utworzony podczas **doczytania leniwego modułu**. Odwołując się w komponencie nr. 5 do serwisu *CustomService* korzystamy z domyślnego *injectora*. Natomiast prosząc o *CustoService2* korzystamy z nowego injectora leniwego modułu.

Aby uwspólnić *injectory* w leniwie doczytywanych modułach można posłużyć się metodą *forRoot()*, która blokuje powstanie dodatkowego *injectora* nawet w przypadku leniwych modułów. Z tej metody korzysta np. *RouterModule* i kilka innych fajnych Angularowych bibliotek, jednak jej opis to temat na osobny wpis.

Leniwe doczytywanie modułów aby osiągnąć instancyjne serwisy? Nie za bardzo. Coś tam się da, jednak nie jest to na pewno zadowalający efekt.

## Redeklaracja serwisu w komponencie

Każdy serwis w Angularze deklarujemy w tablicy *providers* w jakimś module - czy to w głównym czy dodatkowym. Zapewnia to wspólny *injector* i oczywiście jedną singletonową instancję. Redeklaracja serwisu w dekoratorze komponentu powoduje utworzenie nowego *injectora*. Kod wygląda następująco:

	@Component({
	    selector: 'custom-component', 
	    template: 'This is just a test component.',
	    providers: [ CustomService ] // przełamanie łańcucha DI
	})
	class CustomComponent {
	    constructor(private customService: CustomService) {
	        // customService pochodzi z nowego injectora(!)
	    }
	}

Efekt działania powyższego kodu dla komponentu nr. 4 miałby następujący efekt:

{% include parts/postPicture.html page=page img="angular-dependency-injection2" %}

Zarówno komponent nr. 4 jak i wszystkie jemu podległe prosząc o serwis *CustomService* dostałby instancję pochodzącą z nowego *injectora*. Komponentu należące do *injectora* będącego wyższej w hierarchii dziedziczenia (nr. 1, 2 i 3) **dostaną inną instancję** niż komponenty znajdujące się niżej w hierarchii (nr. 4, 5 i 6).

Należy pamiętać, że nowy *injector* przechowuje tylko i wyłączenie konkretny, redeklarowany serwis z tablicy *providers* umieszczonej w dekoratorze komponentu. Reszta serwisów ciągle będzie wczytywana z głównego *injectora*.

Rozwiązanie to jest bardzo skuteczne i ogólnie polecane. Jakie są jego wady? Często korzystając z metody *forRoot()* **decydujemy się dostarczyć do serwisu jakąś dodatkową konfigurację** już na etapie importu (podobnie jak w *RouterModule*). Korzystając z takiego rozwiązania decydujemy się na wielopostaciowość instancji danego serwisu, co może wiązać się z chęcią dostarczenia także unikalnej konfiguracji. Niestety nie za bardzo jest jak ją dostarczyć, chyba że jakąś osobną metodą (np. jakiś setter).

## Ręcznie tworzony serwis

Opiszę moje ostatnie doświadczenie, które generalnie skłoniło mnie do napisania tego wpisu. Pewnego razu w pracy musiałem stworzyć **uniwersalny moduł wyszukiwarki**, który mógłby być użyty w wielu miejscach w projekcie. Był to moduł wyodrębniony do większego, reużywalnego cora.

#### Podejście 1

Początkowo był to zwykły singleton, do którego za pomocą składni metody *forRoot()* przesyłałem odpowiednią konfigurację np.:

	@NgModule({
	    
	    imports: [
	        CoreSearchModule.config({
	            method: HttpMethods.GET,
	            endpoint: '/api/articles',
	            resultsPerPage: 10
	        }),
	    ],
	    
	})
	export class AppModule { }

Rozwiązanie fajne, jednak wstrzykiwanie konfiguracji jest bezsensu. W innym miejscu w projekcie ktoś może chcieć strzelać do innego endpointa. Pierwsza myśl? Umożliwić **przesłanie nowej konfiguracji jako parametr opcjonalny** metody *search()*. Po chwili namysłu stwierdziłem - bezsensu. Serwis jest singletonem, nie mogę nadpisywać jego konfiguracji w rożnych miejscach aplikacji.

#### Podejście 2

Stwierdziłem, że serwis wyszukiwarki nie będzie serwisem *@Injectable*, tylko zwykła klasą TypeScripta. Chcąc stworzyć wyszukiwarkę do artykułów należałoby stworzyć *ArticleSerachService* dziedziczący z kolei z *SearchService*. Serwisy rozszerzające dałoby się już normalnie wstrzykiwać. Fajnie? No fajnie, działałoby. Każdy "pod serwis" miałby osobną instancję w *injectorze*, każdy mógłby mieć inną konfigurację. Jednak to podejście do rozwiązania problemu wydawało mi się bezsensowne. Po co tworzyć **sztuczną warstwę nieprzydanych serwisów**, tylko po to aby wygrać z singletonami Angulara?

#### Podejście 3

Stwierdziłem, że pokonam Angulara jego własną bronią - *injectorami*. Redeklaracja serwisu wyszukiwania w każdym komponencie, gdzie tylko będę chciał użyć serwisu wyszukiwarki. Rozwiązanie początkowe wydawało się fajne, nawet polecane przez dokumentację. Jednak niesie ze sobą kilka problemów:

- brak możliwości dostarczenia konfiguracji
- brak możliwości **2 osobnych instancji w obrębie jednego komponentu**
- totalny syf w kodzie (kto obcy kiedyś będzie wiedział, że w komponencie nr. 28 ktoś przełamał łańcuch DI redeklarując serwis?)

Szybko z tego podejścia zrezygnowałem.

#### Podejście 4

Ostatnia deska ratunku. Przerobiłem **od deski do deski cały mechanizm DI Angulara** w poszukiwaniu dobrego rozwiązania. Przerobiłem wszystkie możliwe operatory i strategie opisywane w różnych miejscach w internecie. Nic ciekawego nie znalazłem. Zrezygnowany stwierdziłem, że **to czas przestać używać DI Angulara**.

Utworzyłem zwykłą klasę - niewstrzykiwalną - pozbawioną dekoratora *@Injectable*. Tworzyłem instancję serwisu operatorem *new* w jakim miejscu chciałem, a konfigurację dostarczałem przez konstruktor. Prawie myślałem, że task jest skończony, ale stwierdziłem - beznadzieja. Nie po to Angular zapewnia mechanizm DI aby teraz robić takie kwiatki. Jak to później testować?

Ostatecznie **satysfakcjonujące rozwiązanie wymyśliłem w ostatniej chwili**. Opiszę je w kolejnym akapicie.

### Funkcja konstruująca - pseudo fabryka

Dość długo myślałem nad rozwiązaniem swojego problemu: dostarczenie elastycznego, reużywalnego, konfigurowalnego serwisu, który byłby gotowy do użycia w każdym miejscu projektu, korzystając przy tym DI Angulara. Zakręciłem się myślami wokół [wzorca budowniczy](/wzorce-projektowe/budowniczy/) oraz [wzorca fabryki](/wzorce-projektowe/fabryka-abstrakcyjna/). Stwierdziłem, że **budowniczy może rozwiązać mój problem**, jednak po chwili facepalm - typescript nie obsługuje klas zagnieżdżonych niezbędnych do stworzenia statycznego budowniczego. Użycie budowniczego w koncepcji GoF (ang. *Gang of Fours*) nie miało w tym wypadku sensu.

Ostatecznie **stworzyłem rozwiązanie przypominające prostą statyczną fabrykę. **Postanowiłem wykorzystać serwis Angularowy w postaci singletona, który za pomocą funkcji konstruującej (ang. *constructor function*) zwróci nową instancję interesującego mnie serwisu, który już nie jest zależy od mechanizmu DI Angulara - dzięki temu, nie jest singletonem. Wykonane kroki:

  1. Dodałem singletonowy serwis *SearchService* wstrzykiwalny przez DI Angulara (z dekoratorem *@Injectable*)
  2. *SearchService* zawiera tylko jedną metodę *createSearch()*, która dodatkowo przyjmuje odpowiednią konfigurację
  3. Powyższa metoda zwraca nową instancję serwisu *SearchEngine*, który nie jest już serwisem obsługiwanym przez DI Angulara
  4. Serwis *SearchEngine* przyjmuje interfejs z konfiguracją i wystawią całą funkcjonalność wyszukiwarki.

Myślę, że takie podejście to bardzo dobre rozwiązanie w przypadku radzenia sobie w wymagających przypadkach Angularowych singletonowych serwisów. Przykładowe użycie mojego rozwiązania wygląda następująco:

	@Component({
	    selector: 'custom-component', 
	    template: 'This is just a test component.'
	})
	class CustomComponent {
	    
	    constructor(private searchService: SearchService) { }
	    
	    searchArticles() {
	        this.searchService().createSearch<Article>({
	            method: HttpMethods.GET,
	            endpoint: '/api/articles',
	            resultsPerPage: 10
	        }).search({
	            surename: 'Kowalski',
	            group: 23
	        }).subscribe((result) => {
	            console.log(result);
	        });
	    }
	}

Oczywiście instancję zwróconą przez metodę *createSearch\<T>()* można także zapisać i reużywać:

	@Component({
	    selector: 'custom-component', 
	    template: 'This is just a test component.'
	})
	class CustomComponent {
	    
	    private articlesSearch: ISearch<Article>;
	    
	    constructor(private searchService: SearchService) {
	        this.articlesSearch = this.searchService().createSearch<Article>({
	            method: HttpMethods.GET,
	            endpoint: '/api/articles',
	            resultsPerPage: 10
	        });
	    }
	    
	    searchArticles() {
	        this.articlesSearch.search({
	            surename: 'Kowalski',
	            group: 23
	        }).subscribe((result) => {
	            console.log(result);
	        });
	    }
	}

Dlaczego zdecydowałem się na taką metodę? Po co mieszać **serwis zarządzany przez DI Angulara z instancyjnym serwisem**? Oprócz listy wniosków , które opisałem w akapitach wyżej pojawia się kolejna bardzo ważna zaleta. Serwis zarządzany przez DI Angulara może dostarczyć naszemu "sub-serwisowi" inne serwisy także zarządzane przez mechanizm DI. Mój serwis do wyszukiwarki korzysta z *HttpClient*, który musi być serwowany przez DI Angulara. Dzięki obudowującemu singletonowi mogę zawsze pobrać instancję tego serwisu:

	@Injectable()
	export class SearchService {
	    
	    constructor(private http: HttpClient) { }
	    
	    /**
	    * @description Factory method which return new instance of `SearchEngine<T>`
	    * @param `searchConfiguration` base search configuration
	    */
	    createSearch<T>(searchConfiguration: SearchConfiguration) {
	        return new SearchEngine<T>(searchConfiguration, this.http);
	    }
	    
	}

Graficzne przedstawienie tej koncepcji wygląda następująco:

{% include parts/postPicture.html page=page img="angular-dependency-injection-singleton" %}

Kilka plusów tego podejścia:

- brak singletonów
- możliwość "wstrzyknięcia" konfiguracji
- możliwość kilku różnych instancji serwisu w jednym komponencie
- całość dobrze opisana JSDoce'm, więc każdy programista bezproblemowo się w tym odnajdzie
- nadal korzystamy z DI Angulara (dla serwisu fabrykującego), co zwiększa skalowalność projektu
- nadal możemy wstrzykiwać i używać serwisy zarządzane przez mechanizm DI Angulara

Metodę *createSearch\<T>()* oczywiście można pominąć, konfigurację wrzucić w konstruktor, a ten dalej zwróciłby nową instancję wyszukiwarki. Ta mała **nadmiarowość z mojej strony miała dać do myślenia programiście kiedyś pracującym na tym kodzie** - aby wiedział, że coś się tam dzieje, że nie jest to tylko zwykłe wywołanie konstruktora z parametrami. Nie jest to standardowe zachowanie serwisów Angularowych, więc lepiej to podkreślić.

Co sądzicie o takim podejściu? Być może ktoś zna **inne, alternatywne rozwiązania** radzenia sobie z niechcianymi singletonami?