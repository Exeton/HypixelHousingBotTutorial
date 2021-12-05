# HypixelHousingBotTutorial
This will show you how to make a Minecraft Hypixel Housing Bot

Start by reading the [Mineflayer Tutorial](https://github.com/PrismarineJS/mineflayer/blob/master/docs/tutorial.md)

## Contents
1. [Setup](##Setup)
2. [Creating a project](##Creating-a-project)


##Setup
[Install Node](https://nodejs.org/en/)

Open a normal command prompt shell and run the command ```npm install mineflayer```

You can write your code using any text editor (e.g. notepad, notepad++, etc.). It is highly recommended to use [Visual Studio Code](https://code.visualstudio.com/download) for an eaiser experience.

## Creating a project

![image](https://user-images.githubusercontent.com/13612376/143779013-5284888c-caff-497a-9bc1-d56958cebab1.png)

To use Visual studio code, start by opening a folder. This folder is where all of your code files will be stored. If prompted, trust the authours of the files in the selected folder. 

![image](https://user-images.githubusercontent.com/13612376/143779097-99c14c4b-919e-4b64-b3cd-247b7ceea3e1.png)
Continue by creating a new file. Name your file HousingBot.js. When you're done, your editor should look like this:
![image](https://user-images.githubusercontent.com/13612376/143779161-cd42fc39-85ab-4f98-9c11-964e90edba14.png)

Add the following code:
```js
console.log("Test");
```

Press F5 (and the FN key if applicable) and select NodeJS as the enviroment:
![image](https://user-images.githubusercontent.com/13612376/143779273-4ba13c4f-65ab-4d91-b633-ed24f3b6eb2a.png)


After selecting NodeJS, your code should execute and display in the output window.
![image](https://user-images.githubusercontent.com/13612376/143779367-6b26f5a4-2afc-4cf7-9169-e15d83ca2e0c.png)


## Setting up a test server
It is not necessary to set up a test server and setting up a test server can be hard. This section has been included only for completeness. The eaisest way to set up a test server is using [paper spigot](https://papermc.io/downloads). I recommend paper spigot over spigot because getting a spigot jar as getting a spigot jar is a more complicated process. [Paper Requires Java](https://paper.readthedocs.io/en/latest/about/faq.html#what-do-i-need-to-run-it). [Running a paper server](https://paper.readthedocs.io/en/latest/server/getting-started.html#running-the-server). 

You can also create a bat file to run your server:
```
cd C:/Path/To/Server
java -Xms2G -Xmx2G -jar paper-###.jar --nogui
```
Ensure that you have replaced paper-###.jar with the name of your server file.

If the server is working, you should be able to connect to the server from the computer running the server. Use the ip 127.0.0.1


### Finding a computers internal ip address

Open command prompt and run the following command:
```
ipconfig
```

Under Ethernet adapter Ethernet, your IPv4 Address is your local ip. This ip will allow connections from within the network. This ip will typically begin with 192.168. 
This ip will change, so if you have access to your router, it is recommended to assign the host computer a static ip address.

### Enabling connections from other computers on your network
Find the computer's internal ip address. Use this as the ip 

Note: If connection fails, you may need to open port 25565 to tcp on the firewall of the computer hosting the server.

### Enabling connections for computers not on your network
Find the ip of the host computer. Forward all TCP traffic on port 25565 to the host computer's ip. To test if your server is working. Google "What's my ip" and try connecting to the ip. 

## Connecting to a Hypixel
We will remove some code from the [Echo Example](https://github.com/PrismarineJS/mineflayer#echo-example) to test that the bot is working.


Replace the code in HousingBot.js with:
```js
const mineflayer = require('mineflayer')

const bot = mineflayer.createBot({
  host: 'play.hypixel.net', // This can also be your test server ip
  username: 'email@example.com', // minecraft username
  password: '12345678'
  // auth: 'mojang'              // only set if you need microsoft auth, then set this to 'microsoft'
})


// Log errors and kick reasons:
bot.on('kicked', console.log)
bot.on('error', console.log)
```

Put in your Minecraft username and password.
![image](https://user-images.githubusercontent.com/13612376/143780950-4c98cf2a-db79-4787-8f9e-473d9df945e5.png)

Run your bot. It will log into whatever lobby it last logged out of. If the bot is on your friends list, it should be eaiser to tell it connected. 

If there are any exceptions, they will show up in your debug console.
![image](https://user-images.githubusercontent.com/13612376/143781031-14288934-d2ba-4bbc-b9ec-0e484b0412e2.png)

## Joining a house
There is a three step process for joining a house on Hypixel
1. Go to the housing lobby
2. Type /visit HouseOwnerName
3. Click the house you'd like to join

To have your bot go to the housing lobby, go onto Hypixel on the bot account, move the bot into the housing lobby, and log out. This will cause the bot to log into the housing lobby when you run your bot script.


Now we need our bot to run /visit HouseOwnerName. An inventory will pop up on the bot's end. We need the bot to searh through it to find the house we want by name. The bot must then click that item. If there are too many houses with the same name or no matches for the house name, the bot should notify the user. Additionally, the bot should log all the houses the house owner owns.

```js
const mineflayer = require('mineflayer')

const bot = mineflayer.createBot({
  host: 'play.hypixel.net', // This can also be your test server ip
  username: 'email@example.com', // minecraft username
  password: '12345678'
  // auth: 'mojang'              // only set if you need microsoft auth, then set this to 'microsoft'
})

const housingOwnerName = "houseOwnerName";
const housingNamePart = "purple";//A case insensitive substring of the housing name used to find the house. Beware of color symblols.

bot.once('spawn', runVisitCommand);

bot.on('windowOpen', (window) => {
    console.log("Window opened");
    let matches = window.containerItems().filter(item => item.customName.toLowerCase().includes(housingNamePart.toLowerCase()));

    if (matches.length == 0){
        console.log();
        console.log(`No matches for housing name "${housingNamePart}". Is the house visible to you?`);
        console.log(`Possible houses for ${housingOwnerName}`);
        const housingCanidates = window.containerItems().filter(item => item != null && item.customName != "");
        for (const housingCanidate of housingCanidates){
            console.log("\t" + housingCanidate.customName);
        }
    }

    if (matches.length > 1){
        console.log();
        console.log(`Too many matches found for housing name ${housingNamePart}`);
        console.log(`Matches`);
        for (const match of matches)
            console.log("\t" + match.customName);     
    }

    if (matches.length == 1){
        bot.once('spawn', botJoinedHouse);
        console.log('Slot to click: ' + matches[0].slot);
        bot.clickWindow(matches[0].slot, 0, 0);
    }

});

function runVisitCommand(){
    console.log(`Executing /visit ${housingOwnerName}`);
    bot.chat(`/visit ${housingOwnerName}`)
}

function botJoinedHouse(){
    console.log(`Joined ${housingOwnerName}'s house'`);
}

// Log errors and kick reasons:
bot.on('kicked', console.log)
bot.on('error', console.log)

```
