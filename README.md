<h1>Implementing Federation Between Azure and Okta</h1>

<h2>Description</h2>
Vulnerability management is essential for organizations to identify and mitigate security vulnerabilities in their systems and networks. This project aims to explore the various stages of the vulnerability management lifecycle using Nessus Essentials and an insecure Windows 10 system. Nessus is a powerful tool that provides extensive scanning and remediation capabilities for vulnerability management. The following link provides an overview of the vulnerability management lifecycle: https://www.ibm.com/blog/vulnerability-management-lifecycle/. 
<br />

<h2>Environments Used </h2>

- <b>Security Assertion Markup Language (SAML)</b>
- <b>Okta</b>
- <b>Microsoft Entra ID</b>

<h2>Making Microsoft Entra ID an Identity Provider</h2> 

<p align="center">
To start, we will configure Entra ID to be an identity provider, which will assign authentication to Entra ID. Before we do this, however, it's important to set-up a break-glass account which will act as a backup account in case of an emergencty. For more information on the importance of break-glass accounts and access, refer to the following link: https://www.strongdm.com/blog/break-glass
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
We will now proceed with adding Entra ID as an identity provider. I will be referencing the steps outlined in the following Okta documentation: 
<br/>
<br/>
https://help.okta.com/en-us/content/topics/provisioning/azure/azure-identify-identity-provider.htm
<br/>
<br/>
Navigate to the following path: Security > Identity Providers > Add identity provider > SAML 2.0 IdP
 <br/>
 <br/>
<img src="https://i.imgur.com/yuHcciM.png" alt="Add IdP"/>
<br/>
<br/>
We will now configure SAML 2.0 using the following settings: 
 <br/>
 <br/>
<img src="https://i.imgur.com/jfwtPyg.png" alt="Select an IdP"/>
 <br/>
 <br/>
<img src="https://i.imgur.com/tQ9gCxu.png" alt="Configure IdP"/>
 <br/>
 <br/>
Ensure that you record the following values, as they will be necessary in the next steps: 'Assertion Consumer Service URL' and 'Audience URI'. 
 
<h2>Creating the Okta Enterprise App in Microsoft Entra ID</h2> 
<p align="center">
We will now create an Okta enterprise app in Entra ID, which will allow Azure to communicate with Okta. I will be referencing the following Okta documentation: 
<br/>
<br/>
https://help.okta.com/en-us/content/topics/provisioning/azure/azure-create-enterprise-app.htm
<br/>
<br/>
Begin by navigating to the Azure portal and to the following location: Microsoft Entra ID > Enterprise applications > New application > Create your own application. 
<br/>
<br/>
<img src="https://i.imgur.com/5kU6VKQ.png" alt="Create Application"/>
<br/>
<br/>
<img src="https://i.imgur.com/p1iCU3w.png" alt="Configure Application"/>
<br/>
<br/>
Once application has been created, navigate to 'Set up single sign on' and 'SAML':
<br/>
<br/>
<img src="https://i.imgur.com/GdltHN0.png" alt="Set up SSO"/>
<br/>
<br/>
<img src="https://i.imgur.com/3iLe5V6.png" alt="SAML Configuration"/>
<br/>
<br/>
In the ‘Basic SAML Configuration’ section, enter placeholder URLs for ‘Identifier’ and ‘Reply URL’, and proceed to save the configuration. Select 'Download for Certificate (Base64)' in the SAML Signing Certificate area to download the certificate to your computer, which will be necessary for the next steps. 

<h2>Mapping Microsoft Entra ID Attributes to Okta Attributes</h2> 
 <p align="center">
SAML claims are are pieces of information about a user that are shared between different systems to help with logging in and accessing services, such as their name or email address. We will be editing our attributes and claims, which is essentially what information we want to send to Okta for authentication. 
 <br/>
 <br/>
 <img src="https://i.imgur.com/Pzmbstz.png" alt="Attributes and Claims"/>
  <br/>
  <br/>
In addition to the default claims, we will add company name and telephone number: 
<br/>
 <br/>
 <img src="https://i.imgur.com/c3QstfC.png" alt="Additional Claims"/>
 <br/>
 <br/>
Return back to Okta, and navigate to SAML Certifications > Edit > New Certificate. Enter the following values to update the placeholders: the 'IdP Issuer URI' is the 'Microsoft Entra Identifier', the 'IdP Single Sign-On URL' is the 'Login URL', and the 'IdP Signature Certificate' is the 'Base64 download'. 
<br/>
 <br/>
 <img src="https://i.imgur.com/DR3tYq1.png" alt="SAML Certificate"/>
 <br/>
 <br/>
 <img src="https://i.imgur.com/peyTXNw.png" alt="SAML Values"/>
  <br/>
 <br/>
 Be sure to make note of the values above. The 'Assertion Consumer Service URL' will be the 'Reply URL in Azure', and the 'Audience URI' will be the 'Identity Entity ID' in Azure.  
<br/>
<br/>
In Entra ID, update the placeholder values: 
<br/>
<br/>
<img src="https://i.imgur.com/lDoz3p7.png" alt="SAML Configuration Update"/>
 <br/>
 <br/>
Back in Okta, navigate to: Edit profile and mappings > Mappings. We will then unmap all attributes except 'username'. 
<br/>
<br/>
<img src="https://i.imgur.com/trmCk9u.png" alt="Unmap Attributes"/>
 <br/>
 <br/>
We will then navigate to 'Custom', and proceed to delete and recreate the attributes. For the attribute mapping, I will be referencing the following Okta documentation: https://help.okta.com/en-us/content/topics/provisioning/azure/azure-map-attributes.htm. Once the attributes are created, we will proceed with updating the mappings: 
 <br/>
 <br/>
<img src="https://i.imgur.com/nzHv3uy.png" alt="Create Mappings"/>
<br />
<br />
 <img src="https://i.imgur.com/r36IKV9.png" alt="Mapping Attributes"/>
 <br />
<br />
We will test this out by navigating to our Okta application in Entra ID and assigning users to the application: Manage > users and groups > Add user/group 
<br />
<br />
<img src="https://i.imgur.com/519WGJ6.png" alt="Assign Users"/>
<br />
<br />
We are now ready to test the authentication! Navigate to Single sign-on > Test. 
<br />
<br />
<img src="https://i.imgur.com/XC7Yhjs.png" alt="Test the SSO"/>
<br />
<br />
<img src="https://i.imgur.com/ytzqWAk.png" alt="SSO Login"/>
<br />
<br />
For advanced testing and troubleshooting, download the 'My Apps Secure Sign-in Extension' Chrome extenstion. As shown below, we were able to successfully authenticate! 
<br />
<br />
<img src="https://i.imgur.com/fIbnlYn.png" alt="Successful SSO"/>
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
