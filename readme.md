## Readme - Guides & Installation for Jenkins LTS automate build Android .apk

## Description
How to setup Jenkins LTS, automate build in MAC OS to release Android .apk, upload Android .apk to Diawi link in SVN (Subversion) with email notification.

## Installation
1. 	Download Jenkins LTS from here [Official] (https://jenkins.io) and install (follow the instructions using Homebrew).

2. 	Make sure your MAC OS is installed with Homebrew. For Homebrew installation, kindly follow the instructions from here [Official] (https://brew.sh)

3. 	Install the latest LTS version: ```brew install jenkins-lts```
	Install a specific LTS version: ```brew install jenkins-lts@YOUR_VERSION```
	Start the Jenkins service: ```brew services start jenkins-lts```
	Restart the Jenkins service: ```brew services restart jenkins-lts```
	Update the Jenkins version: ```brew upgrade jenkins-lts```

4.	Start the Jenkins LTS by using ```brew services start jenkins-lts``` in the Terminal app

5.	After Jenkins LTS is started, proceed to the browser (http://localhost:8080) and setup Jenkins LTS

6. 	Enter the Administrator password from the file /Users/$USER_NAME/.jenkins/secrets/initialAdminPassword. Make sure you are able to see hidden file (Command + Shift + Delete)

7.	Then, choose Install Suggested plugins. Create the First Admin User and save. If you are unable to select Install Suggested plugins, make sure you are installing the latest JAVA jdk from [here](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) with jdk1.8.0_231 to avoid security certification error.

8.	Next step, define the Jenkins URL : http://localhost:8080/ as default. (This URL would be the Jenkins host URL)

9.	The last step is to allow the host Jenkins URL can be accessed from anywhere by modify from here /usr/local/opt/jenkins-lts/homebrew.mxcl.jenkins-lts.plist :
	```<string>--httpListenAddress=127.0.0.1</string>``` to ```<string>--httpListenAddress=0.0.0.0</string>```

# In Jenkins LTS host
1.	Make sure the SVN Server and Jenkins Server timezone is in sync otherwise the Subversion would not be able to get changelogs. Go to Manage Jenkins > Script Console > enter the script and run ```System.setProperty('org.apache.commons.jelly.tags.fmt.timeZone', 'Asia/Singapore')```. Not sure your country time zone format - Go to [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) to check.

2.	Next, Go to Manage Jenkins > Global properties > Check Environment variables > enter the name for ANDROID_HOME & value for location. For example:
Name : ANDROID_HOME
Value : /Users/$USER_NAME/Library/Android/sdk

3.	Then, in the same Manage Jenkins > Go to Extended E-mail Notification > SMTP Server > Setup your email. For my case, im using gmail as Sender.
SMTP Server : smtp.gmail.com
SMTP port : 465
Username : xxx@gmail.com
Password : xxxx
Check - Use SSL

	In the same Manage Jenkins > E-mail Notification. Repeat the same steps as above.

4.	Lastly, setup Diawi as upload your .apk. Go to Manage Jenkins > Manage Plugins > Available > Search for Diawi plugin > Download & Install & Restart Jenkins LTS. Make sure you have Diawi account, if not go to Diawi link [here] (https://www.diawi.com) to setup a new Account. Once the account has been created, go to API access and generate new token. Keep this token in a safe place, because this token would be used to upload your .apk to Diawi and download URL will be generated in email notification.

5.	Select New Item > Free style project ($PROJECT_NAME) and click ok.

# Source Code Management
1.	Go to Source Code Management > Select Subversion > (Project Repository URL & Credential). Make sure the SVN URL ended with '@head' to get latest SVN Head version.

2. Uncheck the Quiet check-out to show SVN changes in the console.

Click Apply & Save

# Build
1.	Go to Build > Invoke Gradle Script > Select Gradle Wrapper > Tasks > (gradlew commands) to build .apk. For example:
	```clean
	   assembleRelease
	```
2.	This step if you want to upload your project Android .apk to Diawi link. Go to Build > Diawi Upload Step >
Token : (the token you have for your project in Diawi API Access token)
Proxy Port : 0
File(s) : (your .apk location) For example : 
```app/build/outputs/apk/release/xxx-release.apk```

Click Apply & Save

# Post-build Actions	   
1.	Go to Post-build Actions > Archive the artifacts >  Files to archive > (your .apk location). For example
	```app/build/outputs/apk/release/*-release.apk```

2.	Go to Post-build Actions > Editable Email Notification > 
Project Recipient List : (Recipient Email address)
Content Type : HTML (text/html)
Default Subject : $PROJECT_NAME package
Default Content : (in HTML text/html) format. For example: 
```
Hi,
<br/>
<br/>
$PROJECT_NAME package- Build # $BUILD_NUMBER - $BUILD_STATUS.
<br/>
<br/>
Proceed to this url ${FILE,path="app/build/outputs/apk/release/xxx-release.apk.diawilink"} to download .apk
<br/>
<br/>
<br/>
${CHANGES_SINCE_LAST_SUCCESS, reverse=true, format="<b>Changes for Build #%n</b><br/>%c<br/>", changesFormat="<br/>Date- %d<br/>Author - %a<br/>SVN Revision - %r<br/>Path Changes - %p<br/>Commit Message - %m <br/>"}

<br/>
<br/>
<br/>
Thanks
```
Then go to Advaned Settings.. > Triggers > Remove Failure Any. Add Trigger > Always > Add > Only Recipient List.

Click Apply & Save

# Last
1.	Restart the Jenkins LTS

2.	Go to the project and click Build Now. Enjoy!


## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)