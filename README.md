# Module 5 Challenge - PyBer RideShare Analysis

## Overview

  A client came to us with some data on their ride sharing business.  They wanted a summary analysis done on the data given that they worked out of three primary types of cities: urban, suburban, and rural.  They wanted to know how much fare was earned in each city type, as well number of drivers and average fare per driver and a few other calculations.  This would be compiled into a summary dataframe using pandas to be presented to the client.  The client also wanted a visual summary of total fare collected by each type of city, organized by week, from January 2019 to the end of April 2019.  The result would be an easy to understand multi-line graph that could be used for any business decisions the client wishes to make to maximize profit/revenue.
  
## Results
  The first thing that needed to be done was to merge the data sets that the client entrusted with us.  The client gave us two data sets, city_data.csv and ride_data.csv.  These two sets were merged on a common column they shared, city.  It was a left merge.  From this merged dataframe, pyber_data_df,  we were able to isolate and calculate all necessary information needed for our summary dataframe as well as our chart.  
  
  Here is the summary dataframe with all the values the client requested as well as the summary chart.  The code for each will also be provided as well as a link to the .ipynb file for any inquries.
  
  Link to .ipynb file: https://github.com/wprich/PyBer_Analysis/blob/main/PyBer_Challenge.ipynb
  
  ~~~
  #Get Ride Total
  ride_total = pyber_data_df.groupby(["type"]).count()["ride_id"]
  ride_total
  #Get Driver Total
  driver_total = city_data_df.groupby(["type"]).sum()["driver_count"]
  driver_total
  #Get Fare Total
  fare_total = pyber_data_df.groupby(["type"]).sum()["fare"]
  fare_total
  #Average Fare earned per Ride
  avg_ride_fare = fare_total / ride_total
  avg_ride_fare
  #Average Fare earned per Driver
  avg_driver_fare = fare_total / driver_total
  avg_driver_fare
  #Create Summary dataframe
  PyBer_summary_df = pd.DataFrame(
          {"Total Rides": ride_total, 
          "Total Drivers": driver_total, 
          "Total Fares": fare_total,
          "Average Fare per Ride": avg_ride_fare, 
          "Average Fare per Driver": avg_driver_fare})
  display(PyBer_summary_df)
  #Formatting and Cleaning Up dataframe
  PyBer_summary_df.index.name = None
  PyBer_summary_df["Average Fare per Ride"] = PyBer_summary_df["Average Fare per Ride"].map("${:,.2f}".format)
  PyBer_summary_df["Average Fare per Driver"] = PyBer_summary_df["Average Fare per Driver"].map("${:,.2f}".format)
  PyBer_summary_df["Total Fares"] = PyBer_summary_df["Total Fares"].map("${:,.2f}".format)
  # Display Data Frame
  display(PyBer_summary_df)
  ~~~
  ![alt text](https://github.com/wprich/PyBer_Analysis/blob/main/analysis/Fare_Summary_Dataframe.png)
  https://github.com/wprich/PyBer_Analysis/blob/main/analysis/Fare_Summary_Dataframe.png
  
  This step was pretty simple in its execution.  Just had to use some groupby methods to get the necessary data collected into a seperate data frame from the merged data frame.  The driver total however only needed the city_data_df to get the totals.  Using the merged data frame would have given duplicates and incorrect figures to our summary.  
  
  
  ~~~
  #Create a daily dataframe using groupby method
  daily_df = pyber_data_df.groupby(["date","type"]).sum()["fare"]
  daily_df.head()
  #Reset the index and then use pivot function to create a new dataframe
  daily_df = daily_df.reset_index()
  pivot_df = daily_df.pivot(index = "date", columns = "type", values = "fare")
  pivot_df
  #Collect only the entries going from beggining of January to the end of April from the pivot dataframe using the loc method
  new_df = pivot_df.loc['2019-01-01':'2019-04-29']
  new_df
  #Change the date index to the datetime datatype and then using resample create a dataframe that shows the sums of each city types fares by the week
  new_df.index = pd.to_datetime(new_df.index)
  weekly_df = new_df.resample('W').sum()
  weekly_df
  #Using the df.plot method create the multi-line visual for the client
  weekly_df.plot(figsize=(20, 5))
  plt.xlabel("Date")
  plt.ylabel("Fare($USD)")
  plt.title("Total Fare By City Type")
  style.use('fivethirtyeight')
  plt.savefig("analysis/PyBer_fare_summary.png")
  ~~~
  ![alt text](https://github.com/wprich/PyBer_Analysis/blob/main/analysis/PyBer_fare_summary.png)
  https://github.com/wprich/PyBer_Analysis/blob/main/analysis/PyBer_fare_summary.png
  
  This step was crucial in that the visual needed to be easy to understand and also condensed enough to contain a large amount of information in a finite space.  The resample method helped ensure that because graphing by the week would be able to show more information withouth taking up too much space.  A graph for each city type by the day would be cumbersome and uneccesary as some entries would have been NaN or zero.  
  
## Summary

  The summary dataframe and chart together gives some valuable insight that might be beneficial for the client to have making decisions going forward.  First and foremost is that the urban city type is their biggest market.  1625 rides with a total fare of nearly $40,000 it clearly outshines the other two, suburban and rural.  While this may seem like the area to focus on, the other two city types could be made more profitable under the right circumstances.  For one, the rural line on the graph shows that at the start of the year it is hitting a peak.  Rural is also hitting another peak at the start of April.  If that current trend continues, then perhaps some discount codes or incentives could be in place for customers and drivers, respectively, to increase the volume of rides in that city type.  At 34 average fare/ride and 55 average fare/driver in rural, it could generate a decent amount of revenue.  Suburban seems to stay pretty consistent according to the graph.  This is likely attributed to people using the ride share app from the client to get to and from work.  By getting the average time that the suburban ride volume is the highest, discounts or incentives could be given to customers to either carpool to increase the average fare per ride or driver.  This would keep the suburban city type consistent in revenue but possibly at a higher level.
  
  Another noticable trend on the summary chart is that all 3 city types peak during the last week of Febuary.  This could be because of warming temperatures or other reasons.  The main point here is that that particular week could be planned for and capitalized on by the client.  Doing more research to find out where the rides are headed towards or possibly at what times of the week, the client could work out deals with local businesses to offer discounts on ride shares after a night out or for a group.  The client could also incentivize more riders to be available to get more customers served during this time.  
  
  Again, another insight by looking at the graph is that the city type urban seems to fluctuate greatly during the month of March.  The client could either ask for customer feedback or get feedback from their drivers to figure out what is going on either locally or regionally to find out why this is.  After collecting that data and looking into it, the client could then level out this flucuation by using different methods so the revenue stays consistent rather than fluctuate too much.  
  
  Lastly, another item that could be taken from this summary and graph is that we did not account for customers if they crossed into different city types or how long their average ride was in terms of time.  If someone goes from the suburban city type to the urban city type for work, this could be taken into account to offer drivers incentives to work in both city types.  Also, the length of time a driver is held by a customer.  A driver who has 5 total rides in an hour may make more money than a driver who only has one or two.  The client could look into this and maybe make adjustments to the fare rate after a certain length of time that a single customer is using the drivers time.
