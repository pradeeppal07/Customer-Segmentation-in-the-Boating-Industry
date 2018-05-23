# Customer-Segmentation-in-the-Boating-Industry
Develop a segmentation that was mostly driven by attitudes that consumer held regarding boating.

**J. Niessing, INSEAD**

**T. Evgeniou, INSEAD**


The Business Issue
---------------------------------------------------------

A boating company had become a victim of the crisis in the boating industry. The business problem of the "Boat" case study, although hypothetical, depicts very well the sort of business problems faced by many real companies in an increasingly data-intensive business environment. The management team was now exploring various growth options. Expanding further in some markets, in particular North America, was no longer something to consider for the distant future. It was becoming an immediate necessity. 

The team believed that in order to develop a strategy for North America, they needed a better understanding of their current and potential customers in that market. They believed that they had to build more targeted boats for their most important segments there. To that purpose, the boating company had been commissioned a segmentation project for that market. Being a data-friendly company, the decision was made to develop a segmentation in a data-driven way - none of that soft hold-hands-and-discuss stuff. 


The Data
--------------------------------------------

With the aid of a market research firm, the boating company gathered various data about the boating market in the US through interviews with almost 3,000 boat owners and intenders. The data consisted, among others, 29 attitudes towards boating, which respondents indicated on a 5-point scale (Q. 1, 1-29 shown in the survey in the appendix).

Other types of information had been collected, such as demographics (Q. 11-15 in the survey). Finally, the boating company had compiled information about the boats, such as the length of the boat they owned, how they used their boats, and the price of the boats (Q. 2-10 in the survey). A segmentation and profile of the market would be done using all this data. 

The Approach
--------------------------------------------

Based on past experience, the decision was made to develop a segmentation that was mostly driven by attitudes that consumer held regarding boating - the attitudinal data from the 29 questions in Q.1.

### Factor Analysis

As a first step, the company decided to simplify the data by grouping the 29 attitudinal statements in Q1 into a few meaningful sets/factors. This could be done using a standard method: factor analysis. The objective is to summarize information (the variation) in correlated raw attributes to a smaller number of manageable factors - which are typically uncorrelated or independent. In the process one decreases the number of raw attributes while keeping most of the information in the data in a statistical sense. This data reduction technique is a very useful step in helping interpreting the data and make better decisions. It also helps to identify the key variables that should be used in the segmentation procedure. 

We first have to read the whole dataset, which is in CSV format.

For the Factor Analysis, we will only use the 29 items of the data that describe the attitudes towards boating of people in the sample. Such 29 items are measured on a Likert scale of 1-5. All the 29 items are integer variables that take values between 1 (=strongly disagree) and 5 (=strongly agree).

Every factor analysis has three basic decision points:  

- Decide the number of factors, 

- Choose an extraction method, 

- Choose a rotation method.  


Sometimes it makes sense to run a quick correlation matrix before the factor analysis. It usually gives a first glance at the inter-relationships between variables and it is often an input for factor analysis (i.e. eigenvalue decomposition of the correlation matrix). In this example the correlaton matrix for the 29 attitudes looks like this: 

<style>
.wrapper{


width: 100%;

overflow-x: scroll;

}
.wrapper1{

height:400px;
overflow-y: scroll;
}
th.wrapper{
display:block;
}

</style>




<div class="wrapper wrapper1 th.display">

```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE, results='asis'}
thecor = round(cor(ProjectDataFactor),2)
thecor=cor(ProjectDataFactor)
#colnames(thecor)<-colnames(thecor)
rownames(thecor)<-rownames(thecor)
## printing the result in a clean-slate table


cat(renderHeatmapX(thecor, border=1))

```

</div>
</div>
### Number of Factors

There are three common ways to select the optimal number of factors from a statistical perspective:

1. All factors with eigenvalue larger than 1

2. Based on the cumulative variance we would like to explain (e.g. at least 50% for example)

3. Pick the number of factors corresponding to the "elbow" of the scree plot

The first two approaches to select the number of factors/derived variables can be seen in the following table: 


```{r echo=FALSE, comment=NA, warning=FALSE, error=FALSE,message=FALSE,results='asis'}
Variance_Explained_Table_results<-PCA(ProjectDataFactor, graph=FALSE)
Variance_Explained_Table<-Variance_Explained_Table_results$eig
Variance_Explained_Table_copy<-Variance_Explained_Table_results

row=1:nrow(Variance_Explained_Table)
name<-paste("Component No:",row,sep="")
Variance_Explained_Table<-cbind(name,Variance_Explained_Table)
Variance_Explained_Table<-as.data.frame(Variance_Explained_Table)
colnames(Variance_Explained_Table)<-c("Components", "Eigenvalue", "Percentage_of_explained_variance", "Cumulative_percentage_of_explained_variance")

#print(gvisTable(Variance_Explained_Table, options=list(width=1200, height=400,allowHTML=TRUE,page='disable',pageSize=15,showRowNumber=FALSE,cssClassNames={tableCell='td.tableRow'})),formats=list(Eigenvalue="#,##") ,'chart')

m<-gvisTable(Variance_Explained_Table,options=list(width=1200, height=400,allowHTML=TRUE,page='disable'),formats=list(Eigenvalue="#.##",Percentage_of_explained_variance="#.##",Cumulative_percentage_of_explained_variance="#.##"))
print(m,'chart')
#Variance_Explained_Table_results<-PCA(ProjectDataFactor, graph=FALSE)
#keepthis<-Variance_Explained_Table_results$eig$eigenvalue
#Variance_Explained_Table_copy<-Variance_Explained_Table
#Variance_Explained_Table<-Variance_Explained_Table_results$eig


#row=1:nrow(Variance_Explained_Table)
#name<-paste("Component No:",row,sep="")
#Variance_Explained_Table1<-cbind(name,Variance_Explained_Table)
#Variance_Explained_Table1<-as.data.frame(Variance_Explained_Table1)
#colnames(Variance_Explained_Table1)<-c("Components", "Eigenvalue", "Percentage_of_explained_variance", "Cumulative_percentage_of_explained_variance")


#m<-gvisTable(Variance_Explained_Table1,options=list(width=1200, height=400,allowHTML=TRUE,page='disable'),formats=list(Eigenvalue="#.##",Percentage_of_explained_variance="#.##",Cumulative_percentage_of_explained_variance="#.##"))
#print(m,'chart')
```


```{r echo=FALSE, comment=NA, warning=FALSE,message=FALSE,results='asis'}
if (factor_selectionciterion == "eigenvalue")
  factors_selected = which(Variance_Explained_Table_copy$eig$eigenvalue > 1)
if (factor_selectionciterion == "variance")
  factors_selected = 1:head(which(Variance_Explained_Table_copy$eig[,"cumulative percentage of variance"]>= minimum_variance_explained),1)
if (factor_selectionciterion == "manual")
  factors_selected = manual_numb_factors_used

#if (factor_selectionciterion == "eigenvalue")
# factors_selected = which(keepthis> 1)
#if (factor_selectionciterion == "variance")
# factors_selected = 1:head(which(Variance_Explained_Table1[,"Cumulative_percentage_of_explained_variance"]>= minimum_variance_explained),1)
```
<br>
<br>
In this data `r sum(Variance_Explained_Table[,2] > 1)`  factors have an eigenvalue > 1. But the team didn't wanted to select the number based on statistical criteria only. They looked at different factor solutions (varying from 4 to 14 factors in total) and realized that they would loose too much information if they would only go with 5 factors. Looking also at the explained variance and based on business sense the team decided to use 10 factors. As shown in the table above these 10 factors explain  `r round(Variance_Explained_Table[10,4],2)` % of the variance. If they would stick to the 5 factors with an eigenvalue over 1 they would only explain a little bit more than 50% of the variance. 

A common basic visualization when running the factor analysis is the Screeplot, which depicts the eigenvalues of the factors and thus allows gaining a quick overview on how many factors should be retained:

<center>
```{r Fig2, echo=FALSE, comment=NA, results='asis', message=FALSE, fig.align='center', fig=TRUE}
Unrotated_Results<-principal(ProjectDataFactor, nfactors=ncol(ProjectDataFactor), rotate=rotation_used)
eigenvalues <- Unrotated_Results$values
df           <- cbind(as.data.frame(eigenvalues), c(1:length(eigenvalues)), rep(1, length(eigenvalues)))
colnames(df) <- c("eigenvalues", "components", "abline")
Line         <- gvisLineChart(as.data.frame(df), xvar="components", yvar=c("eigenvalues","abline"), options=list(title='Scree plot', legend="right", width=900, height=600, hAxis="{title:'Number of Components', titleTextStyle:{color:'black'}}", vAxes="[{title:'Eigenvalues'}]",  series="[{color:'green',pointSize:12, targetAxisIndex: 0}]"))
print(Line, 'chart')
```
</center>

Based on this screeplot you could say that the selected number of factors should be between 3 and 6 factors but as mentioned above the team decided to use 10 factors based on the explained variance because the explained variance was the key selection criterion in this case. 

### Extraction Method

The extraction method will produce factor loadings for every item on every extracted factor. Researchers hope their results will show what is called simple structure, with most items having a large loading on one factor but small loadings on other factors.

There at least five extraction methods. The evidence supports the use of one of these two:

- principal axis factoring
- maximum likelihood approaches (i.e. canonical factoring)

The extraction and rotation steps are usually specificed in the same command in R (next subsection shows such commands).

### Rotation Method

Once an initial solution is obtained, the loadings are rotated. Rotation is a way of maximizing high loadings and minimizing low loadings so that the simplest possible structure is achieved.

There are two commen types:

- no rotation

- `r rotation_used `, which is one of the key rotation methods used

We use the fa() function from the library Phsych because it allows us to specify different factoring methods (e.g. PAF, ML) and also different types of rotation (e.g. varimax, Oblimin and others). In such a function we have to specify r (the corelation matrix: corMat), nfactors (number of factors), rotate (type of rotation or transformations of the solution: oblimin), and fm (factoring method: principal axis).

Below are the tables of factor loadings and other statistics for the principal axis factoring and two rotation scenarios. Only the factors selected based on `r factor_selectionciterion` are shown. The first table is showing the unrotated 10-factor solution while the second table is showing the results from the variamx rotation: 


```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE,results='asis'}
UnRotated_Results<-principal(ProjectDataFactor, nfactors=ncol(ProjectDataFactor), rotate="none",score=TRUE)

UnRotated_Factors<-UnRotated_Results$loadings[,factors_selected]
UnRotated_Factors<-as.data.frame(unclass(UnRotated_Factors))
colnames(UnRotated_Factors)<-paste("Component",1:ncol(UnRotated_Factors),sep=" ")

UnRotated_Factors[abs(UnRotated_Factors) < MIN_VALUE]<-NA


row<-rownames(UnRotated_Factors)

dfnew<-cbind(row,UnRotated_Factors)
change<-colnames(dfnew)
change[1]<-"Attitudes"
colnames (dfnew)<-change

dfnew[,2:ncol(dfnew)]<-round(dfnew[,2:ncol(dfnew)],digits=2)

m1<-gvisTable(dfnew,options=list(showRowNumber=TRUE,width=1220, height=400,allowHTML=TRUE,page='disable'))
print(m1,'chart')

```

<p> </p>

```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE,results='asis'}

Rotated_Results<-principal(ProjectDataFactor, nfactors=max(factors_selected), rotate=rotation_used,score=TRUE)
Rotated_Factors<-Rotated_Results$loadings
Rotated_Factors<-as.data.frame(unclass(Rotated_Factors))
colnames(Rotated_Factors)<-paste("Component",1:ncol(Rotated_Factors),sep=" ")

sorted_rows <- sort(Rotated_Factors[,1], decreasing = TRUE, index.return = TRUE)$ix
Rotated_Factors <- Rotated_Factors[sorted_rows,]
Rotated_Factors[abs(Rotated_Factors) < MIN_VALUE]<-NA

row1<-rownames(Rotated_Factors)

dfnew1<-cbind(row1,Rotated_Factors)

change<-colnames(dfnew1)
change[1]<-"Attitudes"
colnames (dfnew1)<-change

dfnew1[,2:ncol(dfnew1)]<-round(dfnew1[,2:ncol(dfnew1)],digits=2)

m2<-gvisTable(dfnew1,options=list(showRowNumber=TRUE,width=1220, height=400,allowHTML=TRUE,page='disable'))
print(m2,'chart')
```
<br>
<br>
From the factor analysis above, we can identify the attitudinal questions that clearly load into a single derived factor (i.e. a construct). Based on this, we can have an idea of the underlying construct (not observed in the data) that determines the attitudes of different customers. For instance, Q1_27, Q1_28 and Q1_20 load strongly into the same factor (besides other attitudes). Looking at the description of these questions, we can have an idea of the construct that determines the customer responses to these attitude questions. The questions have the following statements:

Q1_27: "Boating is the number one thing I do with my spare time"

Q1_28: "Boating is my true passion in life"

Q1_20: "When not on my boat, I often find myself doing boating related activities"

Based on the descriptions of the attitudes that are loading into the first factor, we could say that the first factor measures a construct of "Passion and expertise around boating". We do not observe the extent to which passion is important for the respondents (the questionnaire does not ask a direct question about something called "personal image"); however, the actual attitudes allow deriving such construct.

Based on the factor analsyis described above we can obtain 10 factor scores for each of almost 3000 customers. These 10 new derived variables can be used instead of the whole 29 attitudinal variables for further analysis. Once we decided the factors to use (like in this example now 10), we typically replace the original data with a new dataset where each observation (row) is now described not using the original raw attributes but using instead the selected factors/derived attributes. 

The factor scores are useful because they allow comparing customers based on few attitude dimensions (in this case 10 of them). For creating market segments, we could use these `r length(factors_selected)` derived variables (constructs) instead of the 29 raw attitudinal variables. For example the factor scores for the first 5 people are:

```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE,results='asis'}
NEW_ProjectData <- Rotated_Results$scores[,factors_selected,drop=F]
colnames(NEW_ProjectData)<-paste("Derived Variable (Factor)",1:ncol(NEW_ProjectData),sep=" ")
print(xtable(head(NEW_ProjectData,5) ,paste("Sample Data described using the selected Factors:",data_name,sep=" "), digits=1,),type="html",html.table.attributes = "class='table table-striped table-hover table-bordered'",caption.placement="top",comment = FALSE,include.rownames = TRUE)
```

Instead of the derived constructs we could also use 'a' single statement from each factor as a proxy for the factor. Usually researchers pick the attitude per factor with the highest loading for that specific factor (eg. looking at the rotated factor loadings in the table above we would choose Q1_27 for the first factor: "Boating is the number one thing I do with my spare time"). This is the approach that we would like to follow here. So for the 2nd step - the cluster analysis - we are using the following 10 statements instead of the full list of 29 statements. Each of these statements has the highest loading within the factor they belong to:

<br>
```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE,results='asis'}
large_loadings <-as.matrix(rownames(Rotated_Factors)[apply(Rotated_Factors,2,which.max)],ncol=1)
rownames(large_loadings)<-paste("Factor",1:nrow(large_loadings), sep=" ")
colnames(large_loadings)<-c("Raw Attributes")
print(xtable(large_loadings ,paste("Raw attributes with highest loading per factor:",data_name,sep=" "), digits=1,),type="html",html.table.attributes = "class='table table-striped table-hover table-bordered'",caption.placement="top",comment = FALSE,include.rownames = TRUE)

```

Cluster Analysis
--------------------------------------------

Clustering organizes observations that are close into groups. This will allow us to define different market segments. In cluster analysis we have to define:

How do we define close?
How do we group things?
How do we visualize the grouping?
How do we interpret the grouping?

Clustering analysis involves calculating how different indivudals in the sample are based on a series of attributes. For instance, in the factor analysis we calculated for each individual in the sample 10 factor scores that summarize well the variance of the 29 attitudional questions. 

People that are similar in terms of the 10 selected attitudinal characteristics can be grouped together. The dendrogram allows having a quick visualization of how different customers stick together. The dendrogram groups customers 'little by little' based on the how different (similar) the 10 attitudes are. People with similar attitudes would stick together and depending on where you cut the dendogram tree you can have different groups or segments. The question of how many groups or clusters we will have depends on the structure of the data but also involves some intuition or knowledge about the market so that the solutions makes not only sense from a statistical perspective but also from a business perspective. So clustering is a very iterative process where the team needs to look at many different solutions to identify the optimal solutions. The optimal solution can only be identified if we profile these solutions and interpret and compare the different segment profiles. 

After a few iterations the team looked at a few different segment profiles (3-7 segment solutions). Based on the inital segment profiles we have decided to move on with a 5 cluster solution as the optimal solution from a business perspective in the boating industry. 

The figure below is the Dendrogram. 

```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE,results='asis'}
# THESE ARE THE SEGMENTATION ATTRIBUTES USED
segmentation_attributes_used=large_loadings
ProjectData_segment= ProjectData[,segmentation_attributes_used]

```

```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE,results='asis'}
Hierarchical_Cluster_distances<-dist(ProjectData_segment,method="euclidean")
Hierarchical_Cluster <- hclust(Hierarchical_Cluster_distances, method="ward")
plot(Hierarchical_Cluster,main = NULL, sub=NULL,labels = 1:nrow(ProjectData_segment), xlab="Our Observations", cex.lab=1, cex.axis=1) # display dendogram
# draw dendogram with red borders around the 3 clusters
rect.hclust(Hierarchical_Cluster, k=numb_clusters_used, border="red") 
```

The next figure (distance plot) displays the heights of the dendrogram branches as we move from smaller to larger segments:

```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE, fig.align='center', results='asis'}
max <- nrow(ProjectData)
num <- max - 1
df1 <- cbind(as.data.frame(Hierarchical_Cluster$height[length(Hierarchical_Cluster$height):1]), c(1:num))
colnames(df1) <- c("distances","index")
Line <- gvisLineChart(as.data.frame(df1), xvar="index", yvar="distances", options=list(title='Distances plot', legend="right", width=900, height=600, hAxis="{title:'Number of Components', titleTextStyle:{color:'black'}}", vAxes="[{title:'Distances'}]", series="[{color:'green',pointSize:12, targetAxisIndex: 0}]"))
print(Line,'chart')
```


### Defining the Market Segments

Once the team has decided on the number of factors they have to profile and describe these segments in a better way. Based on the groups identified by the cluster analysis (recall that cluster analysis calculates differences/similarities of customers based on 10 attitudes that stand as proxies for the 10 identified factors) we now have to profile `r numb_clusters_used` market segments. Below we have the number of observations (i.e. the number of customers of the sample) in each market segment:

<br>
```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE,results='asis'}
cluster_memberships_hclust <- as.vector(cutree(Hierarchical_Cluster, k=numb_clusters_used)) # cut tree into 3 clusters
cluster_ids_hclust=unique(cluster_memberships_hclust)

cluster_sizes <- matrix(sapply(cluster_ids_hclust, function(i) sum(cluster_memberships_hclust==i)), nrow=1)
colnames(cluster_sizes) <-paste("Size of Cluster", 1:length(cluster_ids_hclust), sep=" ")

print(xtable(cluster_sizes ,paste("Size of Clusters:",data_name,sep=" "), digits=1,),type="html",html.table.attributes = "class='table table-striped table-hover table-bordered'",caption.placement="top",comment = FALSE,include.rownames = TRUE)

```


In a next step we can fully profile each of the 5 segments using the other variables from the survey. We can for example look at differences across groups in variables such as income, age, boat preferences, etc. and determine if those differences are significant from a statistical point of view when the data is continuous (e.g. the level income). The usual approach is to use ANOVA (or MANOVA if we want to compare multiple variables across groups at the same time).

In this example most of the variables are categorical. In such cases we can look at the frequency of each of the categories of a variable for each segment group (segment profile). Each variable can be used to decscribe the segments. Ideally we find variables where segments differ a lot but it could also be the case that segments are very similar for some variables. Eg. in the table below we can see that each of the 5 segments have a similar proportion of people in each income category. Therefore, we cannot say that one segment is necessarily wealthier than the other.

<br>

```{r echo=FALSE, comment=NA, warning=FALSE,error=FALSE, message=FALSE,results='asis'}
###THESE LINES PRODUCE THE PROBLEM######
#income_table <- matrix(sapply(cluster_ids_hclust,function(s) mean(ProjectData[which(cluster_memberships_hclust==s),"Q12_HOUSEHOLD.income"])), nrow=1)
#colnames(income_table) <-paste("Average Income of Cluster", 1:length(cluster_ids_hclust), #sep=" ")
#Stratos lines below:
d<-cbind(cluster_memberships_hclust,ProjectData[,"Q12_HOUSEHOLD income"])
d<-as.data.frame(d)
colnames(d)<-c("fit","Q12_HOUSEHOLD income")
r<-0
mean<-list()
for (j in 1:length(cluster_ids_hclust)){
  sum<-0
  k<-0
  for (i in 1:nrow(d)){
    if (d$fit[i]==j){
      k<-k+1
      sum<-sum+d$Q12_HOUSEHOLD.income[i]
      }
    
    }
  r<-r+1
  mean[[r]]<-sum/k
  
  }
income_table<-as.data.frame(mean)
names<-vector()
for (p in 1:length(cluster_ids_hclust) ){
  names[p]<-paste("Average income in cluster:",p,sep="")
  }

colnames(income_table)<-names
income_table<-as.data.frame(income_table)

print(xtable(income_table ,paste("Average income per clusters:",data_name,sep=" "), digits=1,),type="html",html.table.attributes = "class='table table-striped table-hover table-bordered'",caption.placement="top",comment = FALSE,include.rownames = TRUE)

```


This is why finding the optimal solution is a very itereative process. We need to interpret not only the statistical criteria. It is even more important to look at different segment profiles to find the solution that makes the most business sense.

The tables below show a more detailed segment profile for the 5 segments that the team decided to use. The first table is showing the average scores for all 29 attitudes for the 5 segments:


<br>

<style>
.wrapper{


width: 100%;

overflow-x: scroll;

}
.wrapper1{

height:400px;
overflow-y: scroll;
}
</style>
<div class="wrapper wrapper1">

```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE,results='asis'}
profile_table1 <- Reduce(rbind, lapply(profile_attributes_used1, function(profile_id) (sapply(cluster_ids_hclust,function(s) mean(ProjectData[which(cluster_memberships_hclust==s),profile_id ])))))
colnames(profile_table1) <-paste("Average of Cluster", 1:length(cluster_ids_hclust), sep=" ")
rownames(profile_table1)<- colnames(ProjectData)[profile_attributes_used1]

print(xtable(profile_table1 ,paste("Average value per clusters:",data_name,sep=" "), digits=1,),type="html",html.table.attributes = "class='table table-striped table-hover table-bordered'",caption.placement="top",comment = FALSE,include.rownames = TRUE)

```
</div>
<br>
<style>
.wrapper{


width: 100%;

overflow-x: scroll;

}
.wrapper1{

height:400px;
overflow-y: scroll;
}
</style>
<div class="wrapper wrapper1">

With these results the team is now able to understand the attitudes for each segment in a much better way by comparing the scores across the segments - attitudes by attitude. E.g. if you look at attitude #7 "Owning a boat gives me a sense of achievement" you see that segment 2 (with a score of 4.4) scores much higher than segment 4 (score of 3.2). So you could make the point that segment 2 tends to be a "status seeker". Remember that the attitudes were measured on a 5-point scale from 1=strongly disagree to 5=strongly agree (see also survey in the appendix).

The next table is showing the demographics for each segment that also need to be compared segment by segment to build more robust segment profiles. 

<br>
```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE,results='asis'}
profile_table2 <- Reduce(rbind, lapply(profile_attributes_used2, function(profile_id) (sapply(cluster_ids_hclust,function(s) mean(ProjectData[which(cluster_memberships_hclust==s),profile_id ])))))
colnames(profile_table2) <-paste("Average of Cluster", 1:length(cluster_ids_hclust), sep=" ")
rownames(profile_table2)<- colnames(ProjectData)[profile_attributes_used2]

print(xtable(profile_table2 ,paste("Average value per clusters:",data_name,sep=" "), digits=1,),type="html",html.table.attributes = "class='table table-striped table-hover table-bordered'",caption.placement="top",comment = FALSE,include.rownames = TRUE)

```
</div>
<br>
<style>
.wrapper{


width: 100%;

overflow-x: scroll;

}
.wrapper1{

height:400px;
overflow-y: scroll;
}
</style>
<div class="wrapper wrapper1">

<br>
The last table is showing all the remaing questions for the final 5-segment solution. 

<br>
```{r echo=FALSE, comment=NA, warning=FALSE, message=FALSE,results='asis'}
profile_table3 <- Reduce(rbind, lapply(profile_attributes_used3, function(profile_id) (sapply(cluster_ids_hclust,function(s) mean(ProjectData[which(cluster_memberships_hclust==s),profile_id ])))))
colnames(profile_table3) <-paste("Average of Cluster", 1:length(cluster_ids_hclust), sep=" ")
rownames(profile_table3)<- colnames(ProjectData)[profile_attributes_used3]

print(xtable(profile_table3 ,paste("Average value per clusters:",data_name,sep=" "), digits=1,),type="html",html.table.attributes = "class='table table-striped table-hover table-bordered'",caption.placement="top",comment = FALSE,include.rownames = TRUE)

```
</div>
<br>

APPENDIX: The complete survey
--------------------------------------------

**ATTITUDES / NEEDS / MOTIVATIONS**

1. Listed below are some statements regarding attitudes and beliefs you may or may not have regarding boating.  Using the scale provided, please indicate how much you agree or disagree with each statement.

Strongly Disagree (1)

Disagree (2)

Neither Agree or Disagree (3)

Agree (4)

Strongly Agree (5)

1.  When buying a boat, I do a lot of shopping around and visit multiple dealers
2.        When buying a boat, getting the lowest price is more important than the boat brand
3.        The brand of boat I buy says a lot about who I am
4.        I only consider buying a boat from a reputable brand
5.        I am willing to pay a premium for a brand with a reputation for high quality
6.        Owning a boat is a way of rewarding myself for my hard work
7.        Owning a boat gives me a sense of achievement
8.        When buying a boat, functionality is more important than style
9.        I see my boat as a status symbol
10.        When buying a boat, I rely more on expert opinion than my own (e.g. consumer reports, salespeople)
11.        I tend to perform minor boat repairs and maintenance on my own
12.        When it comes to boating, I tend to prefer a basic boat with little to no frills
13.        When buying a boat, I tend to buy the latest and greatest
14.        When buying accessories for my boat, I tend to buy the latest and greatest
15.        I am serious about the technology on my boat
16.        People tend to come to me for advice about boating
17.        I consider myself more knowledgeable about boating than the rest of my boating peers
18.        Boating gives me a feeling of adventure
19.        Boating allows me to experience nature
20.        When not on my boat, I often find myself doing boating related activities (e.g., planning trips, working on my boat)
21.        Boating helps me escape from everyday life and relax
22.        Boating helps me stay active
23.        Boating allows me to excel in the sports that I am passionate about  (e.g. fishing, racing, water sports)
24.        Boating gives me an outlet to socialize with family and/or friends
25.        Being in charge of my boat is very important to me
26.        Having a powerful boat is what is most important to me
27.        Boating is the number one thing I do with my spare time
28.        Boating is my true passion in life
29.        Boating is one of many things I do in my spare time

**PURCHASE PROCESS AND BEHAVIOR**

2. How many powerboats do you currently own?

3. How likely would you be to purchase a new boat in the future?

Definitely will not (1)

Probably will not (2)

Might or might not (3)

Probably will (4)

Definitely will (5) 

4. Not including any taxes, fees or accessories, approximately how much did you pay for your boat? How much are you planning to spend on your new boat?

 1. Less than $10K
  
  2. $10K to $19,999K
  
  3. $20K to $29,999K
  
  4. $30K to $49,999K
  
  5. $50K to $74,999K
  
  6. $75K to $124,999K
  
  7. $125K to $199,999K
  
  8. $200K to $499,999K
  
  9. $500K to $999,999K
  
  l0.$1,000,000 or more
  
  11. Don't know

5. What is the horse power of your engine? What horse power are you looking for? 

1. 0 - 14 HP
2. 15- 60 HP
3. 61 - 115 HP
4. 116 - 150 HP
5. 151 - 200 HP
6. 201 - 250 HP
7. 251 - 350 HP
8. more than 350 HP
9. Don't Know


6. What is the length of your boat? What is the length of the boat you are considering buying? (e.g., 20 feet) 

7. Which of the following statements best describes WHO you boat with?

Never (1), Rarely (2), Sometimes (3), Often (4), Most of the time (5)

1.        I normally boat alone
2.        I boat with my spouse or significant other
3.        I boat with my family, including kids
4.        I boat with my friends

8. How would you rate your experience regarding boating?

1. Beginner
2. Intermediate
3. Advanced
4. Expert

9. Below is a list of activities that may or may not do while boating. Using the scale provided, please indicate how often you engage in each of these activities.  

Never (1)
Rarely (2)
Sometimes (3) 
Often (4)
Most of the time (5)

1.        Fishing
2.        Swimming
3.        Cruising
4.        Water Sports (e.g., skiing, tubing, wakeboarding)
5.        Entertaining/socializing

10. During your boating season, how many days out of the year do you typically use your boat?

**DEMOGRAPHICS**

11. What is your gender? 
1. Male         
2. Female         

12.   We would like to get a better estimate of your total HOUSEHOLD income in the past 12 months before taxes.
1. Less than $5,000        
2. $5,000 to $7,499        
3. $7,500 to $9,999        
4. $10,000 to $12,499        
5. $12,500 to $14,999        
6. $15,000 to $19,999        
7. $20,000 to $24,999        
8. $25,000 to $29,999        
9. $30,000 to $34,999        
10. $35,000 to $39,999        
11. $40,000 to $49,999        
12. $50,000 to $59,999        
13. $60,000 to $74,999        
14. $75,000 to $84,999                
15. $85,000 to $99,999        
16. $100,000 to $124,999        
17. $125,000 to $149,999        
18. $150,000 to $174,999        
19. $175,000 or more

13.   Are you now married, widowed, divorced, separated, never married, or living with a partner?
1. Married        
2. Widowed        
3. Divorced        
4. Separated        
5. Never married        
6. Living with partner

14.   Which statement best describes your current employment status?
1. Working - as a paid employee        1
2. Working - self-employed         2
3. Not working - on temporary layoff from a job
4. Not working - looking for work
5. Not working - retired        
6. Not working - disabled        
7. Not working - other        

15.   What is your age?

**BOATS: BRAND EQUITY **

16. Below are various statements that could describe brand's PRODUCTS, IMAGE or REPUTATION. Please indicate how much you agree or disagree with the statements for the following brand that you are familiar with: INSERT BRAND THAT NEEDS TO BE RATED (eg. Alumacraft, Bayliner, etc)

Strongly Disagree (1)

Disagree (2)

Neither Agree or Disagree (3)

Agree (4)

Strongly Agree (5)


Origin/ Heritage

1.        Is a brand that has been around for a long time Brand reputation

2.        Has best in-class customer service

3.        Has a strong dealer network Brand leadership

4.        Is a leader in cutting edge technology

5.        Is a leader in safety 

6.        Is known for its innovative products 

Users of the brand

7.        Is a brand for people who are serious about boating

8.        Is a good brand for people that are new to boating

9.        Is a brand I see in the water all the time

Boating Experience

10.        Offers boats that provide a fast and powerful boating experience

11.        Offers the best boats for socializing

12.        Offers the best boats for water sports (e.g., tubing, ski, wakeboard)

Aesthetics

13.        Offers boats with superior interior style

14.        Offers boats with superior exterior style

15.        Offers boats that stand out from the crowd

16.        Offers boats that look cool

Product Quality/ Reliability/ Innovation

17.        Offers boats that can handle rough weather or choppy water

18.        Offers boats that can handle frequent and heavy usage

19.        Offers a wide breadth of product offerings and accessories

Product Safety

20.        Offers boats that I can move around safely

Ease of use / convenience

21.        Offers boats that are easy to maintain and/or repair

22.        Offers boats that are easy to use

23.        Offers boats that are easy to clean up

Value/Price

24.        Is low price

Emotional Benefits

25.        Is a brand that gives me peace of mind

26.        Makes me feel I made a smart decision

Self Expressive Benefits

27.        Is a brand that impresses others



BOATS: 

BRAND FUNNEL

17. How likely would you be to recommend that brand to other boaters?   

0=would not recommend/don't know                 

1=would definitely recommend

18. If you were buying a new boat all over again, would you buy that brand in the future? 

0=no                                                

1=yes
