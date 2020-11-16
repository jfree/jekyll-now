---
layout: post
title: Open Source Libraries - Current Versions
---

I'm maintaining a number of open source Java libraries on [Github](https://github.com/jfree).  I have created modular versions for all of them, except JFreeChart (which will take some more time).  Here is a summary of the latest version numbers, with the non-modular versions in the JDK8+ column and the modular versions in the JDK11+ column.


Library                                                     | Description                           | JDK8+ (non-modular) | JDK11+ (modular)  
:---------------------------------------------------------- | :------------------------------------ | ------------------: | ---------------: 
[JFreeChart](https://github.com/jfree/jfreechart)           | Chart library for the Java platform   | 1.5.1               | tbd              
[JFreeChart-FX](https://github.com/jfree/jfreechart-fx)     | JFreeChart extension for JavaFX       | 1.0.1               | 2.0               
[FXGraphics2D](https://github.com/jfree/fxgraphics2d)       | Bridge from Java2D to JavaFX          | 1.8                 | 2.1              
[JFreeSVG](https://github.com/jfree/jfreesvg)               | SVG Graphics2D implementation         | 3.4                 | 4.1              
[OrsonPDF](https://github.com/jfree/orsonpdf)               | PDF Graphics2D implementation         | 1.9                 | JFreePDF     
[JFreePDF](https://github.com/jfree/jfreepdf)               | PDF Graphics2D implementation         | OrsonPDF            | 2.0              
[Orson Charts](https://github.com/jfree/orson-charts)       | 3D chart library                      | 1.7                 | 2.0              
[Orson Charts FX](https://github.com/jfree/orson-charts-fx) | Orson Charts extension for JavaFX     | 1.0                 | 2.0              

I encourage people to use the modular versions of the libraries, but for those still using Java 8 the non-modular versions are still current.