---
layout: post
title: Eclipse and JFreeChart
---
Today I downloaded the latest Eclipse release because a customer is asking about getting started with [JFreeChart](https://github.com/jfree/jfreechart) and Eclipse, and the existing instructions for that are a bit out of date.  Here are some new instructions for getting started.

Start Eclipse then select `New -> Project...` from the `File` menu.  Under the Wizards selection, choose `Maven Project`.

![select_maven_project.png]({{ site.baseurl }}/screenshots/eclipse_jfreechart/select_maven_project.png "Eclipse - Maven Project")

When the New Maven Project dialog appears, select the 'Create Simple Project' checkbox then click the Next button.

![new_maven_project.png]({{ site.baseurl }}/screenshots/eclipse_jfreechart/new_maven_project.png "Eclipse - Maven Project")

At the next step, fill in the details that will be used to set up the initial `pom.xml` file that will be used by Maven to build your application.  Then click the `Finish` button.

At this point you will have a skeleton project that looks like this:

![project_1.png]({{ site.baseurl }}/screenshots/eclipse_jfreechart/project_1.png "Eclipse - Project")

Now you need to edit the `pom.xml` to include a dependency for `JFreeChart 1.5.0`.  Add the lines 8 - 14 shown in the screenshot below:

![edit_pom.png]({{ site.baseurl }}/screenshots/eclipse_jfreechart/edit_pom.png "Eclipse - pom.xml")

Right-click on `src/main/java` and select the `New -> Class` menu item.  Type 'First' as the name of the class and then click the `Finish` button.

![create_first_class.png]({{ site.baseurl }}/screenshots/eclipse_jfreechart/create_first_class.png "Eclipse - create class")

Next you should edit the First.java file to contain the following code:

{% highlight java %} import org.jfree.chart.ChartFactory;
import org.jfree.chart.ChartFrame;
import org.jfree.chart.JFreeChart;
import org.jfree.data.general.DefaultPieDataset;

/**
 * A simple introduction to using JFreeChart. This demo is described in the
 * JFreeChart Developer Guide.
 */
public class First {
    
	/**
     * The starting point for the demo.
     *
     * @param args ignored.
     */
    public static void main(String[] args) {
        // create a dataset...
        DefaultPieDataset data = new DefaultPieDataset();
        data.setValue("Category 1", 43.2);
        data.setValue("Category 2", 27.9);
        data.setValue("Category 3", 79.5);
        // create a chart...
        JFreeChart chart = ChartFactory.createPieChart(
            "Sample Pie Chart",
            data,
            true, // legend?
            true, // tooltips?
            false // URLs?
        );
        // create and display a frame...
        ChartFrame frame = new ChartFrame("First", chart);
        frame.pack();
        frame.setVisible(true);
    }
}
{% endhighlight %}

Now clicking the Run button in Eclipse will run the app and show you the following:

![running_app.png]({{ site.baseurl }}/screenshots/eclipse_jfreechart/running_app.png "JFreeChart Application")
