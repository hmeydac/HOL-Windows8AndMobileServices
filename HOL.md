﻿<a name="title" />
# Introduction to Building Windows Store Apps with Windows Azure Mobile Services #

---
<a name="Overview" />
## Overview ##
In this HOL you will learn how you can leverage Visual Studio 2012 and Windows Azure Mobile Services to add structured storage, push notifications and integrated authentication to your Windows Store applications.

<a name="Objectives" />
### Objectives ###
- Create a Windows Azure Mobile Service
- Use the Windows Azure Mobile Services SDK
- Learn how to Insert, Update, Read and Delete rows from a Mobile Service
- Add Push Notifications to your application
- Lock down your Mobile Service such that only authenticated users can consume it

<a name="technologies" />
### Prerequisites ###

- Windows Azure subscription with Mobile Services preview enabled - [Create a Windows Azure account and enable preview features][1]
- [Visual Studio 2012](http://www.microsoft.com/visualstudio/11/en-us/downloads#groups)

[1]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/create-a-windows-azure-account/

<a name="Exercises" />
## Exercises ##

This hands-on lab includes the following exercises:

1. [Creating your first Mobile Service](#Exercise1)
1. [Adding Push Notifications to your app](#Exercise2)
1. [Adding Auth to Your App and Services](#Exercise3)

<a name="Exercise1" />
## Exercise 1: Creating your first Mobile Service ##

This exercise shows you how to add a cloud-based backend service to a Windows 8 app using Windows Azure Mobile Services.  You will create both a new mobile service and a simple _To do list_ app that stores app data in the new mobile service.

A screenshot from the completed app is below:

![Image 1](images/image-1.png?raw=true)

<a name="create-a-new-mobile-service" />
### Task 1- Create a new mobile service ###
Follow these steps to create a new mobile service.

1. Log into the [Windows Azure Management Portal](https://manage.windowsazure.com) and navigate to Mobile Services

1. Click the **+New** button then click **Mobile Service**, **Create**

	![image-2](images/image-2.png?raw=true)

1. Expand **Mobile Service**, then click **Create**
 
	![Image 3](images/image-3.png?raw=true)
 
	This displays the **New Mobile Service** dialog.

1. In the **Create a mobile service** page, type a subdomain name for the new mobile service in the **URL** textbox and wait for name verification. Once name verification completes, click the right arrow button to go to the next page.
 
	![Image 4](images/image-4.png?raw=true)

	This displays the **Specify database settings** page.

	> **Note:** As part of this tutorial, you create a new SQL Database instance and server. You can reuse this new database and administer it as you would any other SQL Database instance. If you already have a database in the same region as the new mobile service, you can instead chooseUse existing Databaseand then select that database. The use of a database in a different region is not recommended because of additional bandwidth costs and higher latencies.

1. In **Name**, type the name of the new database, then type **Login name**, which is the administrator login name for the new SQL Database server, type and confirm the password, and click the check button to complete the process.
 
	![Image 5](images/image-5.png?raw=true)

	> **Note:**  When the password that you supply does not meet the minimum requirements or when there is a mismatch, a warning is displayed.  We recommend that you make a note of the administrator login name and password that you specify; you will need this information to reuse the SQL Database instance or the server in the future. You have now created a new mobile service that can be used by your mobile apps.

You have now created a new mobile service that can be used by your mobile apps.

<a name="create-a-new-app" />
### Task 2 - Create a new app ###
Once you have created your mobile service, you can follow an easy quick start in the Management Portal to either create a new Windows Store app or modify an existing app to connect to your mobile service.


1. In the Management Portal, click **Mobile Services**, and then click the mobile service that you just created.

1. In the quickstart tab, expand **Create a new Windows 8 application**.

	![Image 6](images/image-6.png?raw=true)

	This displays the three easy steps to create a Windows 8 app connected to your mobile service.

	![Image 7](images/image-7.png?raw=true)

1. If you haven't already done so, download and install [Visual Studio 2012 Express for Windows 8](http://go.microsoft.com/fwlink/?LinkId=257546&clcid=0x409) and the [Mobile Services SDK](http://go.microsoft.com/fwlink/?LinkId=257545&clcid=0x409) on your local computer or virtual machine.

	This downloads the project for the sample _To do list_ application that is connected to your mobile service. Save the compressed project file to your local computer, and make a note of where you save it.

<a name="run-your-app" />
### Task 3 - Run your app ###

1. Browse to the location where you saved the compressed project files, expand the files on your computer, and open the solution file in Visual Studio 2012 Express for Windows 8.

	![Image 23](images/image-23.png?raw=true)

1. Press the **F5** key to rebuild the project and start the app.

1. In the app, type meaningful text, such as _Complete the demo_, in the **Insert a TodoItem** textbox, and then click **Save**.

	![Image 9](images/image-9.png?raw=true)

	This sends a POST request to the new mobile service hosted in Windows Azure. Data from the request is inserted into the TodoItem table. Items stored in the table are returned by the mobile service, and the data is displayed in the second column in the app.

	> **Note:** You can review the code that accesses your mobile service to query and insert data, which is found in either the MainPage.xaml.cs file (C#/XAML project) or the default.js (JavaScript/HTML project) file.

1. Back in the Management Portal, click the **Data** tab and then click the **TodoItems** table and observe that the data as been successfully stored

	![Image 10](images/image-10.png?raw=true)

	This lets you browse the data inserted by the app into the table.

	![Image 11](images/image-11.png?raw=true)

<a name="Explore-your-app-code" />
### Task 4 - Explore your app code ###

In this step we explore _To do list_ application code and see how simple the Windows Azure Mobile Services Client SDK makes it to interact with Windows Azure Mobile Services.

1. Return to the downloaded _To do list_ application Visual Studio 2012

1. In solution explorer **expand the references folder** and show the Windows Azure Mobile Services Client SDK reference.  

	> **Note:** You may also add references to the Windows Azure Mobile Services Client SDK from any Windows Store app. Using the Add reference dialog

1. Open App.xaml.cs and show the MobileServiceClient class.  This is the key class provided by the Mobile Services client SDK that provides a way for your application to interact with Windows Azure Mobile Services. The first parameter in the constructor is the Mobile Service endpoint and the second parameter is the Application Key for your Mobile Service.

	````C#
	public static MobileServiceClient MobileService 
			= new MobileServiceClient( 
				"https://cloudnick.azure-mobile.net/"
				,"vIWepmcOXGPsYCJQDDcFBKsnOVxzLG52" );
	
	````

1. Open **MainPage.xaml.cs** to observe how the mobile service client is then used for Inserts, Updates, Reads and Deletes:

	- The source creates a handle for operations on a table

		````C#
		private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
		
		````
	- Performs an Insert
		<!-- mark:3;-->
		````C#
		private async void InsertTodoItem(TodoItem todoItem)
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);                        
		}

		````
	- Performs an Update
		<!-- mark:3 -->
		````C#
		private async void UpdateCheckedTodoItem(TodoItem item)
		{
			await todoTable.UpdateAsync(item);
			items.Remove(item);
		}
		````
	- Performs a Read
		<!-- mark:3-4 -->
		````C#
		private void RefreshTodoItems()
		{
			items = todoTable
				 .Where(todoItem => todoItem.Complete == false)
				 .ToCollectionView();
			ListItems.ItemsSource = items;
		}
		````

1. As an extension see if you can update the _UpdateCheckedTodoItem_ method to perform a delete rather then update operation using the todoTable.DeleteAsync(...) method

<a name="Exercise2" />
## Exercise 2: Adding Push Notifications to your app ##

In demo, you add push notifications, using the Windows Push Notification service (WNS), to the quickstart project. When complete, an insert in the mobile service todolist table will generate a push notification back to your app. 

<a name="Register-your-app-for-push-notifications-and-configure-Mobile-Services" />
### Task 1 - Register your app for push notifications and configure Mobile Services ###

1. Navigate to the [Windows Push Notifications & Live Connect](http://go.microsoft.com/fwlink/?LinkID=257677&clcid=0x409) page, login with your Microsoft account if needed, and then follow the instructions to register your app. 

	> **Note:** It's important to ensure the value supplied to the CN field is the same as the _Publisher_ field in your applications _package.appxmanifest Packaging tab_

1. At the end of the registration process for your app you will be provided with WNS Credentials.  Keep the page open or make a note of the **Package Name**, **Client Secret** and **Package SID**. 

	![Image 12](images/image-12.png?raw=true) 

	You must provide these values to Mobile Services to be able to use WNS.

	> **Note:** The client secret and package SID are important security credentials. Do not share these secrets with anyone or distribute them with your app.
	
1. Log on to the [Windows Azure Management Portal](https://manage.windowsazure.com/), click **Mobile Services**, and then click your app.

	![Image 13](images/image-13.png?raw=true)

1. Click the **Push** tab, enter the **Client secret** and **Package SID** values obtained for WNS above, and click **Save**.

	![Image 14](images/image-14.png?raw=true)

1. In visual studio open **package.appxmanifest**, select the **Packaging** tab and update the package name field to match that provided in the Windows Push Notification and Live Connect Portal

	![Image 15](images/image-15.png?raw=true)

	![Image 18](images/image-18.png?raw=true)

<a name="Add-push-notifications-to-the-app" />
### Task 2 - Add push notifications to the app ###

1. In Visual Studio open the **package.appxmanifest**, select the packaging tab and copy the _Package Name_ from your WNS Credentials you recieved in the Windows Push Notifications & Live Connect portal and paste it into the Package name field in visual studio.

1. In the package.appxmanifest now select the **Application UI** tab and ensure **toast capable** is set to yes.  

	> **Note:** If you wish to send Wide Tiles then you must provide a default wide tile in the Wide Logo field.

1. Open the file **App.xaml.cs** 

1. Add a **Channel.cs** class as follows.  

	````C#
	public class Channel
	{
		public int Id { get; set; }
		public string Uri { get; set; }
	}

	````
1. add the following using statement:

	````C#
	using Windows.Networking.PushNotifications;
	````

1. Find the OnLaunched method and mark it to be **async** as follows

	````C#
	protected async override void OnLaunched(LaunchActivatedEventArgs args)
	````

1. Add the following two lines of code to OnLaunched request a notification channel and register it with your Mobile Services app

	````C#
	var ch = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
	await MobileService.GetTable<Channel>().InsertAsync(new Channel() { Uri = ch.Uri });
	````

Now that we have the client wired up to request a channel and write it to our Mobile Service we now need to add a Channel table to our Mobile Service and add a server side script to send push notifications.

<a name="Insert-data-to-receive-notifications" />
### Task 3 - Insert data to receive notifications ###

In this section we add a Channel table and server side scripts to send push notifications everytime someone inserts into our todolist.  

1. Return to the [Windows Azure Management Portal](https://manage.windowsazure.com/), click **Mobile Services**, and then click your app.

1. Select the **Data** tab

1. Click **+ Create** in the bottom toolbar

	![Image 19](images/image-19.png?raw=true)

1. In **Table name** type _Channel_, then click the check button.
		
	![Image 20](images/image-20.png?raw=true)

1. Click the new **Channel** table and verify that there are no data rows.

1. Click the **Columns** tab and verify that there is only a single **id** column, which is automatically created for you.
This is the minimum requirement for a table in Mobile Services.

	> **Note:** When dynamic schema is enabled on your mobile service, new columns are created automatically when JSON objects are sent to the mobile service by an insert or update operation.

1. Click the script tab and select the **Insert** Operation

1. Replace the existing script with the following script.  

	````JavaScript
	function insert(item, user, request) { 
		var channelTable = tables.getTable('Channel'); 
		channelTable.where({ uri: item.Uri }) 
			.read({ success: insertChannelIfNotFound});     

		function insertChannelIfNotFound(existingChannels) { 
			if(existingChannels.length > 0) { 
					request.respond(200, existingChannels[0]); 
			} else { 
					request.execute(); 
			} 
		} 
	}
	````
	> **Note:** The purpose of this script is to ensure that multiple channels with the same Uri are not submitted every time the OnLaunched handler executes in the sample application. This code is sufficient for a HOL scenario but in a real application you would use an Id rather then matching on uri: item.Uri to identify the channel to be replaced.  The reasoning is Channels expire and will be replaced by a new unique Channel Uri.

1. Click **Save** in the bottom toolbar 

	![Image 21](images/image-21.png?raw=true)

1. Now in the left navbar select the **TodoItem** table 

1. Click the **Script** tab and select the **Insert** Operation and replace the existing script with the following and walk through the following code


	````JavaScript
	function insert(item, user, request) {		 
		 request.execute({
			  success: function(){
					request.respond();
					sendNotifications(item);
			  },
			  error: function(err){
					request.respond(500, "Error");
			  }
		 });
	}

	function sendNotifications(item){               		 
	  var channelTable = tables.getTable('Channel'); 
	  channelTable.read({ 
		 success: function(channels){
			 channels.forEach(function(channel){  	
				 push.wns.sendToastText04(channel.Uri, {
					  text1: item.text,
					  text2: "Hello World 1",
					  text3:  "Hello World 2"
				 }, {
					  success: function(response){                                               
						  console.log(response);
					  },                                   
					  error: function(err){                                               
							console.error(err);                       
					  }                    
				 });
			});
	  }        
    });    
	}
	````
	> **Note:** This script executes as a each time a the insert operation is executed on the Todoitem table.  The sendNotifications method we select all channels from the Channels table and iterate through them sending a push notification to each channel uri.  While we have only demonstrated a single toast template the push.wns.* namespace provides simple to use methods required for sending toast, tile and badge updates. As you can see in this scenario we are sending a ToastText04 template which requires three lines of text.  When you build your applications we would advise that you do not send toast notifications so frequently but rather only at times when there is a critical or important message to deliver the user of your application.

	![Image 22](images/image-22.png?raw=true)

Next we will move on to look at how you can secure your Mobile Service endpoints

<a name="Exercise3" />
## Exercise 3: Adding Auth to Your App and Services ##

This demo shows you how to authenticate users in Windows Azure Mobile Services from a Windows 8 app. In this demo, you add authentication to the quickstart project using Live Connect. When successfully authenticated by Live Connect, a logged-in will be able to consume your Mobile Service.

<a name="Register-your-app" />
### Task 1 - Register your app ###

To be able to authenticate users, you must register your Windows 8 app at the Live Connect Developer Center. You must then register the client secret to integrate Live Connect with Mobile Services.

1. Navigate to the [Windows Push Notifications & Live Connect](http://go.microsoft.com/fwlink/?LinkID=257677&clcid=0x409) page, log on with your Microsoft account if needed.

1. To enable auth you must now Navigate to the [My Apps dashboard](http://go.microsoft.com/fwlink/?LinkId=262039&clcid=0x409) in Live Connect Developer Center and click on your app in the **My applications** list.

	![Image 24](images/image-24.png?raw=true)

1. Click **Edit settings**, then **API Settings** and make a note of the value of **Client secret**.

	![Image 25](images/image-25.png?raw=true)
 
	You must provide this value to Mobile Services to be able to use Live Connect for authentication.

	> **Note:** The client secret is an important security credential. Do not share the client secret with anyone or distribute it with your app.

1. In **Redirect domain**, enter the domain of your mobile service, in the format **https://****service-name****.azure-mobile.net/**, where _service-name_ is the name of your mobile service, then click **Save**.

1. Return to the [Windows Azure Management Portal](https://manage.windowsazure.com/), click **Mobile Services**, and then click your app.

	![Image 26](images/image-26.png?raw=true)

1. Click the **Identity** tab, enter the **Client secret** obtained from Live Connect, and click **Save**.

	![Image 27](images/image-27.png?raw=true)

<a name="Restrict-permissions" />
### Task 2 - Restrict permissions ###

1. In the Management Portal, click the **Data** tab, and then click the **TodoItem** table.

	![Image 28](images/image-28.png?raw=true)

1. Click the **Permissions** tab, set all permissions to **Only authenticated users**, and then click **Save**. This will ensure that all operations against the **TodoItem** table require an authenticated user. This also simplifies the scripts in the next tutorial because they will not have to allow for the possibility of anonymous users

	![Image 29](images/image-29.png?raw=true)

1. Return to Visual Studio 2012 aand press the **F5** key to run this quickstart-based app; verify that an exception with a status code of 401 (Unauthorized) is raised.
This happens because the app is accessing Mobile Services as an unauthenticated user, but the _TodoItem_ table now requires authentication.

Next, you will update the app to authenticate users with Live Connect before requesting resources from the mobile service.

<a name="Add-authentication" />
### Task 3 - Add authentication ###
	
1. Download and install the [Live SDK for Windows](http://go.microsoft.com/fwlink/?LinkId=262253&clcid=0x409)

1. In the project in Visual Studio, add a reference to the Live SDK.

1. Open the project file mainpage.xaml.cs and add the following using statements

	````C#
	using Microsoft.Live;
	using Windows.UI.Popups;
	````

1. Add the following code snippet that creates a member variable for storing the current Live Connect session and a method to handle the authentication process:


	````C#
	private LiveConnectSession session;
	private async System.Threading.Tasks.Task Authenticate()
	{
		LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT REDIRECT DOMAIN HERE >>");


		while (session == null)
		{
			 // Force a logout to make it easier to test with multiple Microsoft Accounts
			 if (liveIdClient.CanLogout)
				  liveIdClient.Logout();


			 LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
			 if (result.Status == LiveConnectSessionStatus.Connected)
			 {
				  session = result.Session;
				  LiveConnectClient client = new LiveConnectClient(result.Session);
				  LiveOperationResult meResult = await client.GetAsync("me");
				  MobileServiceUser loginResult = await App.MobileService.LoginAsync(result.Session.AuthenticationToken);


				  string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
				  var message = string.Format("You are now logged in - {0}", loginResult.UserId);
				  var dialog = new MessageDialog(message, title);
				  dialog.Commands.Add(new UICommand("OK"));
				  await dialog.ShowAsync();
			 }
			 else
			 {
				  session = null;
				  var dialog = new MessageDialog("You must log in.", "Login Required");
				  dialog.Commands.Add(new UICommand("OK"));
				  await dialog.ShowAsync();
			 }
		}
	}
	````

1. Update string _<< INSERT REDIRECT DOMAIN HERE >>_ from the previous step with the redirect domain that was specified when setting up the app in Live Connect, in the format **https://****service-name****.azure-mobile.net/**.

1. Update the **OnNavigatedTo** event handler to be async and add a call to the **Authenticate** method:
	<!-- mark:1,3 -->
	````C#
	protected async override void OnNavigatedTo(NavigationEventArgs e)
	{
		await Authenticate();
		RefreshTodoItems();            
	}
	````

1. Press the F5 key to run the app and sign into Live Connect with your Microsoft Account.

When you are successfully logged-in, the app should run without errors, and you should be able to query Mobile Services and make updates to data.

---