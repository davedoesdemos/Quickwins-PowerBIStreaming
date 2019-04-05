# Quick Win - Streaming data to Power BI from a Logic App
**Produced by Dave Lusty**

# Introduction
This demo shows how to set up a streaming data source in Power BI and consume it. We use a Logic App to send random data for testing this functionality. Using the previous quick win for [Forms Data](https://github.com/davedoesdemos/Quickwins-FormsData/blob/master/QuickwinsFormsData.md) you can set up a nice demo with a web site to submit a number which will appear on the live dashboard instead of random numbers. I used this at [SQL Bits](www.sqlbits.com) to demo to delegates at the MS booth. Using a short URL they were able to submit data and immediately see it on the screen. Not a complex demo, but a powerful concept.

# Power BI

## Set up workspace
Log in to [https://powerbi.microsoft.com](https://powerbi.microsoft.com) and click "Create App Workspace" under workspaces. Name the workspace StreamingDemo and click Save.
![1.NewWorkspace.png](images/1.NewWorkspace.png)
You'll then see a welcome screen. Click Skip to close it ready for the next step.
![2.Welcome.png](images/2.Welcome.png)

## Data Source

Click +Create and select Streaming Dataset.
![3.NewDataset.png](images/3.NewDataset.png)
Choose API and click Next.
![4.api.png](images/4.api.png)
Now call the dataset DemoStream and set it up to have two values, number (as a number) and dateStamp (as a date). You will need the date to place values on a graph later. Also select historical analysis which will save the incoming data for longer term analysis. Click Create.
![5.NewStreamingData.png](images/5.NewStreamingData.png)
Now you'll be given a secure URL for the API which can be used to submit data, along with a sample JSON of the payload. There are also curl and PowerShell samples for using this API endpoint. We'll be using Logic Apps so don't need these for the demo.
![6.Created.png](images/6.Created.png)

# Logic App

Now, create a new Logic App in a new resource group. Call the Logic App StreamDemo. 
![7.LogicAppCreate.png](images/7.LogicAppCreate.png)
Once the Logic App is created, start with a blank logic app. Add a recurrence to the designer.
![8.Recurrence.png](images/8.Recurrence.png)
Set the recurrence to 5 seconds to ensure that we get data that changes quickly enough to see in the demo. **Remember, you'll be charged every time it triggers** so switch it off when you're done to avoid costs mounting up. Now add two initialize variable actions to the designer. One is for the random number and the other a time stamp.
![9.InitializeVariable.png](images/9.InitializeVariable.png)
For the first one, call it number and set to an integer. Use the dynamic content wizard to make a random number between 0 and 100 with the expression rand(0,100)
![10.Random.png](images/10.Random.png)
For the second variable, name it timestamp and set it to String. Use the dynamic content wizard to add the expression formatDateTime(utcNow(),'u') to create a timestamp.
![11.Timestamp.png](images/11.Timestamp.png)
Finally we add in a Power BI add rows to dataset action. This is in preview, but achieves the same as calling the API directly without manual authentication setup in Azure AD.
![12.PowerBI.png](images/12.PowerBI.png)
Next set up the action with your StreamingDemo workspace, DemoStream dataset and add in the two parameters.
![13.PowerBI2.png](images/13.PowerBI2.png)
You can use the dynamic content wizard to add in the variables we initialised earlier.
![14.Parameters.png](images/14.Parameters.png)
Click Save and your logic app will start running. You can disable it from the Logic App's main screen later on, or delete it when you're finished.

# Report and Dashboard

## Dashboard
Go back into your Power BI workspace and click Dashboards on the StreamingDemo Workspace. Click Create and then Dashboard. Call it DemoDash and click Create. Once created, click add tile and then custom streaming data.
![15.AddTile.png](images/15.AddTile.png)
Select the DemoStream dataset.
![16.AddTile2.png](images/16.AddTile2.png)
Choose Card and select the number field.
![17.AddTile3.png](images/17.AddTile3.png)
Click next and give the tile a name then click apply. You'll now see a tile with your random numbers streaming in. Add a second tile in the same way but choose line chart. Use dateStamp as the axis and number as the value with a 1 minute time window.
![18.AddSecondTile.png](images/18.AddSecondTile.png)
You will now see the current value and a one minute history of the data.
![20.Dashboard.png](images/20.Dashboard.png)

## Report
To use the historical data being stored in Power BI you can click on the dataset in the menu. Here you can create a report with a graph containing all of the historical data just as you would in Power BI Desktop.
![21.Report.png](images/21.Report.png)
Now if you click the pin icon next to the graph you can add this to the dashboard alongside the realtime data. Save the report as DemoReport when asked. Select existing dashboard and choose DemoDash.
![22PintoDash.png](images/22PintoDash.png)
Now when you go to the dashboard you'll see the two streaming tiles as well as the historic data.
![23.FullDash.png](images/23.FullDash.png)

**Now don't forget to disable/delete your Logic App!!**