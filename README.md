# Data_Visualization_Challenge
### Analysis

- Analize potential treatments for squamous cell carcinoma (SCC), a commonly occurring form of skin cancer.

 In this study, 249 mice, 49.39% female mice and 50.60% male mice (fig.1) were analyzed who were identified with SCC tumors received treatment with a range of drug regimens. Over the course of 45 days, tumor development was observed and measured. The purpose of this study was to compare the performance of Pymaceuticals’ drug of interest, Capomulin, against the other treatment regimens.
![image](https://github.com/MelS18/Data_Visualization_Challenge/assets/129136787/ae517f86-063f-479c-bfdb-bf145061fb1b)


Some of the results of the different treatments tested in mice, it was found that the drug Ramicane had the lowest average tumor volume with an average value of 40.21 and a standard deviation of 4.84, On the other hand, the drug that obtained a lower variance in tumor volume was Capomulin with a value of 24.94 and a standard deviation of 4.99. The only treatment that presented potential outliers was Infubinol

![image](https://github.com/MelS18/Data_Visualization_Challenge/assets/129136787/d76a0c22-5a3f-4406-b3eb-85d1fa9f5c90)

Looking at the behavior of a mouse treated with Capomulini during the first 40 days, an important reduction of the tumor is seen in almost 5 mm3.
![image](https://github.com/MelS18/Data_Visualization_Challenge/assets/129136787/d8d53daa-cb38-4d96-9028-282da01c8d48)

Finally, in an analysis of the correlation coefficient and the linear regression model, a coefficient of 0.88 is obtained and a linear regression is obtained.
![image](https://github.com/MelS18/Data_Visualization_Challenge/assets/129136787/af7280e6-84e6-47e5-86a2-d678c8002626)
 
 ## The Code 
- Dependencies and Setup

-Study data files
 mouse_metadata_path = "data/Mouse_metadata.csv"
study_results_path = "data/Study_results.csv"

-Combine the data into a single DataFrame

-Display the data table for preview
maceutical_complete.head()

-Checking the number of mice.
maceutical_complete["Mouse ID"].nunique()

-data should be uniquely identified by Mouse ID and Timepoint
duplicates = maceutical_complete[maceutical_complete.duplicated(["Mouse ID", "Timepoint"], keep= False)]

-Get the duplicate mice by ID number that shows up for Mouse ID and Timepoint. 

-Create a clean DataFrame by dropping the duplicate mouse by its ID
# Summary Statistics
-Generate a summary statistics table of mean, median, variance, standard deviation, and SEM of the tumor volume for each regimen

-Use groupby and summary statistical methods to calculate the following properties of each drug regimen: 

- mean, median, variance, standard deviation, and SEM of the tumor volume. 

- group the data by drug regimen and calculate the summary statistics

-group the data by drug regimen and calculate the summary statistics

summary_df = maceutical_complete.groupby('Drug Regimen')['Tumor Volume (mm3)'].agg(['mean', 'median', 'var', 'std','sem'])

-rename columns

summary_df = summary_df.rename(columns={'mean':'Mean Tumor Volume', 'median':'Median Tumor Volume', 'var':'Tumor VolumeVariance', 'std':'Tumor Volume Std. Dev', 'sem': 'Tumor Volume Std. Err.'}) 

-display the summary statistics table

summary_df

-Using the aggregation method, produce the same summary statistics in a single line

summary_df2 = maceutical_complete.groupby('Drug Regimen')['Tumor Volume (mm3)'].agg(['mean', 'median', 'var', 'std','sem'])
summary_df2

# Bar and Pie Charts
-Generate a bar plot showing the total number of rows (Mouse ID/Timepoints) for each drug regimen using Pandas.

drug_regimen_counts = maceutical_complete.groupby('Drug Regimen').count()['Mouse ID']

-Create a bar plot using the drug_regimen_counts series

drug_regimen_counts.plot(kind='bar', figsize=(10,5))

-Set the title and axis labels

plt.xlabel('Drug Regimen')

plt.ylabel('# Of Observed Mouse Timepoints')

-Display the plot

-Generate a bar plot showing the total number of rows (Mouse ID/Timepoints) for each drug regimen using pyplot.

-Create a list of unique drug regimens

drug_regimens = maceutical_complete['Drug Regimen'].unique()

-Create a dictionary to store the total number of rows for each drug regimen

regimen_counts = {}

for regimen in drug_regimens:

    regimen_counts[regimen] = len(maceutical_complete[maceutical_complete['Drug Regimen'] == regimen])
    
-Create a bar plot

plt.bar(regimen_counts.keys(), regimen_counts.values())

-Set the x-label and y-label

plt.xlabel('Drug Regimen')

plt.ylabel('# Of Observed Mouse Timepoints')

plt.xticks(rotation = 90)

-Show the plot

-Generate a pie plot showing the distribution of female versus male mice using Pandas

counts = maceutical_complete.groupby('Sex')['Sex'].count()

counts.plot(kind='pie',autopct='%1.2f%%')

plt.axis('equal')

plt.title('Distribution of Female vs Male Mice')

plt.show()
# Quartiles, Outliers and Boxplots
-Calculate the final tumor volume of each mouse across four of the treatment regimens:  

-Capomulin, Ramicane, Infubinol, and Ceftamin

-Start by getting the last (greatest) timepoint for each mouse

-Merge this group df with the original DataFrame to get the tumor volume at the last timepoint

-Create a DataFrame with the last timepoint for each mouse

last_timepoints = maceutical_complete.groupby('Mouse ID').Timepoint.max().reset_index()

-Merge this DataFrame with the original DataFrame to get the tumor volume at the last timepoint

merged_data = pd.merge(last_timepoints, maceutical_complete, on=['Mouse ID', 'Timepoint'], how='left')

-Create a subset of the data with only the four treatment regimens of interest

treatments = ['Capomulin', 'Ramicane', 'Infubinol', 'Ceftamin']

subset_data = merged_data[merged_data['Drug Regimen'].isin(treatments)]

-Calculate the final tumor volume for each mouse

final_tumor_volumes = subset_data.groupby('Mouse ID')['Tumor Volume (mm3)', 'Timepoint'].last().reset_index()

-Print the final tumor volumes for each mouse


-Put treatments into a list for for loop (and later for plot labels)

-Create empty list to fill with tumor vol data (for plotting)

-Calculate the IQR and quantitatively determine if there are any potential outliers.     

- Locate the rows which contain mice on each drug and get the tumor volumes    

- add subset     

- Determine outliers using upper and lower bounds

- Put treatments into a list for for loop (and later for plot labels)

treatments = ['Capomulin', 'Ramicane', 'Infubinol', 'Ceftamin']

-Loop through each treatment, appending the final tumor volumes to a list

tumor_volumes = []

for treatment in treatments:

volumes = subset_data.loc[subset_data['Drug Regimen'] == treatment, 'Tumor Volume (mm3)']

tumor_volumes.append(volumes)

-Calculate the quartiles and IQR for each treatment
for i, treatment in enumerate(treatments):

q1 = tumor_volumes[i].quantile(0.25)
    q3 = tumor_volumes[i].quantile(0.75)
    iqr = q3 - q1

- Determine upper and lower bounds for outliers
    upper_bound = q3 + 1.5*iqr
    lower_bound = q1 - 1.5*iqr

-Print results
    print(f"Results for {treatment}:")
    print(f" - Interquartile Range (IQR): {iqr:.2f}")
    print(f" - Potential outliers: {tumor_volumes[i][(tumor_volumes[i] < lower_bound) | (tumor_volumes[i] > upper_bound)].values}")

-Generate a box plot that shows the distrubution of the tumor volume for each treatment group.

fig, ax = plt.subplots()

ax.set_title('Tumor Volume Distribution by Treatment Regimen')

ax.set_xlabel('Treatment Regimen')

ax.set_ylabel('Tumor Volume (mm3)')

ax.boxplot(tumor_volumes, labels=treatments)

plt.show()
# Line and Scatter Plots
-Generate a line plot of tumor volume vs. time point for a single mouse treated with Capomulin

single_mouse = maceutical_complete.loc[maceutical_complete["Mouse ID"] == "l509"]

plt.plot(single_mouse["Timepoint"], single_mouse["Tumor Volume (mm3)"])

plt.xlabel("Timepoint (days)")

plt.ylabel("Tumor Volume (mm3)")

plt.title("Capomulin treatment of mouse l509")

plt.show()
# Correlation and Regression
-Calculate the correlation coefficient and a linear regression model 

-for mouse weight and average observed tumor volume for the entire Capomulin regimen

correlation = st.pearsonr(capomulin_df["Weight (g)"], capomulin_df["Tumor Volume (mm3)"])

print(f'The correlation coeficient for the entire Capomulin Regimen is {round(correlation[0],2)}')

capomulin_df = subset_data.loc[subset_data['Drug Regimen'] == 'Capomulin']

x_values = capomulin_df["Weight (g)"]

y_values = capomulin_df["Tumor Volume (mm3)"]

p_slope, p_int, p_r, p_p, p_std_err = stats.linregress(x_values,y_values)

p_fit = p_slope * x_values + p_int

plt.plot(x_values, p_fit,"-", linewidth=2, color="r")

plt.scatter(x_values, y_values)

plt.xlabel("Weight (g)")

plt.ylabel("Average Tumor Volume (mm3)")

plt.title("Mouse Weight vs Average Tumor Volume for Capomulin Regimen")

plt.show()
