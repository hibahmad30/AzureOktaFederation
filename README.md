<h1>Implementing Federation Between Azure and Okta</h1>

<h2>Description</h2>
Microsoft Entra ID and Okta are two leading identity management platforms renowned for their ability to handle complex authentication needs. By implementing federation between Entra ID and Okta, organizations can streamline authentication processes, enhance security, and improve user experience. This project aims to integrate these systems to create a unified access management framework that supports Single Sign-On (SSO) and centralized control over user identities and permissions. Here is a high-level architecture diagram that displays the configuration for this project: 
<br />
<br />
<p align="center">
<img src="https://i.imgur.com/GEq961u.png" alt="Architecture Diagram"/>

<h2>Environments Used </h2>

- <b>Security Assertion Markup Language (SAML)</b>
- <b>Okta</b>
- <b>Microsoft Entra ID</b>

<h2>Making Microsoft Entra ID an Identity Provider</h2> 

<p align="center">
To begin, we will configure Entra ID as the identity provider responsible for authentication. Before proceeding, it is crucial to set up a break-glass account. This account will serve as a backup in emergencies. For more details on the importance of break-glass accounts and access, please refer to the following link: https://www.strongdm.com/blog/break-glass
<br/>
<br/>
Here are the steps in Okta to configure this account: Directory > People > Add person 
 <br/>
 <br/>
<img src="https://i.imgur.com/NBmVOHA.png" alt="Add Okta User"/>
 <br/>
 <br/>
We will then add the new user to the 'Super Administrator' role: User > Admin roles > Edit individual assignments  > Role > Super Administrator > Save Changes 
 <br/>
 <br/>
<img src="https://i.imgur.com/LhGkYf9.png" alt="Super Administrator Role"/>
  <br/>
 <br/>
We will now proceed with adding Entra ID as an identity provider, following the steps outlined in the following Okta documentation:
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
Ensure to record the following values, as they will be necessary for the next steps: 'Assertion Consumer Service URL' and 'Audience URI'. 
 
<h2>Creating the Okta Enterprise App in Microsoft Entra ID</h2> 
<p align="center">
We will now create an Okta enterprise app in Entra ID, facilitating communication between Azure and Okta. For guidance, I will refer to the following Okta documentation: 
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
In the ‘Basic SAML Configuration’ section, enter placeholder URLs for ‘Identifier’ and ‘Reply URL’, and proceed to save the configuration. Select 'Download for Certificate (Base64)' in the SAML Signing Certificate area to download the certificate to your computer, which will be required for the next steps. 

<h2>Mapping Microsoft Entra ID Attributes to Okta Attributes</h2> 
 <p align="center">
SAML claims are pieces of information about a user that are shared between different systems to help with logging in and accessing services, such as their name or email address. We will be editing our attributes and claims, which is essentially what information we want to send to Okta for authentication. 
 <br/>
 <br/>
 <img src="https://i.imgur.com/Pzmbstz.png" alt="Attributes and Claims"/>
  <br/>
  <br/>
In addition to the default claims, we will add 'company name' and 'telephone number': 
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
We will test the authentication by first navigating to our Okta application in Entra ID and assigning users to the application: Manage > users and groups > Add user/group 
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
For advanced testing and troubleshooting, you may optionally download the 'My Apps Secure Sign-in Extension' Chrome extension. As demonstrated below, we successfully authenticated!
<br />
<br />
<img src="https://i.imgur.com/fIbnlYn.png" alt="Successful SSO"/>
<h2>Key takeaways:</h2>
This project focuses on integrating federation between Microsoft Entra ID and Okta to create a seamless and secure authentication experience across hybrid environments. Federation in this context means connecting these systems so that users can log in once and gain seamless access to resources managed by either platform. This integration simplifies the authentication process, enhances security, and improves user experience across hybrid IT environments. The process begins with the essential step of setting up a "break-glass" account in Entra ID, an emergency backup to ensure continuous access even if primary authentication methods fail. Entra ID is then configured to act as the primary identity provider for Okta by utilizing the SAML 2.0 protocol. 
<br/>
<br/>
This involves establishing a secure communication link between Azure and Okta through the creation of an Okta enterprise application in Entra ID. The integration involves the setup of SAML claims, which define and map user attributes such as name, email, company name, and telephone number. These claims are fundamental in identifying and authenticating users across both systems. The final step in the implementation is thorough testing and validation to confirm that users can successfully log in to Okta applications using their Entra ID credentials.
<br/>
<br/>
Implementing federation between Azure and Okta offers significant benefits such as enhancing user experience by enabling Single Sign-On (SSO), allowing users to log in once and access multiple systems without repeated authentication. This streamlines the process and reduces the frustration of managing multiple passwords. From an administrative perspective, federation simplifies centralized management of user identities and access rights, reducing the overhead for IT teams. 
<br/>
<br/>
Federation is particularly valuable in several use cases such as for organizations operating in hybrid environments, where a mix of on-premises and cloud-based resources needs unified access management. It also facilitates secure cross-organizational collaboration, allowing external partners to access shared resources using their own identity systems. As companies expand their IT infrastructure, federation supports scalable identity solutions by seamlessly integrating multiple identity providers. 
<br/>
<br/>   
Additionally, it aids in regulatory compliance by offering a centralized, auditable log of user access across all systems. Key terms like federation, SAML, identity provider, attributes and claims, and break-glass accounts play crucial roles in understanding this project. By combining the strengths of Microsoft Entra ID and Okta through federation, this project creates a unified and efficient identity management solution that simplifies and secures user access to diverse systems.
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
