# Facade Desing Pattern


```java
interface Shape {
    void draw();
}

class Rectangle implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Rectangle::draw() method.");
    }
}

class Square implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Square::draw() method.");
    }
}

class Circle implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Circle::draw() method.");
    }
}

class Master {
    private Shape circle;
    private Shape rectangle;
    private Shape square;

    public Master() {
        circle = new Circle();
        rectangle = new Rectangle();
        square = new Square();
    }

    public void drawCircle(){
        circle.draw();
    }
    public void drawRectangle(){
        rectangle.draw();
    }
    public void drawSquare(){
        square.draw();
    }

}

public class Main{

    public static void main(String []args){
        Master master = new Master();

        master.drawCircle();
        master.drawRectangle();
        master.drawSquare();
    }
}
```
