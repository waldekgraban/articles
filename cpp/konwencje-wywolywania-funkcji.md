---
title: Konwencje wywoływania funkcji
category: cpp
thumbnail: konwencje-funkcji.png
published: 2014-08-09T00:00:00Z
---
Krótki artykuł opisujący trzy podstawowe **konwencje wywoływania funkcji C++** (a jest ich więcej). Konwencje wywoływania funkcji nie są tematem, na który można się szeroko rozpisać, jednak należy znać i odróżniać ich podstawowe rodzaje, szczególnie bawiąc się w **reverse engineering**.

<!--more-->

## Informacje wstępne

Jak to zwykle bywa, do napisania artykułu skłoniło mnie zapotrzebowanie czytelników. Na wielu forach internetowych można przeczytać o problemach osób, które nie radzą sobie z edycją jakiś prostych funkcji (chodzi o edycję na poziomie debbugera).

Zahaczając o temat **inżynierii wstecznej** konwencje wywoływania funkcji należy znać. O ile operowanie nimi pisząc program w C++ nie niesie większych zmian widocznych gołym okiem, o tyle **ciało funkcji zmienia się podczas procesu debugowania.**

## Wskaźnik stosu

Praktycznie całe zamieszanie dotyczące **konwencji wywoływania funkcji** kręci się w okół wskaźnika stosu.

**Stos** jest liniową strukturą danych używaną przez procesor, do **przechowywania zmiennych lokalnych**, **zapamiętywania stanów rejestrów** oraz do **przekazywania argumentów** do funkcji. Dane dodane na stos jako ostatnie, muszą być ściągnięte jako pierwsze.
{: .alert-info} 

Rejestr *ESP* wskazuje na wierzchołek stosu. Aby lepiej zobrazować sytuację, wyobraź sobie rejestr *ESP* jako wskaźnik z jakąś wartością arytmetyczną. Jeżeli *ESP* wynosi *512*, podczas dodania słowa na stos rozkazem *POP* wskaźnik stosu powiększy się z *512* na *508*. Arytmetyczna wartość jest mniejsza, ale rejestr *ESP* rośnie w stronę zera, aż na końcu nastąpi przepełnienie stosu.

Wywołując dowolny *CALL* wrzucamy argumenty na stos rozkazami *PUSH*. Argumenty zawsze wrzucamy w odwrotnej kolejności, wynika to z zasady odczytywania ze stosu. W chwili kiedy program zaczyna wykonywać instrukcję *CALL*, **wrzuca ona na stos adres powrotu**, z którego później skorzysta funkcja *RET*. Biorąc ten fakt pod uwagę, niezbędne jest zachowanie porządku w rejestrze *ESP* wskazującym wierzchołek stosu. Po wrzuceniu kilku argumentów dla rozkazu *CALL*, niezbędne jest ściągnięcie ich ze stosu rozkazami *POP* lub arytmetyczne przesunięcie wskaźnika stosu *ESP*. W przeciwnym razie po wywołaniu *CALL* (skoku) do dowolnego miejsca, program nie znajdzie instrukcji powrotu.

	PUSH 50          ;argument na stos
	PUSH 50          ;argument na stos
	CALL funkcja1    ;wywołanie funkcji
	add esp, 8       ;przesuwamy stos 4*2=8
	
	PUSH 1           ;argument na stos
	PUSH 2           ;argument na stos
	PUSH eax         ;argument na stos
	CALL funkcja2    ;wywołanie funkcji
	add esp, 12      ;przesuwamy stos 4*3=12

Ponieważ pojedyncze słowo *word* jest *32* bitowe, zwiększamy wskaźnik stosu (właściwie zmniejszamy, ponieważ rośnie on w stronę zera) o *4* bajty. Dla dwóch argumentów, trzeba zmniejszyć wskaźnik stosu już o 8 bajtów itd.

## Konwencje wywoływania funkcji

W różnych **konwencjach wywoływania funkcji** stos jest czyszczony z argumentów w różny sposób.

- **stdcall** - stos za każdym razem czyści funkcja wywoływana
- **cdecl** - stos za każdym razem czyści obiekt, na rzecz którego wywołujemy funkcje
- **fastcall** - nie korzystamy ze stosu

### Konwencja stdcall

Jak podaje MSDN, konwencja **stdcall** jest standardową, jeżeli chodzi o wywoływania funkcji Win32 API. Oznacza to, że nawet jeżeli nie wybierzemy jawnie żadnej konwencji, funkcja automatycznie zostanie wywołana z konwencją **stdcall**. Argumenty przekazywane do funkcji są standardowo umieszczane na stosie.

	#include <iostream>
	
	using namespace std;
	
	void __stdcall potega (int x)
	{
	    cout << "Wynik: " << x*x << endl;
	}
	
	int main()
	{
	    potega(5);
	    return 0;
	}

Cechą charakterystyczną jest to, że **funkcja sama sprząta stos** po jej wywołaniu. Stos czyszczony jest podczas zakończenia funkcji przez rozkaz *retn x*, gdzie *x* oznacza ilość parametrów na stosie. Oto ten sam program przepisany do assemblera (FASM):

	format PE Console 4.0
	include "win32a.inc"
	
	;zauważ, że po wywołaniu (7) funkcji nie przesuwamy wskaźnika stosu
	
	push 5    ;wrzucamy argument na stos
	call potega    ;wywołujemy funkcje
	
	mov eax, 0    ;koniec programu
	ret
	
	potega:
	    mov eax, dword [esp+4]    ;argument do EAX
	    mul eax    ;mnożymy x*x
	    push eax    ;wynik na stos
	    push napis    ;napis na stos
	    call [printf]    ;wyświetlamy napis
	    add esp, 8    ;przesuwamy wskaźnik stosu
	retn 4    ;czyscimy stos przesuwając wskaźnik
	
	napis db "Wynik: %i", 0Ah, 0 ;wyświetlany napis
	
	data import ;importy wymaganych funkcji
	    library msvcrt, 'msvcrt.dll'
	    import msvcrt, printf, 'printf', scanf, 'scanf'
	end data

Widać, że wskaźnik stosu został przesunięty podczas kończenia funkcji. Zaletą **stdcall** jest szybkość działania i mniejsza ilość zajętego miejsca. Jeżeli funkcję wywołujemy dziesiątki razy w różnych miejscach programu, **stos i tak czyszczony jest tylko raz** wewnątrz funkcji.

Podczas debugowania kodu, możemy zauważyć, że funkcje w konwencji **stdcall** zaczynają się od znaku podkreślenia, na końcu dodawana jest małpa oraz ilość przekazywanych do funkcji argumentów:

	_potega@1

Funkcje **stdcall** rozpoznajemy właśnie po tym, że zawsze na ich końcu znajduje się *retn x* lub *ret x*.

### Konwencja cdecl

Konwencja **cdecl** jest drugą najczęściej spotykaną konwencją wywoływania funkcji. Była bardzo często wykorzystywana w języku C. Charakteryzuje się tym, że stos musi zostać wyczyszczony przez program w miejscu wywołania funkcji, a nie przez samą funkcję. Argumenty do funkcji przekazywane są na stosie:

	#include <iostream>
	
	using namespace std;
	
	void __cdecl potega (int x)
	{
	    cout << "Wynik: " << x*x << endl;
	}
	
	int main()
	{
	    potega(5);
	    return 0;
	}

Ponieważ stos nie jest czyszczony wewnątrz funkcji podczas jej zakończenia (tak jak w przypadku stdcall), może się okazać, że kod programu znacznie wzrośnie. Stanie się tak przede wszystkim wtedy, jeżeli będziemy posiadać wiele wywołań funkcji.

Szczególnym zastosowaniem konwencji **cdecl** jest sytuacja, kiedy wywołujemy **funkcję ze zmienną ilością parametrów**. Przykładem takiej funkcji jest np. *printf()*. Ponieważ, możemy umieścić w niej dowolną ilość argumentów, funkcja nie może zajmować się czyszczeniem stosu &#8211; nigdy nie wie ile parametrów zostanie do niej przekazane. **Czyszczeniem stosu**, czyli przewijaniem wskaźnika stosu, **zajmuje się obiekt, na rzecz którego została wywołana funkcja** **cdecl**:

	format PE Console 4.0
	include "win32a.inc"
	
	;zauważ, że po wywołaniu (7) funkcji musimy sami wyczyścić stos
	
	push 5    ;wrzucamy argument na stos
	call potega    ;wywołujemy funkcje
	add esp, 4    ;sami czyścimy stos
	
	mov eax, 0    ;koniec programu
	ret
	
	potega:
	    mov eax, dword [esp+4]    ;argument do EAX
	    mul eax    ;mnożymy x*x
	    push eax    ;wynik na stos
	    push napis    ;napis na stos
	    call [printf]    ;wyświetlamy napis
	    add esp, 8    ;przesuwamy wskaźnik stosu
	ret    ;zakonczenie funkcji i skok
	
	napis db "Wynik: %i", 0Ah, 0 ;wyświetlany napis
	
	data import ;importy wymaganych funkcji
	    library msvcrt, 'msvcrt.dll'
	    import msvcrt, printf, 'printf', scanf, 'scanf'
	end data

Podczas debugowania zauważysz, że funkcje **cdecl** zaczynają się od znaku podkreślenia:

	_potega

Funkcje **cdecl** rozpoznajemy po tym, że na ich końcu *ret* nie posiada żadnego argumentu, jednak nie można pomylić jej z **fastcall**. Aby tego nie zrobić, trzeba się upewnić, że argumenty nie są pobierane z rejestrów.

### Konwencja fastcall

Generalną zasadą konwencji **fastcall**, jest **przekazywanie argumentów poprzez rejestry**, a nie poprzez stos tak jak w wypadku innych funkcji. To jakie rejestry będą używane, zależy od kompilatora &#8211; wszystkie używają innych standardów. Zarówno *GCC* jak i *MVC* działają w tym wypadku na tej samej zasadzie. Dwa pierwsze parametry przesyłane są kolejno w rejestrach *ECX* oraz *EDX*, a wszystkie następne już ze stosu (jeśli zajdzie potrzeba wykorzystania większej ilości parametrów).

	#include <iostream>
	
	using namespace std;
	
	void __fastcall potega (int x)
	{
	    cout << "Wynik: " << x*x << endl;
	}
	
	int main()
	{
	    potega(5);
	    return 0;
	}

Używając **fastcall** program może znacznie przyśpieszyć, w przypadku wywoływania wielu funkcji z małą ilością parametrów. Program **nie operuje wtedy na pamięci**, a jedynie na rejestrach, które są szybsze.

	format PE Console 4.0
	include "win32a.inc"
	
	;nie uzywamy stosu do przekazywania parametru
	
	mov eax, 5    ;wrzucamy argument do EAX
	call potega    ;wywołujemy funkcje
	
	mov eax, 0    ;koniec programu
	ret
	
	potega:
	    mul eax    ;mnożymy x*x
	    push eax    ;wynik na stos
	    push napis    ;napis na stos
	    call [printf]    ;wyświetlamy napis
	    add esp, 8    ;przesuwamy wskaźnik stosu
	ret    ;zakonczenie funkcji i skok
	
	napis db "Wynik: %i", 0Ah, 0 ;wyświetlany napis
	
	data import ;importy wymaganych funkcji
	    library msvcrt, 'msvcrt.dll'
	    import msvcrt, printf, 'printf', scanf, 'scanf'
	end data

Używając **fastcall** stosu nie czyści ani obiekt wywołujący ani sama funkcja wywoływana. Podczas debugowania zauważysz, że funkcje **fastcall** są poprzedzone znakiem małpy, a na ich końcu znajduje się ponownie znak małpy oraz liczba argumentów:

	@potega@1

## Podsumowanie

Istnieje kilka innych konwencji wywoływania funkcji w języku C++, lecz nie są one tak często używane jak trzy opisane wyżej. Mam nadzieję, że po przeczytaniu tego prostego artykułu z większą łatwością będziesz w stanie **modyfikować** różne funkcje zewnętrznych aplikacji, czy to podczas debugowania procesu czy zabaw z dll injection.

Nie utrzymanie porządku we wskaźniku stosu *ESP* zawsze skończy się niespodziewanym crashem aplikacji.