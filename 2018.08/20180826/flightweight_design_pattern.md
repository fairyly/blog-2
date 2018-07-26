# Flightweight Design Pattern

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * Created by starduliang on 7/26/18.
 */

class Item {
    String id;

    Item(String id){
        this.id = id;
    }

    void print(){
        System.out.print(this.id);
    }
}

class ItemPool {
    Map<String, Item> pool;

    public ItemPool(){
        this.pool = new HashMap();
    }

    public Item get(String id){
        Item item = this.pool.get(id);
        if(item == null) {
            item = getItemFromServer(id);
            this.pool.put(id, item);
        }
        return item;
    }

    public Item getItemFromServer(String id){
        System.out.println("fetching item (id : " + id + ")");
        return new Item(id);
    }
}

public class Main{

    public static void main(String []args){

        ItemPool factory = new ItemPool();
        List<Item> items = new ArrayList();
        items.add(factory.get("1"));
        items.add(factory.get("2"));
        items.add(factory.get("3"));
        items.add(factory.get("4"));
        for(Item i : items){
            i.print();
        }

    }
}
```
