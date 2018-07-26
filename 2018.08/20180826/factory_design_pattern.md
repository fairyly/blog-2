# Factory Design Pattern

```
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

class ShapeFactory {
	
   //use getShape method to get object of type shape 
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }		
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
         
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
         
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      
      throw new RuntimeException("this shape type does not exist");
   }
}

public class Main{

     public static void main(String []args){
        ShapeFactory shapFactory = new ShapeFactory();
        Shape circle = shapFactory.getShape("CIRCLE");
        circle.draw();
        Shape rectangle = shapFactory.getShape("RECTANGLE");
        rectangle.draw();
        Shape square = shapFactory.getShape("SQUARE");
        square.draw();
        Shape unkownShape = shapFactory.getShape("UNKNOWN_SHAPE_TYPE");
        unkownShape.draw();

     }
}
```
