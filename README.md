# Data-Visualization-Challenge

    1. Prepare the Data (20 points)
        - The datasets are merged into a single DataFrame. (6 points)
            - Import Dependencies for everything we need in the challenge
            
                import matplotlib.pyplot as plt
                import pandas as pd
                import scipy.stats as st
                import numpy as np
                from scipy.stats import linregress
                from scipy.stats import sem
                
            - Combine the data into a single DataFrame merging on the mouse id column
            
                merged_df = pd.merge(study_results, mouse_metadata, on="Mouse ID", how="left")


            - Code for this portion was taken from the merging activity in class

        - The number of mice are shown from the merged DataFrame. (2 points)
            - Take the length of the number of unique mouse IDs within the system
            
                total_mice = len(merged_df["Mouse ID"].unique())
                total_mice
                
            - Code for this was taken from previous activities we had done in class
            
        - Each duplicate mice is found based on the Mouse ID and Timepoint. (6 points)
            - Ended up using chatgpt to figure out how to find rows where the combination of Mouse ID and Timepoint were duplicated using the Duplicated function

                duplicates = merged_df[merged_df.duplicated(subset=["Mouse ID", "Timepoint"], keep=False)]
                print(duplicates["Mouse ID"].unique())
                
            - Code for this was taken from ChatGPT, specifically the duplicated function. I had tried a few ways by grouping by mouse id and timepoint, but I wasn't able to get it to work.
            
        - A clean DataFrame is created with the dropped duplicate mice. (4 points)
            - For this I used loc to return rows where the Mouse ID was not equal to the mouse ID I had found earlier. 
            
                clean_df = merged_df.loc[merged_df["Mouse ID"] != "g989", :]
                clean_df
                
            - Code for this was taken from activities we had done previously in class
            
        - The number of mice are shown from the clean DataFrame. (2 points)
            - I used the same function as earlier to count the unique mouse IDs on my clean DataFrame
            
                new_total_mice = len(clean_df["Mouse ID"].unique())
                new_total_mice
                
            - Code for this was taken from activities performed in class
            
    2. Generate Summary Statistics (15 points) - I did not do the optional activities for this one and only calculated one way
        - The mean of the tumor volume for each regimen is calculated using groupby. (2 points)
            - I grouped the summary table I had created by Drug Regimen and then took the mean of the Tumor Volume per Drug Regimen
            
                summary_stats = clean_df.groupby("Drug Regimen")
                mean_tumor_volume = summary_stats["Tumor Volume (mm3)"].mean()
                
            - Code for this was taken from activities we had previously performed in class
            
        - The median of the tumor volume for each regimen is calculated using groupby. (2 points)
            - I took my summary table and took the median of the Tumor Volume per Drug Regimen
            
                median_tumor_volume = summary_stats["Tumor Volume (mm3)"].median()
                
            - Code for this was taken from activities we had previously performed in class
            
        - The variance of the tumor volume for each regimen is calculated using groupby. (2 points)
            - I took my summary table and took the variance of the Tumor Volume per Drug Regimen using var()
            
                var_tumor_volume = summary_stats["Tumor Volume (mm3)"].var()
                
            - Code for this was taken from activities we had previously performed in class
            
        - The standard deviation of the tumor volume for each regimen is calculated using groupby. (2 points)
            - I took my summary table and calculated standard deviation of Tumor Volume per Drug Regimen using std()
            
                st_dev_tumor_volume = summary_stats["Tumor Volume (mm3)"].std()
                
            - Code for this was taken from activities we had performed in class
            
        - The SEM of the tumor volume for each regimen is calculated using groupby. (2 points)
            - I took my summary table and calculated the SEM on the tumor volume using sem()
            
                SEM_tumor_volume = summary_stats["Tumor Volume (mm3)"].sem()
                
            - Code for this was taken from activities we had performed in class
            
        - A new DataFrame is created with using the summary statistics. (5 points)
            - I used the results I had previously calculated and then created a DataFrame for each of the necessary columns
            
                summary_stats = pd.DataFrame({"Mean Tumor Volume": mean_tumor_volume, "Median Tumor Volume": median_tumor_volume, 
                              "Tumor Volume Variance": var_tumor_volume, "Tumor Volume Std. Dev.": st_dev_tumor_volume,
                             "Tumor Volume Std. Err.": SEM_tumor_volume})
                summary_stats
                
            - Code for this was taken from activities we performed in class
            
    3. Create Bar Charts and Pie Charts (15 points)
        - A bar plot showing the total number of timepoints for all mice tested for each drug regimen using Pandas is generated. (4.5 points)
            - I used groupby on my original clean_df to groupby Drug Regimen and then counted the Timepoints for each regimen
            - I then used the .plot() method to create my bar chart based on what I calculated
            
                regimen_counts_pandas = clean_df.groupby("Drug Regimen")["Timepoint"].count()
                pandas_bar = regimen_counts_pandas.plot(kind="bar", width=.75)
                pandas_bar.set_xlabel("Drug Regimen")
                pandas_bar.set_ylabel("# of Observed Mouse Timepoints")
                plt.show()
                
            - Code for this was taken from the bar chart activities we performed in class
            
        - A bar plot showing the total number of timepoints for all mice tested for each drug regimen using pyplot is generated. (4.5 points)
            - I used the same method to calculate my results as earlier and then I generated the bar chart using plt.bar
            
                regimen_counts_pyplot = clean_df.groupby("Drug Regimen")["Timepoint"].count()
                plt.bar(regimen_counts_pyplot.index, regimen_counts)
                plt.xticks(rotation="vertical")
                plt.xlabel("Drug Regimen")
                plt.ylabel("# of Observed Mouse Timepoints")
                plt.show()
                
            - Code for this was taken from activities we performed in class
            
        - A pie plot showing the distribution of female versus male mice using Pandas is generated. (3 points)
            - For this I groupedby the "Sex" column and then counted the different entries in the "Sex" column
            - My pie chart was backwards so I reordered the count using code I found from chatGPT
            
                mice_gender = clean_df.groupby("Sex")["Sex"].count()
                desired_order = ["Male", "Female"]
                mice_gender = mice_gender.reindex(desired_order)
                mice_gender
                
            - I then created my pie chart using pandas using .plot
            
                gender_pie = mice_gender.plot(kind="pie", autopct="%1.1f%%")
                plt.axis("equal")
                plt.show()
                
            - Code for this was taken from activities we learned in class and then the reorder code was taken from ChatGPT. Code for the pie chart was taken from activities in class
            
        - A pie plot showing the distribution of female versus male mice using pyplot is generated. (3 points)
            - I used my groupedby DataFrame I had already created and then generated the pie chart using plt.pie
            
                plt.pie(mice_gender, labels=mice_gender.index, autopct="%1.1f%%")
                plt.ylabel("Sex")
                plt.axis("equal")
                plt.show()
                
            - Code for this part was taken from the activities we had performed in class
            
    4. Calculate Quartiles, Find Outliers, and Create a Box Plot (30 points)
        - A DatFrame that has the last timepoint for each mouse ID is created using groupby. (5 points)
            - I first used loc to filter my DataFrame to only including data for the desired treatment groups
            
                tumor_volume = clean_df.loc[(clean_df["Drug Regimen"] == "Capomulin") | 
                             (clean_df["Drug Regimen"] == "Ramicane") | 
                             (clean_df["Drug Regimen"] == "Infubinol") | 
                             (clean_df["Drug Regimen"] == "Ceftamin"), :]
                             
            
        - The index of the DataFrame is reset. (2 points)
            - I reset the index for this when I pulled the maximum timepoint below
        - Retrieve the maximum timepoint for each mouse. (2 points)
            - I calculated the max timepoint by grouping by timepoint and then taking the max of the "Timepoint" column and reset the index
            - I then merged my indexed dataset I had just created with my tumor_volume dataset from earlier on Mouse ID and Timepoint
                max_timepoint = tumor_volume.groupby("Mouse ID")["Timepoint"].max().reset_index()               
                
                merged_tumor_volume = pd.merge(max_timepoint, tumor_volume, on=["Mouse ID", "Timepoint"], how="left")
                merged_tumor_volume
                
            - Code for this was taken from the activities we performed together in class and a little help from ChatGPT. ChatGPT was taking me a different direction, then I wanted to go, but it helped me figure out how to get the above code
            
        - The four treatment groups, Capomulin, Ramicane, Infubinol, and Ceftamin, are put in a list. (3 points)
            - Created a list for each treatment group
                
                treatments = ["Capomulin", "Ceftamin", "Infubinol", "Ramicane"]
                
            - Code for this was taken from activities performed in class
            
        - An empty list is created to fill with tumor volume data. (3 points)
            - Created an empty list fill the tumor volume data
                
                tumor_vol_data = []
                
            - Code for this was taken from activities performed in class
            
        - A for loop is used to display the interquartile range (IQR) and the outliers for each treatment group (10 points)
            - This was the hardest part of the assignment for me. 
            - I created the for loop for each treatment in my list and checking that the drug treatment column was equal to the treatment in my for loop and then pulling the Tumor Volume
                
                for treatment in treatments: 
                    # Locate the rows which contain mice on each drug and get the tumor volumes
                    treatment_data = merged_tumor_volume.loc[merged_tumor_volume["Drug Regimen"] == treatment,
                    "Tumor Volume(mm3)"]
                    
            - I then appended my tumor volume empty list with the Tumor Volume I had found
            
                    tumor_vol_data.append(treatment_data)
            
            - I then set my quartiles and then calculated the upperq, lowerq, and IQR
            - I then calculated the upper and lower bounds
            - I calculated the outliers and then printed the results in a slightly different way than the answer
            
                    # Determine outliers using upper and lower bounds
                    quartiles = treatment_data.quantile([.25,.5,.75])
                    lowerq = quartiles[0.25]
                    upperq = quartiles[0.75]
                    iqr = upperq-lowerq
                    # Calculate upper and lower bounds
                    lower_bound = lowerq - 1.5 * iqr
                    upper_bound = upperq + 1.5 * iqr

                    # Identify potential outliers
                    outliers = treatment_data.loc[(treatment_data < lower_bound) | (treatment_data > upper_bound)]

                    print(f"Results for {treatment}:")
                    print(f"Lower Quartile: {lowerq}")
                    print(f"Upper Quartile: {upperq}")
                    print(f"IQR: {iqr}")
                    print(f"Lower Bound: {lower_bound}")
                    print(f"Upper Bound: {upper_bound}")
                    print(f"Potential Outliers: {outliers.tolist()}")
                    print("\n")
                    
             - Code for this was taken a little bit from class activities and mostly from ChatGPT. I was doing okay with the for loop, but originally tried to calculate the quartile with the tumor_volume_data list. ChatGPT helped me re-write to use treatment_data and then print the results in a nice format. 
    
        - A box plot is generated that shows the distribution of the final tumor volume for all the mice in each treatment group. (5 points)
            - I used plt.boxplot to plot tumor_vol_data and then display the results. I got in a slightly different order. ChatGPT also helped me resize the outlier size and color using flierprops
                
                plt.figure(figsize=(7, 4))
                plt.boxplot(tumor_vol_data, labels=treatments, flierprops=dict(markerfacecolor='red', marker='o', markersize=12))
                plt.ylabel("Final Tumor Volume (mm3)")
                plt.show()
                
            - Code for this was taken from activities performed in class and then ChatGPT to help me with some of the visual aids
            
    4. Create a Line Plot and a Scatter Plot (10 points)
        - A line plot is generated that shows the tumor volume vs. time point for one mouse treated with Capomulin. (5 points)
            - First I chose a single mouse ID and filtered my dataframe to only be rows with that mouse ID
            
                single_mouse = clean_df.loc[clean_df["Mouse ID"] == "b128", :]
                
            - Then I created my line plot using that mouse to show the tumor volume vs timepoint
                
                single_mouse.plot(x="Timepoint", y="Tumor Volume (mm3)")
                plt.xlabel("Timepoint (days)")
                plt.ylabel("Tumor Volume (mm3)")
                plt.title("Capomulin Treatment of mouse b128")
                # Had to look up how to remove a legend because there was none in the answer. ChatGPT helped me with this
                plt.legend().set_visible(False)
                plt.show()
                
            - Code was taken from the activities we performed in class. ChatGPT helped me remove the legend since it created one by default
                
        - A scatter plot is generated that shows average tumor volume vs. mouse weight for the Capomulin regimen. (5 points)
            - I was able to make this work by grouping by mouse ID and then taking the mean of Tumor volume for each mouse ID and resetting the index
            
                capomulin = clean_df.loc[clean_df["Drug Regimen"] == "Capomulin", :]
                grouped_weight = capomulin.groupby("Mouse ID")["Tumor Volume (mm3)"].mean().reset_index()
                
            - I then pulled another datframe where I pulled the weight for each mouse. Luckily the weight was the same for each timepoint
                
                mouse_weight = capomulin.groupby("Mouse ID")["Weight (g)"].max().reset_index()
                
            - I then merged the data together so I could use the weight & average tumor volume columns on my scatter plot

                merged_capomulin = pd.merge(grouped_weight, mouse_weight, on="Mouse ID", how="left")
                
            - I then plotted my scatter plot of weight vs tumor volume
            
                plt.figure(figsize=(8, 6))
                plt.scatter(merged_capomulin["Weight (g)"], merged_capomulin["Tumor Volume (mm3)"])
                plt.xlabel("Weight (g)")
                plt.ylabel("Average Tumor Volume (mm3)")
                plt.show()
                
            - Code for this was taken from activities we performed in class and then using ChatGPT to check my work when I had wrong syntax. 
            
    5. Calculate Correlation and Regression (10 points)
        - The correlation coefficient and linear regression model are calculated for mouse weight and average tumor volume for the Capomulin regimen. (10 points)
            - First set the x and y values and then added the linear regression equation

                x_values = merged_capomulin["Weight (g)"]
                y_values = merged_capomulin["Tumor Volume (mm3)"]
                (slope, intercept, rvalue, pvalue, stderr) = linregress(x_values, y_values)
                regress_values = x_values * slope + intercept
                line_eq = "y = " + str(round(slope,2)) + "x + " + str(round(intercept,2))
                
            - Correlation formula was taken from activities in class. Plugged in the values and then printed the result

                correlation = st.pearsonr(x_values,y_values)
                print(f"The correlation between mouse weight and the average tumor volume is {round(correlation[0],2)}")
                
            - Plotted the scatter plot showing the linear regression line
                
                plt.scatter(x_values,y_values)
                plt.plot(x_values,regress_values,"r-")
                plt.annotate(line_eq,(5.8,0.8),fontsize=15,color="red")
                plt.xlabel('Weight (g)')
                plt.ylabel('Average Tumor Volume (mm3)')
                plt.show()