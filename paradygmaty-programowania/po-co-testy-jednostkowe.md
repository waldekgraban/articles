---
title: Po co testy jednostkowe?
category: paradygmaty-programowania
thumbnail: testowanie-oprogramowania.png
published: 2017-04-22T00:00:00Z
---
Wielu programistów ma bardzo **różne podejście do testów** jednostkowych. Niektórzy piszą, bo muszą. Inni nie lubią lub nie rozumieją, trzymają się z daleka. **Co tak naprawdę dają nam testy jednostkowe** i czy warto zaprzątać sobie nimi głowę?

<!--more-->

## Czym są testy jednostkowe?

Testy jednostkowe to metoda testowania wytwarzanego oprogramowania, polegająca na pisaniu metod testujących określone, małe fragmenty naszego programu (jednostki). Jednostkami mogą być np. metody lub klasy. Kończąc studia informatyczne lub zaczynając przygodę z programowaniem ciężko dostrzec **zalety pisania testów jednostkowych**. Wszystko wydaje się niby proste, jednak nasuwa się pytanie: **po co testować coś, co sami piszemy?** Brzmi to paradoksalnie jak np. sprawdzanie poprawności zadania matematycznego rozwiązanego przez nas samych.

Dodatkowym zniechęceniem do pisania testów mogą okazać się cechy testów jednostkowych takie jak:

- żadnego oprogramowania **nie jesteśmy przetestować całkowicie**
- nigdy nie mamy pewności znalezienia wszystkich błędów, nawet gdyby pokrycie testami wynosiło 100% (co jest nierealne)
- testy wydłużają **czas wytwarzania oprogramowania praktycznie nawet o połowę**

Po tych wszystkich rozważaniach nasuwa się pytanie: po co?

## Kiedy warto testować

Przede wszystkim należy uzmysłowić sobie kiedy warto pisać testy jednostkowe do aplikacji. Jeżeli piszemy prosty programik i mamy pewność, że **będziemy rozwijać go samemu**, to testy raczej nie będą potrzebne. Nie będą potrzebne także w przypadku programów, które chcemy napisać  
"raz i zostawić", nie nastawiamy się na ich systematyczny rozwój.

### Duże projekty i wielu programistów

Całkiem inaczej w dużych projektach pisanych przez kilka osób. Tworzenie dużego projektu niekiedy może przypominać spagetti. Każdy programista dopisuje do projektu swoje własne metody i klasy, rozwija istniejące funkcje i dodaje nowe. W takim przypadku **brak testów może w przyszłości nieść wiele negatywnych skutków**.

Każdy programista ma swój styl kodowania, który dodatkowo zmienia się pod wpływem czasu i doświadczenia. Jeden kod napisany przed dwóch różnych programistów, działający tak samo, **będzie prawdopodobnie wyglądał inaczej**. Przykładem może być proste przekazywanie argumentów do funkcji.

### Odmienne style pisania kodu

Wyobraź sobie sytuację, w której musisz napisać funkcję wypisującą na ekran informacje o osobie. Jeden programista zadeklaruje ją następująco: `void PrintPersonInfo(Person person)` a inny tak: `void PrintPersonInfo(string name, string lastname, int age)`. Niby to samo, a jednak nie.

Będąc programistą dużego projektu, **musisz wiedzieć, że spotkasz się z sytuacjami takimi jak powyższa**. Wynika to z różnego poziomu doświadczenia współpracowników a także choćby z ich języków bazowych z jakich się wywodzą.

Kiedy ktoś użyje typów prostych **raczej nic się nie wysypie pod wpływem czasu**. Jeżeli ktoś użyje typu złożonego takiego jak klasa `Person`, to nikt tak naprawdę nie wie, jak ta klasa będzie wyglądać za 4 miesiące lub za rok. Jeżeli na dodatek będzie ona wykorzystana jako model warstwy biznesowej można być pewnym bugów.

W takich momentach niezbędne są testy. Współpracując z wieloma osobami **nie jesteśmy w stanie wpływać na jakość ich kodu**. Testy jednostkowe gwarantują nam to, że nie musimy. W minimalnym stopniu **chronią nas one przed zmianami, które zajdą w projekcie za wiele miesięcy lub lat**. Mimo, że my o czymś zapomnimy a inny programista nie będzie wiedział, że element systemu, który reużywa jest używany gdzieś indziej, to testy będą pamiętać o tym za nas.

## Jakie ma być pokrycie kodu testami?

Pisanie testów wydłuża proces tworzenia oprogramowania. M.in. z tego powodu **nie jest realne aby projekt był w 100% pokryty testami**. Jest to niepotrzebne, wręcz może prowadzić do błędów. To ile testów napisać zależy od czasu jakim dysponujemy i przede wszystkim od przeczucia. Warto testować fragmenty kodu, które są **bardzo newralgiczne i używane w wielu miejscach**.

Podobno dobre pokrycie testami to 40%, jednak czy ta informacje coś mówi? Można mieć duże pokrycie testami a żadnych korzyści, lub małe pokrycie testami i wielkie korzyści. Zależy to od jakości testów i elementów, które testujemy.

## Testowanie regresyjne

W jednym z projektów (ERP) jaki współtworzyłem nie pisaliśmy testów jednostkowych. Cały projekt w miarę działał **dopóki nie pojawiła się magiczna funkcja kolejki zadań**. Kolejka była fragmentem systemu działającym dosłownie wszędzie. Każda nowa funkcja, która się pojawiała, w jakiejś części musiała zintegrować się z kolejką.

Pomijając fakt, że kolejka była źle zaprojektowana przez programistę, który już nie pracował, **nie dało się jej nawet dotknąć**. Jedna zmiana wysypywała stare funkcje systemu przetestowane wiele miesięcy wcześniej. Nie dało się jej dotknąć w znaczeniu jak najbardziej dosłownym. Dopisanie nowego parametru lub dodanie nowego typu zadania, psuło funkcje systemu zaimplementowane 8 miesięcy wcześniej.

Pomyślisz "no dobrze, nie ma testów jednostkowych więc trzeba testować ręcznie?" - otóż nie. Nie da się przetestować wszystkich scenariuszy systemu tworzonego latami przez kilku/kilkunastu programistów. W tak dużym i złożonym systemie dopisanie jednej funkcji "do kolejki" wymagałoby 2 dni testowania aplikacji. Drobna poprawa funkcji? **Kolejne dwa dni testowania**. Dopisujemy nową funkcję? Kolejne kilka dni testowania. Tak wyglądający proces testowania oprogramowania jest kosmiczny.

Lekarstwem byłyby tutaj testy jednostkowe. Ich pokrycie mogłoby wynosić chociaż 8% projektu. Ważne, aby pokryta była bardzo wrażliwa funkcja systemu, z której korzysta wiele elementów systemu. Zmieniając lub dopisując coś po wielu miesiącach wystarczyłoby odpalić testy, aby przekonać się czy nie zepsuliśmy kogoś pracy.

## Testy wymagają pisania przemyślanego kodu

Kolejną zaletą testów jednostkowych jest pisanie mądrego kodu. Na studiach lub świeżo po studiach nic na ten temat nie wiadomo, jednak po kilku miesiącach pracy zawodowej zaczyna się poznawać wiedzę tajemną. Otóż, **nie wszystkie fragmenty kodu da się pokryć testami**. Aby dało się napisać test jednostkowy, **kod programu musi być testowalny**.

Przykładem platformy programistycznej, która umożliwia pisanie nietestowalnego kodu jest ASP.NET MVC. Nie wiem jak będzie wyglądać sytuacja w .NET Core, jednak MVC w żaden sposób nie wymusza na programiście eleganckich rozwiązań. Jeżeli nie pomyślisz (lub ktoś w zespole nie pomyśli) i **nie wprowadzisz do projektu wzorca repozytorium** z mechanizmem _Inversion of Control_ (czyli odwrócenia zależności np. _dependency injection_) to wasz kod będzie absolutnie nietestowalny. Jeżeli ktoś **zacznie używać klas statycznych**, pisać tzw. klasy helperki czyli niby do wszystkiego, niby do niczego, to wasz kod będzie absolutnie nietestowalny.

Każdy obiekt, z którego chcesz skorzystać, powinien zostać w jakiś sposób wstrzyknięty. Powinien spełniać jakiś interfejs i być od niego zależny (zasady SOLID). Dzięki interfejsom można napisać test i zamockować np. obiekt bazy danych, lub warstwę repozytorium.

Miłym zaskoczeniem było dla mnie tutaj zetknięcie się z platformą Angular, w której czy chcesz, czy nie chcesz, **musisz korzystać z mechanizmu wstrzykiwania zależności**. Idąc dalej, praktycznie zawsze jesteś w stanie przetestować swoje serwisy i kontrolery, co np. nie uda się w ASP.NET MVC.

Jeżeli w projekcie macie testy, to programista pisząc bubla szybko zorientuje się, że tak to działać nie może, ponieważ nie da się tego przetestować. Wymusi to na nim konieczność refaktoryzacji lub ponownego zaimplementowania funkcji.

## Szkoda czasu na testy? Oj nie..

Jest to najpiękniejszy argument za pisaniem testów jednostkowych, z którym spotkałem się na szkoleniu z TDD, oraz odczułem jego skutki na własnej skórze. Możesz myśleć, że **szkoda Ci czasu na pisanie testów**, jednak cały ten **czas zwróci się z nawiązką**.

Zadanie, które możesz napisać w 5h z testami napiszesz bez testów w 3h. Pozornie zaoszczędziłeś 2h pracy, jednak napisałeś nietestowanego/nietestowalnego bubla. Teraz każdy inny programista, który będzie musiał w jakiś sposób skorzystać z Twoich metod, klas, modułów, **będzie musiał przeprowadzać testy ręczne**. Będzie musiał linijka po linijce starać zrozumieć się, dlaczego w 97 linijce jest jakaś dziwna instrukcja warunkowa, sprawdzająca czy zmienna _priceDiff_ jest równa zero, i dlaczego jak jest równa zero to wyskakujesz z obiektu pętli. Jeżeli po jakimś czasie da radę się domyślić - pół biedy. Jeżeli nie da rady się domyślić a projekt skompiluje się bez błędów, to ktoś i tak zmarnuje czas szukając później błędów w działaniu produkcji.

## Brak testów to brak możliwości refaktoryzacji

Niezależnie od poziomu naszej naszej wiedzy oraz od naszego doświadczenia, każdy każdy programista ma świadomość **konieczności systematycznej refaktoryzacji pisanego kodu**. Refaktoryzacja i optymalizacja są tematami tak obszernymi, że należałoby o nich napisać osobny artykuł.

Warto jednak brać pod uwagę, że refaktoryzacja w dużym projekcie, który nie ma testów, jest po prostu całkowicie niemożliwa. Bez testów programista może sobie pozwolić najwyżej na pozmienianie nazw zmiennych, ponieważ jakiekolwiek próby rozbicia klas na osobne podklasy prawdopodobnie zakończą się powstaniem błędów.

## Podsumowanie

W każdym z wypadków wiele czasu da się oszczędzić **świadomie pisząc testy jednostkowe** dla odpowiednich elementów systemu. Nie chodzi tu o to, aby starać się być z testami w granicach 30-40% pokrycia. To tylko wskaźnik, który może mówić wszystko albo nic. W sztuce pisania testów **należy wiedzieć co testować i gdzie testy będą niezbędne**, a gdzie okażą się stratą czasu.

**Czy pisanie testów wydłuża proces pisania oprogramowania?** - zdecydowanie tak, dlatego testy trzeba pisać mądrze, pokrywać testami tylko wrażliwe elementy systemu.

**Po co testować coś co sami napisaliśmy?** Nie piszemy testu, aby przetestować własny kod. Testy piszemy na zapas, to jak inwestycja na przyszłość. Test nie ma na celu wychwycenia naszych błędów, tylko ewentualne błędy podczas przyszłej refaktoryzacji (szczególnie jeżeli wykona ją ktoś inny niż my). Jeżeli tworzymy jakąś funkcję np. kalkulator walut, zakładamy że jesteśmy jedyną osobą, która ma wiedzieć jak ma ten kalkulator działać. Inni nie wiedzą, dlaczego trzeba pokryć go testami.

**Pisanie testów zawsze zaprocentuje w przyszłości**, chyba że piszemy testy "sztuka dla sztuki", pokrywamy testami niepotrzebne elementy.