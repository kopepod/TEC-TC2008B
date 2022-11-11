# TC2008

## Curso

Aqui: [pdf](https://drive.google.com/drive/folders/1BQ4b9J9Hzsgf-mjIgFpz6PkmL_GQVCzI)

## Codigo

Aqui encontraras el codigo base para correr la actividad M1.

### Actividad M1 base:

1. Archivos codigo fuente Main.cpp Cubo.cpp Cubo.h
2. Compilacion
3. Ejecucion

Main.cpp
```c++
PSDGVJFHGdfgshdufsydgf74746tr65

```
Cubo.cpp
```c++
// Cubo.cpp
#include "Cubo.h"
#include <bits/stdc++.h>

const int NUMNODES = 16;

Cubo::Cubo(int dim, float vel)
{
    DimBoard = dim;
    //srand(time(nullptr));
    int c;

    //Se inicializa una posicion aleatoria dentro del tablero
    Position[0] = -200;
    Position[2] = -200;
    //Se inicializa el vector de direccion con un valor aleatorio
    Direction[0] = rand();
    Direction[1] = rand();
    Direction[2] = rand();
    //Se normaliza el vector de direccion
    float m = sqrt(Direction[0]*Direction[0] + Direction[1]*Direction[1] + Direction[2]*Direction[2]);
    m += 0.000001;
    Direction[0] /= m;
    Direction[1] /= m;
    Direction[2] /= m;
    //se multiplica el vector de direccion por la magnitud de la velocidad
    Direction[0] *= vel;
    Direction[1] *= vel;
    Direction[2] *= vel;

    cout << " X=" << Position[0] << "\t Y= " << Position[2] << " dX=" << Direction[0] << "\t dY= " << Direction[2] << endl;
}

Cubo::~Cubo()
{
    //dtor
}

void Cubo::draw()
{
    glPushMatrix();
    glTranslatef(Position[0], Position[1], Position[2]);
    glScaled(5,5,5);
    //Se dibuja el cubo
    glEnableClientState(GL_VERTEX_ARRAY);
    glEnableClientState(GL_COLOR_ARRAY);
    glVertexPointer(3, GL_FLOAT, 0, vertexCoords);
    glColorPointer(3, GL_FLOAT, 0, vertexColors);
    glDrawElements(GL_QUADS,24,GL_UNSIGNED_INT,elementArray);
    glDisableClientState(GL_VERTEX_ARRAY);
    glDisableClientState(GL_COLOR_ARRAY);

    glPopMatrix();
}

float dist2Node(float Position[3], int targetNode, float LocNodos[][2]){
    float dx = LocNodos[targetNode][0] - Position[0];
    float dy = LocNodos[targetNode][1] - Position[2];
    return  sqrt(dx * dx + dy * dy);
}

int findIdxNode(int targetNode, int NodeSeq[]){
    for (int i = 0; i < 100; i++){ 
        if(targetNode == NodeSeq[i]){
            return i;
        }
    }
    return 0;
}

void L2Norm(float Direction[3]){
    float m = sqrt(Direction[0]*Direction[0] + Direction[1]*Direction[1] + Direction[2]*Direction[2]);
    m += 0.00001;
    Direction[0] /= m;
    Direction[1] /= m;
    Direction[2] /= m;
}

void NodeDirection(int targetNode, float LocNodos[][2], float Direction[3], float Position[3]){
    Direction[0] = LocNodos[targetNode][0]-Position[0];
    Direction[2] = LocNodos[targetNode][1]-Position[2];
    L2Norm(Direction);
}

int RetrieveNextNode(int cNode, int TransitionMatrix[NUMNODES][NUMNODES]){
    int aux[NUMNODES];
    int k = 0;
    for(int i = 0; i < NUMNODES; i++){
        if(TransitionMatrix[cNode][i] == 1){
            aux[k] = i;
            k++;
        }
    }
    int sel = rand() % k;
    return aux[sel];
}


int Cubo::update(float LocNodos[][2], int TransitionMatrix[NUMNODES][NUMNODES], int nextNode, float speed){
    
    NodeDirection(nextNode, LocNodos, Direction, Position);
    float dist = dist2Node(Position, nextNode, LocNodos);

    if(dist < 5){
        nextNode = RetrieveNextNode(nextNode, TransitionMatrix);
    }

    Position[0] += speed * Direction[0];
    Position[2] += speed * Direction[2];

    cout.width(7);

    cout << " X=" << Position[0] << "\t Y= " << Position[2] << "\t nNode: "<< nextNode << "\t Distance: " << dist << endl;

    return nextNode;
}
```
Cubo.h
```c++
#ifndef CUBO_H
#define CUBO_H

#include <math.h>
#include <random>
#include <iomanip>
#include <GL/glut.h>
#include <iostream>

using namespace std;

class Cubo
{
    public:
        
        Cubo(int, float);
        ~Cubo();
        void draw();
        int update(float[][2], int[16][16], int, float);

    protected:

    private:
        float vertexCoords[24] = {  // Coordinates for the vertices of a cube.
                   1,1,1,   1,1,-1,   1,-1,-1,   1,-1,1,
                  -1,1,1,  -1,1,-1,  -1,-1,-1,  -1,-1,1  };

        float vertexColors[24] = {  // An RGB color value for each vertex
                   1,1,1,   1,0,0,   1,1,0,   0,1,0,
                   0,0,1,   1,0,1,   0,0,0,   0,1,1  };

        int elementArray[24] = {  // Vertex number for the six faces.
                  0,1,2,3, 0,3,7,4, 0,4,5,1,
                  6,2,1,5, 6,5,4,7, 6,7,3,2  };

        //Vector de direccion
        float Position[3] = {0.0, 5.0, 0.0};
        float Direction[3] = {0.0, 5.0, 0.0};
        int DimBoard;

};

#endif // CUBO_H



```

### Compilacion

```bash
g++ *.cpp -o runme -lglut -lGLU -lGL -I .
```
### Ejecucion
```bash
./runme
```
### Visual Studio
task.json
```json
{
    "version": "0.2.0",
    "configurations": [
        {
   
               "name": "C/C++: gcc build and debug active file",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}/${fileBasenameNoExtension}",
            "args": [
                "-lGL","-lGLU","-lglut"
            ],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                },
                {
                    "description": "Set Disassembly Flavor to Intel",
                    "text": "-gdb-set disassembly-flavor intel",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "C/C++: gcc build active file",
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```
### Temporizador
Timer.cpp
```c++
#include<cstdio>
#include<iostream>
#include<ctime>

using namespace std;


int main(){

	int delay;

	cout<<"Segundos:"<<flush;

	cin >> delay;

	delay *= CLOCKS_PER_SEC;

	clock_t now = clock();

	while(clock() - now < delay){
		cout<<"En ejecucion ... "<<endl;
	};

	cout<<"Fin del ciclo"<<endl;

	return 0;

}
```
Compilacion

```bash
g++ Timer.cpp -o runme
```

Ejecucion

```bash
./Timer
```

### Matriz de conexion
```c++
#include <bits/stdc++.h> 

using namespace std;

int main(){

	// tablero de 4x4 matriz de conexion
	
  int A[3][4] = {1,1,2,3,4,5,6,7,8,9,0,10};

  for (int i = 0; i < 3; i++){
    for (int j = 0; j < 4; j++){
      cout << "Element at A[" << i << "][" << j << "]: ";
      cout << A[i][j] <<endl;
    }
  }
  
}
```

