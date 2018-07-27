# Observer Design Pattern

```java
import java.util.ArrayList;
import java.util.List;

/**
 * Created by starduliang on 7/26/18.
 */

interface Observerable {
    void addObserver(Observer observer);
    void removedObserver(Observer observer);
    void notifyAllObservers();
}

interface Observer {
    void update();
}

class WebsiteHealthMonitor implements Observerable {
    public static final int STATE_RUNNING = 0;
    public static final int STATE_DOWN = 1;
    private int state = STATE_RUNNING;
    List<Observer> observers = new ArrayList();

    public void updateState(int state){
        if (state != this.state) {
          this.state = state;
          this.notifyAllObservers();
        }
    }

    @Override
    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removedObserver(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyAllObservers() {
        for (Observer observer : this.observers) {
            observer.update();
        }
    }
}

class FollowTeam implements Observer {

    public void fix() {
        System.out.println(" developers is fixing ");
    }

    @Override
    public void update() {
        this.fix();
    }
}

class DevelopmentTeam implements Observer {

    public void notifyShopStaffs () {
        System.out.println(" sending mail to shop staffs ");
    }

    @Override
    public void update() {
        this.notifyShopStaffs();
    }
}

public class Main{
    public static void main(String []args){
        WebsiteHealthMonitor monitor = new WebsiteHealthMonitor();
        monitor.addObserver(new FollowTeam());
        monitor.addObserver(new DevelopmentTeam());
        monitor.updateState(WebsiteHealthMonitor.STATE_DOWN);
    }
}
```
