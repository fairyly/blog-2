# Builder Desgin Pattern

```
package link.styler.styler_android.data.source.remote.service;

import java.util.ArrayList;
import java.util.List;

/**
 * Created by starduliang on 7/26/18.
 */

class Straw {
    private String manufacturer = "Styler";
}

class Wrapper {
    private String manufacturer = "Styler";
}

class Bottle {
    private String manufacturer = "Styler";
    private String size;
}

interface Item {
    String name();
    float price();
}

interface Burger extends Item {
}

class ChickenBurger implements Burger{
    @Override
    public String name() {
        return "chicken burger";
    }

    @Override
    public float price() {
        return 10.5f;
    }
}


class BeefBurger implements Burger{
    @Override
    public String name() {
        return "beef burger";
    }

    @Override
    public float price() {
        return 16.0f;
    }
}

interface Drink extends Item {
}

class Coke implements Drink{
    @Override
    public String name() {
        return "koke";
    }

    @Override
    public float price() {
        return 5.0f;
    }
}

class Pepsi implements Drink{
    @Override
    public String name() {
        return "repsi";
    }

    @Override
    public float price() {
        return 5.0f;
    }
}


class Meal {
    List<Item> items = new ArrayList();
    List<Wrapper> wrappers = new ArrayList();
    List<Straw> straws = new ArrayList();
    List<Bottle> bottles = new ArrayList();

    private Meal() {}

    public static Builder builder(){
        return new Builder();
    }

    public static class Builder {
        List<Item> items = new ArrayList();
        List<Wrapper> wrappers = new ArrayList();
        List<Straw> straws = new ArrayList();
        List<Bottle> bottles = new ArrayList();

        public void addItem(Item item) {
            this.items.add(item);
        }

        public Meal build () {
            for(Item item : this.items) {
                if(item instanceof Drink){
                    straws.add(new Straw());
                    bottles.add(new Bottle());
                } else if (item instanceof Burger) {
                    wrappers.add(new Wrapper());
                }
            }

            Meal meal = new Meal();
            meal.items = this.items;
            meal.wrappers = this.wrappers;
            meal.straws = this.straws;
            meal.bottles = this.bottles;

            return meal;
        }
    }

    public float totalPrice() {
        float totalPrice = 0;
        for (Item item : this.items) {
            totalPrice += item.price();
        }
        return totalPrice;
    }

    public void printItems() {
        for (Item item : this.items) {
            System.out.println(item.name());
        }
    }

    public void printStrawCount(){
        System.out.println("straw count : " + this.straws.size());
    }

    public void printBottleCount(){
        System.out.println("bottle count : " + this.bottles.size());
    }

    public void printWrapperCount(){
        System.out.println("wrapper count : " + this.bottles.size());
    }
}


public class Main{

    public static void main(String []args){

        Meal.Builder builder = Meal.builder();
        builder.addItem(new ChickenBurger());
        builder.addItem(new BeefBurger());
        builder.addItem(new Coke());
        builder.addItem(new Pepsi());

        Meal meal = builder.build();
        System.out.println(meal.totalPrice());
        meal.printItems();
        meal.printStrawCount();
        meal.printBottleCount();
        meal.printWrapperCount();
    }
}
```