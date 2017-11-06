---
layout: post
title: Streamlining at JFree
---

Yesterday I released new versions of [JFreeChart](https://github.com/jfree/jfreechart), [JFreeChart-FX](https://github.com/jfree/jfreechart-fx) and [FXGraphics2D](https://github.com/jfree/fxgraphics2d).  These are the first updates I've done in a long while.  I've put some focus into streamlining the build and release procedure so that in future I will be able to do smaller and more frequent releases. 

The first step was to move the JavaFX code out of the JFreeChart project and into JFreeChart-FX.  The non-JavaFX code in JFreeChart requires only JDK1.6 while the JavaFX code won't compile without at least JDK8.  This complicated the "out-of-the-box" build instructions.

The second step was to drop Ant as a build option, to focus on Maven only.  Over the years, I found Ant particularly useful for putting together ZIP file distributions of JFreeChart, including such things as dependencies as jar files, binary only demo files and other documentation.  Distributing new versions of JFreeChart in ZIP files doesn't seem so useful anymore, and it certainly consumes my time to prepare and upload these archives.  I'm convinced that most developers these days are comfortable cloning a Git repo and building from source, or simply relying on downloading dependencies from a Maven repository using whatever build tool is preferred. 

A third step has been to merge the JCommon classes that JFreeChart uses, into JFreeChart itself.  I've wanted to do this for a long time, since the dependency didn't make a lot of sense as there wasn't any other code relying on the "common" classes (well, a long time ago there was JFreeReport).  This means that the core JFreeChart library is now a single jar file.

The last step was to move the demo classes into separate projects as well.  I will maintain one demo project for JavaFX related code and another for Swing based users.  These projects are live already:

* <https://github.com/jfree/jfree-fxdemos>
* <https://github.com/jfree/jfree-demos>

It is really my hope that these changes will lower the overheads for me when updating my projects, which should allow for more frequent releases.
