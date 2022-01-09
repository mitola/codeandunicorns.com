---
title: Simple broad web crawler with Java
date: 2014-10-12T13:45:56+00:00

authors:
- admin

categories:
  - How to
  - The Code
---
The following article is a a very simple presentation with examples and explanation regarding the web crawler I made in Java. The idea behind it was to make **portable** (Java+Sqllite &#8211; No install needed for DB) and **simple** (The program was not designed to be scalable, be multithreaded, store a lot of data about the site or make a clever ranking, but it was rather aimed as a very simple working indexed & webcrawler).

&nbsp;

### Fell free to fork or modify the source code without restrictions. Mentioned author or link to the website is welcome:

  * ### **[Source code is available on Github](https://github.com/mitola/BroadWebCrawlerIndexer)**

### You will also need:

  * ### [Jsoup](http://jsoup.org/)

  * ### [sqllite](http://www.sqlite.org/)

&nbsp;

I'll try to explain the whole logic, so lets just start on the main function.  
The main function initializes database class and through the constructor creates necessary tables if they are not yet created. After that it continues into method executionOfMenuChoice()
```
    public static void main(String[] args) throws Exception {
        //init DB interaction
        try {
            dbClass = new MainDBClass();
        } catch (Exception ex) {
            Logger.getLogger(WebCrawler.class.getName()).log(Level.SEVERE, null, ex);
        }

        //Init menu  choice and execution
        executionOfMenuChoice();
    }
        
From the main function it continues to infinite loop of this function which first displays the menu and prepares reading of user input. The comments in each case should be self explanatory.

    public static void executionOfMenuChoice() throws Exception {
        while (true) {
            menuView();
            Scanner in = new Scanner(System.in);
            String input = in.next();
            switch (input) {
                case "1":
                    //process through all of the links on a page and continue
                    String[] nextEntryForProcessing;
                    while (true) {
                        nextEntryForProcessing = dbClass.getNextEntryForProcessing();
                        urlParsing(nextEntryForProcessing[0]); //0 is url and 1 is title
                        dbClass.moveEntryAfterParsing(nextEntryForProcessing[0], nextEntryForProcessing[1]);
                    }
                case "2":
                    //use this method to get all the results plus number of rows on the end for each table
                    dbClass.getAllEntriesCollected();
                    break;
                case "3":
                    dbClass.getAllEntriesCollectedCount();
                    break;
                case "4":
                    //Use truncate if you wish to delete your tables
                    dbClass.truncateTables();
                    break;
                case "5":
                    //Exiting an application
                    System.exit(0);
            }
        }
    }
        

Function menuview called from executionOfMenuChoice, just a simple console menu&#8230;

    public static void menuView() throws Exception {
        System.out.println("############################");
        System.out.println("1.) Press 1 to start/continue parsing and collecting links and titles");
        System.out.println("2.) Press 2 to print all collected and parsed links");
        System.out.println("3.) Press 3 to print number of processed and collected links");
        System.out.println("4.) Press 4 to truncate all data in the tables");
        System.out.println("4.) Press 5 to exit");
        System.out.println("############################");
    }
        
And the first thing that happens in main method while constructing the DB class. First it defines globally conn variable which is used for handling db connection and after that it continues into creation od DB if content didn&#8217;t exist before.
        
    public MainDBClass() throws Exception {
        Class.forName("org.sqlite.JDBC");
        this.conn = DriverManager.getConnection("jdbc:sqlite:linkdatabase.db");
        createTables();
    }
        
And in the createTable function on the last line, I added the first entry which will be used as a starting point for the parser.
    
    public void createTables() throws Exception{
        Statement stat = conn.createStatement();

        stat.executeUpdate("CREATE TABLE IF NOT EXISTS collected_links (url TEXT UNIQUE, title);");
        stat.executeUpdate("CREATE TABLE IF NOT EXISTS processed_links (url TEXT UNIQUE, title);");
        stat.executeUpdate("INSERT OR IGNORE INTO collected_links VALUES('http://codeandunicorns.com', 'Dummy link');");
    }

          Ok, the basic part of the web crawler is finished. we initialised everything and prepared it for use.<br /> Let&#8217;s return to executionOfMenuChoice function and follow the case 1. It goes into infinite loop and cycles through the lines below:

    nextEntryForProcessing = dbClass.getNextEntryForProcessing();
    urlParsing(nextEntryForProcessing[0]); //0 is url and 1 is title
    dbClass.moveEntryAfterParsing(nextEntryForProcessing[0], nextEntryForProcessing[1]);

    In the first line dbClass.getNextEntryForProcessing it gets the next entry from table of collected_links, processes/parse it in the second line and moves it to processed_links table in third line.
        
          Let us take a closer look at the most important part for a web crawler which is urlParsing function which continues into processPage function.
        
   public static void processPage(String URL) throws Exception {
        //get useful information
        boolean skip = false;
        Document doc = null;
        try {
            doc = Jsoup.connect(URL).timeout(5 * 1000).ignoreContentType(true).get();
        } catch (IOException ex) {
            Logger.getLogger(WebCrawler.class.getName()).log(Level.SEVERE, null, ex);
            dbClass.moveEntryAfterParsing(URL, URL);
            skip = true;
        }

        //get all links and recursively call the processPage method
        if (!skip) {
            Elements questions = doc.select("a[href]");
            for (Element link : questions) {

                //inserts every new url into DB
                if (link.attr("abs:href").startsWith("http")) {
                    System.out.println(link.attr("abs:href"));
                    /*Document titleDoc = Jsoup.connect(URL).get();
                     String title = doc.title();*/
                    if (!link.attr("abs:href").contains(" ")) {
                        dbClass.insertNewEntry(link.attr("abs:href"), link.attr("abs:href"));
                    }
                }
            }
        }
    }

First we use Jsoup.connect with timeout parameter which increases the effectivnes of parser since some websites can be quite slow. We also use ignoreContentType since it helps ignoring false positives for urls.<br /> After that we use the seen code snippet that extracts all the href links from a website and saves them in collected_links table where they wait to be used processed again and put into processed_links.

```