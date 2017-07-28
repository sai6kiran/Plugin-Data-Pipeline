# What is VNE

This is a tool created to implement and deploy virtual nodes to run various processes and plugins. This documentation does not tell you how to create these nodes but will explain how to use them. Using this documentation, tutorials provided below and and the existing virtual nodes, you can create various plugins that will test your software, through a publisher script, and will enable you create various workers that can process and handle various i/o of the plugin, thus sending you the resulting data output.
Furthermore, these workers can be modified to run at the back-end of the beehive server permanently for future use. The following sections below will explain on how to install the corresponding packages and dependencies, and to implement your own publisher and worker scripts to test your respective software in beehive-dev.


# Installation

  * [PyWaggle](https://github.com/waggle-sensor/pywaggle.git)
  * [OpenSSL/TLS Certification & Registeration]()
  * [Hello Publisher](https://github.com/seanshahkarami/hello-publisher.git)
                     

# How to use VNE

VNE mainly comprises of two scripts, you the developer, must implement. The publisher script and the worker script. Below is a diagram illustrating a model of how the end-to-end communication takes place between a publisher and a consumer/database.

|    Creating the Publisher Script:    |    Creating the Worker Script:    |
|:---------------|:--------------|
|The following module will explain how to implement the outershell of your plugin client.|The following module will explain how to implement the outershell of your worker script.|
|`#!/usr/bin/env python3`|`#!/usr/bin/env python3`|                
|`from waggle import beehive`| `from waggle import beehive` |
|`import pika`| `import pika` |
|The following packages and dependicies to import in order to run plugin. Other packages can be imported.|The following packages and dependicies to import in order to run worker client. Other packages can be imported.|
|`config = beehive.ClientConfig(`|`config = beehive.ClientConfig(`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`host='beehive.mcs.anl.gov',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`host='beehive.mcs.anl.gov',`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`port=23181,`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`port=23181,`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`node='000002000000000(n)',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`node='000002000000000(n)',`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`username = '(username1)',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`username = '(username2)',`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`password = '(password1)'`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`password = '(password2)'`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`cacert='/path/to/cacert.pem',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`cacert='/path/to/cacert.pem',`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`cert='/path/to/cert.pem',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`cert='/path/to/cert.pem',`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`key='/path/to/key.pem')`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`key='/path/to/key.pem')`|
|The Config function initalizes your config paramters, allowing you to: Connect to beehive-dev's rabbitmq server, Connect and transmit data to the respective virtual node, and Uses the respective authentication certificates for a secure connection.|The Config function initalizes your config paramters, allowing you to: Connect to beehive-dev's rabbitmq server, Connect and transmit data to the respective virtual node, and Uses the respective authentication certificates for a secure connection.|
| | **This part of the script takes in your software/program that will parse and process the respective data coming from your plugin.**|
|`client = beehive.PluginClient(` | `def callback(data):`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`name='(name_of_queue)',`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`value = data.get('body')`|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`config=config)`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`return(value)`|
|This Client function allows you to create your client script and connection. It allows you to declare a queue to send all your information and sets your configuration parameters to your config file.|Receiving messages from the queue is more complex. It works by subscribing a callback function to a queue. Whenever we receive a message, this callback function is called by the Pika library. In our case this function will send the ouput to beehive-dev's interface: [http://10.10.10.5/?all=true](http://10.10.10.5/?all=true)
|**The remaining part of the script takes in your software/program/data to be sent to beehive-dev.**|
|At the end of your program, in order to send your resepctive data/plugin to rabbitmq server, you must add the following line of code:|`client = beehive.PluginClient(`|
|`client.publish('(key)', (value))`|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`name='(name_of_queue)',`|
|Sends it with a key and value.|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`config=config,`|
| |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`callback=callback)`|
| | This Client function allows you to create your client script and connection. It allows you to declare a queue to send all your information and sets your configuration parameters to your config file and sets your callback function as well.|

# Outcome of VNE

The data sent by your plugin is sent to db-raw at: http://10.10.10.5/?all=true

The processed data, done by worker script, is sent to db-decoded to: http://10.10.10.5/?all=true

The following output data is in the form of a file that can be downloaded from the website. This website is beehive-dev's web interface.


# Sample Use of VNE

The following section will display a sample plugin script and worker used to send and process data through communication with beehive-dev.

The following code demonstrates a simple fibonacci program that runs within beehive-dev and computes the fibonacci number of any data, in the form of a number, sent through the plugin.

| Publisher Script: | Worker Script: |
|:------------------|:-----------------|
|![image 1](https://user-images.githubusercontent.com/25256730/28250983-a740e8b6-6a39-11e7-88b3-9a71f368088d.png)|![image](https://user-images.githubusercontent.com/25256730/28250974-96f004c4-6a39-11e7-96d9-470f9cf822b7.png)|

