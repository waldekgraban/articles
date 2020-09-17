---
title: Gra saper
category: cpp
thumbnail: saper.png
published: 2013-01-14T00:00:00Z
---
Gra **Saper** została napisana w 1981. Jest dostępna w każdej wersji systemu Windows. Polega na odkrywaniu zaminowanej planszy tak, aby nie trafić na minę. Gra działa na bardzo prostej zasadzie i nie wymaga zaawansowanego trybu graficznego, mimo tego potrafi wciągnąć na wiele godzin.  Sapera można łatwo napisać w dowolnym języku np. w **C++** na dodatek używając zwykłej konsoli i nie używając żadnych elementów graficznych (obrazków).

<!--more-->

## Budowa i generowanie planszy

**Planszę do sapera** najlepiej potraktować jako **tablicę dwuwymiarową**. Pozwala nam to w łatwy sposób operować na planszy za pomocą dwóch zagnieżdżonych w sobie pętli, jedna do współrzędnej X a druga do Y. Dużym ułatwieniem jest zastosowanie struktury a następnie utworzenie tablicy z odpowiadającym jej typem. Struktura pozwoli nam przechowywać więcej informacji mając tylko jedną tablicę dwuwymiarową.

Struktura będzie oznaczać jedno pole (jeden kwadracik) na planszy. Będzie zawierała zmienne *int wartość* oraz *bool odkryte*. Zmienna *wartość* oznacza wartość pola od *0-8*. Minę oznaczamy cyfrą *9*, dlatego że maksymalna wartość dla pola bez miny wynosi *8* (wynika to z zasad gry w sapera). Zmienna logiczna *odkryte* mówi, czy aktualne pole jest odkryte czy zakryte.

	struct pole {
	    int wartosc;
	    bool odkryte;
	};
	
	pole plansza[10][10];

Kolejnym krokiem jest wygenerowanie planszy. Najszybciej można zrobić to za pomocą funkcji i dwóch zagnieżdżonych pętli. Jedna pętla to **współrzędna X** a druga **Y**.

	bool genruj_plansze ()
	{
	    for (int x = 0; x<10; x++)
	        for (int y = 0; y<10; y++)
	        {
	            plansza[x][y].wartosc = 0;
	            plansza[x][y].odkryte = false;
	        }
	    return true;
	}

## Losowanie i ustawianie min

Posiadamy wygenerowaną plansze. Jest to tablica 10x10 typu struktury *pole*. Każde z pośród 100 pól na planszy posiada wartość 0. Kolejnym zadaniem do wykonania jest funkcja losująca 10 min. **Mina to wartość 9.** Po wylosowaniu współrzędnej miny (x,y) musimy ją &#8222;otorzyć&#8221; jedynkami. Każde pole dookoła naszej miny (wartość 9) zwiększamy o 1. Będzie to wyglądać tak:

{% include parts/postPicture.html page=page img="saper1" %}

Jeżeli wylosujemy minę w pobliżu już istniejącej miny, funkcja zwiększy jedynki na dwójki. Oznaczać to będzie dwie miny w pobliżu:

{% include parts/postPicture.html page=page img="saper2" %}

Ostatnią sytuacją jaką musimy przewidzieć to krawędzie planszy. Jeżeli mina zostanie wylosowana przy krawędzi lub w rogu, nasza funkcja generująca numery wokół miny nie może wyjść poza zakres tablicy (czyli poza planszę).

{% include parts/postPicture.html page=page img="saper3" %}

Funkcja losująca pozycję miny, losuje **współrzędną X oraz Y** z zakresu od 0 do 9. Następnie sprawdza czy w wylosowanej pozycji (x,y) nie znajduje się już jakaś mina (wartość 9). Jeżeli pole jest wolne dodaje mine.

	void losuj_pozycje ()
	{
	    time_t t;
	    int poz_x, poz_y;
	    int ilosc = 10;
	    
	    srand((unsigned)time(&t));
	    
	    while (ilosc>0)
	    {
	        poz_x = rand()%10;
	        poz_y = rand()%10;
	        
	        if (plansza[poz_x][poz_y].wartosc!=9)
	        {
	            ustaw_mine(poz_x,poz_y);
	            ilosc--;
	        }
	    }
	}

Powyższa funkcja wywołuje kolejną funkcję o nazwie *ustaw_mine()*. Ustawia ona minę w wylosowanej pozycji. Oprócz tego musi **zwiększyć otoczenie miny o 1** oraz sprawdzać **czy otoczenie miny nie wychodzi poza zakres planszy**. Aby otoczyć minę jedynkami można skorzystać z *8* instrukcji *if* (po jednej dla każdego sąsiadującego pola). Można także zrobić to ładniej poprzez zastosowanie dwóch zagnieżdżonych w sobie pętli (*k* oraz *l*), każda z wartością początkową *-1* i kończącą *1*. Wartość pętli *k* i *l* będzie dodawana do pozycji miny, dzięki temu pole miny *(x, y)* zostanie otoczone jedynkami. Należy także dodać warunek sprawdzający czy nie nastąpiło wyjście poza planszę. Działanie pętli *k* i *l* przedstawia rysunek:

{% include parts/postPicture.html page=page img="saper4" %}

Ostatecznie funkcja ustawiające minę ma postać: <code class="cs"></code>

	bool ustaw_mine (int poz_x, int poz_y)
	{
	    if (plansza[poz_x][poz_y].wartosc!=9)
	    {
	        plansza[poz_x][poz_y].wartosc = 9; //ustawiamy mine
	        
	        for (int k = -1; k<2; k++)
	            for (int l = -1; l<2; l++)
	            {
	                if ((poz_x+l)<0 || (poz_y+k)<0 ) continue; //wyjdz bo krawedz
	                if ((poz_x+l)>9 || (poz_y+k)>9 ) continue; //wyjdz bo krawedz
	                
	                if (plansza[poz_x+l][poz_y+k].wartosc==9) continue; //wyjdz bo mina
	                plansza[poz_x+l][poz_y+k].wartosc += 1; //zwieksz o 1
	            }
	    }
	    
	    return true;
	}

## Sterowanie

Sterowanie w konsoli można zrobić na dwa sposoby. Można poruszać się po planszy za pomocą strzałek, naciskając ENTER aby odkryć dane pole, lub wpisywać na sztywno współrzędne pola na które typujemy. Ja przedstawię sposób ruszania się za pomocą strzałek. 

Definiuję kody poszczególnych klawiszy (wszystkie strzałki oraz ENTER). Kody klawiszy można znaleźć w internecie. Następnie sprawdzam czy klawisz nie został naciśnięty funkcją *GetKeyState*. Jeżeli strzałka w lewo została naciśnięta, zmniejszam współrzędną X o *1*. Analogiczne do pozostałych kierunków. Ważne jest aby sprawdzać czy nie wyszliśmy kursorem poza plansze.

Pozycja kursora będzie zapisana w zmiennych globalnych aby łatwo można było się do niej odwołać z poszczególnych funkcji. Kolejną zmienną globalną jest zmienna *koniec*. Przedstawia ona aktualne stany gry (gra, przegrana, wygrana). Funkcja sterująca może wyglądać tak:

	#define strzalka_lewo 0x25
	#define strzalka_prawo 0x27
	#define strzalka_dol 0x28
	#define strzalka_gora 0x26
	#define enter 0x0D
	
	int poz_x = 0, poz_y = 0, o_poz_x = 1, o_poz_y = 1;
	int koniec = 0;
	
	void sterowanie()
	{
	    if ((GetKeyState(enter) & 0x8000))
	    {
	        if (plansza[poz_x][poz_y].wartosc==9) //trafiles na mine
	            koniec=2;
	        
	        odkryj_plansze(poz_x, poz_y); //odkrywanie pól
	        pokaz_plansze(); // wyswietl plansze
	    }
	    
	    if ((GetKeyState(strzalka_prawo) & 0x8000) && poz_x<9) poz_x++;
	    if ((GetKeyState(strzalka_lewo) & 0x8000) && poz_x>0) poz_x--;
	    if ((GetKeyState(strzalka_dol) & 0x8000) && poz_y<9) poz_y++;
	    if ((GetKeyState(strzalka_gora) & 0x8000) && poz_y>0) poz_y--;
	    
	    if (o_poz_y==poz_y && o_poz_x==poz_x) return; //jeżeli nie ma ruchu wyjdz
	    
	    o_poz_y = poz_y; //zmienne pomocnicza do warunku wyżej
	    o_poz_x = poz_x;
	    
	    pokaz_plansze(); // wyswietl plansze
	}

Funkcja będzie wywoływana w pętli. Po naciśnięciu dowolnej strzałki zmienna *poz*x* i *poz*y* będzie przechowywać pozycję kursora.

## Odkrywanie i wyświetlanie planszy

Kolejną funkcją do napisania jest funkcja odkrywająca planszę. Jeżeli pole będzie odkryte, funkcja będzie musiała wyświetlić jego wartość od *1-8* lub spację w przypadku wartości *0*. Jeżeli pole będzie zakryte, funkcja wyświetli np. znak *#*. Oprócz tego funkcja będzie pokazywać pozycję naszego kursora.

	void pokaz_plansze()
	{
	    system ("cls"); //wyczysc ekran
	    
	    for (int i = 0; i<10; i++)
	    {
	        for (int j = 0; j<10; j++)
	        {
	            if (j==poz_x && i==poz_y) //aktualkna pozycja kursora
	            {
	                SetConsoleTextAttribute( GetStdHandle( STD_OUTPUT_HANDLE  ), 0x02);
	                cout << "#";
	            }
	            else
	            {
	                SetConsoleTextAttribute( GetStdHandle( STD_OUTPUT_HANDLE  ), 0x07);
	                if (plansza[j][i].odkryte==true) // pole odkryte
	                {
	                    if (plansza[j][i].wartosc==0)   //wartosc = 0
	                        cout << " ";                //wyswietl spacje
	                    else
	                        cout << plansza[j][i].wartosc; //wyswietl wartosc 1-8
	                    
	                }
	                if (plansza[j][i].odkryte==false) //pole nie odkryte
	                    cout << "#"; //wyswietl #
	            }
	        }
	        SetConsoleTextAttribute( GetStdHandle( STD_OUTPUT_HANDLE ), 0x07 );
	        cout << endl;
	    }
	    
	    cout << "\npozycja kursora:\n";  //aktualkna pozycja kursora
	    cout << "X: " << poz_x << endl;  //aktualkna pozycja kursora
	    cout << "Y: " << poz_y << endl;  //aktualkna pozycja kursora
	}

Nasza gra posiada już większość potrzebnych funkcji. Została do zrobienia jeszcze najważniejsza z nich i w pewnym stopniu najtrudniejsza - **funkcja odpowiedzialna za odkrywanie pól**. Będzie to **funkcja rekurencyjna**. Będzie odkrywać pole począwszy od współrzędnych wejściowych *(x, y)*, w następnym kroku odkryje wszystkich sąsiadów *(x, y)*. Każdy sąsiad ponownie odkryje wszystkich swoich sąsiadów itd.

Gdybyśmy nie ograniczyli funkcji w żaden sposób wtedy po jednym jej wywołaniu odkryta została by cala plansza. Oto jakimi warunkami należy ograniczyć wykonywanie naszej funkcji:

- **Czy nie wychodzi poza plansze**, aby nie wyszła poza tablicę (jeżeli *x<0* lub *x>9* lub *y<0* lub *y>9* wtedy wyjdz)
- **Czy pole już odkryte**, aby nie próbowała odkrywać pól dwa razy (jeżeli *plansza\[x\]\[y\].odkryte==true* wtedy wyjdz)
- **Czy pole nie jest miną**, aby nie odkryła pola zaminowanego (jeżeli *plansza\[x\]\[y\].wartosc==9* wtedy wyjdz)

Jeżeli powyższe trzy warunki nie są spełnione, funkcja odkrywa pole. **Po odkryciu pola należy sprawdzić kolejny bardzo ważny warunek.** Rekurencyjne odkrywanie planszy ma działać na wszystkich pustych polach (wartość 0) oraz dla **jednego sąsiada posiadającego wartość > 0**. Oznacza to, że jeżeli funkcja zostanie wywołana dla pola **nie pustego** (z wartością od *1* do *8*), wtedy odkrywa to pole ale nie wykonuje się dalej dla pól sąsiednich. Wynika to z zasady gry w sapera. Ostatnim warunkiem jest:

- **Czy pole ma wartość 0**, aby nie odkryć całej planszy (jeżeli *plansza\[x\]\[y\].wartosc>0* wtedy odkryj ale wyjdz)

Graficzne działanie funkcji pokazuje rysunek:

{% include parts/postPicture.html page=page img="saper5" %}

Ostateczna postać funkcji:

	void odkryj_plansze(int x, int y)
	{
	    if (x<0 || x>9) return; // poza tablicą wyjście
	    if (y<0 || y>9) return; // poza tablicą wyjście
	    if (plansza[x][y].odkryte==true) return;  // już odkryte wyjście
	    
	    if(plansza[x][y].wartosc!=9 && plansza[x][y].odkryte==false)
	        plansza[x][y].odkryte=true;   // odkryj!
	    
	    if (plansza[x][y].wartosc!=0) return; // wartość > 0 wyjście
	    
	    //wywołanie funkcji dla każdego sąsiada
	    odkryj_plansze(x-1,y-1);
	    odkryj_plansze(x-1,y);
	    odkryj_plansze(x-1,y+1);
	    odkryj_plansze(x+1,y-1);
	    odkryj_plansze(x+1,y);
	    odkryj_plansze(x+1,y+1);
	    odkryj_plansze(x,y-1);
	    odkryj_plansze(x,y);
	    odkryj_plansze(x,y+1);
	}

## Zakończenie

Gra została ukończona. Teraz należy połączyć wszystko w całość. Przyda się jeszcze funkcja sprawdzająca czy nie nastąpiła wygrana. Można ją napisać na takiej zasadzie: jeżeli ilość nie odkrytych pól jest taka sama jak ilość wylosowanych min podczas generowania planszy to nastąpiła wygrana.

	bool sprawdz_czy_wygrana()
	{
	    int miny = 0;
	    for (int i = 0; i<10; i++)
	    {
	        for (int j = 0; j<10; j++)
	        {
	            if(plansza[j][i].odkryte==false)
	            miny++;
	        }
	    }
	    if (miny==10) return true;
	    return false;
	}

Główna funkcja programu, najpierw generuje plansze i losuje miny. Następnie w pętli odbywa się sterowanie i rysowanie planszy na nowo, pętla wykonuje się do czasu wygranej lub przegranej.

	int main()
	{
	    genruj_plansze(); // generuje plansze
	    losuj_pozycje(); // losuj miny
	    
	    Sleep(200);
	    
	    while(koniec==0)
	    {
	        Sleep(60);
	        sterowanie();
	        if (sprawdz_czy_wygrana()==true) koniec=1;
	    }
	    
	    if (koniec==1) cout << "\nGra zakonczona. Wygrales! :)";
	    if (koniec==2) cout << "\nTrafiles na mine! Koniec gry.";
	    
	    system ("pause >nul");
	    return 0;
	}

Przykład działania gry:

{% include parts/postPicture.html page=page img="saper6" %}

Kompletne źródło możesz pobrać pod adresem [xxxxxxxxxxx](https://xxxxxxxxx.xxxxxxxxx)
