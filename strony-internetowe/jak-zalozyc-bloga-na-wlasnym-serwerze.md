---
title: Jak założyć bloga na własnym serwerze?
category: strony-internetowe
thumbnail: jak-zalozyc-bloga.jpg
published: 2013-09-16T00:00:00Z
---
## Wstęp

Zastanawiasz się **jak założyć bloga na własnym hostingu**? Wiele osób myśli, że nie mogą posiadać bloga jeżeli **nie umieją tworzyć stron**. Nic bardziej mylnego. W dzisiejszych czasach **założenie bloga to kwestia kilku kliknięć**. Posiadanie bloga na **własnym serwerze** niesie ze sobą szereg kluczowych plusów.<!--more-->

## Założenie własnego bloga &#8211; wstęp

Zanim zaczniesz czytać artykuł dotyczący **zakładania własnego bloga**, warto abyś rozróżniał podstawowe pojęcia dotyczące blogowania. Ten akapit został przeze mnie dopisany z powodu licznych pytań początkujących użytkowników, którzy nie rozumieją branżowej terminologii. Być może nie potrzebujesz czytać tego wstępu **dla początkujących**, wtedy przeskocz do następnego akapitu.

Podstawowym elementem jaki musi posiadać **blog** jest **hosting** i **domena**. **Hosting** jest miejscem w sieci, gdzie przechowywane są Twoje pliki. **Domena** to inaczej adres internetowy, po wpisaniu którego, użytkownik zostanie przekierowany na Twój hosting.

Jakie pliki należy umieścić na hostingu? Oczywiście pliki **bloga**. Każdy blog musi być obsługiwany przez jakiś system. Systemy te nazwane są w skrócie **CMS**. Są to gotowe "aplikacje" służące do zarządzania treścią. **WordPress** jest jednym z darmowych **systemów CMS** (czyli **systemów zarządzania treścią**). WordPress składa się z kilkudziesięciu/kilkuset plików, które będziesz musiał wrzucić na swój hosting, aby uruchomić **blog**.

Do uruchomienia będzie Ci potrzebny **klient FTP**. Jest to ogólna nazwa programów zapewniających połączenie między kontem hostingowym a Twoim komputerem. Dzięki **klientowi FTP** będziesz mógł wgrać pliki ze swojego dysku (pliki bloga) na konto hostingowe (do internetu).

Każdy **blog** musi być podpięty do bazy danych **MySQL**. Pliki z jakich blog jest zbudowany zapewniają tylko architekturę i funkcjonalności. Wszelkie artykuły i wpisy jakie umieścisz na swoim blogu będą przechowywane w **bazie danych MySQL**. A więc ostatecznie blog musi być podpięty do jakiejś bazy **MySQL**.

{% include parts/postPicture.html page=page img="zakladanie-bloga" %}

**Hosting**, **domenę** oraz **bazę MySQL** możesz wykupić  u wielu firm hostingowych. Warto porównać ich oferty ponieważ każdy usługodawca zapewnia inne parametry za inną cenę. W tym artykule opiszę tworzenie bloga opartego na silniku **WordPress**, z hostingiem i bazą danych kupioną w firmie Proserwer i domeną kupioną w firmie ovh.

## Rejestracja domeny

Jeżeli masz w głowie koncepcję **tematyki swojego przyszłego bloga**, czas zainteresować się domeną. **Dobra domena** jest krótka, łatwa do zapamiętania i zawiera w sobie słowo kluczowe kojarzące się z blogiem. Znacznie usprawni to pozycjonowanie i pozwoli w łatwy sposób **zapamiętać nazwę domeny** Twoim czytelnikom.

Domenę możesz zarejestrować w wielu firmach. Porządnym i **tanim serwisem do rejestracji domen** jest (http://www.ovh.pl/). Rejestracja domeny *.pl* to koszt około 12 złotych. Zawsze korzystam z usług tego serwisu. Rejestracja zawsze jest bardzo tania jeżeli rejestrujesz nową domenę. Koszt odnowienia domeny (czyli przedłużenia jej na kolejny rok) to większy koszt rzędu *60-90zł* zależnie od serwisu.

## Wybór hostingu

Blog na własnym hostingu kosztuje, jednak posiada wiele kluczowych plusów. Masz pełną władzę nad blogiem, możesz go w dowolny sposób edytować i nim zarządzać.

Zakładając bloga wystarczy Ci na początku **najtańszy hosting**. Szukając hostingu zwracaj uwagę na **dostęp do PHP**, możliwość **podpięcia domeny** oraz na przynajmniej jedno **konto MySQL**. Te podstawowe parametry dostarcza praktycznie każdy płatny hosting, nawet jeżeli jest bardzo tani (3zł miesiąc). Osobiście zakładając różne małe blogi często korzystałem z usług serwisu proserwer.pl. Ceny są dość niskie i dla zainteresowanych można płacić SMSem (nie nie, to nie żadna reklama z mojej strony).

Na **miesięczny transfer** jak i **pojemność konta** na początku nie musisz zwracać uwagi (ważne aby zmieścił się skrypt np wordpress). Płatne konta w granicach 3-5zł miesięcznie zapewniają odpowiednią ilość miejsca. W razie wzrostu popularności bloga zastanowisz się nad poszerzeniem oferty hostingowej. Nie ma sensu od samego początku płacić 30zł miesięcznie za hosting, skoro nie wiadomo jak będzie prosperował blog.

## Wybór systemu CMS

Nie możesz napisać własnego bloga, ponieważ zapewne nie umiesz programować. Z pomocą przyjdą Ci gotowe skrypty **służące do prowadzenia blogów**. Najbardziej znanym systemem zarządzania treścią dla blogów jest **skrypt WordPress**. Bardzo dużo blogów jak i zwykłych serwisów dostępnym w internecie jest postawionych właśnie na nim.

**WordPress jest dobrym wyborem** ponieważ jest prosty i łatwy do skonfigurowania. Udostępnia wiele gotowych funkcji i wtyczek a co najważniejsze **jest całkowicie darmowy!**

WordPressa możesz pobrać z adresu: (http://wordpress.org/download/)

## Podpięcie domeny do hostingu

Zaloguj się do swojego kotna hostingowego. Znajdź opcję **podepnij domenę** (może znajdować się w tzw. CPanel czyli panelu kontrolnym). Podpinasz taką samą domenę jaką zarejestrowałeś w pierwszym kroku, dajmy na to **mojadomena.pl**. Oprócz tego musisz ustawić hasło FTP dla domeny.

{% include parts/postPicture.html page=page img="1" %}

Po podpięciu domeny na serwerze, należy ustawić jej **odpowiednie adresy DNS**, tak aby wskazywały na Twoje konto hostingowe. Każda firma hostingowa ma inne **adresy DNS**. Dla przykładu firma proserwer posiada DNS: ns1.proserwer.pl oraz ns2.proserwer.pl. Informacje o adresach DNS zawsze są ogólnodostępne dla klientów.

Zaloguj się do serwisu gdzie zarejestrowałeś domenę i odszukaj opcję **Zmień adresy DNS**. Wpisz adresy DNS własnego hostingu i zapisz zmiany.

{% include parts/postPicture.html page=page img="2" %}

Zmiana adresów DNS może trwać nawet do 48 godzin, nie jest to zabieg natychmiastowy.

## Wgranie skryptu na serwer

Pobrany skrypt WordPressa należy wgrać na konto hostingowe, posługując się **klientem FTP.** Klient FTP umożliwia wysyłanie i pobieranie plików z Twojego serwera (hostingu) na Twój komputer. Dobrym klientem FTP jest program **FileZilla**. Dane do FTP znajdziesz logując się na konto hostingowe. Aby się **zalogować poprzez FileZille do konta FTP** potrzebujesz adresu serwera (może to być nazwa podpiętej domeny czyli **mojadomena.pl**), login użytkownika oraz hasło.

Pamiętaj, że dane którymi logujesz się do **klienta FTP**  są inne niż te, którymi logujesz się w panelu hostingu. Przykładowo: w firmie proserwer.pl dane logowania FTP znajdziesz w zakłdace: *moje serwery* > *admin* > zakładki *dane serwera* oraz *FTP*.

Dodatkiem znacznie ułatwiającym proces wgrywania silnika **WordPress** na konto hostingowe jest zakładka *instalator stron*. Jest to opcja pozwalająca na automatyczne zainstalowanie gotowych, znanych, darmowych **skryptów zarządzania treścią**. Do wyboru mamy zarówno **WordPress** jak i różne skrypty służące do prowadzenia for internetowych. Po wybraniu opcji instalacji automatycznej nie będziesz musiał martwić się ręcznym wgrywaniem plików **FileZillą.** Nie testowałem tego narzędzia, zawsze wgrywałem pliki WordPressa ręcznie.

{% include parts/postPicture.html page=page img="3" %}

Ponieważ zmiana DNS i podpięcie domeny trwa czasem do 48 godzin, dlatego jest kilka adresów serwera Twojego konta hostingowego. Pierwszym jest Twoja nowo podpięta domena, ale zawsze istnieją też inne darmowe z których mógł byś korzystać w wypadku kiedy nie kupujesz płatnej domeny.

Wszystkie pliki WordPressa **wgrywasz za pomocą klienta FTP** do katalogu */mojadomena.pl/public_html/*

## Tworzenie bazy danych

Każdy CMS korzysta z bazy danych MySQL. W CPanel odnajdź zakładkę **bazy danych MySQL** i utwórz bazę o dowolnej nazwie. Następnie musisz utworzyć użytkownika bazy danych wraz z hasłem. Kolejnym krokiem jest przypisanie utworzonego użytkownika do utworzonej wcześniej bazy. Po tym zabiegu **zapisz sobie** nazwę bazy danych, nazwę użytkownika bazy oraz jego hasło.

## Konfiguracja bloga

Wpisz w adresie przeglądarki nazwę Twojej domeny. Jeżeli wszystko poszło zgodnie z planem i odczekałeś 4-48h na rozgłoszenie adresów DNS domeny, powinieneś ujrzeć logo WordPressa i komunikat zachęcający do **rozpoczęcia konfiguracji.** Jeżeli widzisz biały ekran to prawdopodobnie wgrałeś pliki do złego katalogu klientem FTP. Jeżeli widzisz błąd, że strona nie została odnaleziona, **prawdopodobnie źle zaparkowałeś domenę** lub **źle ustawiłeś adresy DNS.**

Pierwsza konfiguracja WordPressa jest prosta. Najważniejszym krokiem jest wpisanie danych potrzebnych do połączenia z bazą danych MySQL. Host bazy danych (adres) to *localhost*, czyli adres tej samej maszyny na której stoi hosting. Ponieważ posiadasz hosting i bazę MySQL od tego samego operatora, możesz wpisać localhost, w przeciwnym wypadku mógł byś wpisać adres IP zewnętrznego serwera MySQL.

Po ukończeniu tej czynności, Twój blog jest gotowy do działania. Zaloguj się do panelu administratora, zmień szablon i zacznij dodawać artykuły!