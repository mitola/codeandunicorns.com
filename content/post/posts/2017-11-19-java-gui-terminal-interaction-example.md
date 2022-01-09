---
title: Java GUI terminal interaction example
date: 2017-11-19T13:24:08+00:00
author:
- admin 

categories:
  - ALL
  - How to
  - The Code
---
![tab2_commands-min](posts/tab2_commands-min.png "")
The following example will go over following Github repo created specifically for this.

**Github: **

<https://github.com/mitola/selftomator>

It was primarily aimed to provide a bit of exploration space since I was looking at several ways to automate a couple of my workflows, so I explored through Java, Node.JS, terminal solutions etc.

In the current iteration of the project certain things are already prepared. The main class, that calls the form which is made out of 2 tabbed windows. On one there is a textPanel, TextInput and a Button.

![tab1_console-min](posts/tab1_console-min.png "")

On the other one, as you can see on the featured post it's where you can save command's that will launch their local consoles (which are cmd on Windows, OS X terminal on Mac and equivalent on Linux) . Currently consisting of TextInput's and Buttons with a label. All the GUI is done with the inbuilt IntelliJ From Builder for Java. The console launching functionality is quite a stub ATM. But at least basic examples I found are attached in the commentary of the code itself.

Now let's focus on the actual fun part.

## Code:

```
public class Main {
    public static void main(String[] args) {
        JFrame frame = new JFrame("Console interaction");
        frame.setContentPane(new MainApp().panelMain);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.pack();
        frame.setVisible(true);
    }
}
```

In the main class we first instantiate a new [JFrame](https://docs.oracle.com/javase/7/docs/api/javax/swing/JFrame.html) titled Console interaction.  
For the frame itself we connect the other main class which contains direct references to the form etc. Of the new class we connect driectly to panelMain.  
PanelMain is the root of the GUI form, on which everything else is attached. The frame object itself contains loads of other useful config which I didn't utilise but feel free to do it with one of existing setters. To create for example a fixed width and height on it.

Now lets take a close look at MainApp class. I'll be adding just the relevant snippets of code in here, and the rest can be seen on above mentioned Github URL.

```
  public class MainApp {
    public JPanel panelMain;
    private JTextField msg;
    private JTextPane consoleOutput;
    private JButton msgSend;
    private JTabbedPane mainTabber;
    private JScrollPane scrollConsoleWrapper;
    private JTextField cmd1;
    private JButton cmd1b;
    private JTextField cmd2;
    private JTextField cmd3;
    private JTextField cmd4;
    private JButton cmd2b;
    private JButton cmd3b;
    private JButton cmd4b;
```

This are all the GUI elements placed on MainApp.form file. It doesn't make much sense to show MainApp.form code since it's very XML-ish look alike, and we can use GUI builder for it anyway.

```
ConsoleIntegration console = new ConsoleIntegration();
      try {
          loadPropertiesProperty();
      } catch (IOException e) {
          e.printStackTrace();
      }
```

We will be using this variables later on to connect listeners and other kind of functionality to it.  
In public MainApp function we first instantiate a helper class ConsoleIntegration which contains methods related to native consoles.

```
msgSend.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String consoleCommand = msg.getText();
                try {
                   consoleOutput.setText(consoleOutput.getText() + console.consoleExecOutput(consoleCommand));
                } catch (IOException e1) {
                    e1.printStackTrace();
                } catch (InterruptedException e1) {
                    e1.printStackTrace();
                }
            }
        });
```

On button that is showed on the last image where you can input a console command and have a button next to it. We attached listener to it. That listener will take the value of the text input (msg.getText()) and execute it in the consoleExecOutput which sends it to native console and outputs whatever the console outputs. An example would be sending a common &#8220;ls&#8221; command that will return the file names of the current pointed to directory.

The output will get appended to the TextArea panel, where outputs of previous commands will rest as well. Everything is made scrollable with wrapper of ScrollPane surrounding the TextArea.

Now we will momentarily jump into ConsoleIntegration consoleExecOutput to see what the method even does.

```
public String consoleExecOutput(String inputCommand) throws IOException, InterruptedException {
        String consoleOutput="";

        Process proc = Runtime.getRuntime().exec(inputCommand);

        // Read the output

        BufferedReader reader =
                new BufferedReader(new InputStreamReader(proc.getInputStream()));

        String line = "";
        while((line = reader.readLine()) != null) {
            System.out.print(line + "\n");
            consoleOutput=consoleOutput+line + "\n";
        }

        proc.waitFor();

        return consoleOutput;
    }
```

In this function as you can see the inputCommand is taken, proc Process executed with command and the [BufferedReader](https://docs.oracle.com/javase/7/docs/api/java/io/BufferedReader.html) collects the output itself. since the output can be more then one line we wait until all output is collected and combine it together and return back to the main class where it gets outputted to textPane.

That's pretty much it for the first tab, some thing's of course don't work yet, such as canceling the input with CTRL+C, and other things which would need to be done custom.

But since it's just a demo and a bit of practice, the code itself isn't up to par anyway, but hopefully it will help with some ideas etc.

Lets take a look at second tab now which contains 4 input fields and buttons.

```
cmd1.addFocusListener(new FocusAdapter() {
            @Override
            public void focusLost(FocusEvent e) {
                savePropertySet(1);
                super.focusLost(e);
            }
        });
        cmd2.addFocusListener(new FocusAdapter() {
            @Override
            public void focusLost(FocusEvent e) {
                savePropertySet(2);
                super.focusLost(e);
            }
        });
        cmd3.addFocusListener(new FocusAdapter() {
            @Override
            public void focusLost(FocusEvent e) {
                savePropertySet(3);
                super.focusLost(e);
            }
        });
        cmd4.addFocusListener(new FocusAdapter() {
            @Override
            public void focusLost(FocusEvent e) {
                savePropertySet(4);
                super.focusLost(e);
            }
        });
```

So in this case I improvised a bit and set when the text field losses focus, it executes the function to save the input in a local property.dat file.  
The thinking about this simplified interaction is, that if you want to change and edit the command for later, you will lose focus sooner or later, either hovering out, tabbing, etc.

```
static void saveProperties(Properties p)throws IOException
    {
        FileWriter fw = new FileWriter("property.dat",true); //the true will append the new data
        p.store(fw,"properties");
        fw.close();
        System.out.println("After saving properties:"+p);
    }
```

In the function itself. we take the properties defined before from the text input and save it in property.dat file. Since it's connected lets take a look at loadProperties as well, which gets loaded on program startup.

```
void loadPropertiesProperty()throws IOException
    {
        Properties p = new Properties();

        FileInputStream fi=new FileInputStream("property.dat");
        p.load(fi);
        fi.close();

        cmd1.setText(p.getProperty("cmd1"));
        cmd2.setText(p.getProperty("cmd2"));
        cmd3.setText(p.getProperty("cmd3"));
        cmd4.setText(p.getProperty("cmd4"));
        System.out.println("After Loading properties:"+p);
    }
```

Of course it's not a good practice at all to write in this kind of un-modular un logical way, but it provides a quick way of prototyping to just confirm some propositions i had, and didn't focus on program architecture itself. In the function we open the property.dat file and get appropriate properties via getProperty and set it in appropariate textInput field.

If you take a look at openTerminalWithCommand function in ConsoleIntegration class it's just a stub of the code, but that provides some hints on adding appropriate ways of starting native consoles.

```
public void openTerminalWithCommand(String inputCommand){
        String nameOS = System.getProperty("os.name");

        System.out.println(nameOS);

        if (nameOS.startsWith("Windows")){
            //// windows only
        /*Process p = Runtime.getRuntime().exec("cmd /c start cmd.exe");
        p.waitFor();*/
        } else if (nameOS.startsWith("Mac")){
            //Mac
        /*
        Runtime.getRuntime().exec("/usr/bin/open -a Terminal /path/to/the/executable");
         */

        } else if (nameOS.startsWith("Linux")){
            //Linux      // GNU/Linux -- example

            //Runtime.getRuntime().exec("/usr/bin/x-terminal-emulator --disable-factory -e cat README.txt");

        }
    }
}
```

And for the end, don't forget you can just clone the repo and take a closer look into it on **Github**:  
<https://github.com/mitola/selftomator>