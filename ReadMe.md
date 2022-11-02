# TC2008

## Curso

Aqui: [pdf](https://drive.google.com/drive/folders/1BQ4b9J9Hzsgf-mjIgFpz6PkmL_GQVCzI)

## Codigo

Actividad M1 base:

1. Fuentes Main.cpp Cubo.cpp Cubo.h

```

#ifdef __APPLE__
#include <GLUT/glut.h>
#else
#include <GL/glut.h>
#endif

#include <stdlib.h>
#include <random>
#include <iomanip>

#include "Cubo.h"

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

Cubo c1(DimBoard, 1.5);
Cubo c2(DimBoard, 0.0);
Cubo c3(DimBoard, 0.2);


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

 void init()
{
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

void display()
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    drawAxis();
    glColor3f(0.3, 0.3, 0.3);
    //El piso es dibujado
    glBegin(GL_QUADS);
        glVertex3d(-DimBoard, 0.0, -DimBoard);
        glVertex3d(-DimBoard, 0.0, DimBoard);
        glVertex3d(DimBoard, 0.0, DimBoard);
        glVertex3d(DimBoard, 0.0, -DimBoard);
    glEnd();

    c1.draw();
    c2.draw();
    c3.draw();

    c1.update();
    c2.update();
    c3.update();

    glutSwapBuffers();
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
    glutCreateWindow("Cubo 1");
    init();
    glutDisplayFunc(display);
    glutIdleFunc(idle);
    glutKeyboardFunc(keyboard);
    glutMainLoop();
    return 0;
}
```

```
#include "Cubo.h"

Cubo::Cubo(int dim, float vel)
{
    DimBoard = dim;
    //srand(time(nullptr));
    int c;

    //Se inicializa una posicion aleatoria dentro del tablero
    Position[0] = (float)(rand()%(2*DimBoard)) - DimBoard;
    Position[2] = (float)(rand()%(2*DimBoard)) - DimBoard;
    //Se inicializa el vector de direccion con un valor aleatorio
    Direction[0] = rand();
    Direction[1] = rand();
    Direction[2] = rand();
    //Se normaliza el vector de direccion
    float m = sqrt(Direction[0]*Direction[0] + Direction[1]*Direction[1] + Direction[2]*Direction[2]);
    Direction[0] /= m;
    Direction[1] /= m;
    Direction[2] /= m;
    //se multiplica el vector de direccion por la magnitud de la velocidad
    Direction[0] *= vel;
    Direction[1] *= vel;
    Direction[2] *= vel;

    cout << Direction[0] << endl;
    cout << Direction[2] << endl;
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

void Cubo::update()
{
    float new_x = Position[0] + Direction[0];
    float new_z = Position[2] + Direction[2];

    cout << "X=" << Position[0] << "; Y= " << Position[2] << endl;

    if (abs(new_x) <= DimBoard)
        Position[0] = new_x;
    else {
        Direction[0] *= -1.0;
        Position[0] += Direction[0];
    }

    if (abs(new_z) <= DimBoard)
        Position[2] = new_z;
    else {
        Direction[2] *= -1.0;
        Position[2] += Direction[2];
    }

    cout << "X=" << Position[0] << "; Y= " << Position[2] << endl;
}

```

```
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
        void update();

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
