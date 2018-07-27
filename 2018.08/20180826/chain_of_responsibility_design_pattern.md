# Change of Responsibility Design Pattern

```java
/**
 * Created by starduliang on 7/26/18.
 */


class HTTPRequest {
    private boolean isLoggedIn;

    public void setIsLoggIn(boolean isLoggedIn) {
        this.isLoggedIn = isLoggedIn;
    }

    public boolean isLoggedin() {
        return this.isLoggedIn;
    }
}

interface Handler {
    Handler next();

    void process(HTTPRequest request);
}

abstract class AbsHandler implements Handler{
    private Handler nextHandler;

    public AbsHandler setNextHandler(AbsHandler nextHandler) {
        this.nextHandler = nextHandler;
        return nextHandler;
    }

    public Handler next() {
        return this.nextHandler;
    }
}

class LoginHandler extends AbsHandler {
    @Override
    public void process(HTTPRequest request) {
        System.out.println("checking if logged in");
        if (!request.isLoggedin()) {
            System.out.println("not logged in");
        } else {
            next().process(request);
        }
    }
}

class LogHandler extends AbsHandler {
    @Override
    public void process(HTTPRequest request) {
        System.out.println("do some log");
        if (next() != null) {
            next().process(request);
        }
    }
}

class RequestHandler extends AbsHandler {
    @Override
    public void process(HTTPRequest request) {
        RequestProcessor.getInstance().process(request);
    }
}

class RequestProcessor {
    private static final RequestProcessor processor = new RequestProcessor();

    private RequestProcessor (){
    }

    public static RequestProcessor getInstance() {
        return processor;
    }

    public void process(HTTPRequest request) {
        System.out.print("process request here");
    }
}

public class Main{
    public static void main(String []args){
        LoginHandler loginHandler = new LoginHandler();
        LogHandler logHandler = new LogHandler();
        RequestHandler requestHandler = new RequestHandler();
        loginHandler.setNextHandler(logHandler).setNextHandler(requestHandler);

        loginHandler.process(new HTTPRequest());

        HTTPRequest loggedinReq = new HTTPRequest();
        loggedinReq.setIsLoggIn(true);
        loginHandler.process(loggedinReq);
    }
}
```
