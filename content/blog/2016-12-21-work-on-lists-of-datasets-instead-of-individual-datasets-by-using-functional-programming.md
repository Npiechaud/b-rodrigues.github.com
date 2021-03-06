---
date: 2016-12-21T00:00:00+00:00
title: "Work on lists of datasets instead of individual datasets by using functional programming"
tags: [R]
menu:
  main:
    parent: Blog
    identifier: /blog/list_of_datasets_R
    weight: 3
---


<p>Analyzing a lot of datasets can be tedious. In my work, I often have to compute descriptive statistics, or plot some graphs for some variables for a lot of datasets. The variables in question have the same name accross the datasets but are measured for different years. As an example, imagine you have this situation:</p>
<pre class="r"><code>data2000 &lt;- mtcars
data2001 &lt;- mtcars</code></pre>
<p>For the sake of argument, imagine that <code>data2000</code> is data from a survey conducted in the year 2000 and <code>data2001</code> is the same survey but conducted in the year 2001. For illustration purposes, I use the <code>mtcars</code> dataset, but I could have used any other example. In these sort of situations, the variables are named the same in both datasets. Now if I want to check the summary statistics of a variable, I might do it by running:</p>
<pre class="r"><code>summary(data2000$cyl)
summary(data2001$cyl)</code></pre>
<p>but this can get quite tedious, especially if instead of only having two years of data, you have 20 years. Another possibility is to merge both datasets and then check the summary statistics of the variable of interest. But this might require a lot of preprocessing, and sometimes you really just want to do a quick check, or some dirty graphs. So you might be tempted to write a loop, which would require to put these two datasets in some kind of structure, such as a list:</p>
<pre class="r"><code>list_data &lt;- list(&quot;data2000&quot; = data2000, &quot;data2001&quot; = data2001)

for (i in 1:2){
    print(summary(list_data[[i]]$cyl))
 }</code></pre>
<pre><code>##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  4.000   4.000   6.000   6.188   8.000   8.000 
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  4.000   4.000   6.000   6.188   8.000   8.000</code></pre>
<p>But this also might get tedious, especially if you want to do this for a lot of different variables, and want to use different functions than <code>summary()</code>.</p>
<p>Another, simpler way of doing this, is to use <code>purrr::map()</code> or <code>lapply()</code>. But there is a catch though: how do we specify the column we want to work on? Let’s try some things out:</p>
<pre class="r"><code>library(purrr)

map(list_data, summary(cyl))</code></pre>
<pre class="r"><code>Error in summary(cyl) : object 'cyl' not found</code></pre>
<p>Maybe this will work:</p>
<pre class="r"><code>map(list_data, summary, cyl)</code></pre>
<pre><code>## $data2000
     mpg             cyl             disp             hp       
Min.   :10.40   Min.   :4.000   Min.   : 71.1   Min.   : 52.0  
1st Qu.:15.43   1st Qu.:4.000   1st Qu.:120.8   1st Qu.: 96.5  
Median :19.20   Median :6.000   Median :196.3   Median :123.0  
Mean   :20.09   Mean   :6.188   Mean   :230.7   Mean   :146.7  
3rd Qu.:22.80   3rd Qu.:8.000   3rd Qu.:326.0   3rd Qu.:180.0  
Max.   :33.90   Max.   :8.000   Max.   :472.0   Max.   :335.0  
     drat             wt             qsec             vs        
Min.   :2.760   Min.   :1.513   Min.   :14.50   Min.   :0.0000  
1st Qu.:3.080   1st Qu.:2.581   1st Qu.:16.89   1st Qu.:0.0000  
Median :3.695   Median :3.325   Median :17.71   Median :0.0000  
Mean   :3.597   Mean   :3.217   Mean   :17.85   Mean   :0.4375  
3rd Qu.:3.920   3rd Qu.:3.610   3rd Qu.:18.90   3rd Qu.:1.0000  
Max.   :4.930   Max.   :5.424   Max.   :22.90   Max.   :1.0000  
      am              gear            carb      
Min.   :0.0000   Min.   :3.000   Min.   :1.000  
1st Qu.:0.0000   1st Qu.:3.000   1st Qu.:2.000  
Median :0.0000   Median :4.000   Median :2.000  
Mean   :0.4062   Mean   :3.688   Mean   :2.812  
3rd Qu.:1.0000   3rd Qu.:4.000   3rd Qu.:4.000  
Max.   :1.0000   Max.   :5.000   Max.   :8.000  

data2001
     mpg             cyl             disp             hp       
Min.   :10.40   Min.   :4.000   Min.   : 71.1   Min.   : 52.0  
1st Qu.:15.43   1st Qu.:4.000   1st Qu.:120.8   1st Qu.: 96.5  
Median :19.20   Median :6.000   Median :196.3   Median :123.0  
Mean   :20.09   Mean   :6.188   Mean   :230.7   Mean   :146.7  
3rd Qu.:22.80   3rd Qu.:8.000   3rd Qu.:326.0   3rd Qu.:180.0  
Max.   :33.90   Max.   :8.000   Max.   :472.0   Max.   :335.0  
     drat             wt             qsec             vs        
Min.   :2.760   Min.   :1.513   Min.   :14.50   Min.   :0.0000  
1st Qu.:3.080   1st Qu.:2.581   1st Qu.:16.89   1st Qu.:0.0000  
Median :3.695   Median :3.325   Median :17.71   Median :0.0000  
Mean   :3.597   Mean   :3.217   Mean   :17.85   Mean   :0.4375  
3rd Qu.:3.920   3rd Qu.:3.610   3rd Qu.:18.90   3rd Qu.:1.0000  
Max.   :4.930   Max.   :5.424   Max.   :22.90   Max.   :1.0000  
      am              gear            carb      
Min.   :0.0000   Min.   :3.000   Min.   :1.000  
1st Qu.:0.0000   1st Qu.:3.000   1st Qu.:2.000  
Median :0.0000   Median :4.000   Median :2.000  
Mean   :0.4062   Mean   :3.688   Mean   :2.812  
3rd Qu.:1.0000   3rd Qu.:4.000   3rd Qu.:4.000  
Max.   :1.0000   Max.   :5.000   Max.   :8.000</code></pre>
<p>Not quite! You get the summary statistics of every variable, <code>cyl</code> simply gets ignored. This might be ok in our small toy example, but if you have dozens of datasets with hundreds of variables, the output becomes unreadable. The solution is to use an anonymous functions:</p>
<pre class="r"><code>map(list_data, (function(x) summary(x$cyl)))</code></pre>
<pre><code>## $data2000
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  4.000   4.000   6.000   6.188   8.000   8.000 

$data2001
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  4.000   4.000   6.000   6.188   8.000   8.000</code></pre>
<p>This is, in my opinion, much more readable than a loop, and the output of this is another list, so it’s easy to save it:</p>
<pre class="r"><code>summary_cyl &lt;- map(list_data, (function(x) summary(x$cyl)))
str(summary_cyl)</code></pre>
<pre><code>## List of 2
$ data2000:Classes 'summaryDefault', 'table'  Named num [1:6] 4 4 6 6.19 8 ...
 .. ..- attr(*, &quot;names&quot;)= chr [1:6] &quot;Min.&quot; &quot;1st Qu.&quot; &quot;Median&quot; &quot;Mean&quot; ...
$ data2001:Classes 'summaryDefault', 'table'  Named num [1:6] 4 4 6 6.19 8 ...
 .. ..- attr(*, &quot;names&quot;)= chr [1:6] &quot;Min.&quot; &quot;1st Qu.&quot; &quot;Median&quot; &quot;Mean&quot; ...</code></pre>
<p>With the loop, you would need to “allocate” an empty list that you would fill at each iteration.</p>
<p>So this is already nice, but wouldn’t it be nicer to simply have to type:</p>
<pre class="r"><code>summary(list_data$cyl)</code></pre>
<p>and have the summary of variable <code>cyl</code> for each dataset in the list? Well it is possible with the following function I wrote to make my life easier:</p>
<pre class="r"><code>to_map &lt;- function(func){
  function(list, column, ...){
    if(missing(column)){
        res &lt;- purrr::map(list, (function(x) func(x, ...)))
      } else {
        res &lt;- purrr::map(list, (function(x) func(x[column], ...)))
             }
    res
  }
}</code></pre>
<p>By following <a href="http://adv-r.had.co.nz/Function-operators.html">this</a> chapter of Hadley Wickham’s book, <i>Advanced R</i>, I was able to write this function. What does it do? It basically <i>generalizes</i> a function to work on a list of datasets instead of just on a dataset. So for example, in the case of <code>summary()</code>:</p>
<pre class="r"><code>summarymap &lt;- to_map(summary)

summarymap(list_data, &quot;cyl&quot;)</code></pre>
<pre><code>$data2000
     cyl       
Min.   :4.000  
1st Qu.:4.000  
Median :6.000  
Mean   :6.188  
3rd Qu.:8.000  
Max.   :8.000  

$data2001
     cyl       
Min.   :4.000  
1st Qu.:4.000  
Median :6.000  
Mean   :6.188  
3rd Qu.:8.000  
Max.   :8.000</code></pre>
<p>So now everytime I want to have summary statistics for a variable, I just need to use <code>summarymap()</code>:</p>
<pre class="r"><code>summarymap(list_data, &quot;mpg&quot;)</code></pre>
<pre><code>## $data2000
      mpg       
 Min.   :10.40  
 1st Qu.:15.43  
 Median :19.20  
 Mean   :20.09  
 3rd Qu.:22.80  
 Max.   :33.90  

$data2001
      mpg       
 Min.   :10.40  
 1st Qu.:15.43  
 Median :19.20  
 Mean   :20.09  
 3rd Qu.:22.80  
 Max.   :33.90</code></pre>
<p>If I want the summary statistics for every variable, I simply omit the column name:</p>
<pre class="r"><code>summarymap(list_data)</code></pre>
<pre><code>$data2000
      mpg             cyl             disp             hp       
 Min.   :10.40   Min.   :4.000   Min.   : 71.1   Min.   : 52.0  
 1st Qu.:15.43   1st Qu.:4.000   1st Qu.:120.8   1st Qu.: 96.5  
 Median :19.20   Median :6.000   Median :196.3   Median :123.0  
 Mean   :20.09   Mean   :6.188   Mean   :230.7   Mean   :146.7  
 3rd Qu.:22.80   3rd Qu.:8.000   3rd Qu.:326.0   3rd Qu.:180.0  
 Max.   :33.90   Max.   :8.000   Max.   :472.0   Max.   :335.0  
      drat             wt             qsec             vs        
 Min.   :2.760   Min.   :1.513   Min.   :14.50   Min.   :0.0000  
 1st Qu.:3.080   1st Qu.:2.581   1st Qu.:16.89   1st Qu.:0.0000  
 Median :3.695   Median :3.325   Median :17.71   Median :0.0000  
 Mean   :3.597   Mean   :3.217   Mean   :17.85   Mean   :0.4375  
 3rd Qu.:3.920   3rd Qu.:3.610   3rd Qu.:18.90   3rd Qu.:1.0000  
 Max.   :4.930   Max.   :5.424   Max.   :22.90   Max.   :1.0000  
       am              gear            carb      
 Min.   :0.0000   Min.   :3.000   Min.   :1.000  
 1st Qu.:0.0000   1st Qu.:3.000   1st Qu.:2.000  
 Median :0.0000   Median :4.000   Median :2.000  
 Mean   :0.4062   Mean   :3.688   Mean   :2.812  
 3rd Qu.:1.0000   3rd Qu.:4.000   3rd Qu.:4.000  
 Max.   :1.0000   Max.   :5.000   Max.   :8.000  

$data2001
      mpg             cyl             disp             hp       
 Min.   :10.40   Min.   :4.000   Min.   : 71.1   Min.   : 52.0  
 1st Qu.:15.43   1st Qu.:4.000   1st Qu.:120.8   1st Qu.: 96.5  
 Median :19.20   Median :6.000   Median :196.3   Median :123.0  
 Mean   :20.09   Mean   :6.188   Mean   :230.7   Mean   :146.7  
 3rd Qu.:22.80   3rd Qu.:8.000   3rd Qu.:326.0   3rd Qu.:180.0  
 Max.   :33.90   Max.   :8.000   Max.   :472.0   Max.   :335.0  
      drat             wt             qsec             vs        
 Min.   :2.760   Min.   :1.513   Min.   :14.50   Min.   :0.0000  
 1st Qu.:3.080   1st Qu.:2.581   1st Qu.:16.89   1st Qu.:0.0000  
 Median :3.695   Median :3.325   Median :17.71   Median :0.0000  
 Mean   :3.597   Mean   :3.217   Mean   :17.85   Mean   :0.4375  
 3rd Qu.:3.920   3rd Qu.:3.610   3rd Qu.:18.90   3rd Qu.:1.0000  
 Max.   :4.930   Max.   :5.424   Max.   :22.90   Max.   :1.0000  
       am              gear            carb      
 Min.   :0.0000   Min.   :3.000   Min.   :1.000  
 1st Qu.:0.0000   1st Qu.:3.000   1st Qu.:2.000  
 Median :0.0000   Median :4.000   Median :2.000  
 Mean   :0.4062   Mean   :3.688   Mean   :2.812  
 3rd Qu.:1.0000   3rd Qu.:4.000   3rd Qu.:4.000  
 Max.   :1.0000   Max.   :5.000   Max.   :8.000</code></pre>
<p>I can use any function:</p>
<pre class="r"><code>tablemap &lt;- to_map(table)

tablemap(list_data, &quot;cyl&quot;)</code></pre>
<pre><code>## $data2000

 4  6  8 
11  7 14 

$data2001

 4  6  8 
11  7 14</code></pre>
<pre class="r"><code>tablemap(list_data, &quot;mpg&quot;)</code></pre>
<pre><code>## $data2000

10.4 13.3 14.3 14.7   15 15.2 15.5 15.8 16.4 17.3 17.8 18.1 18.7 19.2 19.7 
   2    1    1    1    1    2    1    1    1    1    1    1    1    2    1 
  21 21.4 21.5 22.8 24.4   26 27.3 30.4 32.4 33.9 
   2    2    1    2    1    1    1    2    1    1 

$data2001

10.4 13.3 14.3 14.7   15 15.2 15.5 15.8 16.4 17.3 17.8 18.1 18.7 19.2 19.7 
   2    1    1    1    1    2    1    1    1    1    1    1    1    2    1 
  21 21.4 21.5 22.8 24.4   26 27.3 30.4 32.4 33.9 
   2    2    1    2    1    1    1    2    1    1</code></pre>
<p>I hope you will find this little function useful, and as usual, for any comments just drop me an email by clicking the red enveloppe in the top right corner or <a href="https://twitter.com/brodriguesco">tweet me</a>.</p>
</body>
