# Installing Arch the Hard Way

I just want to document everything we do in arch installation.\
First I want to document the steps then overtime I will also provide explaination for the steps as to know more about what we are actually doing.

Lets get started......

First things first we need to setup our network connection.

# Setting up Network Connection

## Checking if it's connected 

- One simple way to check is using ping method, that is basically you ping to an IP.
- Other one can be using tools like `iwctl` which comes with arch.
	- It is used to make WiFi connection during your installation.
- Other way could be using `ip addr show`, if you are connected by wire it will show it under ether net connection.

## If not connected 

- First thing to do is getting the name of your WiFi adapter 
- Then getting using the `iwctl` to access the tool.
- Now next thing we need to get done is getting the networks around us 
	`station wlan0 get-networks`
- Now exit iwctl and type: 
	`iwctl --passphrase "Yourpassword" station wlan0 connect "YourNetworkName"`
- Then check for connectivity using the steps mentioned above.