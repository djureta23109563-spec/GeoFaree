GeoFare - Software Engineering Project

How to Run This Project from the Repository

Follow these steps to successfully clone, build, and run the GeoFare app on your local machine.

1. Clone the Repository
Open your terminal or command prompt and run the following command:
git clone https://github.com/djureta23109563-spec/GeoFaree.git
(Note: Replace the URL with your actual group's GitHub repository URL).

2. Open the Project in Android Studio
Launch Android Studio.
Click File > Open.
Navigate to the GeoFare folder you just cloned and click OK.
Wait for the Gradle Sync to finish (a loading bar will appear at the bottom right).

3. Add the google-services.json file (Required for Firebase)
This app uses Firebase. For security, the google-services.json file is NOT included in the repository. You must generate your own copy:

Go to the Firebase Console (https://console.firebase.google.com/).
Select the "GeoFare" project.
Click the Android icon to register a new app.
Enter the package name: com.example.geofare.
Download the google-services.json file.
Move this file into the app/ folder of your local project (Path: GeoFare/app/google-services.json).
Click the "Sync Now" button in Android Studio.

4. Run the App
You can run the app on a physical device or an emulator:

Option A: Using a Real Android Phone (Recommended)
On your phone, enable Developer Options (Go to Settings > About Phone > Tap "Build Number" 7 times).
Enable USB Debugging (Settings > System > Developer Options > USB Debugging).
Plug your phone into your PC via USB.
On your phone, swipe down and change the USB mode to "File Transfer".
In Android Studio, click the "Run" (Play) button.

Option B: Using an Android Emulator
In Android Studio, click "No Devices" > Device Manager.
Click Create device.
Select a phone (e.g., Pixel 8), download an API 34 image, and click Finish.
Click the Green Play button in the Device Manager to launch the emulator.
Wait for the virtual phone to boot up, then click the "Run" (Play) button in Android Studio.
