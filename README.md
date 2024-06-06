<h1>Implementing Federation Between Azure and Okta</h1>

<h2>Description</h2>
Vulnerability management is essential for organizations to identify and mitigate security vulnerabilities in their systems and networks. This project aims to explore the various stages of the vulnerability management lifecycle using Nessus Essentials and an insecure Windows 10 system. Nessus is a powerful tool that provides extensive scanning and remediation capabilities for vulnerability management. The following link provides an overview of the vulnerability management lifecycle: https://www.ibm.com/blog/vulnerability-management-lifecycle/. 
<br />

<h2>Environments Used </h2>

- <b>Security Assertion Markup Language (SAML)</b>
- <b>Okta</b>
- <b>Microsoft Entra ID</b>

<h2>Create Okta Enterprise App in Microsoft Entra ID</h2> 

<p align="center">
To start, we will create an Okta enterprise app in Entra ID, which will allow Azure to communicate with Okta. Before we do this, however, it's important to set-up a break-glass account which will act as a backup account in case of an emergencty. For more information on the importance of break-glass accounts and access, refer to the following link: https://www.strongdm.com/blog/break-glass
  <br/>
 <br/>
Here are the steps in Okta to configure this account: Directory > People > Add person 
 <br/>
 <br/>
<img src="https://i.imgur.com/NBmVOHA.png" alt="Add Okta User"/>
 <br/>
 <br/>
We will then add the new user to the Super Administrator role: User > Admin roles > Edit individual assignments  > Role > Super Administrator > Save Changes 
 <br/>
 <br/>
<img src="https://i.imgur.com/LhGkYf9.png" alt="Super Administrator Role"/>
  <br/>
 <br/>
We will now proceed with creating the enterprise application and adding Entra ID as an identity provider. I will be referencing the steps outlined in the following Okta documentation: 
<br/>
<br/>
https://help.okta.com/en-us/content/topics/provisioning/azure/azure-create-enterprise-app.htm
<br/>
<br/>
https://help.okta.com/en-us/content/topics/provisioning/azure/azure-identify-identity-provider.htm
<br/>
<br/>
Navigate to the following path: Security > Identity Providers > Add identity provider > SAML 2.0 IdP
 <br/>
 <br/>
<img src="https://i.imgur.com/yuHcciM.png" alt="Add IdP/>
<br/>
<br/>
We will now configure SAML 2.0 using the following settings: 
 <br/>
 <br/>
<img src="https://i.imgur.com/jfwtPyg.png" alt="Add IdP/>
 
<h2>Uncredentialed scan:</h2> 
Prior to running the first scan, it is important to verify that the host machine can connect to the virtual machine. In the Windows 10 virtual machine, navigate to the command line and use the 'ipconfig' command to gather the IPv4 address of the system. With this IPv4 address, navigate to the command line of the host machine and type in the command 'ping x.x.x.x,' where 'x.x.x.x' is the IPv4 address of the virtual machine. If the host machine is not able to ping the target machine, navigate to the Windows Defender Firewall (wf.msc) and make the appropriate firewall state configuration changes.
<br/>
<br/>
In Nessus, click 'New Scan,' then 'Basic Network Scan,' and provide a name for the scan as well as the virtual machine's IPv4 address in the 'Targets' category. Once the scan is saved, click the 'Launch' icon to begin the scan. The image below depicts the results of the first scan. The vulnerabilities discovered by the scan are primarily in the 'Info' severity, with one being in the 'Medium' severity. To read more about the severity levels assigned by Tenable, refer to the following link: https://docs.tenable.com/nessus/Content/RiskMetrics.htm. Without the use of privileged credentials, the scan is not able to go in-depth and find many vulnerabilities. Therefore, the next step is to perform a credentialed scan.
<br />
<br />
<img src="https://i.imgur.com/xCn6H4G.png" height="60%" width="60%" alt="Uncredentialed Scan"/> 
<br />
<br />
<img src="https://i.imgur.com/00b4WGs.png" height="60%" width="60%" alt="Uncredentialed Scan"/> 
<h2>Credentialed scan:</h2> 
 <p align="center">
In order to run a credentialed scan, the virtual machine must be configured to accept authenticated scans. The following link provided by Tenable describes how to configure the virtual machine for credentialed scans: https://community.tenable.com/s/article/Scanning-with-non-default-Windows-Administrator-Account?language=en_US. Some of the steps include enabling 'Remote Registry' and ensuring that 'File and Printer Sharing' is turned on in 'Advanced Sharing Settings.'
 <br/>
 <br/>
 <img src="https://i.imgur.com/chTgvEo.png" height="70%" width="70%" alt="Remote Registry"/>
  <br/>
   <br/>
 <img src="https://i.imgur.com/3V8BHiM.png" height="70%" width="70%" alt="File and Printer Sharing"/>
 <br/>
 <br/>
In Nessus, select the previous scan that was created, click 'More,' and then 'Configure.' Navigate to the 'Credentials' tab to the right of 'Settings.' The 'Authentication method' should be set to 'Password.' Enter the username and password of the target Windows 10 virtual machine. After saving these credentials, launch the scan. The image below displays the results of the first credentialed scan:
 <br/>
 <br/>
 <img src="https://i.imgur.com/6OoK2N3.png" height="60%" width="60%" alt="Credentialed Scan Results"/>
  <br/>
 <br/>
 <img src="https://i.imgur.com/AFDXeDE.png" height="60%" width="60%" alt="Credentialed Scan Detailed Results"/>
  <br/>
 <br/>
In the previous uncredentialed scan, the highest severity was 'Medium,' with only 1 vulnerability in this category. In this credentialed scan, there are 7 'Medium' level vulnerabilities, 34 'High,' and 8 'Critical' vulnerabilities. These results further highlight the significance of performing credentialed scans. The two images below are a direct comparison of the results of the uncredentialed scan (left) and the credentialed scan (right). 
 <br/>
 <br/>
 <img src="https://i.imgur.com/25bx2Ph.png" height="30%" width="30%" alt="Uncredentialed Scan Results"/>     <img src="https://i.imgur.com/WjAcm6L.png" height="30%" width="30%" alt="Credentialed Scan Results"/>
  <br/>
 <br/>
For further analysis, I have downloaded a deprecated version of Firefox on the Windows 10 virtual machine. When installing deprecated software, it is vital to ensure that it is done in a sandboxed environment. I then relaunched the scan and got the following results:
 <br/>
 <br/>
 <img src="https://i.imgur.com/nFye7ok.png" height="60%" width="60%" alt="Credentialed Scan Results w/ Firefox"/>
  <br/>
 <br/>
 <img src="https://i.imgur.com/KLqflvy.png" height="50%" width="50%" alt="Credentialed Scan Detailed Results w/ Firefox"/>
  <br/>
 <br/>
Rather than having 7 'Medium' level vulnerabilities as in the first credentialed scan, there are now 23. The 'High' severity vulnerabilities increased from 34 to 108, and the 'Critical' level vulnerabilities increased from 8 to 86. These scan results emphasize the importance of ensuring that all third-party software are fully patched and up-to-date. Depicted below is a comparison of the first credentialed scan (left) to the second credentialed scan with the deprecated version of Firefox installed (right):
 <br/>
 <br/>
 <img src="https://i.imgur.com/WjAcm6L.png" height="30%" width="30%" alt="Credentialed Scan Results"/>     <img src="https://i.imgur.com/FcqpzOC.png" height="30%" width="30%" alt="Credentialed Scan Results w/ Firefox"/>
<h2>Remediation:</h2> 
 <p align="center">
Prior to running a vulnerability scan, it is essential to ensure that both the operating system and all third-party software are fully up-to-date. This will significantly reduce the number of vulnerabilities present in the scan results and will allow the analyst to have more efficient vulnerability management. To further maximize efficiency, be sure to set up automatic updates on your operating system and third-party software.
 <br/>
 <br/>
<img src="https://i.imgur.com/aMYixCT.png" height="60%" width="60%" alt="Uninstall Firefox"/>
<br />
<br />
 <img src="https://i.imgur.com/2aNsH9T.png" height="60%" width="60%" alt="Windows Updates"/>
 <br />
<br />
After uninstalling the deprecated Firefox and running Windows updates, I recieved the following scan result:
<br />
<br />
<img src="https://i.imgur.com/a1LVKQZ.png" height="60%" width="60%" alt="Windows Update Scan"/>
<br />
<br />
 <img src="https://i.imgur.com/XqWuev6.png" height="60%" width="60%" alt="Windows Update Scan"/>
<br />
<br />
I then investigated more specific vulnerabilities for remediation. I found a few more vulnerabilities related to out-of-date software and installed the necessary software updates.
<br />
<br />
<img src="https://i.imgur.com/lj5Xcv0.png" height="70%" width="70%" alt="3D Viewer Vulnerability"/>
<br />
<br />
 <img src="https://i.imgur.com/vY8a8LB.png" height="70%" width="70%" alt="3D Viewer Update"/>
<br />
<br />
<img src="https://i.imgur.com/Nv8j19h.png" height="70%" width="70%" alt="Onedrive Update"/>
<br />
<br />
Missing or misconfigured registry keys can also cause vulnerabilities on a system. When configuring registry keys, it is crucial to ensure that the changes made align with the intended system or application settings and do not introduce unintended consequences or conflicts with existing configurations. To address the WinVerifyTrust Signature Validation Vulnerability (CVE-2013-3900), refer to the following link: https://msrc.microsoft.com/update-guide/vulnerability/CVE-2013-3900.
<br />
<br />
 <img src="https://i.imgur.com/Zrx2GOj.png" height="70%" width="70%" alt="WinVerifyTrust Vulnerability"/>
<br />
<br />
<img src="https://i.imgur.com/0eXXMzV.png" height="70%" width="70%" alt="WinVerifyTrust Remediation"/>
<br />
<br />
I also addressed the 'SMB Signing not required' vulnerability, which has a 'Medium' severity level. This involved navigating to the 'Local Security Policy' and making the necessary configuration changes. The following link provides more information on how to resolve this vulnerability: https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/microsoft-network-server-digitally-sign-communications-always.
<br />
<br />
<img src="https://i.imgur.com/AtNAkOd.png" height="70%" width="70%" alt="SMB Signing Vulnerability"/>
<br />
<br />
 <img src="https://i.imgur.com/lOaujty.png" height="70%" width="70%" alt="SMB Signing Remediation"/>
<br />
<br />
<img src="https://i.imgur.com/3zNJDy1.png" height="70%" width="70%" alt="SMB Signing Remediation"/>
<br />
<br />
After addressing these more specific vulnerabilities on the target system, I launched the final Nessus scan:
<br />
<br />
<img src="https://i.imgur.com/X4tXd1e.png" height="60%" width="60%" alt="Final Scan Results"/>
<br />
<br />
 <img src="https://i.imgur.com/0rygP8U.png" height="60%" width="60%" alt="Final Scan Results"/>
<br />
<br />
Rather than having 5 'Medium' level vulnerabilities as in the previous scan, there are now 0. The 'High' severity vulnerabilities decreased from 16 to 3, and the 'Critical' level vulnerabilities decreased from 2 to 1. Depicted below is a comparison of the previous scan after Firefox was uninstalled and Windows updates were run (left) to the most recent scan where more specific remediations were also made (right):
<br />
<br />
<img src="https://i.imgur.com/cVK2O89.png" height="35%" width="35%" alt="Update Scan Results"/>     <img src="https://i.imgur.com/229wO2C.png" height="35%" width="35%" alt="Remediation Scan Results"/>
<h2>Key takeaways:</h2>
 Credentialed scans are essential for identifying vulnerabilities in a system, as they allow Nessus to access and scan all parts of the system, including system files, registry entries, and application settings. This results in a more comprehensive and accurate assessment of the system's vulnerabilities.
<br/>
<br/>
Deprecated software is a major source of vulnerabilities, making it crucial to keep all software up to date, including your operating system, browsers, and applications. Deprecated software often contains known security vulnerabilities that have been addressed in newer versions. While vulnerability remediation does often involve applying patches and updating software, it may also require changing system configurations. Nessus scans can be used to identify these vulnerabilities and remediate them. 
<br/>
<br/>
Regularly scanning your systems on an ongoing basis is also crucial to address any new vulnerabilities that may appear. The vulnerability management lifecycle is a powerful tool used to ensure that vulnerabilities are addressed continuously: 
<br/>
<br/>
In the planning stage of vulnerability management (Stage 0), any stakeholders and/or resources that will be involved should be identified. Guidelines and metrics outlining any goals or expectations should also be provided. In this project, the target system was a Windows 10 virtual machine, and the goal was to gain an overview of how vulnerability management works. 
<br/>
<br/>
In this project, the target system was a Windows 10 virtual machine, and the goal was to understand how various configuration changes to both the Windows 10 machine and Nessus can result in varying scan results. Another goal was to explore different vulnerability remediation techniques.
<br/>
<br/>
In the asset discovery phase (Stage 1), an inventory of all hardware and software assets should be created and/or updated regularly. It is common to use specialized solutions for asset management. This stage also involves scanning the assets for vulnerabilities. In this project, the primary asset was the Windows 10 virtual machine, and Nessus Essentials was used as the vulnerability scanner. 
<br/>
<br/>
Stage 2 of the vulnerability management lifecycle is vulnerability prioritization, where the vulnerabilities discovered in Stage 1 are prioritized based on their severity level. This stage focuses on maximizing efficiency. The prioritization is often based on threat intelligence sources such as CVE or CVSS, and may also be based upon how critical the particular asset is, and how likely it is to be exploited. In this project, the prioritization was primarily based on the severity levels provided by Nessus Essentials. 
<br/>
<br/>
Stage 3 involves vulnerability resolution, which involves remediation, mitigation, or acceptance. Remediation may involve patching software or making changes to system configurations, and essentially removing the vulnerability from the system entirely. Mitigation either lessens the severity of the vulnerability or makes it more challenging to exploit, but does not remove it from the system. For vulnerabilities that are less severe or not as high of a priority, they may just be accepted rather than being remediated or mitigated. This project focused on remediation primarily, however it is still crucial to understand mitigation and acceptance when working on larger scale systems in an organization. 
<br/>
<br/>
Stage 4, or the verification and monitoring phase, involves scanning the systems again to ensure that their remediation and mitigation efforts were effective. This is also to ensure that there were no new vulnerabilities created while making any changes to the system. 
<br/>
<br/>
The final stage, reporting and improvement (Stage 5), involves documenting the vulnerabilities that were found, as well as their outcomes. This stage focuses on the most recent scans and actions taken in the life cycle, as vulnerability management is an ongoing process. It is very important to have clear, up-to-date, and comprehensive documentation, particularly when working with stakeholders.

<p align="center">
<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
