
# Policy Support System - WSL

This initiative entails the development of a system dedicated to the generation of case files and the creation of stability modeling visualizations on Tableau.


## Stability Modelling

In this project, our focus will be on modeling the talukas, assessing their performance in relation to various factors like Antenatal care visits(for 2D) and Housing Material availability(for 3D), and subsequently comparing them to inform strategic policy interventions.

To run this, make sure you have pandas, networkx, openpyxl and numpy installed in your system. If not, run this command on your terminal:

```
pip install pandas numpy openpyxl networkx
```

Now, navigate to the stressMapping2D/stressMapping3D file and execute the command 'Run All'. This will execute all cells within the notebook, generating output files in the output_files_2D/output_files_3D folder. Subsequently, these files can be utilized for Tableau visualizations.

Now, Here, is the explaination of the code:

### Making the Graph

`init_graph` : this function just makes the structure of the graph that will represent the talukas. Also, there was some trouble in the Adjecency file(on the 81th taluka), so I simply created the graph, and removed the 81th index taluka(which wasn't there in the 1st place)

`init_graph_attr` : Here, we are just initializing the graph, with capability vector, taluka name, and stress = 0.

### Impact Scaling

The impact values in 3D PIA file were very similar to each other. So, I scaled them using this formula:


`
if Impact[i] > 0 :
            impact_indicator = df[imr_head][i]/net_max
`
            
`
        else:
            impact_indicator = df[imr_head][i]/net_min
`


Note that, net_max and net_min values are the max and min value for the three columns corresponding to Impact in the PIA files.

### Stress

Here, we are calculating stress. These are the steps:

1. Calculate the centeroid for each node, that will be the average of the neighbors of elements of the capability vector. Now, here, we are taking the centroid as the sum of those values, and not average. We will average them at the time of calculation.
2. Now, take the l2 distance between the capability vector of the node and it's centroid. This will be the stress. Also, note that stability is just 1-stress for the taluka, and that is the only difference between get_node_stress and get_node_stability.


### Calculating Stress

Performing stress value calculations for various ANC/HM values presents a challenge as the column names lack a consistent pattern suitable for a straightforward for loop. Additionally, considering the potential for future changes in the column names of the PIA file, a somewhat brute force approach becomes necessary.

Here, will will get the taluka level stress in combined_Impact_ANC/combined_Impact_ANC and district level stress in aggregate_df

### Aggregation

The aggregate function simple takes the average of all the talukas in a district and maps them to the corresponding district. This function outputs a dictionary with key: district name and value: value to be aggregated.


### Dissonance

The dissonance is simply the spread of impact.

For 2D, dissonance is: $$ Dissonance[i] = abs(Impact_{imr}[i] - Impact_{mmr}[i]) $$

And, for 3D, dissonance is: $$ Dissonance[i] = abs(max(Impact_{imr}[i], Impact_{mmr}[i], Impact_{paw}[i]) - min(Impact_{imr}[i], Impact_{mmr}[i], Impact_{paw}[i])) $$



### Sustainable Intervention Score Calculations

The formula for calculating the score is: $$score = {(impact_{avg} * stability) \over dissonance} $$

`district_SI_score`: it calculates the score of the districts. We pass empty lists in it and this function populates them with values. It uses the aggregated values of stress and impacts.

`taluka_SI_score`: it calculates the score of the talukas. We pass empty lists in it and this function populates them with values. It uses the taluka level values of stress and impacts.

`normailize`: takes a dataframe ,column name, empty list, the upper limit and lower limit of normalization values and returna normalized. Let the normalization range be [a, b], and, X be the column, then, the formula used for normalization is: $$X_{normalized}[i] = a + (b - a) * { (X_{original}[i] - max(X_{original})) \over (max(X_{original}) - mix(X_{original}))}$$

## Case Files

### About Case Files
1. The 1st case file comprises indicators and their respective identification codes.
2. The second case file encapsulates parameters, R-square values, and p-values associated with the linear regression line.
3. The third case file encapsulates parameters, R-square values, and p-values associated with the multiple linear regression line.

### Linear Regression

CaseFile-1 and CaseFile-2 are made in CaseFileMaker.ipynb, while the CaseFile-3 is made in CF3.ipynb
Linear regression was used from sklearn in the casefile2.
Multivariate Linear regression from statsmodel was used for casefile3.


## Tableau Visualizations

The output_files_2D and output_files_3D contain the data needed for visualizing them in Tableau
