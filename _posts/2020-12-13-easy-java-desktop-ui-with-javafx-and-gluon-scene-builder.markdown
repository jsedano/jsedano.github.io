---
layout: post
title:  "Easy Java desktop UI with JavaFX and gluon scene builder"
date:   2020-12-13 00:00:00 -0600
categories: java
tags: java javafx gluon gui
---
If you need some `GUI` for your desktop Java application you can use the `scene builder` from Gluon to drag and drop controls and you can then export them into a `.fxml` file that you can load using `JavaFX`.  In this example we are using Java 11 and maven.

You can find the code for this example here [JavaFX example][javafx-example].

You just need these dependencies in your project pom:

{% highlight xml %}
  <dependency>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-controls</artifactId>
    <version>15.0.1</version>
  </dependency>
  <dependency>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-fxml</artifactId>
    <version>11.0.1</version>
  </dependency>
{% endhighlight %}

You will also need to add this plugin:

{% highlight xml %}
<plugin>
  <groupId>org.openjfx</groupId>
  <artifactId>javafx-maven-plugin</artifactId>
  <version>0.0.5</version>
  <configuration>
    <mainClass>dev.jsedano.example.App</mainClass>
  </configuration>
</plugin>
{% endhighlight %}

Where `dev.jsedano.example.App` is where you have your main class. Then you can load the scene like this:

{% highlight java %}
package dev.jsedano.example;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.stage.Stage;

import java.io.File;

public class App extends Application {

    public static void main( String[] args ){
        launch();
    }

    @Override
    public void start(Stage stage) throws Exception {
        File f = new File("src/main/resources/exampleui.fxml");
        Parent root  = FXMLLoader.load(f.toURI().toURL());

        stage.setScene(new Scene(root));
        stage.show();
    }
}
{% endhighlight %}

In this example our `fxml file` is called `exampleui.fxml`

That's pretty much it.

But how about actually creating the `fxml` file, download and install [Gluon scene builder][gluon-scene-builder]. In this example we will start with a `Basic Application`:

![basic application](/assets/images/easy-java-desktop-ui-with-javafx-and-gluon-scene-builder/javafxexample_basic_application.png)


We will drag a `button` and a `label`. Select the `button` and we will change the text to `Press me!` on the `Properties` tab and in the `Code` tab we will set `On Action` to `buttonWasPressed`:

![adding action to button](/assets/images/easy-java-desktop-ui-with-javafx-and-gluon-scene-builder/javafxexample_button_action.png)

For the `label` we will delete the text and set the `fx:id` to `myLabel`.

![adding id to label](/assets/images/easy-java-desktop-ui-with-javafx-and-gluon-scene-builder/javafxexample_label_id.png)

Now we need to define the `controller`. On the left panel look for `Document` and then `Controller` and set the `Controller class` to what your controller will be named, on this example we will use `dev.jsedano.example.UIController` (this is the fully qualified class name).

![defining the controller](/assets/images/easy-java-desktop-ui-with-javafx-and-gluon-scene-builder/javafxexample_setting_controller.png)

And then we can save the generated `fxml` directly into our project `resources` folder, in this example we save to `src/main/resources/exampleui.fxml`

Now create the controller on the project

{% highlight java %}
package dev.jsedano.example;

import javafx.fxml.FXML;
import javafx.scene.control.Label;

public class UIController {

    @FXML
    private Label myLabel;

    @FXML
    private void buttonWasPressed(){
        myLabel.setText("button was pressed");
    }
}
{% endhighlight %}

We run the project using `mvn clean javafx:run`

And if everything was correct we should be seeing this when we press the button:

![button was pressed](/assets/images/easy-java-desktop-ui-with-javafx-and-gluon-scene-builder/javafxexample_button_was_pressed.png)

From then on you can create anything you need, for documentation on JavaFX visit [https://openjfx.io][open-javafx].
Download the complete code from this example here [JavaFX example][javafx-example].

[javafx-example]: https://github.com/jsedano/examples/tree/main/javafx-example
[gluon-scene-builder]: https://gluonhq.com/products/scene-builder/
[open-javafx]: https://openjfx.io
