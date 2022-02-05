# Game 2048

# Introduction

The 2048 game is  a game used to combine the numbers displayed on the tiles until we reach 2048. It has 16 grids.
When you start the game, there will be two “tiles” on the grid, each displaying the number 2.
We use  the directional keys **(Z = Up ,S = Down ,Q = Left ,D = Right)** on the keyboard to move the tiles around. When two equal tiles collide, they combine to give you one greater tile that displays their sum. The more we do this, obviously, the higher the tiles get and the more crowded the board becomes. 
to realize that with Qt we create three classes (gboard, cellule and game2048).

![Capture](https://user-images.githubusercontent.com/86742217/152626535-d6f0111b-5756-4cee-b17a-08d0535a06bb.PNG)


### GBoard Class

We used this class to create the main board of the game , which contains tiles, score ,indication and a button to exit the game.
 We started by defining the size of the board, its name *2048 Game* and the main interface as a vertical layout using the QVBoxLayout, then we set the interface on which we will create the 16 tiles using the QGridLayout.

In the ``gboard.h`` :

```cpp
private:

   QPushButton *exit;
   QVBoxLayout *mainL;
   QHBoxLayout *m;
   QGridLayout *boardL;
   QLabel *s;
   QLabel *I;


```

In the ``gboard.cpp`` :

```cpp
resize(650, 600);
       setWindowTitle("Game 2048");
       mainL = new QVBoxLayout();

       setLayout(mainL);


       boardL = new QGridLayout();
       board();

       mainL->insertLayout(0, boardL);
```

**Score:** A label which gets the value 0 in the beginning of the game.

```cpp
s = new QLabel(QString("SCORE: %1").arg(0));
       s->setStyleSheet("QLabel { color: rgb(245,245,245); font: bold 16pt ; }");
       s->setFixedHeight(50);
       mainL->insertWidget(1, s, 0, Qt::AlignLeft);


```
The background color of the board:

```cpp
setStyleSheet("gboard { background-color: rgba(95,158,160) }");
```
**Exit:** A push button to close the application. We connect it to the signal `clicked()` and the slot`close()`.


```cpp
exit = new QPushButton("&Exit",this);
       exit->setStyleSheet("QPushButton { color: rgb(245,245,245); font: bold 14pt; background-color:rgb(47,79,79); min-width: 5em;min-height: 2em; border-radius: 10px;   }");
       mainL->insertWidget(1, exit, 0, Qt::AlignLeft);
       connect(exit, SIGNAL(clicked()),this, SLOT(close()));



```

**Indication:** A label that indicates to the user which directional keys on the keyboard should be used.

```cpp
I = new QLabel(QString("The directional keys Z = Up ,S = Down ,Q = Left ,D = Right"));
       I->setStyleSheet("QLabel { color: rgb(245,245,245); font: bold 10pt ; }");
       I->setFixedHeight(50);
       mainL->insertWidget(1, I, 0, Qt::AlignLeft);
```

Before completing the **gboard class**, we moved to the *cellule class* and **game2048 class**.

### Cellule Class

In this class, we gave each number of the tile a color using the style sheet and for that we  created `cases()` function.

In the ``cellule.h``:

We added an integer *value* which represents the value of the case .
```cpp
public:
   cellule(int value);
   void cases();
  private:

      int value;

```

In the ``cellule.cpp``:

The position of the number should be in the center of the tile. And, using the switch we attribute to each tile a different color.
 The numbers that should be in the game are : 2, 4, 8, 16, 32, 64, 128, 256, 512, 1024, 2048.

*setStyleSheet(style.arg("use the background color)").arg("and here the number color")*

```cpp
cellule::cellule(int v): value(v)
{
setAlignment(Qt::AlignCenter);
}
void cellule::cases()
{
    setText(QString::number(value));
    auto style = QString("cellule { background: %1; color: %2; font: bold; border-radius: 10px; font: 40pt; }");
    switch (value) {
    case 2: {
        setStyleSheet(style.arg("rgb(245,245,245)").arg("rgb(105,105,105)"));
        break;
    }
    case 4: {
        setStyleSheet(style.arg("rgb(173,216,230)").arg("rgb(105,105,105)"));
        break;
    }
 case 8: {
        setStyleSheet(style.arg("rgb(176,224,230)").arg("rgb(255,255,255)"));
        break;
    }
    case 16: {
        setStyleSheet(style.arg("rgb(72,209,204)").arg("rgb(255,255,255)"));
        break;
    }
    case 32: {
        setStyleSheet(style.arg("rgb(32,178,170)").arg("rgb(255,255,255)"));
        break;
    }
    case 64: {
        setStyleSheet(style.arg("rgb(0,139,139)").arg("rgb(255,255,255)"));
        break;
    }
    case 128: {
        setStyleSheet(style.arg("rgb(70,130,170)").arg("rgb(255,255,255)"));
        break;
    }
    case 256: {

        setStyleSheet(style.arg("rgb(70,130,180)").arg("rgb(255,255,255)"));
        break;
    }
    case 512: {

        setStyleSheet(style.arg("rgb(100,149,237)").arg("rgb(255,255,255)"));
        break;
    }
    case 1024: {

        setStyleSheet(style.arg("rgb(65,105,225)").arg("rgb(255,255,255)"));
        break;
    }
    case 2048: {

        setStyleSheet(style.arg("rgb(25,25,112)").arg("rgb(255,255,255)"));
        break;
    }
    default: {
        setText("");
        setStyleSheet("Cell { background: rgb(220,220,220); border-radius: 10px; }");
    }
    }


```
### Game2048 Class
 This class contains the logical part of the game, in which two equal tiles collide, they combine to give you one greater tile that displays their sum, until we reach 2048. It has 16 cells.
To realize these operations we created some functions.

In the ```game2048.h```:

-We declared a constant c (number of the cells 4*4), and a position which is an integer array of length 2  (we used the #include <array>):


```cpp
#include <array>
#include <algorithm>
#include <cstring>
#include <vector>
#include<QObject>
#include<QPushButton>
using namespace std;
const int c = 4;

using Pos = array<int, 2>;
class game2048
{
public:
   game2048();
   void movement(bool horizontal, bool vertical);

       void reset();

       Pos EmptyPos();

       bool full();

       int b[c][c];

           int scoreT = 0;
           bool change;




};



```

In the ``game2048.cpp`` we did the implementation of functions:

1- ``Pos EmptyPos()``: Choose randomly a position in the board to show tiles when the position is empty.

```cpp
Pos game2048::EmptyPos()
{
   int i,j;
   do {
       i = rand() % c;
       j = rand() % c;
   } while (b[i][j]);
   return {i, j};
}

```

2- `` void reset()`` : Concerns the initial state of the game.In it’s implementation, we used the ``memeset()`` function to show the tiles in the empty positions.`` reset()`` show two tiles in the beginning of the game containing the number 2.

`` b[rand()%c][rand()%c] = 2; `` and   ``  b[pos[0]][pos[1]] = 2;``
generates two random positions to put tiles.
And we initialize the score to be 0 when we start the game, and the ``change`` should be true.

```cpp

void game2048::reset()
{
   memset(b, 0, sizeof b);

       b[rand()%c][rand()%c] = 2;

       auto pos = EmptyPos();
       b[pos[0]][pos[1]] = 2;

       scoreT = 0;
       change = true;
}


```


3- `` bool full()``: To check if the board is full of tiles or not . 
We used the function ``all_of()`` defined in <algorithm> library, to verify from the first position to the last one if the board is full or not.

```cpp
bool game2048::full()
{
   return all_of(*b, *b + c * c, [](int x) { return x != 0; } );
}


```

4- ``void movement(bool horizontal, bool vertical)``: To control the movement of the tiles vertically and horizontally . In the implementation, when a tile is equal to the next one (either vertically or horizontally) we add them and increase the score by multiplying the number of the tile by 2. 
The ``change`` is used to indicate if the cell is changed before and after the movement.

```cpp
void game2048::movement(bool horizontal, bool vertical)
{
   static int arrayb[c][c];
   memcpy(arrayb, b, sizeof b);

   using Vec = vector<int> ;
   auto addVec = [&] (Vec v, bool vertical) -> Vec {
       if (vertical) v = Vec(v.rbegin(), v.rend());
       Vec ans;
       size_t first = 0;
       while (first+1 < v.size()) {
           if (v[first] == v[first+1]) {
               scoreT += v[first] * 2;
               ans.push_back(v[first] * 2), first += 2;
           }
           else ans.push_back(v[first]), first++;
       }
       if (first+1 == v.size()) ans.push_back(v[first]);
       while (ans.size() < c) ans.push_back(0);
       if(vertical){
           return Vec(ans.rbegin(), ans.rend());
       }else{
           return ans;
       }

   };

   auto getVal = [&] (size_t i, size_t j) -> int& {
       if(horizontal){
           return b[i][j];
       }else{
           return b[j][i];
       }

   };

   for (size_t i=0; i < c; ++i) {
       Vec v;
       for (size_t j=0; j < c; ++j) {
           if (getVal(i, j)) v.push_back(getVal(i, j));
       }
       auto ans = addVec(v, vertical);
       for (size_t j=0; j < c; ++j) {
           getVal(i, j) = ans[j];
       }
   }

   change = !equal(*b, *b + c * c, *arrayb );
}
```

                              
                              
To complete our game, we returned to the *gboard* class and declared two functions which allowed us to link the three classes.

We used the ``cellule * cell[c][c] {}`` to store the pointer of the interface for 4x4 squares.

In the ``gboard.h`` :
```cpp
#include"game2048.h"
#include"cellule.h"

class gboard : public QWidget
{
   Q_OBJECT

public:
  
   void board();


private:
   game2048 g;
   cellule * cell[c][c] {};
protected:
   void keyPressEvent(QKeyEvent *e);
```
1-``void board()``: Draw the tiles on the board. 
``cell[i][j]->cases()``: to style each cell depending on its number.
`` cell[i][j] = new cellule(g.b[i][j])``: to match each cell to that of the game2048 class.
```cpp
void gboard::board()
   {
       delete boardL;
       boardL = new QGridLayout();
       for (int i = 0; i < c; ++i) {
           for (int j = 0; j < c; ++j) {
               delete cell[i][j];
               cell[i][j] = new cellule(g.b[i][j]);
               boardL->addWidget(cell[i][j], i, j);
               cell[i][j]->cases();
           }
       }
       mainL->insertLayout(0,boardL);

   }

```
2-``void keyPressEvent(QKeyEvent *e)``: Listen for keyboard key press events and then trigger different game updates methods.

```cpp
void gboard::keyPressEvent(QKeyEvent *e)
   {
       switch (e->key()) {
       case Qt::Key_Z:
           g.movement(false, false);
           break;
       case Qt::Key_S:
           g.movement(false, true);
           break;
       case Qt::Key_Q:
            g.movement(true, false);
           break;
       case Qt::Key_D:
           g.movement(true, true);
           break;
       }
```
If the tile changes after the movement and the board isn’t full, we show another tile(2) randomly .
And the ``scoreT`` should be showed in the score label .
```cpp
       if (g.change) {
           if (!g.full()) {
               auto pos = g.EmptyPos();
               g.b[pos[0]][pos[1]] = 2;
           }
           s->setText(QString("SCORE: %1").arg(g.scoreT));
          board();
       }


}

```
 
 # Conclusion:
 
 This project was rewarding, we used the modules we saw in class and learned new knowledge. 
 
