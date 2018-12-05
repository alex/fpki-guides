Use one of these options to verify COMMON has been redistributed to Windows workstations and devices:

**Automated Solutions (preferred)**:
- [Use BigFix](#use-bigfix)
- [Use LANDesk](#use-landesk)

**Manual Solutions**:
- [Use Microsoft Certificate Manager](#use-microsoft-certificate-manager)
- [Use Microsoft Registry Editor](#use-microsoft-registry-editor)

#### Use BigFix

**Download and Verify the BigFix Enterprise Suite Analysis File**
1. Download the BigFix Enterprise Suite Analysis file (.bes) listed [here](../../truststores/FPKIRootDetection.bes){:target="_blank"}.

2. Verify that the SHA-256 hash of the .bes file matches this one:

     ```
     3ef0277f9edffc965baa0576c2626343f8e2d0a3d37da4e73595ffb06b6116ee
     ```

     _Sample procedure using certutil_:

     ```
     > certutil -hashfile [DOWNLOAD_LOCATION]\FPKIRootDetection.bes SHA256
     ```
    
**Import and Activate the BigFix Enterprise Suite Analysis File**

1. Login to your enterprise's BigFix Console (e.g., **Start** > **IBM BigFix** > **IBM BigFix Console**)

2. Import the *Federal PKI Root Detection Analysis* file
- Select **File**, then **Import**
- *Browse* to the verified analysis file via the Windows file explorer and click **Open**
- Select the **Create in Site** drop-down selection and choose which site you'd like to import the analysis to (e.g., "Master Action Site"
- Click **Okay**

3. Click **Analyses** from the left-hand navigation to reveal imported analysis files. Right click the *Federal PKI Root Detection Analysis* entry and click **Activate Globally**, if the analysis is not activated by default.

**View Analysis Results Using the Console**

1. Login to your enterprise's BigFix Console

2. Click **Analyses** from the left-hand navigation to reveal imported analysis files.

3. Select the *Federal PKI Root Detection Analysis* entry, and click the **Results** tab.

4. Review the analysis results. If COMMON was successfully redistributed to a Windows workstations and device, the *Has COMMON Been Redistributed?* column will present **True**, as pictured below. 
![Sample Output]({{site.baseurl}}/img/bigfix-results.jpg){:style="width:70%;"}
     
#### Use LANDesk
1. Open LANDesk (e.g., **Start** > **LANDesk Management** > **Desktop Manager**)
2. Create a custom data registry item 
- Click **Tools** > **Reporting/Monitoring** > **Manage software list**
- Expand **Custom Data** and click **Registry items**
- Click the **Add** button on the toolbar
- Select and enter the registry data described below, depending on operating system versions in your environment, and method of COMMON's redistribution (e.g., GPO or certutil).

     **Windows 32 Bit Versions**
     
     GPO Distribution
     
          ```
          Root  Key: HKLM
          Key: SOFTWARE\Policies\Microsoft\SystemCertificates\Root\Certificates\905F942FD9F28F679B378180FD4F846347F645C1
          Value: Blob
          Attribute Name: Custom Data – FCPCAWin32 GPO – Certificate
          ```
          
     certutil Distribution
     
          ```
          Root  Key: HKLM
          Key: SOFTWARE\Microsoft\EnterpriseCertificates\Root\Certificates\905F942FD9F28F679B378180FD4F846347F645C1
          Value: Blob
          Attribute Name: Custom Data – FCPCAWin32 certutil – Certificate
          ```

     **Windows 64 Bit Versions**
     
     GPO Distribution
     
          ```
          Root  Key: HKLM
          Key: SOFTWARE\WOW6432Node\Policies\Microsoft\SystemCertificates\Root\Certificates\905F942FD9F28F679B378180FD4F846347F645C1
          Value: Blob
          Attribute Name: Custom Data – FCPCAWin64 GPO - Certificate
          ```
          
      certutil Distribution
      
          ```
          Root  Key: HKLM
          Key: SOFTWARE\WOW6432Node\Microsoft\EnterpriseCertificates\Root\Certificates\905F942FD9F28F679B378180FD4F846347F645C1
          Value: Blob
          Attribute Name: Custom Data – FCPCAWin64 certutil - Certificate
          ```

3. Create a query for the custom data registry item(s)
- From the left-hand navigation, expand **Network View** > **Queries**
- Right click **My Queries** and select **New Query**
- Enter a query *name*, for example, "FCPCA Verification: Win32 Machines"
- Under **Machine Component**, expand **Computer** > **Custom Data** and select the registry items relevant to the operating system types and method of COMMON's redistribution within your environment.
- For the **Boolean** option select **Exists**
- In the **Displayed Scanned Values** option you should see a registry value for **BLOB**, click **Insert**
- Add additional desired result fields (e.g., Display Name, OS, etc.) to the query by clicking **Select Columns: Machine Component**, and then **Add**

4. Run the custom query by double clicking on it.
- If COMMON has been succesfully redistributed, results will look similar to the image below.
![Sample Output]({{site.baseurl}}/img/landesk-results.jpg){:style="width:70%;"}


### Use Microsoft Certificate Manager

1. Open Microsoft Certificate Manager:  **Start**, type **certlm.msc**, and then press **Enter**.

2. Navigate to **Trusted Root Certification Authorities** -> **Certificates**. 

3. You **may** see multiple copies of COMMON, depending on how the certificate is distributed within your environment. For example, the screenshot below lists 3 entries for COMMON:
* The first entry (“dashed” border) is from the Microsoft Certificate Trust List (CTL) (i.e., certificate store). Microsoft-distributed copies show multiple **Intended Purposes** values and a **Friendly Name** of *U.S. Government Common Policy*.
* The remaining two entries (examples of enterprise-distributed copies) result from following the procedures in this Playbook. Enterprise-distributed copies show an **Intended Purposes** value of *ALL* and a **Friendly Name** of *None*. Verify at least one entry for COMMON appears similar to this.

![Sample Steps]({{site.baseurl}}/img/verify_trust.png){:style="width:85%;"}

*Optionally, you can use this method:* 

1. From the **Trusted Root Certification Authorities** screen, select **View** -> **Options**.
2. In the **View Options** box, *check* the **Physical certificate stores** checkbox. 
3. At the main screen's side panel, click the **>** next to **Trusted Root Certification Authorities** to see a list of sub-directories: *Registry*, *Third Party*, *Group Policy*, *Enterprise*, and *Smart Card*

If you redistributed COMMON via _certutil_ (for example), COMMON will be listed in the *Enterprise* sub-directory.

If you redistributed COMMON via Group Policy Object (for example), COMMON will be listed in the *Group Policy* sub-directory.


### Use Microsoft Registry Editor

1. Open Microsoft Registry Editor:  **Start**, type **regedit.exe**, and then press **Enter**.

2. Verify the presence of the registry keys listed below, depending on how COMMON was redistributed.

     If COMMON was redistributed via GPO, the following registry keys should exist:
     - HKLM:\SOFTWARE\Policies\Microsoft\SystemCertificates\Root\Certificates\905F942FD9F28F679B378180FD4F846347F645C1\
     - HKLM:\SOFTWARE\WOW6432Node\Policies\Microsoft\SystemCertificates\Root\Certificates\905F942FD9F28F679B378180FD4F846347F645C1\ 

     If COMMON was redistributed via certutil, the following registry keys should exist:
     - HKLM:\SOFTWARE\Microsoft\EnterpriseCertificates\Root\Certificates\905F942FD9F28F679B378180FD4F846347F645C1\
     - HKLM:\SOFTWARE\WOW6432Node\Microsoft\EnterpriseCertificates\Root\Certificates\905F942FD9F28F679B378180FD4F846347F645C1\
