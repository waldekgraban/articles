---
title: Dll Injection - wywoływanie funkcji z parametrami
category: cpp
thumbnail: dll-injection.png
published: 2013-02-23T00:00:00Z
---
Pierwszy artykuł o **dll injection** wywołał duże zainteresowanie. Wiele osób szuka informacji na temat pisania trainerów do gier, których główną funkcjonalnością jest wywoływanie funkcji gier w określonym momencie. W poprzednim artykule trochę kiepsko opisałem sam moment wywoływania funkcji. Funkcje wywoływane w pierwszym artykule są funkcjami bez parametrów. Dziś postaram się opisać **jak szukać parametrów funkcji** oraz jak je wywoływać.

<!--more-->

## Znajdywanie adresu funkcji z parametrami

Posłużmy się naszą ulubioną grą czyli **Saperem z WindowsXP**. Przykładową funkcją z parametrami jest funkcja ustawiająca flagi i pytajniki na określonym przez gracza polu. Gracz klika prawym przyciskiem na dowolne pole, funkcja przyjmuje jako argumenty: _int wiersz_, _int kolumna_ oraz _int typ_. Kolumna i wiersz mówią, które pole zostało kliknięte. Parametr _typ_ decyduje co ma być ustawione na kliknięte pole (pytajnik, flaga lub nic).

Uruchom Sapera oraz CheatEngine. Prawym przyciskiem myszy ustaw flagę na polu o współrzędnych (2,2). Podłącz CheatEngine do procesu gry. Przeskanuj pamięć gry dla *nieznanej wartości (unknown initial value)*. Interesująca nas wartość ma typ *byte.* Kliknij _FirstScan_. Gdy skanowanie zostanie zakończone, prawym przyciskiem myszy kliknij na zaznaczoną flagę w Saperze. Flaga zamieni się na pytajnik. Zmień typ skanu na *Changed value* (ponieważ wartość pola w pamięci została zmieniona kliknięciem) i kliknij _NextScan_. Po zakończonym skanowaniu, prawym przyciskiem myszy kliknij dwukrotnie na pytajnik, pytajnik zamieni się na flagę. Kliknij przycisk _NextScan_. Powtórz czynność kilka razy.

Nie zmieniaj wartości pola (2,2). Jeżeli jest tam flaga, zostaw flagę, jeżeli jest puste lub ma pytajnik, zostaw to co jest. Jeżeli wcześniej wykonałeś skan dla *Changed value*, teraz wykonaj skan dla *Unchanged value*. Czynność powtarzaj aż na liście nie zostanie tylko jeden adres.

Znaleziony adres reprezentuje wartość pola w pamięci.

{% include parts/postPicture.html page=page img="sac1" %}

Zmieniając stan pola [2,2] zmienia się także wartość pamięci pod adresem *0x01005382* Załóżmy pułapkę (breakpointa) na adres wartości pola. Kliknij prawym przyciskiem myszy na adres a następnie *Find out what writes to this address* a następnie potwierdź klikając w *Yes*. Kliknij kilka razy prawym przyciskiem myszy na pole *[2,2]*. Ustaw flagę, potem pytajnik i znowu flagę. W okienku breakpointa pojawił się adres funkcji ustawiającej flagi pytajniki itp.

{% include parts/postPicture.html page=page img="sac2" %}

Odpal **OllyDbg**. Przenieś i upuść ikoną Sapera do Olly. Naciśnij kilka razy *F9* lub naciśnij kilka razy przycisk *play*. Możesz przestać gdy pojawi się okienko z grą. Naciśnij *Ctrl+G* aby przenieść się pod podany adres. Jako adres wpisz adres znalezionej przez nas funkcji czyli *01002ECB*.

{% include parts/postPicture.html page=page img="sac3" %}

OllyDbg zaznacza nam czarną klamrą ciało funkcji. Zaznaczyłem to czerwoną klamrą aby było dobrze widać. Szara linia to wywołanie z pod naszego adresu. Niebieska strzałka to początek funkcji. Kliknij na linię oznaczoną niebieską strzałką. W okienku niżej (pod instrukcjami) pojawił się napis *Local calls from 0100358C, 0100362D, 010037BA*. Oznacza to że funkcja (zaznaczona czerwoną klamrą) wywoływana jest w tych trzech miejscach.

{% include parts/postPicture.html page=page img="sac4" %}

Kliknij prawym przyciskiem na napis *Local calls from..* a następnie na pozycję *Go to call from_ _010037BA*. Zostaniemy przeniesieni w miejsce gdzie wywołana jest funkcja.

{% include parts/postPicture.html page=page img="sac5" %}

Jak widzimy funkcja przyjmuje 3 argumenty a następnie jest wywoływana. Pierwsze dwa argumenty to kolumna i wiersz klikniętego pola, trzeci to typ. W miejscu zaznaczonym niebieską strzałką załóż **breakpointa** klawiszem *F2*. Zacznij grać w grę i ustaw gdzieś flagę. Breakpoint działa. Naciśnij *F9* aby ruszyć aplikację i *F2* aby zdjąć breakpointa.

Na potrzeby artykułu wcześniej sprawdziłem poprzednie dwa adresy gdzie funkcja była także wywoływana. W pierwszej pozycji były także 3 argumenty, jednak ustawiony breakpoint nie zatrzymywał aplikacji podczas ustawiania flagi w grze. W adresie numer dwa, funkcja była wywoływana tylko z dwoma argumentami co także nas nie zadowala. W adresie numer trzy działa breakpoint oraz funkcja używa trzech parametrów.

Adres *0x1002EAB* jest adresem szukanej przez nas funkcji ustawiającej flagi, pytajniki itp.

Uwaga! Adres _10037BA_ to adres CALLA wywołującego funkcję ustawiającą flagi. Ten adres jest nam potrzebny aby śledzić z jakimi argumentami funkcja jest wywoływana. Adresem funkcji, która ustawia flagi to natomiast *1002EAB*!

## Śledzenie argumentów

Nie jesteśmy skazani na zgadywanie. Ja odrzuciłem poprzednie dwa adresy ponieważ zbadałem je wcześniej dokładnie metodą, którą opiszę. Zbadajmy CALL, który prowadzi do naszej funkcji, aby przekonać się za co odpowiada i z jakimi argumentami wywołuje funkcję. Kliknij prawym przyciskiem myszy na adres *0x10037BA* (niebieska strzałka). Wybierz z menu *Breakpoint > Conditional Log*. Pojawi się okienko. Pola tekstowe zostaw puste. Opcję _‚Pause Program’_ ustaw na *never*. Opcję _‚Log value of expresion’_ ustaw na *never*. Opcję *Log function arguments* ustaw na *always*. Kliknij OK. Zwykły breakpoint świeci się na czerwono, ustawiony przez nas ma kolor różowy. Kliknij w menu programu *Viev > Log*. Pojawi się okienko z logami. W głównym oknie OllyDbg naciśnij kilka razy *F9* aby wystartować Sapera. W oknie sapera kliknij kilka razy na różne pola prawym przyciskiem myszy. Zauważ że w logu zostały zapisane argumenty funkcji.

{% include parts/postPicture.html page=page img="sac6" %}

Jest tak jak pisałem wcześniej. _Arg1_ oraz _Arg2_ to współrzędne klikniętego kwadratu. _Arg3_ to wartość jaka ma być ustawiona. Wartości możesz sprawdzić w CheatEngine. Flaga oznaczana jest jako **_0xE_**.

W ten sposób możesz śledzić argumenty wszystkich wywoływanych funkcji w programie OllyDbg. Nawet jeżeli nie wiesz za co odpowiedzialna jest funkcja, możesz się tego domyślić śledząc jej argumenty. Różowego breakpointa zapisującego argumenty można ustawić tylko na Callach, czyli na wywołaniach funkcji. Np. na operacji _MOV_ lub _AND_ nie będziesz mógł śledzić argumentów funkcji.

## Wywołanie funkcji z parametrami przez Dll Injection

Posiadamy adres funkcji, upewniliśmy się dzięki śledzeniu argumentów za co jest odpowiedzialna. Funkcję można wywołać w prosty sposób za pomocą Dll Injection.

W tym celu musimy utworzyć wskaźnik do funkcji. Dokładną metodę tworzenia **wskaźników na funkcje **opisałem w artykule [Ukrywanie funkcji WinApi](http://www.p-programowanie.pl/cpp/ukrywanie-funkcji-winapi/).

Każdy wskaźnik na funkcję poprzedzamy rozkazem *typedef*. Następnie podajemy co zwraca funkcja w naszym przypadku może być *void*. Otwieramy nawias, piszemy gwiazdkę a po gwiazdce nazwę wskaźnika. W przypadku DLL Injection wymagane jest także dodanie *__stdcall* przed gwiazdką lub *__thiscall*. W następnym nawiasie ustawiamy ilość parametrów, wymieniając po przecinku **ich typy (bez nazw!).**

	typedef void (__stdcall* ustaw_wsk)(int,int, int);

Wskaźnik gotowy. Teraz musimy przypisać go do naszego adresu. Wskaźnik naszej funkcji jest także typem. Sprawa jest prosta, piszemy typ, czyli nazwę naszego wskaźnika, następnie nazwę funkcji. Do nazwy funkcji przypisujemy adres, jednak wymagane jest rzutowanie na typ wskaźnikowy.

	ustaw_wsk Ustaw = (ustaw_wsk)0x01002EAB;

Aby użyć funkcji posługujemy się nazwą **Ustaw**. Nie można zapomnieć o trzech argumentach. Całość zaimplementowana już w DLLce wygląda tak:

	#include "main.h"

	void funkcja()
	{
	    typedef void (__stdcall* ustaw_wsk)(int,int, int);
	    ustaw_wsk Ustaw = (ustaw_wsk)0x01002EAB;
	    
	    Ustaw(1,1, 0xE);
	    Ustaw(2,2, 0xE);
	    Ustaw(3,3, 0xE);
	}

	extern "C" DLL_EXPORT BOOL APIENTRY DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpvReserved)
	{
	    switch (fdwReason)
	    {
	        case DLL_PROCESS_ATTACH:
					    funkcja();
	            break;
	    }
	    return TRUE;
	}

Nie będę pisał loadera DLLki. Do wstrzyknięcia posłużę się programem **Winject.** Jest to bardzo dobry program, za pomocą którego możemy wstrzyknąć DLLkę do dowolnego procesu.

{% include parts/postPicture.html page=page img="sac7" %}

Po wstrzyknięciu funkcja zostaje wywołana ustawiając flagi w pozycję *[1,1]*, *[2,2]* oraz *[3,3]*.