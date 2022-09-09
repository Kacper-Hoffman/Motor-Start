# Sterowanie rozruchem silnika z tablicami Karnaugh - ZelioSoft 🇵🇱
## Wstęp
Układ sterowania stworzony w ramach zajęc laboratoryjnych. Program ten został wykonany w celu sterowania rozruchem silnika pierścieniowego trójfazowego. W tym celu zastosowano tablice Karnaugh. Zdefiniowano sygnały wejściowe i wyjściowe tak aby tworzyły one tablicę prawdy, z której następnie wyciągnięto definicyjne warunki dla wyjść. Sam program 'napisano' językiem drabinowym LAD w programie ZelioSoft. To podejście umożliwia łatwe przeniesienie programu na programowalny sterownik logiczny firmy Schneider Electric.
## Zagadnienie
Celem algorytmu jest sterowanie rozruchem silnika pierścieniowego trójfazowego. Rozwiązanie tego problemu może przybrać dwie postaci. Pierwszym, naiwnym rozwiązaniem jest zastosowanie czasowych przełączeń poszczególnych styków. To jednak nie zapewnia bezpiecznego rozruchu, ponieważ nie ma gwarancji że w trakcie rozruchu silnik będzie się zachowywał jak opóźnienia czasowe wymagają. Aby uniknąć problemów z synchronizacją, znacznie lepiej jest użyć drugie rozwiązanie. Polega ono na pomiarze poziomu prądu. Kiedy spadnie poniżej ustalonego poziomu przełączy się kolejne styki aż do zakończenia rozruchu.

![Motor Start](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_5.png)

## Tablice Karnaugh
Możliwe jest zaprogramowanie kolejnych stanów rozruchu jeden po drugim, ale wtedy uzyskany program będzie znacznie bardziej rozbudowany niż musi być. Tablice Karnaugh pozwalają na usunięcie niepotrzebnych komplikacji. Polegają one na stworzeniu tablic zawierających wszystkie możliwe kombinacje stanów wejściowych i wyjściowych. Na początku musimy ustalić nasze wejścia i wyjścia. Jako wejścia określamy Is, M, K11, K12 oraz K13. Is to wynik porównania poziomów prądów - odpowiedź na pytanie "Czy aktualny poziom prądu jest większy od ustawionego minimum?". Flaga M służy do zapamiętania poprzedniego stanu Is. Przydatne jest to ze względu na przełącznie styków. Kolejny styk powinien być przełączony kiedy poziom prądy jest mniejszy od minimum, ale wcześniej był większy. K11s, K12s oraz K13s to same styki przełączające elementy rezystancji startowej. Jako wyjścia stosujemy M, K11s, K12s oraz K13s.

Po zdefiniowaniu wejść oraz wyjść ustalamy logiczne wartości dla kolejnych stanów rozruchu. Na początku wszystkie wielkości początkowe są zero. Gdy rozruch się rozpoczyna, poziom prądy skokowo przekracza minimum, czyli Is = 1, M = 0. Flaga M się przełącza i przez pewien czas Is = 1, M = 1. Kiedy jednak prąd spadnie poniżej minimum, czyli przez jeden cykl Is = 0, M = 1. To jest sygnał wymagany do przełączenia styku K11s = 1. To umożliwia prądowi wzrost, czyli znowu Is = 1, a po chwili M = 1. Kiedy znowu prąd spadnie poniżej minimum mamy Is = 0, M = 1, K11s = 1. To sygnał do załączenia K12s = 1. Proces ten powtarza się analogicznie aż do uzyskania Is = 0, M = 1, K11s = 1, K12s = 1, K13s = 1. Ten stan oznacza, że rozruch się kończy i można pozostawić załączony tylko styk K13. Pełna tabela logiczna przedstawiona poniżej.

![Logic Table](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_6.png)

Z tabeli początkowej tworzymy tablice Karnaugh. Każda tablica odpowiada wartościom danego wyjścia zależnie od kombinacji wejść. Końcowym efektem są cztery tablice odpowiadające M, K11, K12 oraz K13. W wierszach tych tablic wpisujemy wartości binarne Is oraz M. Kolumny z kolej to wartości binarne K11s, K12s i K13s. Uzyskujemy tym sposoben tablice o czterech wierszach i ośmiu kolumnach. Wszystkie stany które nie występują w tabeli logicznej zastępujemy symbolem 'x'. Ten symbol oznacza, że wyjście może w tym stanie przyjąć dowolną wartość. Potem definiujemy rejony zawierające stany 1. Nie mogą to jednak być rejony dowolne - z definicji matematycznej tablicy Karnaugh rejony stanu 1 muszą mieć rozmiary równe potęgom dwójki. Z tego powodu jedyne 'legalne' rejony w naszych tablicach mają boki 1, 2, 4 lub 8. Na szczęście w tym momencie bardzo pomagają stany 'x'. Ponieważ mogą mieć dowolny stan, to dla wygody możemy je ustalić jako 1 tam gdzie ułatwia to stworzenie rejonu oraz 0 gdzie nie są potrzebne. Istnieją dodatkowe warunki tablic Karnaugh, o których należy pamiętać. Rejony mogą przechodzić przez 'granicę' tablicy jeśli różnica pomiędzy stanami to 1 bit. Przykładowo, można wykonać rejon 000-100 mimo że śa po dwóch różnych stronach tablicy. Po drugie, rejony nie powinny się 'dotykać' bez nakładania. Takie stykanie może wywoływać niestabilne przełączanie pomiędzy zbliżonymi stanami, dlatego należy dotykające się rejony rozszerzyć i nałożyć na siebie. Z tych warunków uzyskujemy po dwa rejony dla M, K11, K12 oraz trzy rejony dla K13 pokazane poniżej.

![Karnaugh Maps](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_7.png)

Na koniec zapisujemy rejony z tablic jako równania binarne. Tu zapisano rejony na dwa sposoby. Sposób bezpieczny to zapisanie rejonów tylko zawierających 1, a sposób ułatwony to rejony zawierające również 'x'. Przykładowo, dla M mamy rejony bezpieczne M = Is * K11s * M12s * NOT K13s + Is * NOT K12s * NOT K13s oraz rejony ułatwione Is * K11s + Is * NOT K12s * NOT K13s. Wykonano równania podwójne, ponieważ niekoniecznie rejon ułatwiony musi zadziałać. Takie jest zagrożenie wynikające z używania stanów 'x'. Jeśli wystąpi problem, można zastąpić wzór ułatwiony wzorem bezpiecznym który na pewno działa. Poniżej wszystkie uzyskane wzory bezpieczne (górne) oraz ułatwione (dolne).

![Karnaugh Equations](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_8.png)

## Algorytm
Wykonane powyżej obliczenia umożliwiają stworzenie algorytmu sterowania w ZelioSoft. Rozwiązanie problemu przez tablice Karnaugh pozwala na bezpośrednie odtworzenie wzorów - styki z równań odpowiadają bezpośrednio stykom w programie. Na początku zerujemy stany początkowe wyjść a potem odtwarzamy wzory drabinowo. Logiczne * oznacza połączenie szeregowe, a logiczne + to połączenie równoległe.

![Initial Reset](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_9.png)

![Algorythm](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/1_10.png)

Wykonanie testów na algorytmie pozwala na potwierdzenie prawidłowego funkcjonowania. Pozostała analiza temu dostępna jest w [projekcie](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/Grupa%202%20-%20ZelioSoft.pdf).

---
# Controlling motor starting with Karnaugh maps - ZelioSoft 🇬🇧
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

Testing the algorithm confirms its correct functioning. The rest of the problem analysis is available in the [project (🇵🇱 only)](https://github.com/Kacper-Hoffman/Motor-Start/blob/main/Grupa%202%20-%20ZelioSoft.pdf).
