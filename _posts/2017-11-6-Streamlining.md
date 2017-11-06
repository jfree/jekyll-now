---
layout: post
title: Streamlining at JFree
---

Yesterday I released new versions of [JFreeChart](https://github.com/jfree/jfreechart) (a 2D chart library for Java), [JFreeChart-FX](https://github.com/jfree/jfreechart-fx) (JavaFX extensions for JFreeChart) and [FXGraphics2D](https://github.com/jfree/fxgraphics2d) (a utility library that allows Java2D code to draw to a JavaFX Canvas).  These are the first updates I've done in a long while.  I have reduced time these days to devote to open source work, so I've put some focus into streamlining the build and release procedures that I'm using, with the aim that I will be able to do smaller and (hopefully) more frequent releases in the future. 

The first step I have taken is to move the JavaFX code out of the JFreeChart project and into a new project JFreeChart-FX.  This means I can have a standard "out-of-the-box" build mechanism for JFreeChart, and not have to provide instructions for "enabling" the JavaFX build which required a later version of the JDK (the non-JavaFX code in JFreeChart requires only JDK 1.6).  Likewise, JFreeChart-FX has a standard build procedure, which simply specifies dependencies on JFreeChart and FXGraphics2D.

The second step was to drop Ant as a build option, to focus on Maven only.  Over the years, I found Ant particularly useful for putting together ZIP file distributions of JFreeChart, including such things as dependencies as jar files, binary only demo files and other documentation.  However, distributing new versions of JFreeChart in ZIP files doesn't seem so useful anymore, and it certainly consumes my time to prepare and upload these archives to <http://www.jfree.org/>.  I'm convinced that most developers these days are comfortable cloning a Git repo and building from source, or simply relying on downloading dependencies from a Maven repository using whatever build tool they prefer.  If that's not the case, at least the work is transferred to someone else! 

A third step has been to merge the JCommon classes that JFreeChart uses, into JFreeChart itself.  I've wanted to do this for a long time, since the dependency didn't make a lot of sense as there wasn't any other code relying on the "common" classes (well, a long time ago there was JFreeReport).  Since I was restructuring the project for JavaFX, I decided to bump the version number on JFreeChart (to 1.5.0) and merge in the JCommon classes - this means that the core JFreeChart library is now a single jar file.

The last step was to move the (publicly available) demo classes into separate projects as well.  I will maintain one demo project for JavaFX related code and another for non-JavaFX related code (typically Swing demos, but other standalone code such as the demos for JFreeSVG will end up in here).  These projects are live already:

* <https://github.com/jfree/jfree-fxdemos>
* <https://github.com/jfree/jfree-demos>

It is really my hope that these changes will lower the overheads for me when updating these projects, which should allow for more frequent releases.

_There are no comments on my blog, but there is my Twitter handle below if you have something to say!_
