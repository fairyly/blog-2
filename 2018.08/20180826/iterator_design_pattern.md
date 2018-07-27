# Iterator Design Pattern

```java
/**
 * Created by starduliang on 7/26/18.
 */


interface Iterator <T>{
    boolean hasNext();
    T next();
}

class SquareNums {
    private int length;

    public void setLength(int length){
        if (length < 0) throw new RuntimeException("length must be greater than 0");
        this.length = length;
    }

    public int getLength(){
        return this.length;
    }

    public int atIndex(int index){
        if (index < 0) throw new RuntimeException("index can not be negative");
        if (index > getLength() - 1) throw new RuntimeException("index out of bound");
        return index * index;
    }

    class SquareIterator implements Iterator<Integer> {
        private int cursor = 0;

        @Override
        public boolean hasNext() {
            return cursor < getLength();
        }

        @Override
        public Integer next() {
            return atIndex(cursor++);
        }
    }

    Iterator iterator(){
        return new SquareIterator();
    }
}


public class Main{
    public static void main(String []args){
        SquareNums nums = new SquareNums();
        nums.setLength(10);
        Iterator<Integer> itr = nums.iterator();
        while (itr.hasNext()) {
            System.out.println(itr.next());
        }
    }
}
```