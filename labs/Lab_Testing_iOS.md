## Testing on iOS simulator

### **School Mac - Get Admin access**

1. Login to a Mac PC

2. Right click the *Jamf* icon from the Mac OS task bar and click *Request Admin Privileges*

   <p json="left"><img src="images/ios/ios_mac_1.png" height=300/><img src="images/ios/ios_mac_1_1.png" height=300/></p>

3. Select *Software Testing* as a reason for the request.

4. If your admin privileges expire, you can simply make a new request.

### **Setting up the iOS simulator**

On the mac, while ensuring you have admin privileges 

1. Launch *XCode*

2. Accept the license agreement

3. From the list of components make sure that *iOS* is selected

4. Click *Download and Install*.

5. Once *XCode* is done downloading the iOS development components, test out the following steps to ensure that the iOS simulator is available:
   - Create a new Project in XCode
   - Select iOS > App
   - Give it a product name and a test organization name 
   - You should see a list of deployment options including the iPhone 16 simulator
   - Build the starter Xcode project to view
   - The iOS simulator should pop open 

   <img src="images/ios/xcode_simulator.png" height=300/>

### **Granting remote access**

1. Go to Settings > General > Sharing

2. Enable **Remote Login** 

   <img src="images/ios/ios_mac_4.png" height=300/>

3. Click the *+* button to add yourself as an authorized user 

4. Record the `ssh` command as this is how you can ssh from the Windows machine to this mac pc in later steps. 

   > Note this is the ip address you need to Pair Visual Studio with Mac.
   >
   > It should be `10.101.x.x` 

   <img src="images/ios/ios_mac_5.png" height=300/>

5. Enable **Remote Management** and add yourself to the list of authorized users:

   <img src="images/ios/ios_mac_6.png" height=300/>

6. Record the IP address: which should start with 10.101.X.X

## Visual Studio pairing with MAC 

1. Once you are ready to test your MAUI app on the iOS simulator, go back to the Development machine running Visual Studio. 

2. **IMPORTANT: ** Ensure Visual Studio is up to date with version 17.11.X  -where X is the latest version. 

3. On your development machine, open the Visual Studio Mac pairing tool:

4. Start pairing the mac and Windows machine:

   <img src="images/ios/pair_mac1.png" height=300/>

5. You might be prompted to install missing components such as `monoruntime` and the `dotnet` SDK, accept and continue. This step might take a while but not more than 15 minutes.  

   <img src="images/ios/pair_mac_2.png" height=300/>

6. If the connection is failing or the mac seems unreachable, ensure you can reach this `ip` address by running a `ping` to the mac ip from the development machine. 

7. Test out `ssh` to the mac to make sure remote connection is working. If it's not working, restart the mac and repeat the admin privilege request. steps. 

8. If connection fails or stalls at the step of installing the required components, open Terminal on the Mac OS or ssh via command-line, while ensuring your have admin privileges, then install the maui workload: 

   > You can also connect via `ssh` and write this via command line on a Windows machine.

   ```cmd
   sudo dotnet workload install maui
   ```

   

9. Another possible mistake is *The Xcode license status couldn't be verified because Xcode has not been found on the default location of the connected Mac. Having Xcode installed and functional is necessary in order to build and run iOS apps, so please check that Xcode is correctly installed and that the path specified under iOS Settings is correct.*:

   From Visual Studio, try the following:

   - Open Tools > Options > Xamarin 
   - Set the XCode path as shown below:

<img src="images/ios/Pairing_with_mac_2.png" height=300/>

1. Once the pairing is successful, click OK

<img src="images/ios/Paired_screen.png" height=300/>

9. From Visual studio, and so that the simulator window appears on the Windows machine, go to Tools > Options > Xamarin > iOS Settings 

10. You should now see a list of simulators as target platform options on Visual Studio:

    > Note: You should see the iPhone 16, or any device corresponding to iOS 18.2

    <img src="images/ios/list_sim.png" height=300/>

11. **IMPORTANT**: Ensure the *Remote Simulator to Windows* is enable and click OK

<img src="images/ios/important_setting.png" height=300/>

12. Run the app you wish to test, you should see appear the iOS simulator, the build process might take some time. 

<img src="images/ios/maui_app_on_ios_2.png" height=350/>

13. The iOS simulator looks better on Mac computers, so it's recommended to also monitor the iOS device from the Mac.

    

## Rider 

If you wish to develop directly from the Mac computer, you can use the [Rider](https://www.jetbrains.com/rider/) IDE developed by IntelliJ 

<img src="images/ios/rider_optional.png" height=300/>
