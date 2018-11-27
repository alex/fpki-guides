Use one of these options to verify COMMON has been redistributed to Windows end-points:

**Automated Solutions (Preferred)**:
- [Use BigFix](#use-bigfix)
- [Use LANDesk](#use-landesk)

**Manual Solutions**:
- [Use Microsoft Certificate Manager](#use-microsoft-certificate-manager)
- [Use Microsoft Registry Editor](#use-microsoft-registry-editor)

#### Use BigFix

**Download and Verify the BigFix Enterprise Suite Analysis File**
1. Download the BigFix Enterprise Suite Analysis file (.bes) listed [here](../../truststores/FPKIRootCertificateDetection.bes){:target="_blank"}.

2. Verify that the SHA-256 hash of the .bes file matches this one:

     ```
     b7ddc5d443dc34e588baafa69fffe4e17ca7cbff58d9894785a2c627156f04af
     ```

     _Sample procedure using certutil_:

     ```
     > certutil -hashfile [DOWNLOAD_LOCATION]\FPKIRootCertificateDetection.bes SHA256
     ```
    
**Import and Activate the BigFix Enterprise Suite Analysis File**

1. Login to your enterprise's BigFix Console

2. Once the console is loaded...
- Select **File**, then **Import**
- *Browse* to the verified analysis file via the Windows file explorer
- Select **Create in Site** and choose which site you'd like to import the analysis to
- Click **Okay**

3. From the list of imported analysis files, right click the recently imported file and click **Activate Globally**

**View Analysis Results Using the Console**

1. Login to your enterprise's BigFix Console

2. Select **Analyses** under the **All Content** domain

3. Search for and select the “Federal Public Key Infrastructure Root Certificate Detection (FPKI)” analysis entry

4. Select the **Results** tab. If COMMON was successfully redistributed to an end-point, the **FPKI: Sha1** column will be populated, as pictured below. If a result is not populated, either the end-point was unavailable at the time of report generation, or COMMON has not yet been redistributed.

![Sample Output]({{site.baseurl}}/img/fpki-analysis-results.png){:style="width:85%;"}

**View Analysis Results Using IBM Web Reports**
1. Login to IBM Web Reports

2. Select the **Explore Data** tab

3. Click the **Edit Columns** button and search for the "FPKI: Sha1" property. Once found, click the selection radio button to aggregate data related to this property.

4. Entries for end-points monitored in the environment will update automatically to display the status of COMMON's redistribution. Note: results reporting "Sha1 Unavailable" or "not reported" would indicate COMMON was not successfully redistributed, or the end-point was unavailable at the time of report generation.
     
#### Use LANDesk
1. Open LANDesk
2. Create a custom data registry item 
- Click **Tools** > **Reporting/Monitoring** > **Manage software list**.
- Expand **Custom Data** and click **Registry items**.
- Click the **Add** button on the toolbar.
- Select and enter data as described below, depending on operating system versions in your environment.

     Windows 32 Bit Versions
     ```
     Root  Key: HKLM
     Key: SOFTWARE\Policies\Microsoft\SystemCertificates\Root\Certificates\905F942FD9F28F679B378180FD4F846347F645C1
     Value: Blob
     Attribute Name: Custom Data – FCPCAWin32 – Certificate
     ```

     Windows 64 Bit Versions
     ```
     Root  Key: HKLM
     Key: SOFTWARE\WOW6432Node\Policies\Microsoft\SystemCertificates\Root\Certificates\905F942FD9F28F679B378180FD4F846347F645C1
     Value: Blob
     Attribute Name: Custom Data – FCPCAWin64 - Certificate
     ```

3. Create a query for the custom data registry item
- Expand **Network View** > **Queries**
- Right click **My Queries** > **New Query**
- Enter a query name, for example, "FCPCA Verification: Win32"
- Under **Machine Component** expand and select: **Computer** > **Custom Data** > **FCPCAWin32**
- For the *Boolean* option select **Exists**
- In the Displayed Scanned Values you should see the Registry Value for BLOB: 
```
0400000001000000100000008c42b6360dd024ce4cb1ba06d26a6bc90f0000000100000020000000375dc361146cfbdd26f82cbf8a4c1a173c9b6a11ac61dfe4c28cac281888ed22140000000100000014000000ad0c7a755ce5f398c479980eac28fd97f4e702fc19000000010000001000000093ee7e01c999df57176c2c (truncated value)
```
- Select above value and then click **Insert**
- Add fields to the query by clicking **Select Columns: Machine Component** and select:
- **Computer** > **Display Name** (Alias Device Name) and **Add**
- **Computer** > **OS**, **Version** and **Add**
- **Computer** > **Custom Data**, **FCPCA** and **Add**

4. Run the custom query by double clicking on it.
- Results will look similar to the following results.
![Sample Output]({{site.baseurl}}/img/landesk-results.jpg){:style="width:70%;"}


### Use Microsoft Certificate Manager

1. Open Microsoft Certificate Manager:  **Start**, type **certlm.msc**, and then press **Enter**.

2. Navigate to **Trusted Root Certification Authorities** -> **Certificates**. 

3. You may see 2 (or more) copies of COMMON, depending on how they were distributed. The screenshot below lists 3 entries for COMMON:
* The first entry (“dashed” border) is from the Microsoft Certificate Trust List (CTL) (i.e., certificate store). Microsoft-distributed copies show multiple **Intended Purposes** values and a **Friendly Name** of *U.S. Government Common Policy*.
* The remaining two entries (examples of enterprise-distributed copies) result from following the procedures in this Playbook. Enterprise-distributed copies show an **Intended Purposes** value of *ALL* and a **Friendly Name** of *None*.

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
