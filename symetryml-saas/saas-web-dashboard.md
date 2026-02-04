# SaaS Dashboard

The SymetryML SaaS dashboard allows users to create new instances and manage existing instances in a user friendly Graphical User Interface (GUI) environment.&#x20;

![SymetryML SaaS: Dashboard](<../.gitbook/assets/saas\_web\_dashboard.png>)

Click on the tab labelled New Instance and the following window will appear:

![SymetryML SaaS: Create Instance](<../.gitbook/assets/saas\_dashboard\_new\_aws.png>)

This window allows the user to select the platform on which to create the instance: **Amazon Web Services (AWS)** or **Oracle Cloud Infrastructure (OCI)**.&#x20;

The user can also select the _**size**_ of the instance and whether to enable _**GPU**_ capabilities.&#x20;

Once the user has selected the _**platform**_ and _**instance size**_, enter credentials and click _**Create**_ and the instance will be created. While the instance is being created the _**Instance State**_ column will display _**Creating**_ and the _**Status**_ column will have a spinning wheel icon, and _**# of Users**_ column will appear as _**0**_ at this stage as no users have been assigned to the instance at this point.

![SymetryML SaaS: Create Instance](<../.gitbook/assets/saas\_dashboard\_instance\_list.png>)

Once the instance has been created and is running the _**Instance State**_ will display _**running**_, the _**Status**_ column will have a check mark, and _**# of Users**_ column will display _**+ Assign Users.**_ Next_**,**_  click on _**+Assign Users**_ and the following screen will appear.

![SymetryML SaaS: Assign user to new instance](<../.gitbook/assets/saas\_dashboard\_add\_users.png>)

Click _**Add**_ and the selected user will be assigned to the instance created, and the _**# of Users**_ column will update from _**0**_ to _**1**_ as demonstrated in the following screen.

![SymetryML SaaS: User assigned to new instance](<../.gitbook/assets/saas\_dashboard\_add\_users1.png>)

When the "_**All Users**_" tab is enabled, this simply allows _**Admin**_ and _**Root**_ users to view instances that are not assigned to them.&#x20;

Once the instance is running, the user can click on the instance and the following tabs will appear under the _**Instance Details**_ banner:

_**Summary**_ - This reveals the _**instance type**_, _**instance id**_, and _**the platform**_ hosting the instance.  &#x20;

![SymetryML SaaS: Instance Details Summary Tab](<../.gitbook/assets/saas\_instance\_summary.png>)

_**Users**_ - This tab details which users are assigned to the instance selected.

![SymetryML SaaS: Instance user assignment](<../.gitbook/assets/saas\_instance\_users.png>)

_**Jobs**_ - This tab shows the details of all running jobs of the instance selected, by user. &#x20;

![SymetryML SaaS: User jobs on running instance](<../.gitbook/assets/saas\_dashboard\_jobs.png>)

Click on the _**Action**_ tab and the following options will appear.

![SymetryML SaaS: Action Tab options](<../.gitbook/assets/saas\_dashboard\_actions.png>)

The user is able to _**Start**_, _**Stop**_, _**Restart,**_ and _**Terminate**_ an instance, as well as _**Assign Users**_ to an instance. The _**Show Orphans**_ option allows an _**Admin**_ or _**Root**_ user to see which instances are currently available and not assigned to a particular user.&#x20;

The user can click on a running instance and click _**Connect**_ to connect directly to the running instance, and the following screens will appear:

![SymetryML SaaS: Click OK and proceed to the SymetryML GUI of selected running instance](<../.gitbook/assets/saas\_dashboard\_connect.png>)

Next the SymetryML GUI will appear and the user can proceed to log into SymetryML GUI of the selected running instance.

![SymetryML SaaS: Connecting to instance SymetryML GUI](<../.gitbook/assets/sym\_web\_login.png>)

The user is now able to proceed to and use the ML Toolkit. &#x20;

