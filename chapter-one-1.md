# CHAPTER ONE

### 1.Static Factory Methods

##### What's a "static factory" method?

Answer: For a class, in order to let the user get a instance of itself, the most casual way is offer a public constructor. But a static factory method is also used to offer class instance e.g. 
```
public static Boolean valueOf(boolean b)
{
    return b ? Boolean.TRUE : Boolean.FALSE;
}
```
The differeces or advantages of this kind of methods are abvious compared to a public constructor.

* Firstly, a static factory method owns a name. How a the BigInteger(int, int, Random), it might return a value of any type of big integer but if we use BigInteger.probablePrime to replace the former usage, the aim of the function became more clear.
* Secondly, a static factory method could avoid the creation of duplicate object. And Boolean.valueOf(boolean) is a good example.(every time you invoke this method, there is no need to invoke new Boolean() before it).
* Thirdly, you can return any of the sub class of current class by using a static factory method. For example, a class contains two methods, one if newInstance() and anothre is newInstance(String name), and subclasses providers(a hashmap to store subclasses) which have a static method to get a instance of itself. The code as follows:

```
public interface Service { }

public interface Provider {
    Service newService();
}

public class Services {
    private Servoce {}
    
    private static final Map<String, Provider> providers = new HashMap<String, Provider>;
    private static final String DEFAULT_PROVIDER_NAME = "def";
    
    public static void registerProvider(Provider provider) {
        providers.put(DEFAULT_PROVIDER_NAME, provider);
    }
    
    public static void registerProvider(String name, Provider provider) {
        providers.put(name, provider);
    }
    
    public static Service newInstance() {
        return newInstance(DEFAULT_PROVIDER_NAME);
    }
    
    public static Service newInstance(String name) {
        Provider provider = providers.get(name);
        if (provider == null) {
            throw new IllegalArgumentException("No provider registered with name: " + name);
        }
        
        return provider.newService();
    }
}
```
* Finally, this kind of method can make your code clear.

Some examples of the usages. 

We avoid providing direct access to database connections because they're resource intensive. So we use a static factory method getDbConnection that creates a connection if we're below the limit. Otherwise, it tries to provide a "spare" connection, failing with an exception if there are none.

```
public class DbConnection
{
   private static final int MAX_CONNS = 100;
   private static int totalConnections = 0;

   private static Set<DbConnection> availableConnections = new HashSet<DbConnection>();

   private DbConnection()
   {
     // ...
     totalConnections++;
   }

   public static DbConnection getDbConnection()
   {
     if(totalConnections < MAX_CONNS)
     {
       return new DbConnection();
     }

     else if(availableConnections.size() > 0)
     {
         DbConnection dbc = availableConnections.iterator().next();
         availableConnections.remove(dbc);
         return dbc;
     }

     else {
       throw new NoDbConnections();
     }
   }

   public static void returnDbConnection(DbConnection dbc)
   {
     availableConnections.add(dbc);
     //...
   }
}
```

And in a game or some application, static factory may become more import due to that there ars always some things need to keep itself the only instance.

```
	private static Main instance = new Main();

	public static Main getInstance() {
		return instance;
	}
```

For a game you only need one instance for the main part of program, and every component which needs to get information from main can easily invoke Main.getInstance().

