# Sterowanie rozruchem silnika z tablicami Karnaugh - ZelioSoft 馃嚨馃嚤
## Wst臋p
Uk艂ad sterowania stworzony w ramach zaj臋c laboratoryjnych. Program ten zosta艂 wykonany w celu sterowania rozruchem silnika pier艣cieniowego tr贸jfazowego. W tym celu zastosowano tablice Karnaugh. Zdefiniowano sygna艂y wej艣ciowe i wyj艣ciowe tak aby tworzy艂y one tablic臋 prawdy, z kt贸rej nast臋pnie wyci膮gni臋to definicyjne warunki dla wyj艣膰. Sam program 'napisano' j臋zykiem drabinowym LAD w programie ZelioSoft. To podej艣cie umo偶liwia 艂atwe przeniesienie programu na programowalny sterownik logiczny firmy Schneider Electric.
## Zagadnienie
Celem algorytmu jest sterowanie rozruchem silnika pier艣cieniowego tr贸jfazowego. Rozwi膮zanie tego problemu mo偶e przybra膰 dwie postaci. Pierwszym, naiwnym rozwi膮zaniem jest zastosowanie czasowych prze艂膮cze艅 poszczeg贸lnych styk贸w. To jednak nie zapewnia bezpiecznego rozruchu, poniewa偶 nie ma gwarancji 偶e w trakcie rozruchu silnik b臋dzie si臋 zachowywa艂 jak op贸藕nienia czasowe wymagaj膮. Aby unikn膮膰 problem贸w z synchronizacj膮, znacznie lepiej jest u偶y膰 drugie rozwi膮zanie. Polega ono na pomiarze poziomu pr膮du. Kiedy spadnie poni偶ej ustalonego poziomu prze艂膮czy si臋 kolejne styki a偶 do zako艅czenia rozruchu.

![Motor Start](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_5.png)

## Tablice Karnaugh
Mo偶liwe jest zaprogramowanie kolejnych stan贸w rozruchu jeden po drugim, ale wtedy uzyskany program b臋dzie znacznie bardziej rozbudowany ni偶 musi by膰. Tablice Karnaugh pozwalaj膮 na usuni臋cie niepotrzebnych komplikacji. Polegaj膮 one na stworzeniu tablic zawieraj膮cych wszystkie mo偶liwe kombinacje stan贸w wej艣ciowych i wyj艣ciowych. Na pocz膮tku musimy ustali膰 nasze wej艣cia i wyj艣cia. Jako wej艣cia okre艣lamy Is, M, K11, K12 oraz K13. Is to wynik por贸wnania poziom贸w pr膮d贸w - odpowied藕 na pytanie "Czy aktualny poziom pr膮du jest wi臋kszy od ustawionego minimum?". Flaga M s艂u偶y do zapami臋tania poprzedniego stanu Is. Przydatne jest to ze wzgl臋du na prze艂膮cznie styk贸w. Kolejny styk powinien by膰 prze艂膮czony kiedy poziom pr膮dy jest mniejszy od minimum, ale wcze艣niej by艂 wi臋kszy. K11s, K12s oraz K13s to same styki prze艂膮czaj膮ce elementy rezystancji startowej. Jako wyj艣cia stosujemy M, K11s, K12s oraz K13s.

Po zdefiniowaniu wej艣膰 oraz wyj艣膰 ustalamy logiczne warto艣ci dla kolejnych stan贸w rozruchu. Na pocz膮tku wszystkie wielko艣ci pocz膮tkowe s膮 zero. Gdy rozruch si臋 rozpoczyna, poziom pr膮dy skokowo przekracza minimum, czyli Is = 1, M = 0. Flaga M si臋 prze艂膮cza i przez pewien czas Is = 1, M = 1. Kiedy jednak pr膮d spadnie poni偶ej minimum, czyli przez jeden cykl Is = 0, M = 1. To jest sygna艂 wymagany do prze艂膮czenia styku K11s = 1. To umo偶liwia pr膮dowi wzrost, czyli znowu Is = 1, a po chwili M = 1. Kiedy znowu pr膮d spadnie poni偶ej minimum mamy Is = 0, M = 1, K11s = 1. To sygna艂 do za艂膮czenia K12s = 1. Proces ten powtarza si臋 analogicznie a偶 do uzyskania Is = 0, M = 1, K11s = 1, K12s = 1, K13s = 1. Ten stan oznacza, 偶e rozruch si臋 ko艅czy i mo偶na pozostawi膰 za艂膮czony tylko styk K13. Pe艂na tabela logiczna przedstawiona poni偶ej.

![Logic Table](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_6.png)

Z tabeli pocz膮tkowej tworzymy tablice Karnaugh. Ka偶da tablica odpowiada warto艣ciom danego wyj艣cia zale偶nie od kombinacji wej艣膰. Ko艅cowym efektem s膮 cztery tablice odpowiadaj膮ce M, K11, K12 oraz K13. W wierszach tych tablic wpisujemy warto艣ci binarne Is oraz M. Kolumny z kolej to warto艣ci binarne K11s, K12s i K13s. Uzyskujemy tym sposoben tablice o czterech wierszach i o艣miu kolumnach. Wszystkie stany kt贸re nie wyst臋puj膮 w tabeli logicznej zast臋pujemy symbolem 'x'. Ten symbol oznacza, 偶e wyj艣cie mo偶e w tym stanie przyj膮膰 dowoln膮 warto艣膰. Potem definiujemy rejony zawieraj膮ce stany 1. Nie mog膮 to jednak by膰 rejony dowolne - z definicji matematycznej tablicy Karnaugh rejony stanu 1 musz膮 mie膰 rozmiary r贸wne pot臋gom dw贸jki. Z tego powodu jedyne 'legalne' rejony w naszych tablicach maj膮 boki 1, 2, 4 lub 8. Na szcz臋艣cie w tym momencie bardzo pomagaj膮 stany 'x'. Poniewa偶 mog膮 mie膰 dowolny stan, to dla wygody mo偶emy je ustali膰 jako 1 tam gdzie u艂atwia to stworzenie rejonu oraz 0 gdzie nie s膮 potrzebne. Istniej膮 dodatkowe warunki tablic Karnaugh, o kt贸rych nale偶y pami臋ta膰. Rejony mog膮 przechodzi膰 przez 'granic臋' tablicy je艣li r贸偶nica pomi臋dzy stanami to 1 bit. Przyk艂adowo, mo偶na wykona膰 rejon 000-100 mimo 偶e 艣a po dw贸ch r贸偶nych stronach tablicy. Po drugie, rejony nie powinny si臋 'dotyka膰' bez nak艂adania. Takie stykanie mo偶e wywo艂ywa膰 niestabilne prze艂膮czanie pomi臋dzy zbli偶onymi stanami, dlatego nale偶y dotykaj膮ce si臋 rejony rozszerzy膰 i na艂o偶y膰 na siebie. Z tych warunk贸w uzyskujemy po dwa rejony dla M, K11, K12 oraz trzy rejony dla K13 pokazane poni偶ej.

![Karnaugh Maps](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_7.png)

Na koniec zapisujemy rejony z tablic jako r贸wnania binarne. Tu zapisano rejony na dwa sposoby. Spos贸b bezpieczny to zapisanie rejon贸w tylko zawieraj膮cych 1, a spos贸b u艂atwony to rejony zawieraj膮ce r贸wnie偶 'x'. Przyk艂adowo, dla M mamy rejony bezpieczne M = Is * K11s * M12s * NOT K13s + Is * NOT K12s * NOT K13s oraz rejony u艂atwione Is * K11s + Is * NOT K12s * NOT K13s. Wykonano r贸wnania podw贸jne, poniewa偶 niekoniecznie rejon u艂atwiony musi zadzia艂a膰. Takie jest zagro偶enie wynikaj膮ce z u偶ywania stan贸w 'x'. Je艣li wyst膮pi problem, mo偶na zast膮pi膰 wz贸r u艂atwiony wzorem bezpiecznym kt贸ry na pewno dzia艂a. Poni偶ej wszystkie uzyskane wzory bezpieczne (g贸rne) oraz u艂atwione (dolne).

![Karnaugh Equations](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_8.png)

## Algorytm
Wykonane powy偶ej obliczenia umo偶liwiaj膮 stworzenie algorytmu sterowania w ZelioSoft. Rozwi膮zanie problemu przez tablice Karnaugh pozwala na bezpo艣rednie odtworzenie wzor贸w - styki z r贸wna艅 odpowiadaj膮 bezpo艣rednio stykom w programie. Na pocz膮tku zerujemy stany pocz膮tkowe wyj艣膰 a potem odtwarzamy wzory drabinowo. Logiczne * oznacza po艂膮czenie szeregowe, a logiczne + to po艂膮czenie r贸wnoleg艂e.

![Initial Reset](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_9.png)

![Algorythm](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_10.png)

Wykonanie test贸w na algorytmie pozwala na potwierdzenie prawid艂owego funkcjonowania. Pozosta艂a analiza temu dost臋pna jest w [projekcie](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/Grupa%202%20-%20ZelioSoft.pdf).

---
# Controlling motor starting with Karnaugh maps - ZelioSoft 馃嚞馃嚙
## Introduction
Control system created during laboratory classes. This program is used to control the starting of a slip ring motor. To this end we used Karnaugh maps. Input and output signals were defined to create a truth table, from which output conditions were derived. The program itself was 'written' in LAD in ZelioSoft. This approach allows easy transfer of the program on a Schneider Electric PLC.
## Problem
The goal of the algorythm is to control the starting of a slip ring motor. This can be solved in two ways. The first, naive solution is to use time delay to switch contacts. This does not ensure a safe start, because there is no guarantee the motor will always behave as the delays require. To solve synchronisation issues, it's much better to use the second solution - current measurement. When the current falls below a minimum, the contacts switch.

![Motor Start](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_5.png)

## Karnaugh Maps
While it's possible to create the program by programming conditions one by one, that would result in a program much more complex then it has to be. Karnaugh maps allow us to reduce all complexity. They work by creating all possible combinations of inputs and outputs. First, the inputs and outputs must be defined. As inputs we use Is, M, K11, K12 and K13. Is is the result of current comparison - the result of the question "Is the current value of current higher than the minimum?". Flag M is used to remember the previous state of Is. It's used to stwitch contacts. The next contact swould be switched when the current is below minimum, but used to be higher. K11s, K12s and K13s are the contacts used to switch starting resistances. As outputs we use M, K11s, K12s and K13s.

After defining inputs and outputs we determine their logical states. All initial values are zero. When the start begins, current soars above minimum, so Is = 1, M = 0. Flag M switches and for some time Is = 1, M = 1. When the current falls below the minimum, for one cycle we get Is = 0, M = 1. This signal is used to switch the contact K11s = 1. This allows the current to rise Is = 1, and so M = 1. When the current falls below minimum again we get Is = 0, M = 1, K11s = 1. This signal switches K12s = 1. The process repeats analogically, until we reach Is = 0, M = 1, K11s = 1, K12s = 1, K13s = 1. This state means the starting is almost done and we can simply leave K13. The full logical is below.

![Logic Table](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_6.png)

From the initial table we create Karnaugh maps. Each map represents the state of one output based on inputs. The final result are four maps for M, K11, K12 and K13. In the rows we write the binary states of Is and M. On the other hand columns are the binary values of K11s, K12s and K13s. We get tables with four rows and eight columns. All states that don't appear are replaced with the symbol 'x'. This symbol means that the output can have any value. Afterwards we define regions that contain the values 1. However, those regions cannot be random - from the mathematical definition of Karnaigh maps they must have a size that's a power of 2. Because of this the only 'legal' sides of our regions are 1, 2, 4 or 8. Fortunately, the 'x' states help with our regions. Because they can have any state, we define them to be 1 where it makes reagion making easier and 0 when they're not necessary. There are additional requirenments of Karnaugh maps that we should keep in mind. The regions can pass through the 'borders' as long as the difference between states is one byte. For example, we can define the region 000-1000 even thought they are on different sides of the table. Seconds, regions cannot 'touch' without overlap. This touching can cause unstable state switching, and so to be safe we overlap regions. Due to these conditions we get two regions for M, K11, K12 as well as three for K13 as shown below.

![Karnaugh Maps](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_7.png)

Finally we write down the regions as logical equations. Here the regions are written in two ways. The safe method is to write down only regions containing 1, while the simplified method also includes 'x' states. For example, the safe regions for M are M = Is * K11s * M12s * NOT K13s + Is * NOT K12s * NOT K13s while the simplified regions are Is * K11s + Is * NOT K12s * NOT K13s. Double equations were created, becaude the simplified ones don't necessarily work. That's the problem resulting from the use of 'x' states. If there's a problem, we can just replace the simplified equations with safe ones. Below are the safe (upper) and simplified (lower) equations for our outputs.

![Karnaugh Equations](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_8.png)

## Algorithm
The calculations performed above pave way to the creation of a simple algorithm in ZelioSoft. The use of Karnaugh maps allow direct representation of the equations above - contacts in equations are the same as contacts in the program. We zero out the initial states and then represent the equations in LAD. Logical * means serial connection, while the logical + is a parallel connection.

![Initial Reset](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_9.png)

![Algorythm](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_10.png)

Testing the algorithm confirms its correct functioning. The rest of the problem analysis is available in the [project (馃嚨馃嚤 only)](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/Grupa%202%20-%20ZelioSoft.pdf).
