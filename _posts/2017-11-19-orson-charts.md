---
layout: post
title: 3D Charts for Java
---

In the last week I continued my efforts to streamline my open source libraries, and released a new version of [Orson Charts](https://github.com/jfree/orson-charts) (a 3D chart library for the Java platform).  I moved the JavaFX support into a separate project [Orson Charts FX](https://github.com/jfree/orson-charts-fx), transferred the demo code into separate projects and removed Ant build support (leaving just the Maven-based builds).

Here is a screenshot from the JavaFX demo application (which you can find in the [JFree FX Demos](https://github.com/jfree/jfree-fxdemos) project):

![orson-charts-fx.png]({{ site.baseurl }}/screenshots/orson-charts-fx.png "Orson Charts FX Demo Application")

Also included in the release are a few bug fixes - here are the notes from the README.md file:

#### Version 1.7 : 17-Nov-2017

* removed JavaFX support to a separate project Orson Charts FX;
* fixed cell content bug in `GridElement`;
* fixed bug in `GradientColorScale`;
* protect from `NullPointerException` in `AbstractValueAxis3D`;
* streamline build by removing Ant build support and moving demo code to external projects.

#### Run the Demo
Finally, if you are interested in running the demo code locally but don't want to check out the demo project from GitHub, you can easily download the [demo file](https://s3-us-west-2.amazonaws.com/jfree-demos/jfree-demos-1.2-jar-with-dependencies.jar) and run it with:

`java -jar jfree-demos-1.2-jar-with-dependencies.jar`
