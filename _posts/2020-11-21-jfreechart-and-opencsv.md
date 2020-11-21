---
layout: post
title: JFreeChart and OpenCSV
---
A developer that uses [JFreeChart](https://github.com/jfree/jfreechart) asked me how they should go about using JFreeChart with data from a CSV file (that is, a text file containing a table of comma-separated values).  There are many different approaches that could be taken, so here I will give one example that makes use of the [OpenCSV](http://opencsv.sourceforge.net/) project.  We are going to create a chart showing excess death statistics by country during the COVID19 crisis (the source of the data is https://ourworldindata.org/excess-mortality-covid).  The end result looks like this:

![opencsv-jfreechart-svg.png]({{ site.baseurl }}/screenshots/opencsv-jfreechart-svg.png "Chart created with JFreeChart and OpenCSV")

We will use the latest release of Java (version 15) to create a Maven-driven modular Java application that outputs the chart in SVG format using JFreeSVG.  This, of course, requires that you have Maven installed on your computer (at the time of writing, the latest version is 3.6.3).

To begin, create a working directory (name it however you like) and switch to that directory:

    $ mkdir working
    $ cd working

Next, go to https://ourworldindata.org/excess-mortality-covid and download the `excess-mortality-p-scores.csv` file to the working directory.  If you are curious, you can open this file with a text editor (or a spreadsheet program) and take a look at the data.  Our goal is to read this data and create a chart that lets us visualise it easily.

We can now start creating the Maven project and our Java source files.  The first step is to create a project file (pom.xml) for Maven, to describe the application we will create, it's dependencies and the versions of the Maven plugins that we want to use.  The file should be created at the top level in the working directory, and looks like this:

    <?xml version="1.0" encoding="UTF-8"?>

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>

        <groupId>org.jfree</groupId>
        <artifactId>jfreechart-csv-demo</artifactId>
        <version>1.0</version>

        <name>jfreechart-csv-demo</name>

        <properties>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
            <maven.compiler.source>15</maven.compiler.source>
            <maven.compiler.target>15</maven.compiler.target>
        </properties>

        <dependencies>
            <dependency>
                <groupId>com.opencsv</groupId>
                <artifactId>opencsv</artifactId>
                <version>5.3</version>
            </dependency>
            <dependency>
                <groupId>org.jfree</groupId>
                <artifactId>jfreechart</artifactId>
                <version>1.5.1</version>
            </dependency>
            <dependency>
                <groupId>org.jfree</groupId>
                <artifactId>org.jfree.svg</artifactId>
                <version>4.1</version>
            </dependency>
        </dependencies>
    
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-clean-plugin</artifactId>
                    <version>3.1.0</version>
                </plugin>

                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.1</version>
                    <configuration>
                        <source>${maven.compiler.source}</source>
                        <target>${maven.compiler.target}</target>
                        <encoding>${project.build.sourceEncoding}</encoding>                
                    </configuration>
                </plugin>

                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-resources-plugin</artifactId>
                    <version>3.1.0</version>
                    <configuration>
                        <encoding>${project.build.sourceEncoding}</encoding>
                    </configuration>
                </plugin>

                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-jar-plugin</artifactId>
                    <version>3.2.0</version>
                    <configuration>
                        <archive>
                            <manifest>
                                <mainClass>org.jfree.chart.demo.csv.App</mainClass>
                            </manifest>
                        </archive>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-dependency-plugin</artifactId>
                    <version>3.1.2</version>
                    <executions>
                        <execution>
                            <id>copy-dependencies</id>
                            <phase>package</phase>
                            <goals>
                                <goal>copy-dependencies</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
    </project>


If you are not very familiar with Maven, don't focus too much on the details in the pom.xml for now.  The most important section is the dependencies, where we have declared that we want to use OpenCSV version 5.3, JFreeChart version 1.5.1 and JFreeSVG version 4.1.  Later when we build the project, Maven will download these dependencies automatically.  

The plugin section is related to Maven itself and controls the versions of plugins that will be used by Maven when we build our application.  There is also an important detail in the configuration for the maven-jar-plugin, where we've specified the name of the main class for the application.  This will add some info to the jar file to make it simpler to run the application once it is complete.

Next, we will begin creating the source files for our Java application.  Now, Maven expects to find project files arranged in a certain way, so we will first create a directory to contain the Java application source code, by convention `src/main/java`:

    $ mkdir -p src/main/java

Now switch to the java directory:

    $ cd src/main/java

Recall that we said we will create a modular Java application.  Create the following `module-info.java` file to define the name of the module for the demo applicaiton and list the modules that our application will depend upon (the same dependencies we already set up in Maven, plus a couple of modules that ship with the JDK itself):

module opencsvdemo {
    requires opencsv;
    requires org.jfree.jfreechart;
    requires org.jfree.svg;
    requires java.desktop;
    requires java.sql;
    exports org.jfree.chart.csv;
}

The module name is `opencsvdemo`.  If you plan to distribute a Java module (whether it be an application or an API) you should ensure that the module name is unique.  We don't need to worry so much about that here, since our demo is a "throw-away" project.  You can read some more about that in this post at Stack Overflow: https://stackoverflow.com/questions/43192741/how-should-i-name-my-java-9-module

The first two modules required are `opencsv` and `org.jfree.jfreechart`.  These are not yet fully modular libraries but, rather, Java 8 jar files.  However, the Java module system can import these jars as "automatic modules".  

The third module listed is `org.jfree.svg` is the JFreeSVG library.  This library is a first-class Java module, the JFreeSVG jar file contains module information inside it.

Our application will also require `java.desktop` and `java.sql` from the JDK (the latter is a consequence of some features in `OpenCSV` that we won't be using).

With these formalities out of the way, we can begin working on the Java code for our application.  We will use OpenCSV to read the CSV file.  To do this, we first create a Java class that will be used to store one data item (or row) from the CSV file.  I've called this `ExcessMortalityBean.java`, and created it in the `org.jfree.chart.demo.csv` package.  This class has one attribute per column in the data file.

From within the src/main/java directory, create the subdirectories required for the package (if you are using a Java integrated development environment (IDE), it will take care of this for you):

    $ mkdir -p org/jfree/chart/demo/csv
    $ cd org/jfree/chart/demo/csv

Now create the following file, ExcessMortalityBean.java:

    package org.jfree.chart.demo.csv;

    import java.time.LocalDate;
    import com.opencsv.bean.CsvBindByName;
    import com.opencsv.bean.CsvDate;

    /**
     * Bean class for recording excess mortality data from 
     * https://ourworldindata.org/excess-mortality-covid
     * 
     * Includes annotations to configure for OpenCSV to read the data file.
     */
    public class ExcessMortalityBean {

        @CsvBindByName
        private String entity;

        @CsvBindByName
        private String code;

        @CsvDate("yyyy-MM-dd")
        @CsvBindByName(column="Date")
        private LocalDate date;

        @CsvBindByName(column = "Excess mortality P-scores, all ages")
        private double excessPercent;

        public ExcessMortalityBean() {
        }

        public String getEntity() {
            return entity;
        }

        public void setEntity(String entity) {
            this.entity = entity;
        }

        public String getCode() {
            return code;
        }

        public void setCode(String code) {
            this.code = code;
        }

        public LocalDate getDate() {
            return date;
        }

        public void setDate(LocalDate date) {
            this.date = date;
        }

        public double getExcessPercent() {
            return excessPercent;
        }

        public void setExcessPercent(double excessPercent) {
            this.excessPercent = excessPercent;
        }

    }

We will create a second class, App.java that contains the main application code.  We've put everything in the main() method, which isn't how you would structure a larger Java application but for the purposes of a demo it let's us focus on just the important code:

    package org.jfree.chart.demo.csv;

    import com.opencsv.bean.CsvToBeanBuilder;
    import java.awt.BasicStroke;
    import java.awt.Rectangle;
    import java.io.File;
    import java.io.FileNotFoundException;
    import java.io.FileReader;
    import java.io.IOException;
    import java.time.LocalDate;
    import java.util.List;
    import java.util.Arrays;
    import org.jfree.chart.JFreeChart;
    import org.jfree.chart.ChartFactory;
    import org.jfree.chart.StandardChartTheme;
    import org.jfree.chart.plot.XYPlot;
    import org.jfree.chart.renderer.xy.XYLineAndShapeRenderer;
    import org.jfree.chart.title.TextTitle;
    import org.jfree.data.time.TimeSeriesCollection;
    import org.jfree.data.time.TimeSeries;
    import org.jfree.data.time.Day;
    import org.jfree.chart.ChartUtils;
    import org.jfree.svg.SVGGraphics2D;
    import org.jfree.svg.SVGUtils;

    /**
     * App that produces a chart from CSV data.
     */
    public class App {

        private static final String FILENAME = "excess-mortality-p-scores.csv";

        /**
         * Entry point for the application.
         * 
         * @param args  ignored.
         * 
         * @throws FileNotFoundException
         * @throws IOException 
         */
        public static void main(String[] args) throws FileNotFoundException, IOException {

            // read the CSV file
            System.out.println("Reading the file " + FILENAME);
            FileReader fileReader = new FileReader(FILENAME);
            List<ExcessMortalityBean> excessMortalityList = new CsvToBeanBuilder(fileReader)
                    .withType(ExcessMortalityBean.class).build().parse();

            // move the data into a JFreeChart dataset
            System.out.println("Creating a JFreeChart dataset from a subset of the data");
            List<String> countries = Arrays.asList("CAN", "CHE", "FRA", "ITA", "LTU", "NZL");
            TimeSeriesCollection dataset = new TimeSeriesCollection();
            for (ExcessMortalityBean bean : excessMortalityList) {
                String country = bean.getCode();
                if (!countries.contains(country)) continue;
                TimeSeries series = dataset.getSeries(country);
                if (series == null) {
                    series = new TimeSeries(country);
                    dataset.addSeries(series);
                }
                LocalDate day = bean.getDate();
                series.addOrUpdate(new Day(day.getDayOfMonth(), day.getMonthValue(), day.getYear()), bean.getExcessPercent());
            }

            // configure JFreeChart for displaying the data
            System.out.println("Setting up JFreeChart");
            JFreeChart chart = ChartFactory.createTimeSeriesChart(
                "Excess Mortality By Country", "Date", "Excess Mortality (%)", dataset);
            chart.addSubtitle(new TextTitle("Source: https://ourworldindata.org/excess-mortality-covid"));
            XYPlot plot = (XYPlot) chart.getPlot();
            XYLineAndShapeRenderer renderer = (XYLineAndShapeRenderer) plot.getRenderer();
            renderer.setAutoPopulateSeriesStroke(false);
            renderer.setDefaultStroke(new BasicStroke(3.0f));
            StandardChartTheme.createDarknessTheme().apply(chart);

            // render to chart to a PNG file
            System.out.println("Rendering the chart to a PNG file");
            ChartUtils.saveChartAsPNG(new File("chart.png"), chart, 1000, 640);

            // render the chart to an SVG file
            System.out.println("Rendering the chart to a SVG file");
            SVGGraphics2D g2 = new SVGGraphics2D(720, 480);
            chart.draw(g2, new Rectangle(720, 480));
            //SVGUtils.writeToHTML(new File("svg.html"), "JFreeChart CSV Demo", g2.getSVGElement());
            SVGUtils.writeToSVG(new File("chart.svg"), g2.getSVGElement());
        }

    }

Now that everything is in place, we can use Maven to build our application:

    $ mvn clean package

And finally we can run our application using Java and specifying the module path and module to run:

    $ java --module-path target/jfreechart-csv-demo-1.0.jar:target/dependency -m opencsvdemo

As the program runs, it will produce the following output on the console:

    Reading the file excess-mortality-p-scores.csv
    Creating a JFreeChart dataset from a subset of the data
    Setting up JFreeChart
    Rendering the chart to a PNG file
    Rendering the chart to a SVG file

After the program completes, you will find two files in the working directory, a PNG and an SVG version of the chart.  If you open the SVG file with a web-browser, you will see the chart shown at the top of this post.  Congratulations on creating a modular Java application!