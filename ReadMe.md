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

#include <GL/glut.h>
#include <stdlib.h>
#include <random>
#include <iomanip>

#include "Cubo.h"
#include <string> 



//Variables dimensiones de la pantalla
int WIDTH=500;
int HEIGTH=500;
//Variables para establecer los valores de gluPerspective
float FOVY=60.0;
float ZNEAR=0.01;
float ZFAR=900.0;
//Variables para definir la posicion del observador
//gluLookAt(EYE_X,EYE_Y,EYE_Z,CENTER_X,CENTER_Y,CENTER_Z,UP_X,UP_Y,UP_Z)
float EYE_X=300.0;
float EYE_Y=200.0;
float EYE_Z=300.0;
float CENTER_X=0;
float CENTER_Y=0;
float CENTER_Z=0;
float UP_X=0;
float UP_Y=1;
float UP_Z=0;
//Variables para dibujar los ejes del sistema
float X_MIN=-500;
float X_MAX=500;
float Y_MIN=-500;
float Y_MAX=500;
float Z_MIN=-500;
float Z_MAX=500;
//Size del tablero
int DimBoard = 200;
const int NUMNODES = 16;
// Localizacion de los nodos
float LocNodos[NUMNODES][2];
int TransitionMatrix[NUMNODES][NUMNODES];
//int NodeSeq[] = {0,1,2,3,7,6,5,4,8,9,10,11,15,14,13,12};
int NodeSeq[100];
//
int A1nextNode = 0;
int A2nextNode = 2;
float speed = 2;

Cubo c1(DimBoard, 4);
Cubo c2(DimBoard, 4);
// Cubo c3(DimBoard, 0.2);


void drawAxis()
{
     glLineWidth(3.0);
     //X axis in red
     glColor3f(1.0f,0.0f,0.0f);
     glBegin(GL_LINES);
       glVertex3f(X_MIN,0.0,0.0);
       glVertex3f(X_MAX,0.0,0.0);
     glEnd();
     //Y axis in green
     glColor3f(0.0f,1.0f,0.0f);
     glBegin(GL_LINES);
       glVertex3f(0.0,Y_MIN,0.0);
       glVertex3f(0.0,Y_MAX,0.0);
     glEnd();
     //Z axis in blue
     glColor3f(0.0f,0.0f,1.0f);
     glBegin(GL_LINES);
       glVertex3f(0.0,0.0,Z_MIN);
       glVertex3f(0.0,0.0,Z_MAX);
     glEnd();
     glLineWidth(1.0);
 }

void drawString(int x, int y, int z, const char* text) {
  //glEnable(GL_TEXTURE_3D);
  glColor3f(1.0f, 1.0f, 1.0f);
  // Render the text
  glRasterPos3i(x, y, z);
  string s = text;
  void * font = GLUT_BITMAP_9_BY_15;
  for (string::iterator i = s.begin(); i != s.end(); ++i){
    char c = *i;
    glutBitmapCharacter(font, c);
  }
}

void RandNodes(int NodeSeq[]){
  NodeSeq[0] = 0;
  for(int i = 1; i < 100; i ++){
    NodeSeq[i] = rand() % 16;
  }

}

void PopulateLocNodes(){
  float cx = -150;
  float cy = -150;

  for (int i = 0; i < NUMNODES; i++){
  	LocNodos[i][0] = cx;
  	LocNodos[i][1] = cy;
  	cx += 100;
  	if(cx > 150){
  		cx = -150;
  		cy += 100;
  	}
  }
}

void PopulateTMatrix(){
  int n = sqrt(NUMNODES);
  int Nodes[n][n];
  int k = 0;
  for (int i = 0; i < n; i++){
    for (int j = 0; j < n; j++){
        Nodes[i][j] = k;
        k++;
    }
  }

  int nNode = 0;
  int cNode = 0;

  for (int i = 0; i < n; i++){
    for (int j = 0; j < n; j++){
      cNode = Nodes[i][j];
      for(int x = i-1;x <= i+1; x++){
        for(int y = j-1;y <= j+1; y++){
          if(x >= 0 && x < n && y >= 0 && y < n){
            nNode = Nodes[x][y];
            TransitionMatrix[cNode][nNode] = 1;
          }
        }
      }
    }
  }

  for (int i = 0; i < NUMNODES; i++){
    for (int j = 0; j < NUMNODES; j++){
        if(i == j || TransitionMatrix[i][j] != 1){
          TransitionMatrix[i][j] = 0;
        }
    }
  }

  cout << "Transition Matrix" << endl;
  for (int i = 0; i < NUMNODES; i++){
    for (int j = 0; j < NUMNODES; j++){
      cout << TransitionMatrix[i][j] << " ";
    }
    cout << "\n" << endl;
  }

}

void init(){

  RandNodes(NodeSeq);
  PopulateLocNodes();
  PopulateTMatrix();

  glMatrixMode(GL_PROJECTION);
  glLoadIdentity();
  gluPerspective(FOVY, (GLfloat)WIDTH/HEIGTH, ZNEAR, ZFAR);
  glMatrixMode(GL_MODELVIEW);
  glLoadIdentity();
  gluLookAt(EYE_X,EYE_Y,EYE_Z,CENTER_X,CENTER_Y,CENTER_Z,UP_X,UP_Y,UP_Z);
  glClearColor(0,0,0,0);
  glEnable(GL_DEPTH_TEST);
  srand(time(nullptr));
}

void delay(float secs)
{
	float end = clock() / CLOCKS_PER_SEC + secs;
	while ((clock() / CLOCKS_PER_SEC) < end);
}

void display()
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

    //drawAxis();
    glColor3f(0.5, 0.5, 0.5);
    //Floor
    glBegin(GL_QUADS);
        glVertex3d(-DimBoard, 0.0, -DimBoard);
        glVertex3d(-DimBoard, 0.0, DimBoard);
        glVertex3d(DimBoard, 0.0, DimBoard);
        glVertex3d(DimBoard, 0.0, -DimBoard);
    glEnd();

  for (int i = 0; i < NUMNODES; i++){
    std::string s = std::to_string(i);
    char const *pchar = s.c_str();
    drawString(LocNodos[i][0],10,LocNodos[i][1], pchar);
  }

    

    c1.draw();
    c2.draw();
    //c3.draw();

    A1nextNode = c1.update(LocNodos, TransitionMatrix, A1nextNode, A2nextNode, speed);
    A2nextNode = c2.update(LocNodos, TransitionMatrix, A2nextNode, A1nextNode, speed);
    //c2.update(LocNodos);
    //c3.update(LocNodos);

    glutSwapBuffers();
    //delay(0.25);
}



void idle()
{
     display();
}

void keyboard(unsigned char key, int x, int y)
{
    switch(key)
    {//SOLID
    case 's':
    case 'S':
            glPolygonMode(GL_FRONT_AND_BACK,GL_FILL);
            glShadeModel(GL_FLAT);
      break;
    //INTERPOL
    case 'i':
    case 'I':
            glShadeModel(GL_SMOOTH);
            glPolygonMode(GL_FRONT_AND_BACK,GL_FILL);
      break;

    case 'w':
    case 'W':
            glPolygonMode(GL_FRONT_AND_BACK,GL_LINE);
      break;

    case 27:   // escape
      exit(0);
      break;
    }
    glutPostRedisplay();
}

int main(int argc, char **argv)
{

  glutInit(&argc, argv);
  glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH );
  glutInitWindowPosition(100, 100);
  glutInitWindowSize(WIDTH, HEIGTH);
  glutCreateWindow("ActividadM1");
  init();
  glutDisplayFunc(display);
  glutIdleFunc(idle);
  glutKeyboardFunc(keyboard);
  glutMainLoop();




  return 0;
}


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

int RetrieveNextNode(int cNode, int otherNode, int TransitionMatrix[NUMNODES][NUMNODES]){
    int aux[NUMNODES];
    int k = 0;
    for(int i = 0; i < NUMNODES; i++){
        if(TransitionMatrix[cNode][i] == 1 && i != otherNode){
            aux[k] = i;
            k++;
        }
    }
    int sel = rand() % k;
    return aux[sel];
}


int Cubo::update(float LocNodos[][2], int TransitionMatrix[NUMNODES][NUMNODES], int nextNode, int otherNode, float speed){
    
    NodeDirection(nextNode, LocNodos, Direction, Position);
    float dist = dist2Node(Position, nextNode, LocNodos);

    if(dist < 5){
        nextNode = RetrieveNextNode(nextNode, otherNode, TransitionMatrix);
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
        int update(float[][2], int[16][16], int, int, float);

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

