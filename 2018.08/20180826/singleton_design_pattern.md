# Singleton Design Pattern

```
package link.styler.styler_android.data.source.remote.service;

import java.util.ArrayList;
import java.util.List;

/**
 * Created by starduliang on 7/26/18.
 */

class DBConnection {
    public void connect(){
        System.out.println("connecting to database");
    }
}

class DBConnPool {
    private static final DBConnPool pool = new DBConnPool();

    private List<DBConnection> conns = new ArrayList<>();

    public static DBConnPool getInstance(){
        return pool;
    }

    private DBConnPool() {
        for(int i = 0; i < 10; i++){
            DBConnection conn = new DBConnection();
            conn.connect();
            conns.add(new DBConnection());
        }
    }

    public DBConnection openConnection() throws InterruptedException {
        System.out.println("opening connection");
        while (conns.size() == 0) {
           Thread.currentThread().sleep(10000);
        }
        DBConnection conn = conns.remove(conns.size() - 1);
        System.out.println("connection opened");
        return conn;
    }

    public void closeConnection(DBConnection conn) throws InterruptedException {
        if (this.conns.contains(conn)) {
            System.out.println("connection has been closed");
            return;
        }
        System.out.println("closing connection");
        conns.add(conn);
        System.out.println("connection closed");
    }
}

public class Main{

    public static void main(String []args){

        try {
            DBConnPool pool = DBConnPool.getInstance();

            DBConnection conn_1 = pool.openConnection();
            pool.closeConnection(conn_1);

            DBConnection conn_2 = pool.openConnection();
            pool.closeConnection(conn_2);
            pool.closeConnection(conn_2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```