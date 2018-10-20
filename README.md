# oupd
**_Opensource  Universal Privacy Daemon_**

**_Executive Summary:_**

With ever-growing concern about privacy and regulation around data, it is imperative to have a universal privacy daemon that can handle both, the trust layer and the consent layer while also providing tools and techniques for the end to end data lifecycle management. In this paper, am presenting the approach that can be taken to create such a daemon along with issues and challenges that may need to be addressed and several recommendations for the same.

**_What are Daemons:_**

Daemons are the processes which run in the background and are not interactive. They have no controlling terminal. They perform certain actions at predefined times or in response to certain events. In Windows environment, we generally know them as background services.

**_The scope of Privacy Daemon:_**

Privacy Daemon can expose an Application Binary Interface (ABI), using which all the applications which need to access user's data can make the relevant call. While making the call, based on certain metadata, the Daemon can check the policy schema available at the relevant applicable regulatory authority's website or other trusted policy brokers. For instance, If a user's location is in EU then the Daemon can resolve the GDPR policy schema from a policy broker and accordingly expose the data to another requesting application. This data can be encapsulated with 'Data Policy Markup Language' (DPML - more on it later), tokenized and shared through a permission driven blockchain (like Hyperledger Fabric or Multichain), which can allow for greater policy-based restrictions and transparency.

**_Why Open Source and Universal?_**

Given the nature of this project, where transparency, trust, collaboration, and security are of paramount importance, making this project an opensource endeavor is the only viable option. Further, to make it effective, it has to be universally acceptable to avoid fragmented ecosystem of such isolated initiatives.

**_What are the benefit of this Daemon besides data privacy?_**

Due to various diverse data protection laws across various industries, sectors, and governments/countries, the cost of compliance is expected to grow multifold in the next few years. In most cases, since there is no underlying policy broking framework, it is almost impossible to implement the data privacy rules. There is also a concern about overall effectiveness, transparency, and efficiency of such systems in the long-term. The given solution is expected to reduce the overall cost of compliance by 60% and ensure greater transparency and audit logs throughout the ecosystem.

**_How will it work under the hood:_**

It is obvious that our privacy daemon must work seamlessly across the various platforms/OS with an ability have greater control over overall functionality and because of that C programming language (for marking OS-level interface) and Java (for cross-platform Daemon APIs) can be used to develop such daemon.

The daemon will provide for two-sided information exchange protocol by exposing various encrypted ABI/API calls on OS level, which will allow different applications to request for the personal data or submit/store the personal data along with policy encapsulation.

This Privacy Daemon will have different approaches for different datasets. For automatically generated datasets, like sensors related data on the device, this daemon will provide a direct interface to other applications. It will still ask for the user consent where applicable (one time or recurring), maintain logs of applications which requested the data, store the intent/ purpose along with the relevant policy encapsulated data. For user's personal data, there will be a secure encrypted vault and for third-party controlled but user-owned data (like Bank data etc) there will be an additional layer of consent and security to ensure all the parties involved are properly authorized to do so.

This Daemon can be integrated directly in OS, can be imported to a platform/website or can be accessed through cloud service.

This daemon will also provide for verification service to instill trust. Compliant OS/platforms/websites can add be issued a trust/seal (similar to Verisign trust seal).

**_Policy-based data encapsulation and DPML:_**

Policy encapsulation in this context refers to the ability to wrap and embed the data along with the relevant policy. This can be achieved using DPML (Data Policy Markup Language). Following is the basic example of DPML in action. This can be auto-generated with front-end or API inputs so no need to write this every time. Further, since there is no dedicated ISO standard for Data Privacy we can just extend the existing ISO 27001 framework.

In order to make this simpler, let's take an example and understand how will it work. Let's say there is a data fiduciary (A Bank) based out of EU who has a data processor (Outsourced vendor) in India. Let's say Walnut application would like to have access to users' basic personal information, SMS data, GPS data, Audio (to take voice notes), Camera (to take pictures), credit card statement and Bank account statements.

**_Section 1 : Schema Definition & Setup_**
```
<!-- (c) OUPD Foundation -->
<!-- Date/time created: 19/10/2018 1:02:57 PM -->
<policy xmlns="http://www.oupd.org/2018/instance"
xmlns:xsi="http://www.w3.org/2018/XMLSchema-instance" 
xmlns:iascf-pfs="http://www.oupd.org/dpml/ResolvePolicy"  
xmlns:iso27001= "http://www.oupd.com/DPML/iso27001/2018-10-30" 
xsi:schemaLocation=" http://www.oupd.org/DPML/ResolvePolicy.xsd>
```
In the above example, we are describing how to interpret the encapsulated data. Based on several parameters like entity type, origin, and destination, it will automatically resolve the relevant data protection policy from the server.

**_Section 2 : Data Request by Walnut (Requestor):_**
```
pubkey entitytype="requestor" id="5B94A29C5EE602FA40FC4E149514DA3DF2AD31B8F57684EFB6330AA983D0BBC3">**base64 public key of data requestor**</pubkey>
<pubkey entitytype="fiduciary" id="4381DC2AB14285160C808659AEE005D51255ADD7264B318D07C7417292C7442C">**base64 public key of bank**</pubkey>
<pubkey entitytype="owner" id="0A041B9462CAA4A31BAC3567E0B6E6FD9100787DB2AB433D96F6D178CABFCE90">**base64 public key of data principal/owner**</pubkey>

<data requestid="B06AA45EB35423F988E36C022967B4C02BB719B037717DF13FA57C0F503D8A20">
    <field index=0>
        <datatype>string</datatype>
        <minlen>2</minlen>
        <maxlen>60</maxlen>
        <title defaultschema="FirstName">First Name</title>
    </field>

    <field index=1>
        <datatype>string</datatype>
        <minlen>2</minlen>
        <maxlen>60</maxlen>
        <title defaultschema="LastName">Last Name</title>
    </field>

    <field index=2>
        <datatype>date</datatype>
        <title defaultschema="DOB">Date of Birth</title>
    </field>

    <field index=3>
        <datatype>double</datatype>
        <decimal>4</decimal>
        <seperatorstyle>IN</seperatorstyle>
        <title defaultschema="Customer Balance">Balance</title>
    </field>
</data>

<data requestid="2FC41EF02A3216E4311805A9A11405A41A8D7A9F179526B4F6F2866BFF009A10">
    <deifnitionpath>http://example.com/bank_statement_schema.csv</definitionpath>
</data>

<origin>
    <ipv4>xxx-xxx-xxx-xxx</ipv4>
    <ipv6>2001:0db8:85a3:0000:0000:8a2e:0370:7334</ipv6>
    <location schema="ISO 3166-2">
        <country>IN</country>
    </location>
</origin>

<destination>
    <url>**Website/Service/API path where request is being made</url>
    <location schema="ISO 3166-2">
        <country>DE</country>
    </location>
</destination>
```
As can be seen above, the requestor can either share data definition directly in policy DPML or through a reference to a pre-formatted policy compliant CSV file. Before the Bank can receive this data request, the policy broker service will parse the request and check for the customer consent requirement. If consent is required, it will notify the customer and get the consent. Origin and destination tags can be within the individual data tags (in case each data element is going from/to different origin/destination or it can be outside the data tags to imply global applicability (applicable to all the data exchanges).

**_Section 3: Request received by the Bank and response shared_**

Besides aforesaid request parameters, the Bank will also receive the customer consent to share the data. For instance, the one part of the request will look like below where consent id is shared against the relevant request id. Further, the Bank will also receive the applicable policy path scheme added by the policy broker.
```
<data requestid="2FC41EF02A3216E4311805A9A11405A41A8D7A9F179526B4F6F2866BFF009A10">
    <consent id="5AA5985F5D2DA7FC558C6352F304088D5709059A056451046664DDAF4CC6CC2C" timestamp=1539984268 channel="APP14786436">
        <location schema="GPS">
            <latitude>51.1657N</latitude>
            <longtitude>10.4515E</longtitude>
        </location>
    </consent>
    <deifnitionpath>http://example.com/bank_statement_schema.csv</definitionpath>
    <policies>
        <originpolicypath>https://oupd.org/PDPR.xsd</originpolicypath>
        <destinationpolicypath>https://oupd.org/GDPR.xsd</destinationpolicypath>
    </policies>
</data> 
```

It should be noted that all request id and consent id are signed hashes. i.e. One can verify the origination using public keys provided. This adds non-repudiation layer to the framework.

Further, by design, our privacy daemon only exposes relevant bare minimum information to the policy brokers. Only metadata which is required to resolve policy related matters is shared. Under the hood, privacy daemon will also store relevant **'Proof of Compliant Data Exchange' (PCDE)** to the permissioned blockchain. While the PCDE will be publically visible, other aspects of the transmission will only be visible to the relevant parties involved in original transmission.

**_Sample Response:_**

**Scenario 1:**

Based on the origin and destination policy schemas, if the EU client data cannot be shared with Indian agency (even after consumer's consent) then the policy broker immediately will respond with the following output.
```
<response 
requestid="2FC41EF02A3216E4311805A9A11405A41A8D7A9F179526B4F6F2866BFF009A10">
Policy violation. Under the GDPR provisions as adopted by the country of origin DE, 
data transfer to destination country IN is not permissible </response>
```

**Scenario 2:**

If the data exchange is approved by the policy broker then along with required keys to verify the sender/receiver as outlined earlier following data will be exchanged:
```
<data>
    <blob index=0 compression="gzip" encryption="pgp">
        **Compressed & Encrypted Data Blob**
    </blob>
</data>

<destination>
    <url>**Website/Service/API path where request is being made</url>
    <location schema="ISO 3166-2">
        <country>IN</country>
    </location>
</destination>

<origin>
    <ipv4>xxx-xxx-xxx-xxx</ipv4>
    <ipv6>2001:0db8:85a3:0000:0000:8a2e:0370:7334</ipv6>
    <location schema="ISO 3166-2">
        <country>DE</country>
    </location>
</origin>
```
When the requestor (Walnut) receives the data shared as above, the policy broker will also add storage limit tag to ensure compliance with relevant rules. Daemon will automatically instruct OS/platform to remove/archive the data based on the storage limit tag.

Of course, this is just the initial idea and there are several other aspects on policy broking side that need to be thought through and issues need to be iron out. Feel free to share your thoughts and suggestions.
