---
layout: post
title:  "Lissajous curve Java"
date:   2022-10-28 00:00:00 -0600
categories: java
tags: java
comments: true
---

Just a Lissajous curve on Java displayed on a JFrame.

You can find the complete code for this here: [Lissajous.java][Lissajous.java].

First of all, the code is a complete rip-off from [The Go Programming Language][The Go Programming Language] book, specifically from this [code from chapter 1][Lissajous.go].

I won't even pretend to understand the maths on this one, so lets just dive into the code, first we initialize our window:

{% highlight java %}

int size = 100;
JFrame frame = new JFrame("Lissajous");
frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
JPanel panel = new JPanel();
panel.setPreferredSize(new Dimension(2*size+1, 2*size+1));
frame.add(panel);
frame.pack();
frame.setResizable(false);
frame.setLocationRelativeTo(null);
frame.setVisible(true);

{% endhighlight java %}

With that we have a non resizable JFrame on the middle of the screen, that terminates the program when closed which contains a JPanel where we can draw, in order to do so we only need the Graphics from the JPanel:

{% highlight java %}
Graphics g = panel.getGraphics();
{% endhighlight java %}

Then we start shamelessly translating the Go code from the link above to Java:

{% highlight java %}
double cycles = 5;
double angularResolution = 0.001;
long delay = 80;
int frames = 64;
for(;;){
    double frequency = Math.random() * 3.0;
    float phase = 0.0f;
    for( int i = 0; i < frames; i++) {
        g.setColor(Color.BLACK);
        g.fillRect(0,0, panel.getWidth(), panel.getHeight());
        g.setColor(Color.GREEN);
        for(double t = 0.0; t < cycles*2*Math.PI; t+= angularResolution){
            double x = Math.sin(t);
            double y = Math.sin(t * frequency + phase);
            g.drawRect( (size + (int)(x*size+0.5)), (size + (int)(y*size+0.5)),1,1);
        }
        phase += 0.1;
        Thread.sleep(delay);
    }
}
{% endhighlight java %}

Since its a single file, if we have Java 11 or above we can run it without compiling with:

{% highlight shell %}
java Lissajous.java
{% endhighlight shell %}

And we should see this:

<div style="text-align: center;">
<a href="/assets/images/lissajous-curve-java/lissajous-curve-java.gif">
<img src="/assets/images/lissajous-curve-java/lissajous-curve-java.gif" alt="lissajous-curve-java" width="350" height="350">
</a>
</div>
<br/>



Download the complete code for this here: [Lissajous.java][Lissajous.java].

[Lissajous.java]:https://github.com/jsedano/examples/blob/main/java-snippets/Lissajous.java
[The Go Programming Language]:https://www.gopl.io
[Lissajous.go]:https://github.com/adonovan/gopl.io/blob/master/ch1/lissajous/main.go
