---
title: Jak ogarnąć formularze w złożonych systemach
category: angular
thumbnail: formularze-angular.png
published: 2020-04-06T00:00:00Z
---
Chyba każdy zgodzi się z faktem, że Angular i formularze to nierozłączny duet. Wszystkie aplikacje internetowe składają się z dziesiątek krótszych bądź dłuższych formularzy. Zapanowanie nad formularzami spowoduje, że Twój projekt stanie się piękny i przejrzysty. W tym artykule przedstawię swoje doświadczenia w pracy z formularzami, dzięki temu Ty nie popełnisz moich błędów.

<!--more-->

## Aplikacje Angularowe to formularze

Angular służy do tworzenia aplikacji SPA (ang. *single page application*). Cechą szczególną SPA jest pojedyncza strona załadowana z serwera jeden raz. Na tej stronie znajduje się wiele małych komponentów, które **komunikują się ze sobą i pobierają dane asynchroniczne z API**. Aplikacje SPA doskonale sprawdzają się do tworzenia zaawansowanych systemów webowych takich jak: panele administracyjne, aplikacje bankowe, aplikacje służące do zarządzania przedsiębiorstwami.

Aplikacje, które wymieniłem, są po brzegi wypełnione formularzami. Odpowiadają one za pobieranie od użytkownika systemu dziesiątek informacji.

### Najczęstsze problemy z formularzami

Organizacja złożonych formularzy nie jest łatwa. O ile raczej nikt nie ma problemów z organizacją modułów, serwisów czy routingów, o tyle wiele osób ma **problem z poprawnym zaplanowaniem formularzy**.

Piszę to także na bazie własnych doświadczeń, ponieważ w wielu sytuacjach zastanawiałem się, które wyjście będzie lepsze, i ciężko było mi znaleźć odpowiedź. Odpowiedź przychodzi przeważnie sama, po czasie, kiedy na zmiany jest już za późno.

Według mnie, najczęściej popełniane błędy to:

- brak granulacji formularza na mniejsze komponenty
- brak pomysłu na reużywanie logiki przy jednoczesnym podmienianiu widoku
- zła synchronizacja komponentów formularzy (np. korzystanie z *@ViewChild*)
- brak hermetyzacji formularza w osobnym komponencie (kontener), tylko wtapianie go w istniejące elementy strony
- niewyłączanie systemu detekcji (ang. *change detector*), co przy "dużych" formularzach bardzo zabija wydajność
- brak separacji logiki biznesowej oraz mieszanie jej z logiką aplikacji

Wiele powyższych problemów łączy się ze sobą, i są swoimi naturalnymi następstwami.

## Formularz z wieloma widokami

Częstym przypadkiem jest konieczność **wyświetlenia jednego formularza z użyciem różnych widoków HTML**. Dzieje się tak np. wtedy, gdy tryb edycji jakiejś encji różni się od trybu dodawania. Bardzo często jest też tak, że formularz dodawania wyświetlamy w modalu, a formularz edycji dopiero po przejściu na konkretną podstronę.

W takim wypadku skopiowanie formularza jest największym możliwym błędem, ponieważ trzeba kopiować całą logikę komponentu. Czasem próbowałem wychodzić z takiej sytuacji wprowadzając dodatkowe inputy określające "tryb dzialania" formularza (edycja, dodawanie, wyświetlanie). Przeważnie takie podejście kończy się powstaniem gniota, który posiada wiele instrukcji warunkowych i setki linijek kodu.

O wiele lepszą metodą rozwiązania tego problemu jest **użycie dziedziczenia, gdzie komponenty potomne posiadają różne widoki**, a komponent bazowy nie ma widoku lecz agreguje całą logikę:

{% include parts/postPicture.html page=page img="angular-reuse-form" %}

Dziedziczenie komponentów Angularze **jest możliwe, i właściwie nigdy nie przydatne**. Wyjątkiem jest sytuacja, kiedy chcemy wykorzystać kod TypeScript komponentu i dostarczyć mu różne szablony. Warto zaznaczyć, że w takiej sytuacji bazowy komponent wcale nie musi posiadać własnego pliku widoku.

Aby móc wykorzystać tę metodę, konieczne jest skorzystanie z reaktywnych formularzy (ang. *reactive-driven forms*). W odróżnieniu od podejścia szablonowych formularzy (ang. *template-driven forms*), formularze reaktywne umożliwiają przeniesienie prawie całej logiki do pliku TS.

Przykładowy kod wykorzystujący tę technikę: <https://github.com/p-programowanie/angular-forms/tree/forms-inheritance>

## Podzielenie formularza na komponenty

Duże formularze w aplikacjach webowych z pewnością należy dzielić na mniejsze, i to samo dotyczy formularzy. Istnieją **4 podstawowe metody na podzielenie formularza**:

1. Tworzenie formularzy w osobnych komponentach (niepowiązanych w żaden sposób) 
    - synchronizacja za pomocą *@Input*
    - synchronizacja za pomocą *@ViewChild*
1. Tworzenie formularzy w osobnych komponentach i używanie wspólnego *FormGroup* 
    - przekazywanie *FormGroup* za pomocą *@Input*
1.  Tworzenie formularzy w osobnych komponentach i używanie wspólnego *ControlContainer* 
    - wstrzykiwanie *ControlContainer* przez *viewProviders*
1. Enkapsulacja formularza za pomocą *ControlValueAccessor*

Przed analizą powyższych rozwiązań warto przypomnieć o dwóch typach komponentów. Pisząc aplikację w Angularze możemy używać komponentów prostych oraz stanowych:

- **komponenty proste** - nie powinny posiadać stanu ani zależeć od danych zewnętrznych. Powinny zachowywać się deterministycznie, czyli dla tych samych wartości *@Input()* powinny wyświetlać te same wartości. Są bardzo wydajne, nie obciążają mechanizmu wykrywania zmian. Przeważnie strategia wykrywania zmian jest ustawiona na *OnPush*.
- **komponenty stanowe** - przechowują stan aplikacji, komunikują się z zewnętrznymi serwisami (np. API). Są rodzicami dla komponentów prostych.

Z tą podstawową wiedzą możemy przejść do dalszych akapitów.

### 1 Synchronizacja za pomocą Input/Output lub ViewChild

#### ViewChild

W najprostszym możliwym przypadku, możemy utworzyć dwa osobne komponenty z dwoma niezależnymi formularzami. Następnie wyświetlić te dwa formularze obok siebie i synchronizować ich stan. Efektem tej metody powinien być jeden formularz docelowy, który użytkownik widzi jako pojedynczy formularz.

Załóżmy, że posiadamy dwa osobne formularze *ProductForm* oraz *UserForm*. Chcemy utworzyć trzeci formularz o nazwie *OrderForm*, który będzie zbudowany z dwóch poprzednich.

	@Component({
	    selector: 'app-order-form',
	    template: `
	        <h2>Order form</h2>
	        
	        <app-product-form></app-product-form>
	        <app-user-form></app-user-form>
	    `
	})
	export class OrderFormComponent  {
	
	}

Możemy te komponenty zsynchronizować, dostając się do zagnieżdżonych wewnątrz nich formularzy za pomocą *@ViewChild()*. Przykładowy kod:

	export class OrderFormComponent {
	    @ViewChild(ProductFormComponent) productForm: ProductFormComponent;
	    @ViewChild(UserFormComponent) userForm: UserFormComponent;
	    
	    ngAfterViewInit() {
	        merge(
	            this.productForm.productForm.valueChanges,
	            this.userForm.userForm.valueChanges
	        ).subscribe(_ => {
	            const formValue = {
	                ...this.userForm.userForm.getRawValue(),
	                ...this.productForm.productForm.getRawValue()
	            };
	        });
	        
	        this.cdr.detectChanges();
	    }
	}

W powyższym przykładzie zasubskrybowaliśmy się na zmiany w formularzach *user* oraz *product* przy każdej zmianie wartości tych formularzy. Dostanie się do instancji formularzy było możliwe dzięki użyciu dekoratorów *@ViewChild*.

{% include parts/postPicture.html page=page img="angular-view-child" %}

Niewątpliwym plusem takiego podejścia jest możliwość synchronizacji dowolnych komponentów, bez żadnego szczególne przygotowania. Zawsze możemy złapać instancję komponentu i odczytać z niego interesujące nas dane. Na tym plusy się kończą.

Używanie *ViewChild* do synchronizacji formularzy niesie ze sobą wiele negatywnych skutków. Przepływ danych pomiędzy komponentami staje się niejednokierunkowy - nie można wyłączyć detekcji zmian. Gdy formularz robi się rozbudowany a Ty będziesz chciał dodać do niego więcej logiki (np. odczytywanie flag *valid* lub *pristine*), wtedy to podejście stanie się gwoździem do trumny Twojego projektu. Programiści mają tendencję do nadużywania dekoratorów *ViewChild*, ale tak nie programuje się w Angularze.

Problemy jakie wynikają z używania tego dekoratora to:

- przepływ danych nie jest jednokierunkowy (czyli od komponentów rodziców aż do dzieci). Zamiast tego, gdyby pokusić się o narysowanie grafu takiej aplikacji, wszystko komunikuje się ze wszystkim w różnych niespodziewanych momentach (niespodziewanych, ponieważ nie podlegających cyklom życia komponentu Angularowego)
- instancję widoku możemy "złapać" dopiero w zdarzeniu *ngAfterViewInit*. Można też ominąć tę niedogodność podpinając *setter* do zmiennej *ViewChild* i tam dodać jakąś logikę

Przykład formularza synchronizowanego za pomocą *ViewChild*: <https://github.com/p-programowanie/angular-forms/tree/forms-view-childs>

#### Input/Output

Synchronizować formularze można także za pomocą dekoratorów *Input* oraz *Output*. Jest to podejście najbardziej intuicyjne i najbliższe "wzorcowej" architekturze Angulara. Tak jak w poprzednim wypadku spróbujmy zsynchronizować ze sobą dwa komponenty *UserForm* oraz *ProductForm*:

	<h2>Order form</h2>
	
	<app-product-form 
	    (formChange)="setProductForm($event)" 
	    [initialValue]="initialValue" 
	    [categories]="categories"></app-product-form>
	<app-user-form 
	    (formChange)="setUserForm($event)" 
	    [initialValue]="initialValue">
	    </app-user-form>

Jak widzisz w kodzie powyżej, pojawiły się jakieś *Outputy*, które będą informować o zmianach w formularzu, oraz jakieś *Inputy*, które przekazują wymagane słowniki oraz początkową wartość dla formularza.

Synchronizacja w tym wypadku będzie bardzo prosta, skupia się ona wyłącznie na podpięciu do właściwych *emitterów*, które będą przekazywać aktualne wartości formularzy dzieci:

	export class OrderFormComponent {
	    private userForm!: FormGroup;
	    private productForm!: FormGroup;
	    
	    setProductForm(formGroup: FormGroup) {
	        this.productForm = formGroup;
	        this.refreshFormState();
	    }
	    
	    setUserForm(formGroup: FormGroup) {
	        this.userForm = formGroup;
	        this.refreshFormState();
	    }
	    
	    private refreshFormState() {
	        const value = { ...this.userForm?.value, ...this.productForm?.value };
	        const isValid = this.userForm?.valid && this.productForm?.valid;
	        const isPristine = this.userForm?.pristine && this.productForm?.pristine;
	    }
	}

Korzyścią takiego rozwiązania jest dość przejrzysta architektura, **jednokierunkowy przepływ informacji** oraz łatwość utrzymania kodu, nawet jeżeli formularz zaczyna niebezpiecznie obrastać w dodatkową logikę. Wszystkie komponenty formularza mogą mieć ustawiony mechanizm wykrywania zmian na tryb *OnPush*, dzięki czemu aplikacja znacząco przyśpiesza.

Dodatkowo, możesz wykorzystać zdarzenia cyklu życia komponentu opisane wyżej. Dlaczego jest to tak ważne? W przypadku skomplikowanej logiki biznesowej, gdy poszczególne elementy formularza trzeba ukrywać, w zależności od wartości różnych pól, Twoja aplikacja będzie zachowywać się sposób deterministyczny.

{% include parts/postPicture.html page=page img="angular-input-output" %}

Przykład formularza synchronizowanego za pomocą *Input*/*Output*: <https://github.com/p-programowanie/angular-forms/tree/forms-inputs-outputs>

### 2. Wspólny FormGroup

Przekazywanie wspólnego *FormGroup* pomiędzy komponentami formularza pozwala w wygodny sposób rozłożyć duży formularz na kilka mniejszych komponentów. Z mniejszych komponentów można w łatwy sposób komponować zaawansowane formularze. Stosując to podejście często tworzę sobie następującą strukturę:

- /form 
  - invoice-form
  - vendor-form
- /form-part 
  - user-details-form
  - address-form
  - invoice-items-form
  - invoice-details-form
  - vendor-type-form

W tym podejściu wszystkie komponenty znajdujące się w katalogu *form* są gotowymi formularzami, które są wykorzystywane w aplikacji. Te znajdujące się katalogu *form-part*, są natomiast prostymi komponentami, niezawierającymi logiki ani efektów ubocznych, spodziewającymi się obiektu *FormGroup* przekazanego przez *@Input*. Jest to **podejście budowania formularza na wzór kompozytu**, składającego się z wielu małych reużywalnych części.

Kod głównego komponentu agreguje cały formularz:

	@Component({
	    selector: 'app-order-form',
	    template: `
	        <h2>Order form</h2>
	        
	        <form [formGroup]="form">
	            <app-product-form [formGroup]="form.controls.product" [categories]="categories"></app-product-form>
	            <app-user-form [formGroup]="form.controls.user"></app-user-form>
	        </form>
	    `,
	    changeDetection: ChangeDetectionStrategy.OnPush
	})
	export class OrderFormComponent implements OnInit, OnDestroy {
	    form!: FormGroup;
	    
	    constructor(private formBuilder: FormBuilder) { }
	    
	    ngOnInit() {
	        this.form = this.formBuilder.group({
	            product: this.formBuilder.group({
	                name: ['', Validators.required],
	                categoryId: [0, Validators.required],
	                price: [0, Validators.required]
	            }),
	            user: this.formBuilder.group({
	                firstname: ['', Validators.required],
	                lastname: ['', Validators.required],
	                email: ['', Validators.required],
	            })
	        });
	    }
	}

Zauważ, że każdy z komponentów prostych ma przekazany *FormGroup*, na którym następnie bazuje:

	@Component({
	    selector: 'app-user-form',
	    template: `
	        <form [formGroup]="formGroup">
	            <input formControlName="firstname" type="text">
	            <input formControlName="lastname" type="text">
	            <input formControlName="email" type="text">
	        </form>
	    `,
	    changeDetection: ChangeDetectionStrategy.OnPush
	})
	export class UserFormComponent {
	    @Input() formGroup: FormGroup;
	}

Cechą tego podejścia, jest przeniesienie całej logiki do jednego, głównego komponentu. Komponenty proste (mniejsze części) nie mogą zawierać właściwie żadnej logiki, ponieważ nie posiadają definicji formularza - ona zostanie przekazana przez *@Input*. Jedyny wyjątek stanowią proste operacje np. mapowania słowników.

{% include parts/postPicture.html page=page img="angular-shared-fromgroup" %}

Plusem tego podejścia jest niewątpliwie brak polegania na *@ViewChild* oraz wykorzystanie *@Input* jako naturalny mechanizm przekazywania danych do komponentów potomnych. Dzięki **jednokierunkowej komunikacji** oczywiście ustawiamy mechanizm wykrywania zmian na *OnPush*. Zmian w komponentach prostych nie trzeba emitować w górę za pomocą *Output*, ponieważ **referencję do formularza przetrzymuje główny komponent**. Zawiera on też całą logikę.

Minusem jest konieczność agregacji całej logiki w głównym komponencie, co może być minusem jak i plusem. Bardzo fajnie można tę cechę wykorzystać do wydzielenia logiki biznesowej aplikacji - opiszę to w kolejnych akapitach.

Przykład formularza opartego o *FormGroup*: <https://github.com/p-programowanie/angular-forms/tree/forms-form-group>

### 3. Wspólny ControlContainer

Poprzednio opisane podejście polegające na wspólnym *FormGroup* przekazywanym za pomocą *@Input* jest fajne, ale nie idealne. Głównym minusem tego rozwiązania jest silne związanie komponentu rodzica i dziecka. Możemy pozbyć się tego silnego powiązania wykorzystując mechanizm wstrzykiwania zależności Angulara.

Każda dyrektywa Angularowa związana z formularzami dziedziczy z abstrakcyjnej klasy *ControlContainer*. Są to m.in. dyrektywy *ngForm*, *ngControl*, *ngGroup*. Do komponentu dziecka możemy wstrzyknąć instancję *ControlContainer* zainicjalizowaną w komponencie rodzica. Uzyskamy wtedy instancję formularza, zdefiniowanego w komponencie rodzica.

Tak jak w poprzednim wypadku komponent rodzica agreguje całą logikę i inicjalizuje formularz:

	@Component({
	    selector: 'app-order-form',
	    template: `
	        <h2>Order form</h2>
	        
	        <form [formGroup]="form">
	            <app-product-form [categories]="categories"></app-product-form>
	            <app-user-form></app-user-form>
	        </form>
	    `,
	    changeDetection: ChangeDetectionStrategy.OnPush
	})
	export class OrderFormComponent implements OnInit, OnDestroy {
	    form!: FormGroup;
	    
	    constructor(private formBuilder: FormBuilder) { }
	    
	    ngOnInit() {
	        this.form = this.formBuilder.group({
	            product: this.formBuilder.group({
	                name: ['', Validators.required],
	                categoryId: [0, Validators.required],
	                price: [0, Validators.required]
	            }),
	            user: this.formBuilder.group({
	                firstname: ['', Validators.required],
	                lastname: ['', Validators.required],
	                email: ['', Validators.required],
	            })
	        });
	    }
	}

Zwróć uwagę, że do komponentów dzieci nie są już przekazywane przez *@Input* instancje *FormGroup*. Aby komponenty dzieci miały do nich dostęp wystarczy w komponentach prostych dodać:

	viewProviders: [
	    {
	        provide: ControlContainer,
	        useExisting: FormGroupDirective
	    }
	]

Dlaczego akurat *viewProviders*? Wynika to z architektury platformy Angular. Angular uzyskuje instancję *ControlContainer* z użyciem dekoratora *@Host*. Ten dekorator powoduje, że próba uzyskania instancji ograniczy się do aktualnej instancji komponentu (w kontekście tablicy *providers*) lub także komponentu rodzica (ale w kontekście tablicy *viewProviders*). W skrócie można powiedzieć, że dekorator *@Host* działa jak *@Self*, jednak bierze pod uwagę także zależności *viewProviders* rodzica.

Nasz komponent dziecka może wyglądać następująco:

	@Component({
	    selector: 'app-user-form',
	    template: `
	        <form formGroupName="user">
	            <input formControlName="firstname" type="text">
	            <input formControlName="lastname" type="text">
	            <input formControlName="email" type="text">
	        </form>
	    `,
	    changeDetection: ChangeDetectionStrategy.OnPush,
	    viewProviders: [
	        {
	            provide: ControlContainer,
	            useExisting: FormGroupDirective
	        }
	    ]
	})
	export class UserFormComponent {}

Dzięki wstrzyknięciu *ControlContainer* dyrektywa *formGroupName* uzyska instancję formularza zdefiniowanego w komponencie rodzica. Komponent dziecka i rodzica nie są ze sobą związane:

{% include parts/postPicture.html page=page img="angular-control-container" %}

Właściwie trzeba otwarcie przyznać, że **ciężko obecnie znaleźć lepsze rozwiązanie na tworzenie skomplikowanych formularzy**. Ja osobiście na nic lepszego nie trafiłem. To podejście niesie ze sobą wiele korzyści,a jednocześnie nie jest tak pracochłonne, jak to opisane w kolejnym akapicie.

Przykład formularza opartego o *ControlContainer*: <https://github.com/p-programowanie/angular-forms/tree/forms-control-container-provider>

### 4. Enkapsulacja formularza z ControlValueAccessor

Jeżeli chcemy aby nasz komponent umiał komunikować się poprzez *ngModel*, czyli wykorzystywać tzw. dwustronne bindowanie w Angularze, musi on implementować interfejs *ControlValueAccessor*. Ten interfejs został stworzony po to, aby mieć możliwość tworzenia niestandardowych kontrolek.

Nic nie stoi jednak na przeszkodzie, aby **zahermetyzować formularz wewnątrz komponentu, który implementuje ten interfejs**. Jest to najcięższe działo jakie można wykorzystać starając się tworzyć niezależne części formularza.

Aby formularz działał należy zaimplementować dwa interfejsy:

- ControlValueAccessor - aby móc komunikować się przez *ngModel*
- *Validator* - aby informować *ngForm*, czy formularz jest poprawnie wypełniony

Implementacja wygląda następująco:

	@Component({
	    selector: 'app-user-form',
	    templateUrl: './user-form.component.html',
	    changeDetection: ChangeDetectionStrategy.OnPush,
	    providers: [
	        {
	            provide: NG_VALUE_ACCESSOR,
	            useExisting: forwardRef(() => UserFormComponent),
	            multi: true
	        },
	        {
	            provide: NG_VALIDATORS,
	            useExisting: forwardRef(() => UserFormComponent),
	            multi: true
	        }
	    ]
	})
	export class UserFormComponent implements ControlValueAccessor, Validator, OnInit {
	    form!: FormGroup;
	    onTouched: any = () => { };
	    
	    constructor(private formBuilder: FormBuilder) { }
	    
	    ngOnInit() {
	        this.form = this.formBuilder.group({
	            firstname: ['', Validators.required],
	            lastname: ['', Validators.required],
	            email: ['', Validators.required]
	        });
	    }
	    
	    /* ControlValueAccessor */
	    
	    writeValue(value: User): void {
	        if (value) {
	            this.form.setValue(value);
	        }
	    }
	    registerOnChange(fn: any): void {
	        this.form.valueChanges.subscribe(fn);
	    }
	    registerOnTouched(fn: any): void {
	        this.onTouched = fn;
	    }
	    setDisabledState?(isDisabled: boolean): void {
	        if (isDisabled) {
	            this.form.disable();
	        } else {
	            this.form.enable();
	        }
	    }
	    
	    /* Validator */
	    
	    validate(control: AbstractControl): ValidationErrors {
	        if (this.form.valid) {
	            return null;
	        }
	        return { user_form_invalid: true };
	    }
	}

Działanie mechanizmu *ControlValueAccessor* jest bardzo proste, a do hermetyzacji formularza można to działanie znacząco uprościć.

- funkcja *writeValue* uruchamia się gdy zmienia się wartość *ngModel*, wtedy zapisujemy wartość do naszego formularza funkcją *setValue*
- funkcja *registerOnChange* wywołuje się tylko raz, dostarcza nam referencję funkcji, którą musimy wywołać, aby powiadomić *ngModel* o zmianach
- funkcja *validate* uruchomi się za każdym razem, gdy zmieni się *ngModel*

Normalnie należałoby w komponencie utworzyć nową zmienną na przechowywanie wartości, i wywoływać instancję funkcji dostarczonej przez *registerOnChange* przy każdej zmianie tej wartości. **W przypadku hermetyzacji formularza można zastosować jednak duże uproszczenie**. Polega ono na podpięciu instancji funkcji dostarczonej przez *registerOnChange* od razu, jako funkcja zwrotna subskrypcji na funkcję *valueChanges* formularza. Dzięki temu zabiegowi każda zmiana formularza automatycznie powiadomi *ngModel* o zmianach.

Formularz rodzica może wyglądać następująco:

	@Component({
	    selector: 'app-order-form',
	    templateUrl: `
	        <form [formGroup]="form">
	            <app-product-form formControlName="product" [categories]="categories"></app-product-form>
	            <app-user-form formControlName="user"></app-user-form>
	        </form>
	    `,
	    changeDetection: ChangeDetectionStrategy.OnPush
	})
	export class OrderFormComponent implements OnInit {
	    form!: FormGroup;
	    
	    constructor(private formBuilder: FormBuilder) { }
	    
	    ngOnInit() {
	        this.form = this.formBuilder.group({
	            product: [''],
	            user: ['']
	        });
	    }
	}

Takie podejście jest bardzo skuteczne i posiada kilka zalet, których nie posiadali poprzednicy.

- hermetyzuje logikę aplikacji i logikę biznesową wewnątrz formularza
- nie wymaga komunikacji ani przez *@Input*/*@Output* ani *@ViewChild*, po prostu potrafi obsłuzyć *ngModel* formularza

Przykład formularza opartego o *ControlValueAccessor*: <https://github.com/p-programowanie/angular-forms/tree/forms-control-value-accessor>

## Całościowa hermetyzacja formularza

Pracując nad formularzami bardzo ważne jest, aby zamykać je w osobne dedykowane komponenty. Niezależnie w jaki sposób tworzysz/dzielisz/organizujesz formularze, zewnętrzny komponent jest bardzo ważny.

Przykład błędnego osadzenia formularza może wyglądać tak:

{% include parts/postPicture.html page=page img="angular-form1" %}

Jest to przykład podzielonego na kilka komponentów formularza faktury, jednak sam formularz został zdefiniowany w komponencie *AppComponent*. Może Ci się wydawać, że hermetyzacja nie jest potrzebna, ponieważ wokół użytych komponentów nie będzie żadnej logiki, jednak to prosta droga do problemów.

Gdy aplikacja urośnie i pojawią się nowe wymagania, **ilość kodu zacznie lawinowo rosnąć**. Trzeba będzie obsłużyć błędy formularza, inicjalizację, mapowanie słowników w komponencie *AppComponent*, który **absolutnie nie powinien za to odpowiadać**. Jeszcze gorzej, gdy kiedyś okaże się, że formularz trzeba gdzieś skopiować.

Osobiście, niezależnie jak prosty formularz przygotowuję, zawsze hermetyzuję go w komponencie-kontenerze. Czasem jest on prawie całkiem pusty, jednak ubezpieczam się na przyszłość ewentualnego użycia formularza w kilku miejscach:

	@Component({
	    selector: 'app-invoice-form',
	    template: `
	        <form [formGroup]="form">
	            <app-invoice-items [formGroup]="form.controls.invoiceDetails" [categories]="categories"></app-invoice-items>
	            <app-user [formGroup]="form.controls.user"></app-user>
	        </form>
	    `,
	    changeDetection: ChangeDetectionStrategy.OnPush
	})
	export class InvoiceFormComponent implements OnInit, OnDestroy {
	    @Input() categories!: Dictionary[];
	    @Output() formChange = new EventEmitter<{value: Invoice}>();
	    
	    form!: FormGroup;
	    subscription!: Subscription;
	    
	    constructor(private formBuilder: FormBuilder) { }
	    
	    ngOnInit() {
	        this.form = this.formBuilder.group({
	            invoiceDetails: this.formBuilder.group({}),
	            user: this.formBuilder.group({})
	        });
	        
	        this.subscription = this.form.valueChanges.subscribe(_ => {
	            this.formChange.emit(this.form.value);
	        });
	    }
	    
	    ngOnDestroy() {
	        this.subscription.unsubscribe();
	    }
	}

Dzięki takiemu prostemu zabiegowi formularz można łatwo wykorzystać w wielu miejscach, a komponentu, które go używają, nie mają żadnej logiki związanej z obsługą formularza. Niezbędne jest tylko przekazanie słowników przez *@Input* oraz nasłuchiwanie zmian podpinając się do *@Output*.

Cały cykl życia aplikacji Angularowej bazuje na jednokierunkowym przepływanie danych, czyli na dekoratorach *@Input*. Jeżeli przestaniemy z nich korzystać, **wtedy zrobimy sobie bardzo duży problem**. Właściwie utracimy możliwość sterowania aplikacją w sposób spójny i przewidywalny. Taka sytuacja ma miejsce, kiedy nadużywa się dekoratorów *@ViewChild*, próbując zastąpić nimi dekoratory *@Input*.

### Hermetyzacja formularza a sposób dzielenia na komponenty

Napisałem już w życiu dużo brzydkich formularzy. Refaktoryzowałem je później, gdy z czasem dostrzegłem wady zastosowanych przeze mnie rozwiązań. Ostatecznie kieruje się następującą strategią:

{% include parts/postPicture.html page=page img="angular-formularze" %}

Na powyższym obrazku dobrze widać kilka mechanizmów, występujących pomiędzy opisanymi strategiami dzielenia formularzy.

W pierwszym podejściu *@ViewChild()* oraz *@Input()*/*@Output()* kontener powinien być bardzo lekki, ponieważ **cała logika zawarta jest w formularzach w komponentach dzieci**. Zadaniem kontenera jest tylko synchronizacja wartości formularzy z komponentów dzieci, i ewentualne przekazanie słowników. Kontener nie powinien definiować żadnych przycisków ani komunikować się z API, tylko wystawić dane formularza.

W drugim podejściu *FormGroup*/*ControlContainer* kontener jest komponentem decyzyjnym. Zawiera on definicję formularza oraz całą jego logikę. **Komponenty dzieci są natomiast komponentami prostymi, które jedynie definiują widok i szablon formularza**. Tak jak zawsze, kontener nie powinien strzelać do API tylko wystawić odpowiedni formularz przez *@Output()*.

W trzecim podejściu z jakiegoś powodu trzeba było zahermetyzować skomplikowaną logikę za pomocą *ControlValueAccessor*. Jest to przeważnie połączenie podejścia dwa i trzy. Przeważnie część formularza jest obsługiwana przez *FormGroup* lub *ControlContainer* - i wtedy są to komponenty proste - a część przez *ControlValueAccessor*. Takie rozwiązanie (podejście trzecie) zastosowałem ostatnio gdy jedna z części formularza zawierała skomplikowany przybornik wyboru kolorów. Przybornik zawarłem w formie osobnego komponentu, jednak był on także elementem wielu formularzy.

## Separacja logiki biznesowej formularza

Niestety formularze w Angularze to ciężki orzech do zgryzienia, i ciężki do zaprojektowania fragment aplikacji (z punktu widzenia architektonicznego). Zapewne zgodzisz się ze mną, że **formularze to często zlepek wielu przeplatających się subskrypcji oraz instrukcji warunkowych**. Pierwszym krokiem do zbudowania przejrzystego formularza powinno być zawsze podzielenie go na mniejsze komponenty, jeżeli już to zrobisz, możesz pokusić się o odseparowanie logiki biznesowej do osobnego serwisu.

Stwórzmy przykładowy formularz o nazwie *VendorComponent* przy użyciu podejścia wspólnego *FormGroup*. W tym podejściu cała logika skupia się w głównym komponencie, a komponenty dzieci są komponentami prostymi.

#### Wymagania:

*Nasz formularz będzie formularzem tworzenia nowego klienta. Klient może być klientem prywatnym lub korporacyjnym. Klient prywatny i korporacyjny mają różne zestawy pól z danymi (prywatny: imię, nazwisko, PESEL; korporacyjny: państwo, REGON, NIP). Wymagamy informacji o koncie bankowym klienta. Jeżeli jest to klient zagraniczny oprócz konta potrzebujemy  numeru SWIFT.*

Podział na komponenty może wyglądać następująco:

{% include parts/postPicture.html page=page img="angular-vendor-form" %}

Struktura komponentów, którą utworzyłem w projekcie wygląda natomiast tak:

{% include parts/postPicture.html page=page img="formularze-struktura" %}

W głównym komponencie formularza *vendor.component.ts* trzeba skupić całą logikę biznesową, utworzyć nowy formularz (instancja *FormGroup*) i przekazać go do komponentów dzieci. Dodatkowo jest to kontener formularza, więc stan formularza trzeba emitować w górę, do komponentu rodzica.

Całą logikę biznesową postanowiłem przenieść do osobnego serwisu o nazwie *VendorBusinessLogicService*. Będzie on wstrzykiwany na poziomie komponentu (w dekoratorze komponentu), po to aby stworzył się lokalny *injector*, i **serwis nie był singletonem**. Dzięki temu zawsze gdy pojawi się nowa instancja komponentu formularza, pojawi się także nowa instancja naszego serwisu z logiką biznesową.

Główny komponent formularza może wyglądać następująco:

	@Component({
	    selector: 'app-vendor',
	    template: `
	    <form [formGroup]="form">
	        <app-vendor-type [formGroup]="form.controls.vendorType" [vendorTypes]="vendorTypes"></app-vendor-type>
	        <app-vendor-company [hidden]="businessLogic.hideCompanyForm" [formGroup]="form.controls.vendorCompany"></app-vendor-company>
	        <app-vendor-private [hidden]="businessLogic.hidePrivateForm" [formGroup]="form.controls.vendorPrivate"></app-vendor-private>
	        <app-payment [shouldRequireSwift]="businessLogic.requireSwift" [formGroup]="form.controls.payment"></app-payment>
	    </form>
	    `,
	    changeDetection: ChangeDetectionStrategy.OnPush,
	    providers: [VendorBusinessLogicService]
	})
	export class VendorComponent implements OnInit, OnDestroy {
	    @Output() formState = new EventEmitter<FormState>(true);
	    @Input() vendorTypes!: Dictionary[];
	    @Input() initialValue!: any;
	    
	    form!: FormGroup;
	    private subscription!: Subscription;
	    
	    constructor(public businessLogic: VendorBusinessLogicService) { }
	    
	    ngOnInit() {
	        this.form = this.businessLogic.createForm(this.initialValue);
	        
	        this.subscription = this.form.valueChanges.subscribe(value => {
	            this.businessLogic.applyBusinessLogic(value);
	            this.emitFormState();
	        });
	        
	        this.emitFormState();
	    }
	    
	    ngOnDestroy() {
	        this.subscription.unsubscribe();
	    }
	    
	    private emitFormState() {
	        this.formState.emit({
	            isValid: this.form.valid,
	            isPristine: this.form.pristine,
	            value: this.form.value
	        });
	    }
	}

Komponent napisany w ten sposób naprawdę może obsłużyć cały formularz ze wszystkimi możliwymi wymaganiami biznesowymi. W zdarzeniu *ngOnInit* następuje wywołanie metody serwisu odpowiedzialnej za inicjalizację formularza. Tworzy ona instancję *FormGroup* i jeżeli jest taka potrzeba wypełnia domyślnymi wartościami z *@Inputa* o nazwie *initialValue*.

Serwis zajmuje się utworzeniem instancji formularza, **i ważne jest, aby tę instancję przetrzymywał**. Dzięki temu mamy dostęp do całego formularza wraz z zawartością. Ostatnim krokiem jest subskrypcja na zmiany w formularzu i przy każdej ze zmian:

1. Zastosowanie logiki biznesowej zawartej w serwisie
2. Wyemitowanie zmian formularza do rodzica

Jak zapewne się domyślasz metoda *applyBusinessLogic* jest dość rozbudowana, ale tak po prostu musi być. Sukcesem jest to, że jest ona odseparowana od komponentów formularza, a serwis o wiele łatwiej jest testować. Powinna ona zawierać **samą logikę biznesową a nie logikę aplikacji**. Teoretycznie nic nie stoi na przeszkodzie, aby posiadać osobny serwis na logikę biznesową i logikę aplikacji. W tym przykładzie jednak nie ma tego rozdzielenia.

Jakie są korzyści takiego podejścia?

- **widoki formularza** to komponenty proste - bardzo łatwe do wykorzystania w innych miejscach (brak logiki i stanu)
- **kontener formularza** to komponent stanowy - skupia widoki w całość i uruchamia serwis z logiką biznesową, emituje wartość formularza do rodzica
- **serwis z logiką biznesową** - nakłada na formularz różne reguły biznesowe (kiedy jakieś pole jest wymagane, kiedy niewidoczne, a kiedy kasuje się w nim wartość)

Przykład formularza z odseparowaną logiką biznesową: <https://github.com/p-programowanie/angular-forms/tree/forms-separated-business-logic>

## Podsumowanie

Formularze Angularowe to jeden z najtrudniejszych rejonów podczas projektu nowej aplikacji. Na wiele występujących problemów można znaleźć odpowiednie rozwiązania. Najważniejszymi kwestiami są:

- dzielenie formularzy na mniejsze komponenty
- unikanie *@ViewChild()*, a jeżeli trzeba go użyć to tylko tam, gdzie nie spodziewamy się reakcji zwrotnej formularza
- używanie *FormGroup* lub *ControlContainer* jako jedynego słusznego rozwiązania
- używanie *ControlValueAccessor* jeżeli coś jest naprawdę "ciężkie" i zależy nam na odseparowaniu tego
- próba wydzielenia logiki biznesowej zawsze gdy jest to możliwe - chociaż w minimalnym stopniu

Oczywiście niektóre z opisanych technik dotyczą dużych formularzy i dużych problemów jakie za nimi stoją. Pisząc prosty formularz, trzeba umieć dobrać narzędzie współmierne do problemu i nie przedobrzyć.