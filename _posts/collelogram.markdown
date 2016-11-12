---
layout: post
title:  "How to make multiple line graphs with error bars using ggplot in R!"
date:   2016-11-11 11:49:45 +0200
categories: R
---
In this tutorial, I would like to show you how to make multiple line graphs with error bars in R. To understand this tutorial, you must have at least some basic knowledge about R. You may need to download this data file (Data_file_for_line_graphs.csv) from my Github page (https://raw.githubusercontent.com/Sainur/test_website/master/Data_file_for_line_graphs.csv) and save it to your local drive. The another easy option is: just call the "import" function and show the weblink.   

**Very brief description about the data set:**
<br>*Soil samples were treated with two levels of urea and two levels of moisture content representing 4 different treatments:*
<br>*1) Urea+High_moisture content;*
<br>*2) Urea+Low_moisture content;*
<br>*3) No_Urea+High_moisture content;*
<br>*4) No_Urea+Low_moisture content*  
*Soil samples were incubated for 63 days in tension tables. DNA was extracted for next generation sequencing (Illumina MiSeq) at different time points (Day 0, 7, 14, 21, 35 and 63). The sequencing data was processed in Qiime and then exported as a Biom format for further processing in R. The data file we are using here is a subset from a Biom file. There are 6 columns in this data set. The first column shows OTU_name (which represents organismal classification at genus or family level, you may simply consider these as different bacteria). The second, third, fourth, fifth and sixth columns represent Abundance, Day (0, 7, 14, 21, 35 and 63), Treatment1, Treatment2, and Combined_Treatment12, respectively.*


**Step 1: Install and run R packages (ggplot, Rmisc, lattice, plyr, rio):**

{% highlight ruby %}
install.packages ("ggplot2")
install.packages ("Rmisc")
install.packages ("lattice")
install.packages ("plyr")
install.packages ("rio")
library("ggplot2")
library("Rmisc")
library("lattice")
library("plyr")
library("rio")

{% endhighlight %}


**Step 2: Import the data file (Data_file_for_line_graphs.csv) from your local drive. Make sure you are showing the full file path properly. (e.g. "/Users/Desktop/.../Data_file_for_line_graphs.csv")**

{% highlight ruby %}
Data_file <- read.csv("/Users/sainur/Desktop/Data_file_for_line_graphs.csv",fill = TRUE, header = TRUE, sep = ",")
#OR, you can use the below command to import the file from the web.
Data_file <- import("https://raw.githubusercontent.com/Sainur/test_website/master/Data_file_for_line_graphs.csv")

{% endhighlight %}

If you want to see the first 10 rows of this data file (Data_file_for_line_graphs.csv) then run this command:
{% highlight ruby %}
head (Data_file, 10)
{% endhighlight %}
```
    OUT_name          Abundance Day   Treatment1    Treatment2  Combined_Treatment12
1  01: g_Sporosarcina         4   0    With urea High moisture     Urea+Low_moisture
2  01: g_Sporosarcina         4   0    With urea  Low moisture    Urea+High_moisture
3  01: g_Sporosarcina         2   0    With urea  Low moisture    Urea+High_moisture
4  01: g_Sporosarcina         2   0    With urea High moisture     Urea+Low_moisture
5  01: g_Sporosarcina         2   0    With urea  Low moisture    Urea+High_moisture
6  01: g_Sporosarcina         1   0    With urea High moisture     Urea+Low_moisture
7  01: g_Sporosarcina         1   0 Without urea  Low moisture No_Urea+High_moisture
8  01: g_Sporosarcina         1   0 Without urea  Low moisture No_Urea+High_moisture
9  01: g_Sporosarcina         1   0 Without urea  Low moisture No_Urea+High_moisture
10 01: g_Sporosarcina         0   0 Without urea High moisture  No_Urea+Low_moisture
```

**Step 3: We will now call the summary function (i.e. summarySE)**
<br>**Note:** *summarySE provides the standard deviation, standard error of the mean, and a (default 95%) confidence interval*
{% highlight ruby %}
Data_file_summary <- summarySE(Data_file, measurevar="Abundance", groupvars=c("Combined_Treatment12","Day","OUT_name", "Treatment1", "Treatment2"))
{% endhighlight %}
If you want to see the first 10 rows of this data file (Data_file_for_line_graphs.csv) then run this command:

{% highlight ruby %}
head (Data_file_summary, 10)
{% endhighlight %}



**Step 4: Let's make the multiple line graphs using the summarySE output:**
{% highlight ruby %}
pd <- position_dodge(0.001)

p=ggplot(Data_file_summary, aes(x=Day, y=Abundance, group=Combined_Treatment12))+
  facet_wrap(~OUT_name, ncol = 4, scales = "free_y")+
  geom_errorbar(aes(ymin=Abundance-se, ymax=Abundance+se), colour="black", position=pd)+
  geom_line(aes(linetype=Treatment1, colour=Treatment2),position=pd)+
  geom_point(aes(shape=Treatment2,colour=Treatment2))+

  xlab("Day")+
  scale_x_continuous()+
  ylab("Absolute abundance")+
  expand_limits(y=0)+                        # Expand y range
  scale_y_continuous()+        
  theme_bw()+
  theme(panel.grid.major = element_blank(), panel.grid.minor = element_blank(),
        strip.text.x = element_text(size=7, angle=0))  
pp= p+scale_color_manual(values=c("#EF6C00", "#0000FF"))
#pp= p+scale_color_manual(values=c("#111111", "gray70"))  # for Black and gray
pp
#You may save the image file in different size and formats (.pdf, jpg, png) in your computer.
ggsave("/Users/Sainur/Desktop/Line_graph.pdf",width=8,height=10)
{% endhighlight %}

<img src="https://github.com/Sainur/test_website/blob/master/Tutoria1_figure1.png?raw=true" alt="paper" width="700" height="1000">
