---
title: Git - podstawowe informacje
category: git
thumbnail: git-rozpoczecie-pracy.png
published: 2020-01-12T00:00:00Z
---
GIT to jeden z najpopularniejszych systemów kontroli wersji. Jest bardzo znany w świecie programowania, właściwie we wszystkich krajach. Sprawne posługiwanie się systemem kontroli wersji GIT to jedna z podstawowych umiejętności jakiej potrzebuje początkujący programista.

<!--more-->

## Czym jest GIT?

Git to jedno z wielu dostępnych narzędzi kontroli wersji. Służy do **śledzenia zmian w kodzie oprogramowania**. Nie należy jednak kojarzyć systemów kontroli wersji jedynie z wytwarzaniem oprogramowania. Można ich z powodzeniem używać wszędzie tam, gdzie istnieje potrzeba śledzenia zmian treści jakiś plików.

**Git to system kontroli wersji**, bardzo popularny wśród programistów. Jest całkowicie darmowy. Jego zaletą jest działanie w architekturze rozproszonej - co wpływa na jego niezawodność.
{: .alert-info} 

Niewątpliwie przewagą Gita jest jego popularność oraz dostępność. Jest to oprogramowanie darmowe, stworzone przez twórcę Linuksa. Używanie Gita jest zupełnie darmowe zarówno do zastosowań prywatnych jak i komercyjnych.

## Rozproszona architektura

Dużą ważną zaletą Gita jest **rozproszona architektura**. Polega ona na tym, że repozytoria znajdują się na wielu komputerach, a nie na jednym scentralizowanym serwerze (jak np. w przypadku SVN).

{% include parts/postPicture.html page=page img="git-architektura" %}

W przypadku repozytoriów scentralizowanych (takich jak np. SVN) **awaria serwera danych oznacza utratę wszystkich danych**. Informacji nie da się odtworzyć z komputerów użytkowników tych repozytoriów.

W systemie kontroli wersji Git każdy komputer, który pobierze określone repozytorium, zawiera jego pełną treść oraz historię. W przypadku awarii któregoś z komputerów historię można odtworzyć z każdego innego. Dzięki tej dogodności repozytoria Gita nie wymagają tworzenia kopii zapasowych (no chyba, że w określonym repo pracuje tylko jeden programista).

## Git vs Github

Git sam w sobie jest aplikacją konsolową i nie posiada żadnego graficznego ani webowego interfejsu. Możesz pracować w Gicie lokalnie lub łącząc się z innym komputerem poprzez sieć.

Aby usprawnić pracę użytkownikom Gita powstało wiele serwisów hostujących repozytoria Gitowe. Do najpopularniejszych należą:

- github.com
- gitlab.com
- bitbucket.org

Dzięki tym serwisom możesz zarządzać swoimi repozytoriami z poziomu wygodnego, webowego interfejsu. Dodatkowo w bardzo ładny sposób prezentują one historię zmian, treść plików, hierarchię plików, członków danego repozytorium i tak dalej. Wprowadzają także warstwę organizacyjną i autoryzacyjną.

Kolejną dodatkową funkcją w.w. serwisów jest **pełnienie roli hostingu**. Gdybyś hipotetycznie pracował w swoim repozytorium sam, w przypadku awarii komputera mógłbyś odzyskać dane z drugiego komputera, który w tym wypadku byłby np. *githubem*.

Najpopularniejszym serwisem jest *github*. Jednak warto zastanowić się także nad *gitlabem*, ponieważ on udostępnia możliwość tworzenia prywatnych repozytoriów całkiem za darmo.

### Rola githuba

Tak jak zostało napisane wyżej, Git działa w architekturze rozproszonej. Mógłbyś pracować nad jakimś repozytorium razem ze swoim kolegą i razem z nim się synchronizować. Trzeba jednak podkreślić, że taki tryb pracy byłby totalnie niewygodny. Synchronizacja z wieloma osobami mogłaby okazać się niemożliwa ze względów logistycznych.

Z tego powodu serwisy hostujące repozytoria Gita pełnią ważną rolę synchronizującą.

{% include parts/postPicture.html page=page img="git-github" %}

Mimo że synchronizacja horyzontalnie pomiędzy użytkownikami byłaby możliwa (Git jest rozproszony), to **takie praktyki nie są stosowane**. Zamiast tego wielu użytkowników repozytorium synchronizuje się używając zewnętrznego serwera.

Warto też podkreślić, że mimo używania hostingu dla repozytoriów **nie tracimy zalet rozproszonego repo**. Synchronizacja np. poprzez *githuba* niewątpliwie niesie wiele korzyści, jednak nic nie stoi na przeszkodzie, aby z repozytorium użytkownika *komp1* odtworzyć całe repo, nawet gdy awarii ulegną wszyscy pozostali użytkownicy oraz *github*.

## Klient programu Git

Ostatnią ważną rzeczą przed rozpoczęciem pracy z Gitem jest wybór klienta. Oprogramowanie **Git obsługuje się z linii poleceń, dlatego dla wielu użytkowników może być to niewygodne**. Aby wyeliminować ten problem powstało wiele programów będących nakładką graficzną na konsolę. Bardzo popularnym i darmowym klientem gita jest *TortoiseGit*.

Co ciekawe obsługa klienta Gita jest wbudowana w wiele środowisk programistycznych np. *Visual Studio*, *Visual Studio Code* czy też *WebStorm*. Używając takich nowożytnych środowisk możesz korzystać z Gita posługując się graficznym interfejsem, i nie musisz instalować dodatkowych klientów.

### Konsola jako jedyny słuszny wybór

Jeżeli masz w planach uczyć się Gita i mógłbym Ci coś doradzić, **zachęcam do jego nauki wyłącznie z poziomu linii komend**. Na początku może wydawać Ci się to niewykonalne - jednak naprawdę - niesie to wiele korzyści.

Osobiście nie jestem typem konsolowca. Jako programista .NETa obracam się główne w świecie kolorowego Windowsa, a nie Linuxowej konsoli. Mimo to - moim zdaniem - umiejętność obsługi GITa z konsoli to jedna z najlepszych umiejętności jakie możesz posiadać w świecie IT.

Nakładki graficzne na Gita (tzw. klienci Gita) wysyłają do serwera różne komendy, których nie widzisz i nie rozumiesz. Normalnie musiałbyś te komendy wpisywać w konsoli, jednak klient Gita potrafi zrobić to za Ciebie w tle. Mimo, że początkowo nauka interfejsu graficznego przebiega o wiele szybciej, to jednak końcowy efekt nie jest taki zadowalający.

Oto lista moich argumentów przeciw używaniu Gita z poziomu programu graficznego:

- każda zmiana pracy/komputera wymaga instalacji klienta Gita, którego Ty umiesz (a nie wszędzie jest to możliwe)
- nakładki graficzne nie obsługują wszystkich funkcji Gita, które można zrobić z konsoli (załóżmy 80-90%)
- używając graficznego interfejsu - powiedzmy to szczerze - nawet po 5 latach ciągle nie będziesz wiedział jak działa Git
- cokolwiek zepsuje się w Twoim repo: będziesz musiał wołać konsolowego wymiatacza, który naprawi Twój problem
- cokolwiek zepsuje się w Twoim repo: wszystkie odpowiedzi na stackoverflow.com to sekwencje komend, których należy użyć, aby naprawić problem. Dobrze je rozumieć, aby nie wywalić repo w kosmos..
- pewnych rzeczy nie da się osiągnąć z interfejsu graficznego, jeszcze niedawno była to praca na repo opartym o *submodule* (nie wiem jak teraz)

Zauważyłem też, że ludzie obsługujący Gita z programu graficznego strasznie syfią repo. Nie za bardzo znają różnice pomiędzy *merge*, *rebase*, *squash*.

## Gdzie się uczyć i jak zacząć

Gita można pobrać za darmo na stronie <https://git-scm.com/downloads>. Dość fajna instrukcja w języku polskim znajduje się na tej samej stronie i jest dostępna tutaj: <https://git-scm.com/book/pl/v2>.

Oprócz tego warto posiłkować się materiałami dostępnymi za darmo w internecie.