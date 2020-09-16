---
title: RxJs - wycieki pamięci
category: angular
thumbnail: rxjs-wycieki-pamieci.png
published: 2020-06-13T00:00:00Z
author: karol-trybulec
---
Wiele projektów Angularowych ma wycieki pamięci, które objawiają się coraz wolniejszym działaniem aplikacji. Z mojego doświadczenia wynika, że najczęściej jest za nie odpowiedzialne niewłaściwe użycie biblioteki RxJs. W tym artykule pokażę jak szukać wycieków pamięci oraz co zrobić, aby do nich nie dopuszczać.

<!--more-->

## Czym jest wyciek pamięci?
Wszystkie zmienne zadeklarowane w języku JavaScript są zapisywane na stercie (ang. *heap*). Jest to obszar pamięci zarządzany przez silnik JavaScripta. Sterta jest okresowo skanowana przez mechanizm GC (ang. *garbage collector*), który dba o zwalnianie nieużywanych obiektów. Mechanizm jest bardzo podobny do tego zaimplementowanego w platformie .NET.

Dzięki istnieniu GC **programista nie musi martwić się o ręczne sprzątanie nieużywanych obiektów** (tak jak było to konieczne w języku C/C++). GC dzięki użyciu algorytmu „oznacz i zamieć” (ang. *mark-and-sweep*), wie które obiekty nie są już używane, i wtedy bezpowrotnie je usuwa.

**Wyciek pamięci** to nieposprzątana pamięć aplikacji. Może doprowadzić do błędów działania aplikacji i spadku wydajności. Może wystąpić praktycznie we wszystkich językach programowania.
{: .alert-info} 

Wyciek pamięci powstaje w momencie, kiedy z jakiegoś powodu, obiekt umieszczony na stercie ciągle posiada referencje do siebie – nawet wtedy, kiedy nie jest już używany. Pamięć zajęta przez przeglądarkę internetową ciągle rośnie, aż po pewnym czasie wszystko zaczyna zacinać się użytkownikowi.

## Wycieki pamięci w Angularze
Aplikacje SPA (ang. *single page application*) są **szczególnie wrażliwe na wycieki pamięci**, ponieważ często użytkownik pracujący na aplikacji nie odświeża jej przez cały dzień. Najbardziej narażone będą więc wszelkiego rodzaju panele administracyjne, aplikacje finansowe czy aplikacje MES.

3 podstawowe powody wycieków pamięci w angularze to:

- **błędy związane z biblioteką RxJs**
- błędy związane z językiem JavaScript
- źle zaimplementowany mechanizm pamięci podręcznej

Błędy związane z RxJs to na pewno największa grupa i główna przyczyna. Strzelałbym, że będzie to 80-90% wszystkich przypadków. Błędy typowe dla języka JavaScript to np. nie zwalnianie uchwytów funkcji *addEventListener*, kiedy nie są już potrzebne.

Przyczyną może też być źle zaimplementowany mechanizm pamięci podręcznej. To tzw. *in-memory cache*. Polega to na zapisywaniu wewnątrz serwisu singletonowego coraz większej ilości informacji. Jeżeli zarówno serwis ani dane nie są okresowo sprzątane, wtedy po wielu godzinach działania aplikacji jest ona przeciążona.

## Jak zapobiegać wyciekom pamięci w RxJs?

Biblioteka RxJs jest potężnym narzędziem, które pomaga nam programować w sposób reaktywny. Jedyną ważną rzeczą, o której należy pamiętać, jest **konieczność odsubskrybowania od każdego strumienia**, gdy nie jest już używany.

Aby się odsubskrybować należy na wszystkich subskrypcjach wywołać metodę *.unsubscribe()*. Są jednak przypadki, kiedy nie jest to konieczne, lub można sobie to zautomatyzować.

## Strumienie skończone i nieskończone

Strumienie w RxJs mogą być skończone lub nieskończone (ang. *finite/infinite*). Kiedy strumień jest skończony wtedy emituje wartość i kończy się. **Zakończenie strumienia powoduje, że każdy subskrybent zostanie automatycznie odsubskrybowany**. Przykładem takiego automatycznie kończącego się strumienia jest *HttpClient*.

	getBooks$() {
		return this.httpClient.get('api/books');
	}

	ngOnInit() {
		this.getBooks$().subscribe(books => {
			this.books = books;
		});
	}
	
W powyższym przykładzie nie jest konieczne ręczne odsubskrybowanie się, ponieważ strumień HttpClient jest skończony. Przykładem innego skończonego strumienia jest *timer*.

Właściwie **wszystkie inne strumienie są nieskończone**. Oznacza to, że będą emitować wartości bez końca. Jeżeli się od nich nie odsubskrybujesz stworzysz wyciek pamięci. Dotyczy to szczególnie strumieni takich jak:

- własny *Subject*, *BehaviorSubject*, *ReplaySubject*
- własny *Observable*
- strumienie klasy *Router*
- strumienie formularzy (*valueChanges*)
- strumienie stworzone ze zdarzeń (*fromEvent*)

Ja profilaktycznie zawsze używam *.unsubscribe()*, jedynym wyjątkiem jest *HttpClient*. Dla bezpieczeństwa polecam się kierować tą zasadą.

### Co powoduje, że strumień jest skończony?

Strumień zostanie zakończony w momencie, kiedy zostanie wywołana na nim metoda *.complete()*. Od tego czasu, niemożliwe staje się emitowanie jakichkolwiek wartości. Gdy zostaje wywołana metoda *.complete()* wtedy wszyscy obserwatorzy zostają automatycznie odsubskrybowani.

## Jak skutecznie odsubskrybowywać się?

Jak ustaliliśmy, praktycznie wszystkie strumienie w Angularze są nieskończone. Oznacza to, że zawsze musisz posprzątać po subskrypcji. Istnieje na to kilka sposobów.

### 1. Metoda unsubscribe()

Podstawowym sposobem na odsubskrybowanie się jest oczywiście wywołanie metody *.unsubscribe()*. Przeważnie robimy to w „destruktorze” komponentu czyli metodzie *ngOnDestroy*:

	export class AppComponent implements OnInit, OnDestroy {
		counter = 0;
		private subscription!: Subscription;

		constructor(private counterService: CounterService) {}

		ngOnInit() {
			this.subscription = this.counterService.getCounter$().subscribe(value => {
				this.counter = value;
			});
		}

		ngOnDestroy() {
			if (!this.subscription.closed) {
				this.subscription.unsubscribe();
			}
		}
	}

Jest to oczywiście mechanizm poprawny.  Warto dodać, że próba ponownego odsubskrybowania subskrypcji spowoduje rzucenie wyjątku, dlatego warto się na taki wypadek ubezpieczyć i sprawdzać wartość atrybutu closed. Czy zawsze mamy panowanie nad tym, czy subskrypcja została już odsubskrybowana? – nie. Ktoś mógł przecież wywołać *.completed()*.

Gdy subskrypcji mamy więcej wtedy robi się mały bałagan. Ja przeważnie **tworzę dedykowaną tablicę subskrypcji** i załatwiam wszystko za jednym zamachem:

	export class AppComponent implements OnInit, OnDestroy {
		couter = 0;
		private subscriptions: Subscription[] = [];
		
		constructor(private counterService: CounterService) {}
		
		ngOnInit() {
			this.subscriptions.push(
				this.counterService.getCounter$().subscribe(value => {
					this.counter = value;
				})
			);

			this.subscriptions.push(...);
			this.subscriptions.push(...);
		}
		
		ngOnDestroy() {
			this.subscriptions.forEach(e => !e.closed ? e.unsubscribe() : null);
		}
	}

### 2. Operator takeUntil()

Bardzo ciekawym mechanizmem jest zastosowanie operatora *.takeUntil()*. Przyjmuje on jako parametr dowolny strumień (czyli instancję *Observable* lub *Subject*). Dzięki jego zastosowaniu nasza subskrypcja będzie istniała tak długo, aż strumień z parametru wyemituje jakąkolwiek wartość.

W praktyce tworzymy sobie nowy *Subject* o nazwie *$destroyed*. Następnie we wszystkich naszych subskrypcjach korzystamy z operatora *takeUntil()*, i przekazujemy mu *$destroyed* jako parametr funkcji. W *ngOnDestroy* komponentu/serwisu emitujemy dowolną wartość strumienia *$destroyed*, czym powodujemy automatyczne odsubskrybowanie wszystkich subskrypcji.

	export class AppComponent implements OnInit, OnDestroy {
		counter = 0;
		private destroyed$ = new Subject();

		constructor(private counterService: CounterService) {}

		ngOnInit() {
			this.counterService.getCounter$()
				.pipe(
					takeUntil(this.destroyed$)
				)
				.subscribe(value => {
					this.counter = value;
				});
		}

		ngOnDestroy() {
			this.destroyed$.next();
		}
	}

### 3. Operator takeWhile()

Nie polecam stosowania tego operatora. Działa podobnie jak *.takeUntil()*, jednak przyjmuje jako parametr funkcję zwrotną (predykat). Funkcja zwrotna przyjmuje dowolną wartość i zwraca *boolean*. Jeżeli zostanie zwrócona wartość *false*, wtedy dopiero subskrypcja zostanie odsubskrybowana.

Korzystając z operatora **takeWhile()**, subskrypcja zostanie przerwana dopiero wtedy, kiedy źródłowy strumień wyemituje jakąś wartość. Nie mamy jednak kontroli nad strumieniem i nie wiemy czy wyemituje wartość. Jest to bardzo zdradliwe i często powoduje wycieki pamięci.
{: .alert-danger}

Przykładowy kod wygląda następująco:

	export class AppComponent implements OnInit, OnDestroy {
		counter = 0;
		private alive = true;

		constructor(private counterService: CounterService) {}

		ngOnInit() {
			this.counterService.getCounter$()
				.pipe(
					takeWhile(() => this.alive),
				)
				.subscribe(value => {
					this.counter = value;
				});
		}

		ngOnDestroy() {
			this.alive = false;
		}
	}

W powyższym kodzie ustawiając flagę *this.alive* na *false*, nie mamy wpływu na to, czy strumień *getCounter$()* wyemituje. Jeżeli nie wyemituje (co prawdopodobnie się stanie) wtedy mamy wyciek pamięci – subskrypcja nie zostanie odsubskrybowana.

Nie polecam stosowania tego operatora, umieściłem go tutaj raczej jako ostrzeżenie.

### 4. Operator take(1)

Operator *take(1)* powoduje, że subskrypcja zostanie automatycznie odsubskrybowana po pierwszym wyemitowaniu przez strumień. Oczywiście nie zawsze możemy skorzystać z tego operatora, bo nie zawsze będziemy chętni aby przestać nasłuchiwać po otrzymaniu pierwszej wartości.

Ten operator służy raczej jako pewna sztuczka, kiedy chcemy nieskończony strumień zamienić na skończony – tzn. odsubskrybować automatycznie po pierwszym emicie. Oczywiście należy podkreślić, że bazowy strumień nie zostanie zakończony, odsubskrybuje się jedynie sybskrypcja:

	export class AppComponent implements OnInit {
		counter = 0;

		constructor(private counterService: CounterService) {}

		ngOnInit() {
			this.counterService.getCounter$()
				.pipe(
					take(1)
				)
				.subscribe(value => {
					this.counter = value;
				});
		}
	}

Istnieje też operator *first()*, jednak nie polecam go używać. Nie jest on tak bezpieczny jak *take(1)*. Różnica polega na tym, że rzuci wyjątkiem w przypadku emitowania wartości *NULL*, oraz w przypadku kiedy strumień wcale nie wyemituje wartości i zostanie zakończony.

### 5. Async

W Angularze jest dostępny pipe o nazwie *async*. Umożliwia on pobranie wartości ze strumienia bez konieczności subskrybowania się do niego. Tak naprawdę subskrypcja zostanie utworzona niejawnie przez sam kompilator, jednak my nie musimy się zajmować jej sprzątaniem:

	@Component({
		selector: 'app-component',
		template: `Wartość licznika: {% raw %}{{{% endraw %} counter$ | async }}`
	})
	export class AppComponent implements OnInit {
		counter$!: Observable;

		constructor(private counterService: CounterService) {}

		ngOnInit() {
			this.counter$ = this.counterService.getCounter$();
		}
	}

Używanie tego pipa jest najbardziej pożądaną formą obsługi strumieni RxJs w Angularze. Za jego pomocą możemy osiągnąć właściwie wszystko czego potrzebujemy. Oto zalety *async*:

- niejawnie tworzy za nas subskrypcję
- automatycznie odsubskrybowuje strumień podczas niszczenia komponentu
- niejawnie wywołuje *.markForCheck()* gdy mechanizm wykrywania zmian jest w trybie *OnPush* (warto wiedzieć!)
- mniej kodu

Opis wszystkich fajnych możliwości tego operatora wykracza poza ramy tego artykułu.

## Własny operator RxJs do subskrybowania

Ostatnio pracowałem w pewnym projekcie z biblioteką do modali. Po otwarciu modal zwracał obiekt, w którym znajdowała się funkcja *onClose()* zwracająca strumień. Podczas otwarcia modala subskrybowałem się na metodę *onClose()*, ponieważ podczas jego zamknięcia musiałem przeładować tabelę z danymi.

Później odkryłem, że strumień zwracany przez metodę jest nieskończony. Autor biblioteki po prostu zapomniał wywołać na strumieniu *.complete()*. Odkryłem to dopiero gdy zauważyłem w aplikacji wycieki pamięci. Przyzwyczajony do metod rozszerzających (ang. *extension methods*) dostępnych w języku C# postanowiłem dodać coś podobnego do RxJs.

Rozszerzając *prototype* klasy *Observable<T>* uzyskujemy efekt bardzo podobny do metod rozszerzających:

	declare module 'rxjs' {
		export interface Observable<T> {
			subscribeFirst(next?: (value: T) => void, error?: (error: any) => void, complete?: () => void): Unsubscribable;
		}
	}

	Observable.prototype.subscribeFirst = function<T>(
		this: Observable<T>,
		next?: (value: T) => void,
		error?: (error: any) => void,
		complete?: () => void) {
			return this.pipe(take(1)).subscribe(next, error, complete);
	};

Po pierwsze rozszerzyłem tu typowanie o nową funkcję, a po drugie dodałem definicje prostej funkcji, która hermetyzuje użycie operatora *take(1)*. Dzięki takiemu zabiegowi każda instancja strumienia posiada funkcję *subscribeFirst()*, która zwróci pierwszą wartość i automatycznie się odsubskrybuje:

	export class AppComponent implements OnInit {
		counter!: number;
		
		constructor(private counterService: CounterService) { }
		
		ngOnInit() {
			this.counterService.getCounter$().subscribeFirst(val => {
				this.counter = val;
			});
		}
	}

Taki zabieg oczywiście nie wszędzie jest potrzebny. U mnie sprawdził się wyśmienicie, ponieważ **dzięki temu nie musiałem 20 subskrypcji w aplikacji obklejać dodatkowymi operatorami**, a zawsze interesowała mnie pierwsza i jedyna wartość.

Nie musiałbym tego robić, gdyby autor biblioteki poprawnie zamknął strumień po pierwszym emicie, świat jednak nie jest idealny.

## Jak szukać wycieków pamięci?

Przeglądarka Google Chrome posiada wbudowane narzędzia developerskie (otwierane klawiszem *F12*). Korzystając z zakładki *Memory* oraz opcji *Heap snapshot* **można bardzo łatwo szukać wycieków pamięci**.

Scenariusz szukania błędów jest następujący:

1. Uruchomienie aplikacji i przejście do jakiegoś ekranu/stanu, który chcemy testować
1. Pobranie zrzutu pamięci (przycisk *Take heap snapshot*)
1. Otworzenie jakiegoś komponentu z subskrypcjami a następnie zamknięcie go (powrót do ekranu pkt 1)
1. Pobranie zrzutu pamięci

Czynność powtarzamy kilkukrotnie uzyskując w ten sposób kilka zrzutów pamięci. Zrzuty pamięci możemy następnie porównać między sobą szukając instancji klas z biblioteki RxJs.

W tym celu ustawiamy tryb widoku na *Comparison* a następnie porównujemy najnowszy zrzut pamięci z jakimkolwiek go poprzedzającym. Na poniższym zdjęciu porównałem *Snapshot2* ze *Snapshot1*. W wyszukiwarce nazw obiektów wpisałem wartość „sub”, aby zakroić widok tylko do obiektów z klasy RxJs:

{% include parts/postPicture.html page=page img="angular-memory-leak" %}

Kolumna *Delta* informuje nas o ilości nowych instancji określonego obiektu porównując ze sobą dwa zrzuty pamięci. Jeżeli w każdym kolejnym zrzucie pamięci rośnie ilość instancji oznacza to wyciek pamięci.

W poprawnie działającej aplikacji *Delta* powinna wynosić 0. Wtedy oznacza to, że mechanizm GC poprawnie usuwa ze sterty instancje nieużywanych obiektów. Rozwijając strzałki znajdujące się obok nazw obiektów można wyświetlić stos wywołań. Pozwala to na dość łatwe znalezienie lokalizacji niezamkniętych subskrypcji.

## Czy wyciek pamięci występuje zawsze?

Jeżeli zaczniesz testować różne scenariusze w jakich występują wycieki pamięci, szybo zauważysz, że **czasem ich nie ma, tam gdzie byś się ich spodziewał**.

Dzięki wykorzystaniu algorytmu *mark-and-sweep*, JS potrafi radzić sobie z wyciekami pamięci tam, gdzie programista zapomni po sobie posprzątać. Ponieważ wspomniany algorytm działa na zasadzie zliczania referencji do instancji, można łatwo przewidzieć, kiedy JS będzie umiał posprzątać subskrypcje za nas a kiedy nie.

#### Brak wycieku pamięci

Silnik JS nie dopuści do wycieku pamięć w następujących sytuacjach:

- kiedy instancja strumienia zostanie utworzona w tym komponencie co subskrypcja
- kiedy instancja strumienia zostanie utworzona w serwisie przypisanym do *injectora* komponentu, w którym zostanie utworzona subskrypcja (tablica *providers* komponentu)

JS będzie umiał ogarnąć powyższe przypadki, ponieważ zliczając referencję do strumienia i subskrypcji okaże się, że żadne już nie występują. Najpierw zostanie usunięta ze sterty instancja komponentu, później strumienia, później subskrypcji.

#### Wyciek pamięci

Silnik JS nie będzie potrafił zwolnić zasobów ze sterty w następujących sytuacjach:

- kiedy instancja strumienia jest utworzona w serwisie należącym do *injectora* wyżej, niż komponent z subskrypcją:
	- serwis zadeklarowany w *AppModule*
	- serwis zadeklarowany jako *providedIn: 'root'*
	- serwis zadeklarowany w jakimkolwiek komponencie rodzica
- kiedy instancja strumienia jest utworzona w innym leniwym module
- kiedy instancja strumienia jest utworzona na podstawie zdarzenia (*fromEvent*)
- kiedy instancja strumienia pochodzi z klasy *Router*

JS nie ogarnie powyższych przypadków, ponieważ podczas niszczenia komponentu nie zostanie zniszczona instancja strumienia. Strumień działa koncepcyjnie tak samo jak wzorzec obserwator opisany przez GOF (ang. *gank of four*). Oznacza to, że każdy *Subject* i *Observable* przetrzymuje referencje do wszystkich jego obserwatorów czyli subskrypcji (tylko wtedy może ich powiadamiać o zdarzeniach).

W tym wypadku algorytm zliczania referencji wykryje referencję przypisaną do subskrypcji i **nie zostanie ona zwolniona ze sterty**. Myślisz, że jedna mała instancja subskrypcji to niezbyt duży problem? No tak, problem pojawia się wtedy, gdy dodatkowo nie zostanie zniszczona instancja komponentu i 100 innych obiektów.

Najlepiej nigdy **nie polegać na mechanizmach oczyszczacza pamięci, i samodzielnie czyścić subskrypcje** tam gdzie trzeba. Oczyszczacz pamięci dba o wydajność i czasem potrafi nas uratować, jednak jego zadaniem nie jest pilnowanie błędów programisty.

