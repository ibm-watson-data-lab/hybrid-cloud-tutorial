# Hybrid Cloud Tutorial (Part 1): Create a Secure Gateway 
See how easy it is to unlock your data for use in mobile and web applications, or for more flexible analysis and reporting. Bluemix Secure Gateway service lets you move data from on-prem to the cloud in a secure manner. This is a multi-part tutorial which shows how to set up a gateway and then build an app on top of it. Here, in Part 1, we’ll cover:

- Secure gateway architecture overview
- How to set up a secure gateway client

## Why use a secure gateway?

Lots of enterprises have valuable data they need to protect. To keep sensitive data secure, databases are often stored on-premise within an organization’s physical location, where staff can protect it more easily. But more and more, organizations also want to host data in the cloud, for scalability, fast access and interaction

Secure gateway lets you safely connect to an on-premise database. It works by creating a secure tunnel through which you can access protected data. The gateway encrypts and authenticates user connections, to prohibit unauthorized access. It’s a way to open your on-premise data to the cloud and enjoy the flexibility, security and scalability that it offers.

## How secure gateway works

1. When you create a new Secure Gateway on Bluemix, you are given a gateway id that will be used to start the gateway client in the on-prem network
2. Optionally add additional security by enforcing the use of a security token when starting the client
3. Start the gateway client
4. Create one or more destinations to your on-prem database servers. Each destination will be assigned its own port on the proxy server.
5. Access your data from your bluemix app, through the url given for each destination

The following diagram denotes how the different pieces connect together. 
<p align="center"><img src="images/secure_gateway_diagram.png"/></p>

## Set up a secure gateway

In this tutorial, we’ll set up a secure gateway for access a sample CouchDB database (note: the point of using CouchDB is to verify that the Secure Gateway instance works. You can replace it with any database of your choice to achieve the same results). Of course, the whole point of a secure gateway is to provide secure, protected access, but here in Part 1, we won’t yet configure specific security settings. First things first: for now, we’ll just set up the basic connection.

## Install Docker client

Docker Engine is a lightweight runtime and packaging tool for apps.  Docker works best on Linux OS. If you want to use Docker on Mac or Windows, just install the helper app, Boot2Docker.  You’ll find all the details and instructions at  https://docs.docker.com/installation/#installation. Just choose your operating system and follow the  instructions.

## Add Bluemix secure gateway service and connect
Now we’re ready to set up the gateway.

1. Go to the Bluemix site: https://console.ng.bluemix.net/
2.  Sign up or sign in.

   If you’re new to Bluemix, you can sign up for a free trial. 

3. Within the left-hand menu, click **Services**.

4. Click **+ Add a service or API**.
5. Scroll down to **Integration** and click **Secure Gateway**.
6. On the upper right of the screen, click the **APP** dropdown and choose **Leave unbound**.
7. Click **Create**.

   The Secure Gateway page opens. 

    _**Note:** If you haven’t yet installed the Docker client, you must go do so now (see previous section)._

8. Click **Add Gateway**.
9. Enter any name you want for the gateway.
10. Click **Connect it**.
11. Under **How would you like to connect this gateway?** choose **Docker**.
12. Copy the text and, if you’re on Mac or Windows, add additional text.

    If you’re on Linux, this command works fine as-is. But for Mac and Windows, you need to insert the following additional text, right after `docker run`

   ``` --net=host ```

    Insert spaces on either side. The beginning of the line should look like this:

   ``` docker run --net=host -it ibmcom/secure-gateway-client… ```

13. Go to your computer’s command line, paste in the text, and press Enter.

   Your gateway client is now connected to Bluemix. 

<p align="center"><img src="images/connected.png"</p>
<p align="center"><i><strong>Connected!</strong> If you go back and open the gateway in Bluemix, 
status in the upper right corner shows as Connected.</i></p>

   Leave your terminal command line window open. You’ll return to it in a few minutes.


## Set destination

Next, we must set the data source endpoint. This will be the on-premise source database we want to share out to the cloud. For the purposes of this tutorial, we’ll use a simple CouchDB database.

1. On your on-premise laptop or computer, install CouchDB.

    Visit http://couchdb.apache.org/, then download and installCouchDB.
3. In Bluemix, add the destination.
 
    Return to or open the gateway in Bluemix. Under **Create Destinations** Enter a name for the connection. Then enter the IP address and port of the on-premise machine where your couchDB database resides and click the +plus button on the far right of the line (use 127.0.0.1 if CouchDB is installed on the current laptop)

<p align="center"><img src="images/add_destination.png"</p>

3. If you're on Windows or Mac, configure Boot2Docker to provide access to the data.

    On Windows and Mac, you must allow access through multiple containers. To do so, open a new instance of Boot2Docker and run the following command--inserting your own IP and port information (You can use 127.0.0.1 if couchDB is running on your local laptop).
``` bash-3.2$ boot2docker ssh -R 127.0.0.:5984:127.0.0.1:5984 ```

> **Tip:** If you want to see what just ran, open up your Terminal command line and at the `cli>` prompt type `loglevel TRACE`

## Test the connection

Now you'll see some results. Follow these steps to view your local couchDB data from outside your network.

4. On a laptop or machine outside your on-premise network, open a browser and sign in to Bluemix.
5. Locate the secure gateway connection you created and click its i information button.
6. Beside the destination, click **Copy**.

<p align="center"><img src="images/copy_dest.png"</p>

7. Open another browswer or browser window and paste the string into the address bar and add /_utils e.g: http://cap-sg-prd-2.integration.ibmcloud.com:15109/_utils/

Then Press Enter.

   You'll see your couchDB dashboard (Futton app) appear. That's it!  Your database is now accessible from outside your on-premise network!

5. See the traffic in Bluemix.
    Return to or open the gateway in Bluemix. The chart shows a spike in traffic.

<p align="center"><img src="images/traffic_spike.png"/></p>

Now you know how create a secure gateway that opens your on-premise data to the cloud. You can try these same steps  with  MYSQL, DB2, MongoDB or any other databases you use on-premise. 

## Configuring security for your gateway
There are 2 type of security to consider:

1. Require a security token be used when starting the gateway client. This is useful if you want to control who can start the gateway client. To do so, simply check off the "Enforce Security Token on Client" check box that can be found in the first screen of the Add Gateway process:

<p aligh="center"><img src="images/add_gateway_security.png"/></p>

You can then find the security token in Gateway details infobox (beside the key icon). You can then use it when starting the gateway on the client:

``` 
   docker run --net=host -it ibmcom/secure-gateway-client <gatewayid> --sectoken <token>
```
2. [Advanced] You can extend TLS encryption between the gateway client and your on-premise data source. To do so, you'll need to click the "Enable client TLS" checkbox that is located in the Advance section of the destination configuration. Optionally, you can upload a certificate file (.pem extension). Note: You do not have to do this step if the certificate is self-signed.

## Stay tuned...
for additional parts of this tutorial which will show you how to include data sets from multiple sources (cloud-based and local) for combination and analysis. Last but not least, we'll show how to implement security and permissions that let you control who sees what.
