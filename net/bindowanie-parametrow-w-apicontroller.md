---
title: Bindowanie parametrów w ApiController
category: net
thumbnail: webapi.png
published: 2015-12-31T00:00:00Z
---
Ostatnio w pracy napotkałem drobne problemy związane z **przekazywaniem parametrów do ApiControllera**. Z tego krótkiego wpisu dowiesz się w jaki sposób parametry akcji są bindowane z danymi zawartymi w żądaniu. Dodatkowo zaprezentuję i omówię niektóre metody z protokołu HTTP. Z artykułu dowiesz się **jak utworzyć kontroler WebAPI** i ustawić format zwracanych przez niego danych na JSON.

<!--more-->

## Czym jest REST?

**REST jest to sposób komunikacji** oparty na protokole HTTP. Cechują go **prosta budowa i łatwość implementacji**. Polega on na ujednoliceniu sposobu wymiany danych pomiędzy klientem a serwerem. Systemy informatyczne używające RESTa są zrozumiałe i nie wymagają tak szczegółowej dokumentacji jak API budowane w innych standardach.

Potęga RESTa polega na tym, że **do zbudowania API wykorzystuje on jedynie metody dostępne w protokole HTTP**, są to: `Post`, `Get`, `Put`, `Delete`. Dzięki temu użytkownik danego repozytorium podświadomie rozumie działanie systemu bez konieczności długiego wdrażania się.

Dawniej budując API aplikacji także korzystało się np. z protokołu HTTP, jednak nie było tam podejścia RESTowego. Wywołując jakąś akcję z API pod adresem *api/deleteUser/5* otrzymywaliśmy odpowiedź w formacie *json* lub *xml* zawierającą odpowiedź czy zabieg się udał np.:

	-- response --
	200 OK
	Cache-Control:  no-cache
	Content-Type:  application/json;
	
	{"Status":"Błąd", "Message":"Użytkownik o tym ID nie istnieje"}

Jak widzisz w powyższym przykładzie, mimo że operacja nie powiodła się (otrzymaliśmy status &#8222;Błąd&#8221;), serwer zwrócił nam kod *200 OK*.

W przypadku trzymania się stylu REST, serwer zwróciłby odpowiedź 200 tylko w wypadku usunięcia użytkownika, a w przypadku błędu jakikolwiek kod oznaczający błąd. Trzymając się standardu REST, użytkownik API ma pewność, że każde repozytorium wystawione w API obsługuje 5 przypadków:

- *GET api/user* - zwróci listę wszystkich użytkowników
- *GET api/user/1* - zwróci użytkownika o identyfikatorze 1
- *POST api/user* - doda nowego użytkownika
- *PUT api/user/1* - edycja użytkownika o identyfikatorze 1 (nadpisanie)
- *DELETE api/user/1* - usunięcie użytkownika o identyfikatorze 1

Powyższy przykład obrazuje całą siłę RESTa. Po pierwsze otrzymujemy uporządkowane API więc nie musimy domyślać się pod jaki adres wysłać POSTa żeby usunąć użytkownika. Gdyby nie był to REST usunięcie użytkownika mogłoby się odbyć przez *POST api/deleteUser*. Dodatkowo nie musimy sprawdzać treści odpowiedzi od serwera, wszystkiego dowiemy się już po kodzie odpowiedzi.

### Dlaczego REST będzie zawsze używany?

REST jest szeroko wykorzystywany ponieważ jest prosty i intuicyjny. Bardzo dobrze sprawdza się pod względem wieloplatrofmowości gdzie nie sprawdza się już np. SOAP. **Serwer postawiony w NodeJS korzystający z podejścia RESTfull API zajmuje zaledwie kilka linijek kodu**. Dla porównania funkcjonalność REST jest automatycznie wbudowana w protokół HTTP i nie wymaga żadnych dodatkowych nakładów pracy. Wdrożenie SOAP jest już bardziej pracochłonne np. ze względu na konieczność definiowania *XML* Schemas.

REST jest wygodny zarówno dla programisty piszącego kod jak i dla odbiorcy. Narzuca na obie strony pewne umowne standardy, dzięki czemu programista nie musi się zastanawiać od jakiej strony ugryźć problem lub jakie metody wystawić w nowym fragmencie API. Podejście REST stało się wygodnym standardem implementowanym wszędzie tam, gdzie nie potrzeba bardziej zaawansowanych narzędzi, choć czasem prowadzi to do nadmiarowości.

### WebAPI wspiera RESTa

Chcąc nie chcąc, **standard REST jest wspierany przez framework ASP.NET MVC**. Decydując się na użycie WebAPI wykorzystując ApiControllery najlepszym rozwiązaniem dla Ciebie będzie użycie RESTa. Ten fakt znajduje potwierdzenie w kilku szczegółach, o których dowiesz się z treści artykułu.

## Tworzenie kontrolera WebAPI

Do budowania wszelkich API oraz repozytoriów naszych aplikacji najlepiej użyć kontrolerów WebAPI. Są to wszelkie kontrolery dziedziczące jawnie z klasy *ApiController*. Różnica pomiędzy zwykłym kontrolerem a WebAPI kontrolerem jest taka, że zwykłe kontrolery zwracają widoki a WebAPI kontrolery zwracają dane (w postaci JSON lub XML).

Utwórz nowy projekt ASP.NET MVC, następnie kliknij prawym przyciskiem myszy na katalog *Controllers* i wybierz pozycję *Add Web API Controller v2*.

	public class TestController : ApiController
	{
	    [HttpGet]
	    public IEnumerable<String> Akcja()
	    {
	        return new List<String>() { "aaa", "bbb" };
	    }
	}

Kontroler stworzony przeze mnie na potrzeby artykułu posiada tylko jedną metodę. Będziemy ją modyfikować w zależności od potrzeb.

### Domyślny format zwracanych danych

Kontrolery WebAPI domyślnie zwracają dane w postaci XML. Można to bardzo łatwo sprawdzić uruchamiając projekt wchodząc pod adres *localhost:port/api/test*. Ponieważ **format JSON jest dla mnie bardziej przyjazny** a także bardziej przyjazny dla JavaScripta, zmienimy domyślny format zwracanych danych.

W tym celu należy otworzyć plik *WebApiConfig.cs* znajdujący się w katalogu &#8222;App*Start&#8221;. Z kolekcji *SupportedMediaTypes* usuwamy nagłówek *application/xml*. Dzięki temu dane wyjściowe kontrolera zostaną automatycznie niejawnie przekształcone na JSON.

	public static void Register(HttpConfiguration config)
	{
	    config.MapHttpAttributeRoutes();
	    
	    config.Routes.MapHttpRoute(
	        name: "DefaultApi",
	        routeTemplate: "api/{controller}/{id}",
	        defaults: new { id = RouteParameter.Optional }
	    );
	    
	    // usuwamy content-type XML co spowodouje wyświetlanie JSON
	    var xmlType = config.Formatters.XmlFormatter.SupportedMediaTypes.FirstOrDefault(o => o.MediaType == "application/xml");
	    config.Formatters.XmlFormatter.SupportedMediaTypes.Remove(xmlType);
	}

Warto zauważyć, że kontrolery WebAPI posiadają własne definicje rutingów, co jest nawet zrozumiałe ponieważ stanowią odrębny element systemu. Możemy mieć z tym problem budując wspólne API dla poszczególnych *Areas*.

## Wysyłanie danych przez GET i POST

Protokół HTML jest protokołem bardzo prostym, **służącym do przesyłania informacji pomiędzy klientem a serwerem**. Jest to protokół bezstanowy, w żadnym momencie nie ma nawiązanego stałego połączenia pomiędzy dwoma odbiorcami. Nigdzie nie są zapisywane żadne informacje o połączeniu lub autoryzacji. Jest to protokół zapytań i odpowiedzi, klient wysyła zapytanie określonego typu i dostaje odpowiedź. Do każdego zapytania i odpowiedzi jest dołączona treść.

Zarówno **zapytania do serwera jak i jego odpowiedzi można monitorować a także nimi manipulować**. Do podglądu komunikacji służą wbudowane w przeglądarkę narzędzia, które znajdują się w konsoli. Konsolę uruchamiasz naciskając F12. Przechodząc do zakładki &#8222;Sieć&#8221; zobaczysz wszelkie zapytania oraz ich odpowiedzi.

Do wysyłania własnych spreparowanych żądań warto posłużyć się tzw. REST Klientem. Ja używam wtyczki do Firefoxa o nazwie Open HttpRequester. Umożliwia mi ona wysyłanie żądań na na dowolny adres, dowolnego typu i z dowolną treścią.

Głównymi typami zapytań, które nas interesują są POST oraz GET. **Zapytania POST służą do wysyłania informacji do serwera w postaci niejawnej**. Dzieje się to np. w momencie logowania lub rejestracji nowego konta na jakiejkolwiek stronie. Dane wpisane w formularz zostają wysłane do serwera, jednak ich nie widzimy (chyba że podglądniemy je w konsoli analizując zapytanie).

**Zapytania GET służą do wysyłania zapytań w postaci jawnej a dokładnej w postaci adresu URL**. Wszystko co wpisujesz w pasku przeglądarki jest żądaniem typu GET. Są używane po pierwsze do ładowania głównych widoków stron, a także do budowania hierarchii nawigacji.

Co ważne jest to założenie specyfikacji. Metody GET i POST można ze sobą &#8222;miksować&#8221;, tzn przesłać przez GET parametr w treści albo przez POST parametr w adresie URL.

## Bindowanie parametrów ApiController

Przekazywanie danych do metod wystawionych w kontrolerach **nosi nazwę bindowania parametrów**.  Bindowanie parametrów **polega na dopasowaniu parametrów podanych wewnątrz zapytania do parametrów podanych jako argumenty** akcji kontrolera.

{% include parts/postPicture.html page=page img="control1" %}

W tym artykule przedstawiam mechanizm bindowania parametrów konkretnie do kontrolerów WebAPI a więc tych dziedziczących jawnie z *ApiController*. Istnieje cała pula problemów, których ten artykuł nie poruszy. Jeżeli chcesz bardziej zagłębić się w temat musisz przeczytać jakiś kurs ASP.NET MVC.

## Przekazywanie parametrów typów prostych

**Jeżeli parametry kontrolera są typami prostymi, wtedy WebAPI próbuje automatycznie zbindować je z adresu URL**. Typami prostymi są wszelkie typy wywodzące się z przestrzeni nazw *System* a więc np. *int*, *string*, *float* itd.

Tworzę nową metodę zwracającą typ *dynamic*. Dzięki temu będę mógł zwrócić obiekt anonimowy, który zostanie automatycznie przekształcony w JSON. Dzięki temu nie muszę bawić się w tworzenie modeli na potrzeby artykułu. Rozważmy przykład:

	public class TestController : ApiController
	{
	    [HttpPost]
	    public dynamic Akcja(string imie, int wiek)
	    {
	        return new
	        {
	            Osoba = new {
	                Imie  = imie,
	                Wiek = wiek
	            }
	        };
	    }
	}

Wyślę teraz zapytanie typu POST na adres *api/test?imie=Karol&wiek=23*.

{% include parts/postPicture.html page=page img="control3" %}

Bindowanie odbyło się zgodnie z planem. Jednak parametry zostały przesłane jako elementy adresu URL.

### Przez treść zapytania

Chcąc przesłać parametry prawilnie przez treść zapytania, tak jak powinno to wyglądać w metodzie POST, należy nazwę parametru poprzedzić atrybutem `[FromBody]`.

Niestety pojawiają się tutaj pierwsze ograniczenia. Możemy przesłać **tylko jeden parametr będący typem prostym** poprzedzony atrybutem `[FromBody]`. Dodatkowo należy ustawić kodowanie na *application/x-www-form-urlencoded*. Użycie czegokolwiek innego będzie skutkować pustą wartością.

Ciekawy jest także sposób formatowania treści zapytania. Przesyłając typ prosty przez treść zapytania należy przedstawić go w postaci *=wartość*. Użycie czegokolwiek innego będzie skutkować pustą wartością. Sprawdźmy przykład:

	public class TestController : ApiController
	{
	    [HttpPost]
	    public dynamic Akcja([FromBody] string imie)
	    {
	        return new
	        {
	            Osoba = new {
	                Imie  = imie
	            }
	        };
	    }
	}

Treść zapytania i jego wynik wygląda następująco:

{% include parts/postPicture.html page=page img="control4" %}

### Przez URL i przez treść zapytania

Nic nie stoi na przeszkodzie aby połączyć ze sobą **dwa powyższe sposoby przekazywania parametrów typów prostych**. Przykładowa metoda może wyglądać następująco:

	public class TestController : ApiController
	{
	    [HttpPost]
	    public dynamic Akcja([FromBody] string imie, int wiek, string strona)
	    {
	        return new
	        {
	            Osoba = new {
	                Imie  = imie,
	                Wiek = wiek,
	                Strona = strona
	            }
	        };
	    }
	}

Treść zapytania i jego wynik wygląda następująco:

{% include parts/postPicture.html page=page img="control5" %}

Wszystko działa poprawnie. Nasuwają się wnioski, po pierwsze sposób przesyłania **typów prostych przez treść zapytania** został przez Microsoft zaimplementowany dość dziwnie. Możesz przesłać tylko jeden parametr przez treść zapytania, resztę trzeba wcisnąć poprzez parametry w URL.

## Przekazywanie parametrów typów złożonych

Typy złożone czyli wszelkiego rodzaju obiekty i modele **są automatycznie niejawnie przekazywane przez treść zapytania**. W ich wypadku bardzo ważne jest określenie w jakim formacie dane trafiają do serwera a więc określenie *Content-type*.

Przykładowy kontroler może wyglądać następująco:

	public class Osoba
	{
	    public string Imie { get; set; }
	    public int Wiek { get; set; }
	}
	
	public class TestController : ApiController
	{
	    [HttpPost]
	    public dynamic Akcja(Osoba arg)
	    {
	        return arg;
	    }
	}

Treść i wynik zapytania:

{% include parts/postPicture.html page=page img="control6" %}

### Przez URL

Nie jest to zbyt dobry pomysł aby przekazywać typy złożone przez adres URL, jednak można osiągnąć taki efekt dopisując do parametru atrybut `[FromUri]`. Przykładowa klasa:

	public class Model
	{
	    public string Imie { get; set; }
	    public int Wiek { get; set; }
	}
	
	public class TestController : ApiController
	{
	    [HttpPost]
	    public dynamic Akcja([FromUri] Model arg)
	    {
	        return arg;
	    }
	}

Treść zapytania oraz jego wynik:

{% include parts/postPicture.html page=page img="control7" %}

## Podsumowanie

Przekazywanie parametrów do WebAPI kontrolerów jest sprawą nieskomplikowaną. Drobne **problemy może sprawdzić budowanie zapytań dla parametrów o typach prostych**. Jeżeli informacje zawarte w artykule nie rozwiązały Twojego problemu, zapoznaj się z własnym binderem. W Asp.NET MVC możliwe jest napisanie własnego bindera dziedziczącego z klasy `IModelBinder`, jednak jest to temat na osobny artykuł.