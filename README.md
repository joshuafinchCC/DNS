<p align="center">
<img src="https://github.com/joshuafinchCC/DNS/assets/155266044/a59adb21-a953-4845-8fa4-6c1e7373321c" height = 20% width = 60%/>
</p>

<h1 align = "center">Understanding and Using DNS (Domain Name System)</h1>
This guide covers the configuration and use of Domain Name Systems. DNS serves as a kind of phonebook for the Internet, translating domain names to IP addresses. This allows your internet browser to load Internet resources such as "www.google.com" without already knowing the IP address associated with that website. This guide follows up from the <a href = "https://github.com/joshuafinchCC/Activedirectory-config">Installing and Configuring of an Active Directory Guide</a>. Here we will go through some simple exercises to understand how DNS works

<br />

<h2>Environments and Technologies Used</h2>
<ul>
  <li>Microsoft Azure</li>
  <li>Remote Desktop Connections</li>
  <li>Active Directory Domain Services</li>
  <li>Command Prompt</li>
</ul>

<br />

<h2>Operating Systems Used</h2>
<ul>
  <li>Windows Server 2022</li>
  <li>Windows 10 Pro (21H2)</li>
</ul>

<br />

<h2>DNS Exercises</h2>

<h3>A-Record</h3>

<p>
  <ul>
    <li>The most basic type of DNS record is an "A-Record" or address record. This shows the IP address of an associated domain.</li>
    <li>Connect and log in to your Domain Controller and Client virtual machines as administrators</li>
    <li>Within <b>Client1</b>, open up Command Prompt and attempt to ping "mainframe" by entering the command <b>ping mainframe</b>; notice that it fails. Our Client machine checks its cache for "mainframe" and gets nothing. Then it checks its host files, and finally it checks the DNS. Until we set an IP for "mainframe", our virtual machine doesnt know who to try and talk to :(</li>
<p align="center">
<img src="https://github.com/joshuafinchCC/DNS/assets/155266044/961e44fd-ab38-4809-938f-46f10352f644" height = 20% width = 60%/>
</p>
 <li>To create a DNS A-Record, use Remote Desktop to connect to <b>DC1</b> and open the <b>Server Manager</b>. Navigate to Tools->DNS and select DC1. The <b>Forward Lookup Zones</b> tab shows the human readable records (google etc.)that DC1 currently has on file.</li>
    
  <p align="center">
<img src="https://github.com/joshuafinchCC/DNS/assets/155266044/a1a4e572-bed3-43f3-9c77-0c485dd74bd4" height = 20% width = 60%/>
</p>
    
  <li>Right click on this page and create a <b>New Host (A or AAAA)</b>. We will name the host <b>mainframe</b> and the IP address should be the same IP as the domain controller so that ping can resolve. Once the information is entered, click <b>Add Host</b> and refresh the DNS server so that the new record can be updated. This is basically adding a new entry in our DNS phonebook, defining "mainframe" as the IP "10.0.0.4". Once added hit the refresh icon on the DNS manager window to refresh our A-Records.</li>

 <p align="center">
<img src="https://github.com/joshuafinchCC/DNS/assets/155266044/fad97558-2393-4556-9581-4d916285537e" height = 20% width = 60%/>
</p>
    <li>Heading back to the Client VM and attemptting to ping the "mainframe" again, we should now see a successful ping!</li>
    
   <p align="center">
<img src="https://github.com/joshuafinchCC/DNS/assets/155266044/086fe211-b77d-4e5a-9990-280cbc367d60" height = 20% width = 60%/>
</p>

<br />

<h3>Local DNS Cache</h3>

<p>
  <ul>
    <li>Now we can observe how our client machine's DNS cache handles updates to our A-Records</li>
    <li>Within <b>DC1</b>, head to the <b>DNS Manager</b> and locate the mainframe host we've created and edit the IP address to <b>8.8.8.8</b> (remember to refresh after any record changes)</li>
    <p align="center">
<img src="https://github.com/joshuafinchCC/DNS/assets/155266044/d43742b4-eb6e-406e-8b39-354ad729e406" height = 20% width = 60%/>
</p>

  <li>Heading back to the Client VM, ping the mainframe and you'll notice it pings the mainframe's old IP address and not 8.8.8.8. As our client virtual machine has a cache of the old IP address, it skips contacting DC1 for "updates". To get Client1 to ping our newly updated record, we must flush the cache.
   <li>To do this, on <b>Client1</b> run Command Prompt as <b>Administrator</b> and enter the command <b>ipconfig /flushdns</b> and observe that the cache is now empty</li>
   <p align="center">
<img src="https://github.com/joshuafinchCC/DNS/assets/155266044/85d57cf9-2812-4087-b7bb-40e7dfb1924d" height = 20% width = 60%/>
</p>

 <li>Now if we try to ping the mainframe again, our client VM will pull from the updated DNS records</li>
     <p align="center">
<img src="https://github.com/joshuafinchCC/DNS/assets/155266044/e640df94-75d5-428e-8c1f-fd55b9bb470e" height = 20% width = 60%/>
</p>
    

<br />

<h3>CNAME Record</h3>

<p>
  <ul>
    <li>"CNAME" is abbreviated form of "Canonical Name". This basically records a name to another name instead of to an IP address unlike A-Record. With CNAME our machine can being set words such as "search" and associate it with websites such as google</li>
    <li>Within <b>DC1</b>, open the <b>DNS Manager</b>b> and go to the domain we created within the <b>Forward Lookup Zones</b> tab</li>
    <li>Right click on the page and create a <b>New Alias (CNAME)</b>. We will name the alias <b>search</b>, and the fully qualified domain name (FQDN) to any website such as <b>www.google.com</b>. Once the information is entered, click <b>OK</b> and refresh the DNS server so that the new record can be updated.</li>
    

<p align="center">
<img src="https://github.com/joshuafinchCC/DNS/assets/155266044/83716053-7502-40a1-82fe-643dc7b02e9b" height = 20% width = 60%/>
</p>
    
    <li>Back to the Client VM, ping the record we've named "search" by the command <b>ping search</b> and observe the results of the CNAME Record. It should ping to the website listed in the FQDN (for this case, Google)</li>
    <ul>
      <li><img src = "https://github.com/ColtonTrauCC/dns/assets/147654000/22b54d0c-2c8a-4ed3-a3be-b5a752c13368" width = 80% height = 80% /></li>
    </ul>

<p align="center">
<img src="" height = 20% width = 60%/>
</p>
    
    <li>Performing the nslookup command with "search" (<b>nslookup search</b>) will result in an name server lookup for Google</li>
  </ul>
</p>

<br />
