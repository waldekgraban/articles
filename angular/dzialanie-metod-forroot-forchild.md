---
title: Działanie metod forRoot i forChild
category: angular
thumbnail: forroot-angular.png
published: 2019-05-10T00:00:00Z
---
Pracowałem przy wielu aplikacjach Angularowych. Przy wszystkich z nich ktoś popełniał jakieś błędy związane z metodą forRoot i wstrzykiwaniem zależności. Mechanizm wstrzykiwania zależności na platformie Angular jest niezwykle prosty. Po przeczytaniu tego artykułu nigdy więcej nie będziesz się zastanawiał nad różnicami pomiędzy forRoot i forChild. Ich używanie nie będzie dla Ciebie żadnym problemem.

<!--more-->

## Moduły

Podstawowym elementem służącym do organizowania aplikacji napisanych w Angularze są moduły. Z ich pomocą możemy grupować pewne elementy aplikacji (serwisy, komponenty, dyrektywy) i zamykać je w pojedyncze "pojemniki". Poprawnie zaprojektowana aplikacja Angularowa **powinna być podzielona na odpowiednią ilość modułów**. Częstym błędem początkujących programistów jest umieszczanie wszystkich elementów aplikacji w jednym głównym module tzw. *AppModule*.

Start aplikacji Angularowej zaczyna się zawsze od modułu *AppModule*, jest to tzw. moduł wejściowy. Dlaczego akurat ten? Ponieważ takie jest domyślne ustawienie pliku *main.ts*:

	platformBrowserDynamic().bootstrapModule(AppModule);

Główny moduł aplikacji Angularowej może/musi importować inne moduły. Jeżeli utworzymy moduł, który nie jest importowany, wtedy zostanie on pominięty podczas procesu kompilacji  po prostu nie będzie go w aplikacji. Tak więc, moduły łączą się między sobą poprzez wzajemne importowanie.

### Kompilacja aplikacji a moduły

Moduły Angularowe **nie tworzą żadnej hierarchii**. Jest to bardzo ważny fakt, którego niezrozumienie prowadzi do niezrozumienia całej koncepcji projektowania aplikacji Angularowych. Hierarchiczne są *injectory*, a moduły nie. Co to oznacza? Podczas komplikacji aplikacji wszystkie importowane moduły są łączone w jeden globalny moduł:

{% include parts/postPicture.html page=page img="kompilacja-modulow" %}

Dla globalnego modułu zostaje utworzony globalny *injector*, który zawiera serwisy pochodzące ze wszystkich importowanych modułów. Istnieje możliwość **nadpisania jednego serwisu przez inny**, jeżeli przed kompilacją znajdują się w różnych modułach ale mają te same nazwy.

Jak widać na obrazku wyżej, serwisy z różnych modułów są łączone w jeden wspólny moduł. Ta sytuacja **nie dotyczy już jednak komponentów**, dyrektyw ani innych strukturalnych elementów platformy Angular. **Moduł, w którym zostaje zadeklarowany komponent jest jego przestrzenią nazw**.

Wynika z tego ciekawy fakt: serwisy i inne wstrzykiwalne elementy **są globalne dla całej aplikacji Angularowej**, ale elementy strukturalne (np. komponenty, dyrektywy) **już nie**. Dlatego za każdym razem chcąc odwołać się do jakiejś dyrektywy, musimy ponawiać import danego modułu.

### Przykład z NgxBootstrap

Biblioteka NgxBootstrap jest Ci na pewno dobrze znana. Jest to Angularowa "nakładka" na arkusz styli Bootstrapa, dzięki czemu posiadamy gotowe serwisy, dyrektywy i komponenty.

Dla przykładu **użyjmy komponentu wyboru dat** z tejże biblioteki. Jego użycie jest opisane na <https://valor-software.com/ngx-bootstrap/#/datepicker>. Zauważ, że autor zamkną całość w osobnym module o nazwie *BsDatepickerModule*, który zaleca zaimportować.

Rozważmy taką przykładową architekturę aplikacji:

{% include parts/postPicture.html page=page img="importowanie-modulow2" %}

Tworzymy aplikację z dwoma modułami: moduł główny oraz moduł zamówień. Importujemy do naszej aplikacji *BsDatepickerModule*, który jest modułem biblioteki NgxBootstrap. Do poprawnego działania wybierania dat niezbędne są serwis *DatepickerConfig* oraz dyrektywa nakładana na pole *input* o nazwie *BsDatepickerDirective*.

Czy, przy obecnej konfiguracji, w naszym komponencie *OrderComponent* możemy dodać komponent wyboru dat? - **odpowiedź brzmi nie**.

Przeanalizujmy powyższy obrazek. Główny moduł naszej aplikacji importuje moduł z biblioteki NgxBootstrap. Zostanie utworzony wspólny *injector* i dzięki temu serwis niezbędny do wstrzykiwania dat **będzie dostępny w całej naszej aplikacji** (nawet w *OrderComponent*). Jednak dyrektywa o nazwie *BsDatepickerDirective* jest elementem strukturalnym a nie wstrzykiwalnym. Dlatego:

- próba użycia dyrektywy *BsDatepickerDirective* w komponencie w głównym module *AppModule* **uda się, ponieważ importujemy tam moduł *BsDatepickerModule***, a jest on przestrzenią nazw dla tej dyrektywy.
- próba użycia dyrektywy *BsDatepickerDirective* w komponencie *OrderModule* **nie uda się, ponieważ nie importujemy tam *BsDatepickerModule***. ALE mimo to, uda się wstrzyknięcie *DatepickerConfig*, ponieważ mamy wspólny injector

Rozwiązanie problemu jest proste. Aby móc użyć zarówno dyrektywy jak i serwisu komponentu wyboru daty, należy do *OrderModule* zaimportować *BsDatepickerModule*. I to rozwiązanie rodzi nam kolejny problem. Serwis już mamy, ponieważ istnieje wspólny *injector*. Chcieliśmy doimportować **tylko przestrzeń nazw dla dyrektyw**, a kolejny raz, przy okazji, zaimportowaliśmy serwis.

Jak bronić się przed tym problemem? O tym w kolejnym akapicie.

### Czym więc jest moduł?

Powyższy akapit miał dać zarys czym jest moduł, oraz jakie problemy czasem stwarza. Podsumowując możemy uznać, że:

- serwisy zadeklarowane w module **są publiczne** (będą widoczne w całej aplikacji)
- komponenty, dyrektywy i pipy **są prywatne** (musimy importować moduł (z którego pochodzą) w każdym module, gdzie chcemy ich użyć)

Ta pozornie prosta idea przyświecająca Googlowi (twórca Angulara), rodzi problem reimportowania serwisów wszędzie tam, gdzie chcemy odwołać się do modułu jako przestrzeni nazw dla komponentów. Rozwiązaniem tego problemu, jest metoda *forRoot* o czym będzie w kolejnych akapitach.

## Sposoby importowania modułów

Jeżeli używasz edytora Visual Code, wtedy łatwo za pomocą klawisza F12 możesz nawigować się do definicji typów poszczególnych elementów. Przechodząc do definicji typu atrybutu *import* modułu Angularowego zobaczymy jakiego jest typu:

	imports?: Array<Type<any> | ModuleWithProviders>;

Jest to pole opcjonalne, przyjmujące unię *Type\<T>* i *ModuleWithProviders*. Generyczny interfejs *Type\<T>* reprezentuje dowolny typ, który jest typu funkcji konstruującej (z ang. *constructor function*). Może to być moduł, komponent, klasa, serwis lub cokolwiek innego. Ta pierwsza opcja jest używana zawsze wtedy, **gdy importujemy moduł "w normalny sposób"**.

Drugi przypadek to specjalny interfejs, który wygląda następująco:

	interface ModuleWithProviders<T> {
	    ngModule: Type<T>
	    providers?: Provider[]
	}

Jest on używany zawsze wtedy, kiedy chcemy użyć funkcji *forRoot()*. Dzięki temu interfejsowi, **możemy zaimportować moduł wraz z serwisami, ale bez żadnych elementów strukturalnych**.

## Metoda forRoot

Wiesz już, jaki problem istnieje z importowaniem Angularowych moduł. W akapicie wyżej zostały opisane dwie metody na importowanie modułów: jedna standardowa importująca wszystko, druga pozwalająca zaimportować tylko moduł i serwisy.

Metoda *forRoot()* to nic innego, jak funkcja zwracająca instancję interfejsu *ModuleWithProviders\<T>*. Po co? Dzięki takiemu rozwiązaniu **możemy importować moduł z serwisami lub bez nich**. Rozważmy taki kod:

	@NgModule({
	    declarations: [BsDatepickerDirective, ...],
	    exports: [BsDatepickerDirective, ...]
	})
	export class BsDatepickerModule {
	    public static forRoot(): ModuleWithProviders {
	        return {
	            ngModule: BsDatepickerModule,
	            providers: [DatepickerConfig]
	        };
	    }
	}

Powyższy kod pokazuje typową implementację funkcji *forRoot*. Utworzyliśmy moduł *BsDatepickerModule*, który w standardowy sposób (czyli w dekoratorze) deklaruje dyrektywę do wyboru dat. Mimo to, definicja serwisu została wyniesiona z dekoratora do pewnej statycznej metody *forRoot()* zdefiniowanej na klasie tego modułu. Jest to metoda statyczna, a więc nieinstancyjna, a więc możemy ją wywołać niezależnie czy instancja modułu istnieje czy nie.

Powinieneś już dostrzegać, po co potrzebna jest metoda *forRoot*? Odpowiedź jest bardzo prosta. Metoda *forRoot* jest rozwiązaniem na **importowanie modułów bez serwisów**, czyli rozwiązaniem naszego problemu z pierwszego akapitu.

Wywołując metodę *forRoot()* (w imporcie) w głównym module naszej aplikacji zaimportujemy moduł oraz serwisy. Dzięki temu serwisy będą dostępne w całej naszej aplikacji:

	@NgModule({
	    imports: [
	        BsDatepickerModule.forRoot(), // ładuje serwisy
	    ],
	    declarations: [AppComponent],
	    bootstrap: [AppComponent]
	})
	export class AppModule { }

Jeżeli będziemy potrzebować zaimportować moduł *BsDatepickerModule* w innym module naszej aplikacji (jakimkolwiek oprócz *AppModule*), wtedy zaimportujemy go w sposób normalny. Spowoduje to załadowanie elementów strukturalnych (np. komponentów) **bez podwójnego ładowania serwisów** (ponieważ już załadowaliśmy je przez *forRoot*).

	@NgModule({
	    imports: [
	        BsDatepickerModule, // ładuje komponenty i dyrektywy
	    ],
	    declarations: [OrderComponent],
	})
	export class OrderModule { }

Zauważ proszę i zapamiętaj, że w gruncie rzeczy metoda *forRoot* **nie ma nic wspólnego z singletonami** - jak wiele osób czasem twierdzi. Sytuacja z typowej rozmowy rekrutacyjnej:

*A: Czy wie Pan coś na temat metody forRoot?  
B: A no tak coś tam było, używałem kiedyś. To coś z singletonami było.*

No nie, niestety, singletony nie mają nic do rzeczy. Singletony są zależne od ilości *injectorów*. Wieloinstancyjność serwisów można osiągnąć tworząc nowe *injectory*, na co jest kilka sposób w Angularze. Opisałem to w artykule [jak pozbyć się singletonów w Angularze?](https://www.p-programowanie.pl/angular/jak-pozbyc-sie-singletonow-w-angularze/)

### Używanie metody forRoot

Metoda *forRoot* zabezpiecza nas przed wielokrotnym ładowaniem serwisów. Dzięki jej użyciu możemy ładować poszczególne moduły wiele razy w "podmodułach" naszej aplikacji, **bez obawy, że wielokrotnie załadujemy serwisy**. Dlaczego w ogóle mamy chcieć ładować moduły wiele razy? **Bo to przestrzenie nazw** np. dla komponentów i dyrektyw.

W prostych serwisach (np. serwisy API) takie wielokrotne ładowanie może nie wyrządzić szkód, jednak w serwisach przechowujących stan aplikacji może być różnie (np. architektura flux/redux).

Istnieje pewna złota zasada mówiąca, że **metodę *forRoot()* możemy wywołać tylko w głównym module naszej aplikacji i nigdzie indziej**. Jest to zalecenie znajdujące się w oficjalnej dokumentacji Angulara. Jeżeli się na tym zastanowisz ma to sens. *ForRoot* ma na celu zainicjalizowanie głównego *inectora* naszej aplikacji i tylko tyle.

## Przekazanie konfiguracji przez forRoot

Metoda *forRoot* jest zwykłą metodą statyczną modułu. Nic nie stoi na przeszkodzie aby dodać do niej jakieś parametry. Dzięki temu, możemy przesyłać konfigurację do naszego modułu jeszcze przed jego załadowaniem.

Przykładem wykorzystania tej strategii jest *RouterModule*. Za pomocą metody *forRoot* ładujemy odpowiednie serwisy  jeden raz, a dodatkowo przesyłamy konfigurację nawigacji.

Przykładowe przesyłanie konfiguracji przez *forRoot* opiszę w innym artykule, ponieważ jest to także temat dość rozbudowany.

## Leniwe moduły

Leniwie doczytywane moduły rządzą się swoimi prawami, jednak różnice nie są jakieś znaczące. Leniwie doczytany moduł posiada **własny osobny *injector***, jednak utworzony jako dziecko głównego *injectora*. Oznacza to, że jeżeli jakiś serwis nie zostanie odnaleziony w *injectorze* leniwego modułu, wtedy mechanizm wstrzykiwania zależności Angulara i tak przeszuka główny *injector*.

W leniwym module nie możesz wywołać metody *forRoot* z innego modułu, ponieważ jak mówi dokumentacja Angulara, ***forRoot*** **można wywołać tylko w głównym *AppModule***.

### Metoda forChild

Jeżeli chcesz możesz natomiast dodać kolejną metodę o nazwie *forChild*, która dla Twojego leniwego modułu zwróci jakiś inny serwis, niż zwróciła metoda *forRoot*. Czy jest sens zwracać ten sam serwis? - raczej nie. Leniwy moduł i tak przeszuka główny *injector* i znajdzie serwis załadowany wcześniej metodą *forRoot*. Czy jest sens używać *forChild*? - w większości przypadków nie.

Po co więc implementować metodę *forChild*? Istnieją dwa scenariusze:

- jeżeli jakiś **serwis ma działać inaczej w leniwym module** niż działa w całej aplikacji - wtedy za pomocą *forChild* możemy zwrócić interfejs *ModuleWithProviders* z innym zestawem serwisów. Możemy nawet zwrócić ten sam serwis co z *forRoot*, wtedy rozbijemy singleton na serwis wieloinstancyjny (Jedna instancja w *AppModule* a druga w leniwym module).
- jeżeli chcemy jakiemuś serwisowi dostarczyć dane - wtedy metoda *forChild* zwraca pustą tablicę serwisów, jednak przyjmuje jakąś wartość, z którą coś później robi.

Przykładem takiego serwisu jest *RouterModule*. W głównym module aplikacji importujemy ten serwis obowiązkowo z wywołaniem metody *forRoot*. Ta metoda zwraca nam serwisy oraz przyjmuje konfigurację nawigacji. W leniwie ładowanych modułach **ponownie możemy przesłać konfigurację nawigacji, jednak nie są już zwracane żadne serwisy** - leniwy moduł pobierze wymagane serwisy z głównego *injectora*.

## ProvidedIn - Angular6

Od wersji Angular6.0 pojawił się dodatkowy parametr dekoratora *@Injectable* o nazwie *providedIn*. Przykładowe użycie wygląda następująco:

	@Injectable({
	    providedIn: 'root'
	})
	export class AuthorizationService {
	
	}

Użycie tego atrybutu to alternatywa dla implementowania metody *forRoot*. Jego użycie powoduje **brak konieczności dodawania serwisu do tablicy *providers* modułu**, w którym się znajduje. Mało tego, zaimportowanie tego modułu przez *AppModule* automatycznie spowoduje dodanie udekorowanego serwisu do głównego *injectora*.

Po co Google wprowadził taką dodatkową metodę? Ma to pomóc w mechanizmie ang. *tree-shaking*. Polega on na usuwaniu nieużywanych fragmentów kodu aplikacji podczas fazy kompilowania. Dzięki temu plik wynikowy może być znacznie mniejszy.

Powyższa metoda będzie prawdopodobnie powodować błędy w przypadku leniwych modułów. Pojawi się problem z *curcullar dependency*. Należy wtedy użyć standardowego podejścia importowania serwisów.

## Podsumowanie

Na koniec mała ciekawostka dotycząca metod *forRoot* i *forChild*. Ich nazwy wynikają z przyjętej konwencji a nie z żadnego interfejsu. Interfejs mówi tylko, aby metoda zwracała obiekt *ModuleWithProviders* ale nie definiuje jej nazwy.

Co to oznacza? Można zdefiniować sobie metodę o nazwie *loadModuleServices()* i wszystko nadal będzie działać. Oczywiście tak samo musi się wtedy nazywać statyczna metoda w klasie modułu.

Oto krótkie podsumowanie tego artykułu:

- metoda *forRoot* nie może Ci się kojarzyć z singletonami, to nie ta bajka
- metoda *forRoot* umożliwia **oddzielenie ładowania serwisów danego modułu od ładowania jego komponentów i dyrektyw**
- za pomocą tych metod można **przesyłać dodatkową konfigurację** do modułów
- w leniwie doczytywanych modułach rzadko stosowane, najwyżej aby przesłać konfigurację (jak *forChild* modułu *RouterModule*)
- od Angular6 można wykorzystywać atrybut *providedIn*, zamiast implementacji całej metody

Metoda *forRoot* i używanie jej w efektywny sposób to naprawdę prosta rzecz. Jej efektywne wykorzystywanie spowoduje, że cała nasza aplikacja będzie zbudowana z małych, współpracujących ze sobą modułów.