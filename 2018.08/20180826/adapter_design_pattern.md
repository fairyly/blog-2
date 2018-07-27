# Adapter Design Pattern

```java
package link.styler.styler_android.data.source.remote.service;

import java.lang.reflect.Array;
import java.util.ArrayList;
import java.util.List;

/**
 * Created by starduliang on 7/26/18.
 */

interface Queue<T> {
  void push(T s);
  T pop();
  int length();
}

class AdapterQueue implements Queue<String>{

    private List<String> lists = new ArrayList();

    @Override
    public void push(String s) {
        lists.add(s); // append to list
    }

    @Override
    public String pop() {
        if (lists.size() == 0) {
            throw new RuntimeException("queue is empty");
        }
        String s = lists.remove(0); // remove the first one from list
        return s;
    }

    @Override
    public int length() {
        return lists.size();
    }
}


public class Main{

    public static void main(String []args){
        Queue aq = new AdapterQueue();
        aq.push("item_1");
        System.out.println(aq.length());
        aq.push("item_2");
        System.out.println(aq.length());
        aq.pop();
        System.out.println(aq.length());
        aq.pop();
        System.out.println(aq.length());
        aq.pop();
        System.out.println(aq.length());
    }
}
```